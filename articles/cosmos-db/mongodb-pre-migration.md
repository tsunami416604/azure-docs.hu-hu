---
title: Az Azure Cosmos DB MongoDB-hoz való adatáttelepítés előtti lépései
description: Ez a dokumentum áttekintést nyújt a MongoDB-ból a Cosmos DB-re történő adatáttelepítés előfeltételeiről.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 73ac1a6ffd5fc2b2d52f169e1e0332044638f9f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942077"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>A MongoDB-ról az Azure Cosmos DB MongoDB-hoz készült adatáttelepítési lépések előtti áttelepítése

Mielőtt áttelepítené az adatokat a MongoDB-ból (akár a helyszínen, akár a felhőben) az Azure Cosmos DB MongoDB-hoz készült API-jára, a következőket kell tennie:

1. [Olvassa el az Azure Cosmos DB MongoDB-hoz való API-jának használatával kapcsolatos legfontosabb szempontokat](#considerations)
2. [Válassza ki az adatok áttelepítési lehetőségét](#options)
3. [A számítási feladatokhoz szükséges átviteli terhelés becslése](#estimate-throughput)
4. [Válassza ki az adatok optimális partíciókulcsát](#partitioning)
5. [Az adatokon beállítható indexelési házirend ismertetése](#indexing)

Ha már elvégezte a fenti előfeltételeket az áttelepítéshez, [áttelepítheti a MongoDB-adatokat az Azure Cosmos DB MongoDB API-jába az Azure Database Migration Service használatával.](../dms/tutorial-mongodb-cosmos-db.md) Ezenkívül ha még nem hozott létre fiókot, böngészhet a [rövid útmutatók](create-mongodb-dotnet.md) bármelyikében, amelyek a fiók létrehozásának lépéseit mutatják.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Szempontok az Azure Cosmos DB MongoDB-hoz készült API-jának használatakor

Az azure Cosmos DB MongoDB-hoz való API-jának az alábbiakban találhatók:

- **Kapacitásmodell:** Az Azure Cosmos DB adatbázis-kapacitása egy átviteli képességen alapuló modellen alapul. Ez a modell a [másodpercenkénti kérelemegységeken](request-units.md)alapul, amely egy olyan egység, amely a gyűjteményen másodpercenként végrehajtható adatbázis-műveletek számát jelöli. Ez a kapacitás [adatbázis- vagy gyűjteményszinten](set-throughput.md)foglalható le, és kiépíthető egy foglalási modellre, vagy használhatja az [AutoPilot modellt.](provision-throughput-autopilot.md)

- **Kérelemegységek:** Minden adatbázis-művelet rendelkezik egy társított kérelemegységek (RUs) költség az Azure Cosmos DB.Request Units: Every database operation has a associated request units (RUs) cost in Azure Cosmos DB. Végrehajtáskor ezt a rendszer kivonja a rendelkezésre álló kérelemegységek szintjéből egy adott másodpercben. Ha egy kérelem hez több VT igényel, mint az aktuálisan lefoglalt RU/s két lehetőség van a probléma megoldására - növelje a VT-k mennyiségét, vagy várjon a következő második indításig, majd próbálkozzon újra a művelettel.

- **Rugalmas kapacitás:** Egy adott gyűjtemény vagy adatbázis kapacitása bármikor változhat. Ez lehetővé teszi, hogy az adatbázis rugalmasan alkalmazkodjon a számítási feladatok átviteli követelményeihez.

- **Automatikus horizontális:** Az Azure Cosmos DB automatikus particionálási rendszert biztosít, amely csak egy szegmenst (vagy partíciókulcsot igényel). Az [automatikus particionálási mechanizmus](partition-data.md) az összes Azure Cosmos DB API-k között meg van osztva, és lehetővé teszi a zökkenőmentes adatokat és a horizontális elosztáson keresztüli skálázást.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Az Azure Cosmos DB MongoDB-hoz való API-jának áttelepítési lehetőségei

Az [Azure Database Migration Service for Azure Cosmos DB MongoDB API-ja](../dms/tutorial-mongodb-cosmos-db.md) olyan mechanizmust biztosít, amely leegyszerűsíti az adatáttelepítést azáltal, hogy teljes körűen felügyelt üzemeltetési platformot, áttelepítésfigyelési beállításokat és automatikus szabályozáskezelést biztosít. A lehetőségek teljes listája a következő:

|**Áttelepítés típusa**|**Megoldás**|**Megfontolások**|
|---------|---------|---------|
|Offline|[Adatáttelepítési eszköz](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Egyszerűen beállítható és több forrást támogat <br/>&bull;Nem alkalmas nagy adatkészletek.|
|Offline|[Azure-adatgyár](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Egyszerűen beállítható és több forrást támogat <br/>&bull;Az Azure Cosmos DB tömeges végrehajtó könyvtárát használja <br/>&bull;Nagy adatkészletekhez alkalmas <br/>&bull;Az ellenőrzőpontok hiánya azt jelenti, hogy a migráció során felmerülő bármely probléma a teljes áttelepítési folyamat újraindítását tenné szükségessé<br/>&bull;A kézbesítetlen levelek várólistájának hiánya azt jelentené, hogy néhány hibás fájl leállíthatja a teljes áttelepítési folyamatot <br/>&bull;Egyéni kódszükséges bizonyos adatforrások olvasási átviteli igényének növeléséhez|
|Offline|[Meglévő Mongo Tools (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Könnyen beállítható és integrálható <br/>&bull;Egyéni kezelést igényel a fojtószelepek hez|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull;Teljes körűen felügyelt áttelepítési szolgáltatás.<br/>&bull;Az áttelepítési feladat üzemeltetési és figyelési megoldásait biztosítja. <br/>&bull;Nagy adatkészletek esetén is alkalmas, és gondoskodik az élő módosítások replikálásáról <br/>&bull;Csak más MongoDB forrásokkal működik|


## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a>A számítási feladatok átviteli igényének becslése

Az Azure Cosmos DB-ben az átviteli energia előre kivan építve, és másodpercenkénti kérelemegységekben (RU) mérik. A virtuális gépekkel vagy a helyszíni kiszolgálókkal ellentétben a virtuális államok bármikor könnyen skálázható. A kiépített rúzok száma azonnal módosíthatja. További információ: [Egységek kérése az Azure Cosmos DB-ben.](request-units.md)

Az Azure [Cosmos DB kapacitáskalkulátor](https://cosmos.azure.com/capacitycalculator/) segítségével meghatározhatja a kérelemegységek mennyiségét az adatbázisfiók konfigurációja, az adatok mennyisége, a dokumentum mérete és a szükséges olvasási és írási másodpercenként.

A következő kulcsfontosságú tényezők befolyásolják a szükséges RT-k számát:
- **Dokumentum méret:** Egy elem/dokumentum méretének növekedésével az elem/dokumentum olvasásához vagy írásához felhasznált termékazonosítók száma is nő.

- **Dokumentumtulajdonságok száma**:A dokumentum létrehozásához vagy frissítéséhez felhasznált Javak száma a tulajdonságok számához, összetettségéhez és hosszához kapcsolódik. Az írási műveletek kérésegység-felhasználását [az indexelt tulajdonságok számának korlátozásával](mongodb-indexing.md)csökkentheti.

- **Lekérdezési minták**: A lekérdezés összetettsége befolyásolja, hogy a lekérdezés hány kérelemegységet használ fel. 

A lekérdezések költségének megértéséhez a legjobb módja a mintaadatok használata az Azure Cosmos DB-ben, [és a mongoDB rendszerhéjból a](connect-mongodb-account.md) `getLastRequestStastistics` parancs használatával a kérelemdíj lekérése, amely a felhasznált több kérelem ből származó kérelem ből származó lekérdezéseket futtat:

`db.runCommand({getLastRequestStatistics: 1})`

Ez a parancs a következőhöz hasonló JSON-dokumentumot ad ki:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

A diagnosztikai [beállítások segítségével](cosmosdb-monitor-resource-logs.md) is megismerheti az Azure Cosmos DB-vel végrehajtott lekérdezések gyakoriságát és mintáit. A diagnosztikai naplók ból származó eredmények elküldhetők egy tárfiókba, egy EventHub-példányba vagy [az Azure Log Analytics szolgáltatásba.](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Válaszbillentyű kiválasztása
Particionálás, más néven szilánkos, kulcsfontosságú szempont az adatok áttelepítése előtt. Az Azure Cosmos DB teljes körűen felügyelt particionálást használ az adatbázis kapacitásának növeléséhez a tárolási és átviteli követelmények teljesítéséhez. Ennek a szolgáltatásnak nincs szüksége az útválasztó kiszolgálók üzemeltetésére vagy konfigurálására.   

Hasonló módon a particionálási képesség automatikusan növeli a kapacitást, és ennek megfelelően újra kiegyensúlyozza az adatokat. Az adatok megfelelő partíciókulcsának kiválasztásával kapcsolatos részletekért és javaslatokért olvassa el a [Partíciókulcs kiválasztása című cikket.](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) 

## <a name="index-your-data"></a><a id="indexing"></a>Adatok indexelése
Alapértelmezés szerint az Azure Cosmos DB automatikus indexelést biztosít az összes beszúrt adaton. Az Azure Cosmos DB által biztosított indexelési képességek közé tartozik a kompozit indexek, az egyedi indexek és az élő (TTL) indexek hozzáadása. Az indexkezelő felület le van `createIndex()` képezve a parancsra. További információ az [Indexelés az Azure Cosmos DB MongoDB API-jában.](mongodb-indexing.md)

[Az Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) automatikusan áttelepíti a MongoDB-gyűjteményeket egyedi indexekkel. Az egyedi indexeket azonban az áttelepítés előtt létre kell hozni. Az Azure Cosmos DB nem támogatja az egyedi indexek létrehozását, ha már vannak adatok a gyűjtemények. További információ: [Unique keys in Azure Cosmos DB.](unique-keys.md)

## <a name="next-steps"></a>További lépések
* [Telepítse át a MongoDB-adatokat a Cosmos DB-be az adatbázis-áttelepítési szolgáltatás használatával.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Átviteli átbocsátás kiépítése az Azure Cosmos-tárolókon és -adatbázisokon](set-throughput.md)
* [Particionálás az Azure Cosmos DB-ben](partition-data.md)
* [Globális disztribúció az Azure Cosmos DB-ben](distribute-data-globally.md)
* [Indexelés az Azure Cosmos DB-ben](index-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
