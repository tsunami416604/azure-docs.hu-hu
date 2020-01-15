---
title: Áttelepítés előtti lépések a Azure Cosmos DB API-MongoDB való áttelepítéshez
description: Ez a dokumentum áttekintést nyújt a MongoDB és a Cosmos DB közötti adatáttelepítés előfeltételeiről.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 73ac1a6ffd5fc2b2d52f169e1e0332044638f9f7
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942077"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Áttelepítés előtti lépések a MongoDB-ből Azure Cosmos DB API-MongoDB való áttelepítéshez

Mielőtt áttelepíti az adatait a MongoDB-ből (akár a helyszínen, akár a felhőben) Azure Cosmos DB API-ját a MongoDB-hez, a következőket kell tennie:

1. [A MongoDB-hez készült Azure Cosmos DB API használatának főbb szempontjai](#considerations)
2. [Válassza ki az adatáttelepítés lehetőségét](#options)
3. [A munkaterhelésekhez szükséges átviteli sebesség becslése](#estimate-throughput)
4. [Válasszon ki egy optimális partíciós kulcsot az adataihoz](#partitioning)
5. [Az adataihoz beállítható indexelési szabályzat ismertetése](#indexing)

Ha már elvégezte a fenti előfeltételeket az áttelepítéshez, [áttelepítheti a MongoDB-Azure Cosmos db API-ját a MongoDB Azure Database Migration Service használatával](../dms/tutorial-mongodb-cosmos-db.md). Ha még nem hozott létre fiókot, böngészhet bármely olyan rövid útmutatóban [, amely megjeleníti a fiók](create-mongodb-dotnet.md) létrehozásának lépéseit.

## <a id="considerations"></a>Szempontok a Azure Cosmos DB API-MongoDB való használatához

A MongoDB Azure Cosmos DB API-ját a következők konkrét jellemzőkkel bírnak:

- **Kapacitási modell**: az Azure Cosmos db adatbázis-kapacitása egy átviteli alapú modellen alapul. Ez a modell másodpercenkénti [kérelmeken](request-units.md)alapul, ami egy olyan egység, amely az adatbázis-műveletek másodpercenkénti számát mutatja. Ezt a kapacitást [adatbázis vagy gyűjtemény szintjén](set-throughput.md)lehet lefoglalni, és kiosztási modellben vagy az [Autopilot modell](provision-throughput-autopilot.md)használatával is kiépíthető.

- **Kérelmek egységei**: minden adatbázis-művelethez tartozik egy társított kérési egység (RUs) díja Azure Cosmos db. A művelet végrehajtásakor a rendszer kivonja az elérhető kérelmek egységének szintjéről az adott másodpercben. Ha egy kérelem több RUs-t igényel, mint a jelenleg lefoglalt RU/s, két lehetőség van a probléma megoldására – növelje az RUs mennyiségét, vagy várjon, amíg a következő másodperc elindul, majd próbálja megismételni a műveletet.

- **Rugalmas kapacitás**: egy adott gyűjtemény vagy adatbázis kapacitása bármikor megváltozhat. Ez lehetővé teszi, hogy az adatbázis rugalmasan alkalmazkodjon a számítási feladatok átviteli követelményeihez.

- **Automatikus**skálázás: a Azure Cosmos db egy automatikus particionálási rendszer, amely csak egy szegmens (vagy egy partíciós kulcs) megadását igényli. Az [automatikus particionálási mechanizmus](partition-data.md) az összes Azure Cosmos db API-ban meg van osztva, és lehetővé teszi a zökkenőmentes adattárolást és a horizontális eloszláson keresztüli teljes méretezést.

## <a id="options"></a>Áttelepítési lehetőségek a Azure Cosmos DB API-MongoDB

A [Azure Cosmos db API-MongoDB való Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) egy olyan mechanizmust biztosít, amely leegyszerűsíti az adatáttelepítést azáltal, hogy teljes körűen felügyelt üzemeltetési platformot biztosít, áttelepítési figyelési lehetőségeket és automatikus szabályozási kezelést tesz lehetővé. A lehetőségek teljes listája a következő:

|**Áttelepítés típusa**|**Megoldás**|**Megfontolások**|
|---------|---------|---------|
|Kapcsolat nélkül|[Adatáttelepítési eszköz](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; könnyen beállítható és támogatott több forrás <br/>a &bull; nagyméretű adathalmazokhoz nem alkalmas.|
|Kapcsolat nélkül|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; könnyen beállítható és támogatott több forrás <br/>a &bull; a Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé <br/>nagyméretű adatkészletekhez megfelelő &bull; <br/>&bull; ellenőrzőpontok hiánya azt jelenti, hogy az áttelepítés során esetlegesen felmerülő problémákhoz a teljes áttelepítési folyamat újraindítása szükséges.<br/>&bull; a kézbesítetlen levelek várólistájának hiánya azt jelenti, hogy néhány hibás fájl leállíthatja a teljes áttelepítési folyamatot <br/>az adatforrások olvasási sebességének növeléséhez &bull; egyéni kód szükséges|
|Kapcsolat nélkül|[Meglévő Mongo-eszközök (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; egyszerűen beállítható és integrálható <br/>&bull; egyéni kezelést igényel a szabályozáshoz|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; teljes körűen felügyelt áttelepítési szolgáltatás.<br/>a &bull; üzemeltetési és figyelési megoldásokat biztosít az áttelepítési feladathoz. <br/>a nagyméretű adatkészletekhez megfelelő &bull;, és gondoskodik az élő módosítások replikálásáról <br/>a &bull; csak más MongoDB-forrásokkal működik|


## <a id="estimate-throughput"></a>A számítási feladatokhoz szükséges átviteli sebesség becslése

Azure Cosmos DB az átviteli sebességet előre kiépítve, és a rendszer a kérelmek egységében (RU) mérve másodpercenként méri. A virtuális gépekkel és a helyszíni kiszolgálókkal ellentétben a RUs bármikor könnyedén méretezhető. A kiosztott RUs-ket azonnal megváltoztathatja. További információ: [kérelmek egységei a Azure Cosmos DBban](request-units.md).

A [Azure Cosmos db kapacitás-kalkulátor](https://cosmos.azure.com/capacitycalculator/) segítségével meghatározhatja a kérelmek mennyiségét az adatbázis-fiók konfigurációja, az adatmennyiség, a dokumentum mérete és a szükséges olvasási és írási műveletek alapján.

A következő kulcsfontosságú tényezők befolyásolják a szükséges RUs-ket:
- **Dokumentum mérete**: az elem/dokumentum méretének növekedésével az elem/dokumentum olvasásához vagy írásához felhasznált RUs száma is nő.

- **Dokumentum tulajdonságainak száma**: a dokumentumok létrehozásához vagy frissítéséhez felhasznált RUs száma a tulajdonságok számával, összetettségével és hosszával kapcsolatos. Az [indexelt tulajdonságok számának korlátozásával](mongodb-indexing.md)csökkentheti a kérések egység általi felhasználását írási műveletekhez.

- **Lekérdezési minták**: a lekérdezés bonyolultsága befolyásolja, hogy a lekérdezés hány kérési egységet használ fel. 

A lekérdezési költségek megismerésének legjobb módja a Azure Cosmos DBban található mintaadatok használata, és a [MongoDB-rendszerhéjból](connect-mongodb-account.md) a `getLastRequestStastistics` parancs használatával a kérések díjainak lekérdezése, amely a felhasznált RUs mennyiségét fogja kiadni:

`db.runCommand({getLastRequestStatistics: 1})`

Ez a parancs a következőhöz hasonló JSON-dokumentumot fog kiadni:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

A [diagnosztikai beállítások](cosmosdb-monitor-resource-logs.md) segítségével megismerheti a Azure Cosmos DBon végrehajtott lekérdezések gyakoriságát és mintáit. A diagnosztikai naplók eredményei elküldhetők egy Storage-fiókba, egy EventHub-példányba vagy egy [Azure-log Analyticsba](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).  

## <a id="partitioning"></a>Válassza ki a partíciós kulcsot
A particionálás (más néven horizontális skálázás) az adatáttelepítés előtt kulcsfontosságú szempont. A Azure Cosmos DB a teljes körűen felügyelt particionálással növelheti a kapacitást egy adatbázisban a tárolási és átviteli sebességre vonatkozó követelmények teljesítése érdekében. Ennek a szolgáltatásnak nincs szüksége az útválasztási kiszolgálók üzemeltetésére vagy konfigurálására.   

Hasonló módon a particionálási funkció automatikusan bővíti a kapacitást, és ennek megfelelően kiegyensúlyozza az adatelosztást. Az adatok megfelelő partíciós kulcsának kiválasztásával kapcsolatos részletekért és javaslatokért tekintse meg a [partíciós kulcs kiválasztása című cikket](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Az adatai indexelése
Alapértelmezés szerint a Azure Cosmos DB a beszúrt összes adattal automatikus indexelést biztosít. A Azure Cosmos DB által biztosított indexelési képességek közé tartozik az összetett indexek, az egyedi indexek és az élettartam (TTL) indexek hozzáadása. Az index felügyeleti felülete a `createIndex()` parancsra van leképezve. További információ: [Azure Cosmos db API-MongoDB való indexelése](mongodb-indexing.md).

A [Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) automatikusan áttelepíti a MongoDB-gyűjteményeket egyedi indexekkel. Az áttelepítést megelőzően azonban létre kell hozni az egyedi indexeket. A Azure Cosmos DB nem támogatja az egyedi indexek létrehozását, ha már szerepelnek a gyűjtemények adatai. További információ: [Azure Cosmos DBban található egyedi kulcsok](unique-keys.md).

## <a name="next-steps"></a>Következő lépések
* [Telepítse át a MongoDB-adatait Cosmos DB a Database Migration Service használatával.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Átviteli sebesség az Azure Cosmos-tárolók és-adatbázisok számára](set-throughput.md)
* [Particionálás az Azure Cosmos DB-ben](partition-data.md)
* [Globális eloszlás Azure Cosmos DB](distribute-data-globally.md)
* [Indexelés Azure Cosmos DB](index-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
