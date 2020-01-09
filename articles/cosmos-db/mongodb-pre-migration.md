---
title: Áttelepítés előtti lépések a Azure Cosmos DB API-MongoDB való áttelepítéshez
description: Ez a dokumentum áttekintést nyújt a MongoDB és a Cosmos DB közötti adatáttelepítés előfeltételeiről.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 4dc7038d0ff5180f15a43268fd3f3aa0cbb0c7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445196"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Áttelepítés előtti lépések a MongoDB-ből Azure Cosmos DB API-MongoDB való áttelepítéshez

Mielőtt áttelepíti az adatait a MongoDB (helyszíni vagy a felhőben (IaaS)) Azure Cosmos DB a MongoDB API-ra, a következőkre van szüksége:

1. [Azure Cosmos DB-fiók létrehozása](#create-account)
2. [A munkaterhelésekhez szükséges átviteli sebesség becslése](#estimate-throughput)
3. [Válasszon ki egy optimális partíciós kulcsot az adataihoz](#partitioning)
4. [Az adataihoz beállítható indexelési szabályzat ismertetése](#indexing)

Ha már végrehajtotta a fenti előfeltételeket az áttelepítéshez, tekintse [meg a MongoDB Azure Cosmos db-adatok áttelepítése a MONGODB API](../dms/tutorial-mongodb-cosmos-db.md) -ját a tényleges adatáttelepítési utasításokhoz. Ha nem, ez a dokumentum útmutatást nyújt az előfeltételek kezeléséhez. 

## <a id="create-account"></a>Azure Cosmos DB fiók létrehozása 

Az áttelepítés megkezdése előtt létre kell [hoznia egy Azure Cosmos-fiókot a MongoDB-hez készült Azure Cosmos db API](create-mongodb-dotnet.md)-val. 

A fiók létrehozásakor megadhatja a beállításokat, hogy [globálisan terjessze](distribute-data-globally.md) az adatait. Lehetősége van a többrégiós írások (vagy a több főkiszolgálós konfiguráció) engedélyezésére is, amely lehetővé teszi, hogy mindegyik régió írási és olvasási régióként is használható legyen.

![Fiók létrehozása](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a>A számítási feladatokhoz szükséges átviteli sebesség becslése

Mielőtt elindítja az áttelepítést a [Database Migration Service (DMS)](../dms/dms-overview.md)használatával, becsülje meg az Azure Cosmos-adatbázisok és-gyűjtemények számára rendelkezésre álló átviteli sebesség mértékét.

Az átviteli sebesség kiépíthető a következők valamelyikére:

- Gyűjtemény

- Adatbázis

> [!NOTE]
> A fentiek kombinációja is lehet, ha egy adatbázis egyes gyűjteményei dedikált átviteli sebességgel rendelkezhetnek, és mások is megoszthatják az átviteli sebességet. További részletekért tekintse meg az [átviteli sebesség beállítása egy adatbázisban és a tároló](set-throughput.md) oldalon.
>

Először döntse el, hogy szeretné-e kiépíteni az adatbázis vagy a gyűjtési szint átviteli sebességét, vagy a kettő kombinációját. Általánosságban elmondható, hogy egy dedikált átviteli sebességet kell beállítani a gyűjtemény szintjén. Az adatbázis szintjén kiépített átviteli sebesség lehetővé teszi, hogy az adatbázisban lévő gyűjtemények megosszák a kiosztott átviteli sebességet. A megosztott átviteli sebességnek megfelelően azonban nem garantálható egy adott átviteli sebesség az egyes gyűjtemények esetében, és nem érhető el előre jelezhető teljesítmény egy adott gyűjteményen.

Ha nem biztos abban, hogy mekkora átviteli sebességet kell kijelölni az egyes gyűjteményekhez, az adatbázis szintű átviteli sebességet is kiválaszthatja. Gondolja át az Azure Cosmos-adatbázison konfigurált kiépített átviteli sebességet a MongoDB virtuális gép vagy a fizikai kiszolgáló számítási kapacitásának megfelelő logikai értékre, de költséghatékonyan, a rugalmas skálázási képességgel. További információ: az [átviteli sebesség kiépítése az Azure Cosmos-tárolók és-adatbázisok](set-throughput.md)számára.

Ha az átviteli sebességet az adatbázis szintjén hozza létre, az abban az adatbázisban létrehozott összes gyűjteményt partíció/szegmens kulccsal kell létrehozni. További információ a particionálásról: [particionálás és horizontális skálázás Azure Cosmos DBban](partition-data.md). Ha az áttelepítés során nem ad meg partíciót/szegmens kulcsot, a Azure Database Migration Service automatikusan feltölti a szegmens kulcs mezőt egy olyan *_id* attribútummal, amely automatikusan létrejön az egyes dokumentumokhoz.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>A kiépíteni kívánt kérési egységek (RUs) optimális száma

Azure Cosmos DB az átviteli sebességet előre kiépítve, és a rendszer a kérelmek egységében (RU) mérve másodpercenként méri. Ha olyan munkaterhelésekkel rendelkezik, amelyek virtuális gépen vagy helyszíni MongoDB futnak, a fizikai erőforrások egyszerű absztrakciója, például egy virtuális gép vagy egy helyszíni kiszolgáló mérete, valamint az általuk birtokolt erőforrások, például a memória, a processzor és a IOPs. 

A virtuális gépekkel és a helyszíni kiszolgálókkal ellentétben a RUs bármikor könnyedén méretezhető. A kiépített RUs száma másodpercek alatt módosítható, és csak az adott egyórás időszakra kiépített RUs maximális száma után számítunk fel díjat. További információ: [kérelmek egységei a Azure Cosmos DBban](request-units.md).

A következő kulcsfontosságú tényezők befolyásolják a szükséges RUs-ket:
- **Elem (például dokumentum) mérete**: az elem/dokumentum mérete nő, az elem/dokumentum olvasásához vagy írásához felhasznált RUs száma is nő.
- **Elem tulajdonságainak száma**: Ha az összes tulajdonságnál az [alapértelmezett indexelést használja](index-overview.md) , az elem írásához felhasznált RUs száma nő, mivel az elem tulajdonságainak száma nő. Az [indexelt tulajdonságok számának korlátozásával](index-policy.md)csökkentheti a kérések egység általi felhasználását írási műveletekhez.
- **Egyidejű műveletek**: a felhasználható kérelmek köre attól függ, hogy milyen gyakorisággal történik a különböző szifilisz-műveletek (például írások, olvasások, frissítések, törlések) és összetettebb lekérdezések végrehajtása. A [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) segítségével az aktuális MongoDB-adatainak párhuzamossági igényeit is kipróbálhatja.
- **Lekérdezési minták**: a lekérdezés bonyolultsága befolyásolja, hogy a lekérdezés hány kérési egységet használ fel.

Ha a [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) használatával exportálja a JSON-fájlokat, és megértette, hogy hány írási, olvasási, frissítési és törlési műveletet hajt végre, akkor a [Azure Cosmos db Capacity Planner](https://www.documentdb.com/capacityplanner) segítségével megbecsülheti a kiépíthető RUs kezdeti számát. A Capacity Planner nem befolyásolja az összetettebb lekérdezések költségeit. Ha tehát összetett lekérdezésekkel rendelkezik az adatain, a rendszer további RUs-t fog használni. A számológép azt is feltételezi, hogy az összes mező indexelve van, és a munkamenet konzisztenciája van használatban. A lekérdezési költségek megismerésének legjobb módja az adatok (vagy mintaadatok) Azure Cosmos DBba való migrálása, a [Cosmos db végponthoz való kapcsolódás](connect-mongodb-account.md) és a MongoDB-rendszerhéjból való lekérdezés futtatása a `getLastRequestStastistics` parancs használatával a kérések díja, amely a felhasználható RUs mennyiségét fogja kinyerni:

`db.runCommand({getLastRequestStatistics: 1})`

Ez a parancs a következőhöz hasonló JSON-dokumentumot fog kiadni:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Ha megértette, hogy a lekérdezés által felhasznált RUs száma és az adott lekérdezés párhuzamossága szükséges, beállíthatja a kiépített RUs számát. Az RUs optimalizálása nem egyszeri esemény – folyamatosan optimalizálni vagy bővíteni kell az RUs kiosztását, attól függően, hogy nem várható-e nagy adatforgalom, vagy az adatok importálásával szemben.

## <a id="partitioning"></a>Válassza ki a partíciós kulcsot
A particionálás kulcsfontosságú szempont, mielőtt migrál egy globálisan elosztott adatbázisba, például Azure Cosmos DBba. Azure Cosmos DB particionálás használatával méretezi az egyes tárolókat egy adatbázisban az alkalmazás méretezhetőségének és teljesítményének kielégítéséhez. A particionálás során a tároló elemei a logikai partíciók nevű különálló részhalmazokra vannak osztva. Az adatok megfelelő partíciós kulcsának kiválasztásával kapcsolatos részletekért és javaslatokért tekintse meg a [partíciós kulcs kiválasztása szakaszt](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Az adatai indexelése
Alapértelmezés szerint a Azure Cosmos DB indexeli az összes adatmezőt a betöltés után. Az [indexelési szabályzatot](index-policy.md) bármikor módosíthatja Azure Cosmos DBban. Valójában általában ajánlott kikapcsolni az indexelést az adatáttelepítés során, majd visszakapcsolja azt, ha az adatCosmos DBban már szerepel. Az indexeléssel kapcsolatos további részletekért tekintse meg a Azure Cosmos DB szakaszban található [indexeléssel](index-overview.md) foglalkozó témakört. 

A [Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) automatikusan áttelepíti a MongoDB-gyűjteményeket egyedi indexekkel. Az áttelepítést megelőzően azonban létre kell hozni az egyedi indexeket. A Azure Cosmos DB nem támogatja az egyedi indexek létrehozását, ha már szerepelnek a gyűjtemények adatai. További információ: [Azure Cosmos DBban található egyedi kulcsok](unique-keys.md).

## <a name="next-steps"></a>Következő lépések
* [Telepítse át a MongoDB-adatait Cosmos DB a Database Migration Service használatával.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Átviteli sebesség az Azure Cosmos-tárolók és-adatbázisok számára](set-throughput.md)
* [Particionálás az Azure Cosmos DB-ben](partition-data.md)
* [Globális eloszlás Azure Cosmos DB](distribute-data-globally.md)
* [Indexelés Azure Cosmos DB](index-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
