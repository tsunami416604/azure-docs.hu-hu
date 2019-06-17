---
title: Migrálási lépéseket adatmigrálások a MongoDB-ből az Azure Cosmos DB API a mongodb-hez
description: Ezt a dokumentumot adatmigrálás előfeltételeinek áttekintést nyújt a mongodb-hez a Cosmos DB-hez.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 476a143555323bbb5058541000a5b1a26d23b71a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61330855"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Migrálási lépéseket adatmigrálások a MongoDB-ből az Azure Cosmos DB API a mongodb-hez

MongoDB-ből az adatok áttelepítése előtt (a helyszínen vagy a felhőben (IaaS)) az Azure Cosmos DB API a mongodb-hez, akkor:

1. [Azure Cosmos DB-fiók létrehozása](#create-account)
2. [Becsülni az átviteli sebességet a számítási feladatokhoz szükséges](#estimate-throughput)
3. [Válasszon ki egy optimális partíciókulcsot az adatok](#partitioning)
4. [Az indexelési házirendet, amely lehet az adatok megismerése](#indexing)

Ha már végrehajtotta a fenti előfeltételek az áttelepítéshez, tekintse meg a [MongoDB át adatokat az Azure Cosmos DB MongoDB API-](../dms/tutorial-mongodb-cosmos-db.md) a tényleges adatok áttelepítési utasításokért. Ha nem, ez a dokumentum útmutatást ad kezelje ezeket az előfeltételeket. 

## <a id="create-account"></a> Az Azure Cosmos DB-fiók létrehozása 

A frissítés megkezdése előtt kell [létre Azure Cosmos DB API használatával a mongodb-hez készült Azure Cosmos-fiókot](create-mongodb-dotnet.md). 

A fiók létrehozásakor kiválaszthatja a beállítások [terjessze globálisan](distribute-data-globally.md) adatait. A beállítás engedélyezése többrégiós írási műveletek (vagy több főkiszolgálós configuration), amely lehetővé teszi, hogy a régió is az írási és olvasási régió is rendelkezik.

![Account-Creation](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a> A számítási feladatok átviteli szükség becslése

Segítségével az áttelepítés megkezdése előtt a [Database Migration Service (DMS)](../dms/dms-overview.md), az összeg kiosztása az Azure Cosmos-adatbázisok és gyűjtemények átviteli becslései szerint kell.

Átviteli sebesség bővítheti a következők:

- Gyűjtemény

- Adatbázis

> [!NOTE]
> A fenti kombinációját is lehet, ahol az adatbázis egyes gyűjtemények előfordulhat, hogy rendelkezik dedikált kiosztott átviteli sebesség, és másokkal megoszthatják az átviteli sebességet. Részletekért tekintse meg a [teljesítmény beállítása az adatbázis és a egy tároló](set-throughput.md) lapot.
>

Kell először eldöntheti, hogy szeretné-e kiépítése az adatbázis vagy a gyűjtemény szintű teljesítményt, illetve mindkettőt. Általában javasoljuk egy dedikált átviteli sebesség konfigurálásához, a gyűjtemény szintjén. Az adatbázis szintjén kiépítési átviteli sebesség lehetővé teszi, hogy a gyűjtemények a kiosztott átviteli sebesség megosztani az adatbázisban. Megosztott átviteli sebességgel azonban nem garantált az egy adott egyéni gyűjtemények átviteli sebességet, és kiszámítható teljesítményt biztosítanak a bármilyen adott gyűjtemény nem kap.

Ha nem biztos abban, milyen átviteli sebességre kell minden egyes gyűjtemény számára dedikált, kiválaszthatja az adatbázisszintű átviteli sebességet. Is felfoghatók a kiosztott átviteli sebesség konfigurált az Azure Cosmos Database, egy logikai megfelelője a MongoDB virtuális gép vagy fizikai kiszolgálók számítási kapacitását, de a költséghatékonyabb lehetővé teszi az rugalmasan méretezhető. További információkért lásd: [az Azure Cosmos-tárolók és adatbázisok kiépítése átviteli](set-throughput.md).

Ha üzembe helyezi az átviteli sebesség az adatbázis szintjén, partíció/szegmenskulccsal rendelkező adatbázis belül létrehozott összes gyűjteményt kell létrehozni. A particionálás további információkért lásd: [particionálás és a horizontális skálázás az Azure Cosmos DB](partition-data.md). Ha nem ad meg egy partíció-és szegmenskulcs az áttelepítés során, az Azure Database Migration Service automatikusan kitölti a szegmensek kulcsmező egy *_azonosítója* attribútum, amely automatikusan jön létre minden egyes dokumentum.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Üzembe helyezni a kérelemegység (RU) optimális számát

Az Azure Cosmos DB az átviteli sebességet előre ki van építve, és a másodpercenkénti Kérelemegységek (RU) mérve. Ha a virtuális gép vagy a helyszíni MongoDB futó számítási feladatokat, úgy, RU a mint egy egyszerű absztrakciós fizikai erőforrások, például a méretét, a virtuális gép vagy a-egy helyszíni kiszolgáló és rendelkeznek, például az erőforrások memória, Processzor, az iops-t. 

Virtuális gépek és a helyszíni kiszolgálók eltérően RUs is skálázható felfelé és lefelé bármikor egyszerűen. A fenntartott egységek számát másodpercek alatt módosíthatja, és csak egy adott egy órás időszak kiosztott Kérelemegységek maximális száma lesznek számlázva. További információkért lásd: [Azure Cosmos DB-Kérésegységeiről](request-units.md).

Kulcsfontosságú tényező befolyásolja a szükséges fenntartott egységek számát a következők:
- **Elem (azaz a dokumentum) mérete**: Egy elem/dokumentum mérete nő, ahogy a fenntartott egységek számát felhasznált olvasására vagy írására elem/dokumentum is nő.
- **Konfigurációelem-tulajdonság száma**: Feltéve, hogy a [alapértelmezett indexelési](index-overview.md) az összes tulajdonság a fenntartott egységek számát írhat egy elem növeli az elem tulajdonság száma növekszik, felhasznált. Csökkentheti a kérelem-egységek felhasználását az írási műveletek által [száma a indexelt tulajdonságok](index-policy.md).
- **Az egyidejű művelet**: Felhasznált egységek is függ, mely különböző CRUD-műveletek (például az írás, Olvasás, frissítések, törli) az gyakran és a bonyolultabb lekérdezések végrehajtásakor kérelmet. Használhat [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) a kimenetben az aktuális MongoDB-adatok egyidejűségi igényeit.
- **Lekérdezési minták**: A lekérdezés összetettségétől befolyásolja a lekérdezés által felhasznált kérelemegységek számát.

Ha JSON-fájlok használatával exportálja [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) , és ismerje meg, hány írási, olvasási, frissíti, és törli az adott másodpercenként kerül sor, akkor használhatja a [Azure Cosmos DB-kapacitás planner](https://www.documentdb.com/capacityplanner) becslése érdekében a kezdeti sok Kérelemegységet üzembe helyezni. A capacity planner nem kéttényezős díja beleszámít a összetettebb lekérdezéseket. Ezért ha bonyolult lekérdezéseket az adatokon, további Kérelemegységet fognak használni. A kalkulátor is feltételezi, hogy az összes mezőt indexeli ugyan, és munkamenet-konzisztencia szolgál. A legjobb módszer a lekérdezések költsége van az adatok (vagy a mintaadatok) áttelepítése az Azure Cosmos DB [csatlakozás a Cosmos DB végpont](connect-mongodb-account.md) mintalekérdezés futtatását a MongoDB Shell használatával, és a `getLastRequestStastistics` parancs használatával beszerezheti az kérelem számolunk fel, amely kimenete felhasznált Kérelemegységek számát:

`db.runCommand({getLastRequestStatistics: 1})`

Ez a parancs a következőhöz hasonló JSON-dokumentumok:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Miután megismerkedett a lekérdezések által felhasznált Kérelemegységek számát, és az egyidejűséget van szüksége az adott lekérdezés, módosíthatja a fenntartott egységek számát. Fenntartott egységek optimalizálása nem egyszeri esemény – érdemes folyamatosan optimalizálhatja vagy vertikális felskálázás a RUs kiépített, attól függően, hogy, nem várt érték egy nagy forgalmú figyelésekor nagy terhelést vagy adatok importálása.

## <a id="partitioning"></a>Válassza ki a partíciós kulcs
A particionálás nem szempont, például az Azure Cosmos DB egy globálisan elosztott adatbázis-ba való migrálás előtt a fő pont. Az Azure Cosmos DB használja a particionálás egy adatbázisban az igényeinek a méretezhetőség és teljesítmény az alkalmazás egyes tárolók méretezését. A particionálás, a tárolóban lévő elemek meg vannak osztva nevű logikai partíció különböző alkészleteiben. Részletek és az adatok a megfelelő partíciókulcs kiválasztása a javaslatok, tekintse meg a [kiválasztása egy Partíciókulcsot szakasz](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Az adatok indexelése
Alapértelmezés szerint az Azure Cosmos DB indexeli az Adatbetöltési esetén minden adatmezőket. Módosíthatja a [indexelési szabályzat](index-policy.md) bármikor az Azure Cosmos DB-ben. Sőt milyen gyakran ajánlott kapcsolja ki az indexelés adatainak áttelepítésekor, majd kapcsolja vissza, ha az adatok már Cosmos DB-ben. Indexelő olvashat további részletes a [indexelése az Azure Cosmos DB](index-overview.md) szakaszban. 

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) automatikusan áttelepíti a MongoDB-gyűjteményeket, amelyek egyedi indexeket. Az egyedi indexek azonban az áttelepítés előtt kell létrehozni. Az Azure Cosmos DB nem támogatja a egyedi indexek létrehozása, ha már van adatok ebben a gyűjteményben. További információkért lásd: [Azure Cosmos DB-ben egyedi kulcsaival](unique-keys.md).

## <a name="next-steps"></a>További lépések
* [A MongoDB-adatok áttelepítése a Cosmos DB-hez a Database Migration Service segítségével.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Az Azure Cosmos-tárolók és adatbázisok kiépítése átviteli](set-throughput.md)
* [Particionálás az Azure Cosmos DB-ben](partition-data.md)
* [Az Azure Cosmos DB globális terjesztését](distribute-data-globally.md)
* [Az Azure Cosmos DB indexelése](index-overview.md)
* [Az Azure Cosmos DB-kérésegységeiről](request-units.md)
