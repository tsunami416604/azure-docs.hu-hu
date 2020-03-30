---
title: Cosmos DB áttelepítési beállításai
description: Ez a dokumentum ismerteti a különböző lehetőségeket a helyszíni vagy felhőbeli adatok azure Cosmos DB-ba való áttelepítésére
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 9111193bb441487b9e3c49bc9ee1a296d49f8a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72882386"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>A helyszíni vagy felhőbeli adatok Azure Cosmos DB-be való áttelepítésének lehetőségei

Különböző adatforrásokból tölthet be adatokat az Azure Cosmos DB-be. Emellett, mivel az Azure Cosmos DB több API-t is támogat, a célok a meglévő API-k bármelyike lehetnek. Annak érdekében, hogy a különböző forrásokból a különböző Azure Cosmos DB API-k áttelepítési elérési útjait támogassa, több megoldás is létezik, amelyek speciális kezelést biztosítanak az egyes áttelepítési útvonalakhoz. Ez a dokumentum felsorolja a rendelkezésre álló megoldásokat, és ismerteti azok előnyeit és korlátait.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>A migrációs eszköz kiválasztását befolyásoló tényezők

Az áttelepítési eszköz kiválasztását a következő tényezők határozzák meg:
* **Online és offline áttelepítés:** Számos áttelepítési eszköz csak egyszeri áttelepítést biztosít. Ez azt jelenti, hogy az adatbázishoz hozzáférő alkalmazások leállási időszakot tapasztalhatnak. Egyes áttelepítési megoldások lekínálják az élő áttelepítést, ahol a forrás és a cél között létrejön egy replikációs folyamat.

* **Adatforrás**: A meglévő adatok lehetnek különböző adatforrások, mint az Oracle DB2, Datastax Cassanda, Azure SQL Server, PostgreSQL, stb. Az adatok is lehet egy meglévő Azure Cosmos DB-fiók, és a szándék az áttelepítés lehet az adatmodell módosítása vagy az adatok újraparticionálása egy tárolóban egy másik partíciókulcssal.

* **Azure Cosmos DB API:** Az Azure Cosmos DB SQL API-ja az Azure Cosmos DB csapata által kifejlesztett számos olyan eszköz található, amelyek a különböző áttelepítési forgatókönyvekben segítik. Az összes többi API-nak saját speciális eszközkészlete van, amelyet a közösség fejlesztett ki és tartott karban. Mivel az Azure Cosmos DB támogatja ezeket az API-kat egy vezetékes protokoll szintjén, ezek az eszközök úgy kell működniük, ahogy van, miközben az adatok áttelepítése az Azure Cosmos DB is. Azonban előfordulhat, hogy a szabályozások egyéni kezelése, mivel ez a koncepció az Azure Cosmos DB-re jellemző.

* **Az adatok mérete**: A legtöbb áttelepítési eszköz nagyon jól működik a kisebb adatkészletek esetében. Ha az adatkészlet meghaladja a néhány száz gigabájtot, az áttelepítési eszközök választéka korlátozott. 

* **Várható áttelepítési időtartam:** Az áttelepítések konfigurálhatók lassú, növekményes ütemben, amely kevesebb átviteli sebességgel, vagy a teljes átviteli készültség a cél Azure Cosmos DB tároló, és az áttelepítés befejezése kevesebb idő alatt.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
|**Áttelepítés típusa**|**Megoldás**|**Megfontolások**|
|---------|---------|---------|
|Offline|[Adatáttelepítési eszköz](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Egyszerűen beállítható és több forrást támogat <br/>&bull;Nem alkalmas nagy adatkészletek|
|Offline|[Azure-adatgyár](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Egyszerűen beállítható és több forrást támogat <br/>&bull;Az Azure Cosmos DB tömeges végrehajtó könyvtárát használja <br/>&bull;Nagy adatkészletekhez alkalmas <br/>&bull;Ellenőrzőpontok hiánya - Ez azt jelenti, hogy ha probléma merül fel a migráció során, újra kell indítania a teljes áttelepítési folyamatot<br/>&bull;A kézbesítetlen levelek várólistájának hiánya – Ez azt jelenti, hogy néhány hibás fájl leállíthatja a teljes áttelepítési folyamatot.|
|Offline|[Azure Cosmos DB Spark-összekötő](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;Az Azure Cosmos DB tömeges végrehajtó könyvtárát használja <br/>&bull;Nagy adatkészletekhez alkalmas <br/>&bull;Egyéni Spark-beállításra van szükség <br/>&bull;A Spark érzékeny a séma-inkonzisztenciákra, és ez problémát okozhat az áttelepítés során |
|Offline|[Egyéni eszköz a Cosmos DB tömeges végrehajtó könyvtárral](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;Ellenőrzőpontok, holtpontok at biztosít, amelyek növelik az áttelepítés rugalmasságát <br/>&bull;Nagyon nagy adatkészletekhez alkalmas (10 TB+)  <br/>&bull;Ehhez az eszközhöz egyéni beállítás szükséges, amely App Service-ként fut |
|Online|[Cosmos DB függvények + ChangeFeed API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;Könnyen beállítható <br/>&bull;Csak akkor működik, ha a forrás egy Azure Cosmos DB-tároló <br/>&bull;Nem alkalmas nagy adatkészletek <br/>&bull;Nem rögzíti a törléseket a forrástárolóból |
|Online|[Egyéni áttelepítési szolgáltatás a ChangeFeed használatával](https://aka.ms/CosmosDBMigrationSample)|&bull;Folyamatkövetést biztosít <br/>&bull;Csak akkor működik, ha a forrás egy Azure Cosmos DB-tároló <br/>&bull;Nagyobb adatkészletek esetén is működik <br/>&bull;A felhasználónak be kell állítania egy App Service szolgáltatást a hírcsatorna-feldolgozó üzemeltetéséhez <br/>&bull;Nem rögzíti a törléseket a forrástárolóból|
|Online|[Striim között](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;Sokféle forrással működik, mint az Oracle, DB2, SQL Server <br/>&bull;Könnyen felépíthető ETL-csővezetékek és irányítópultot biztosít a monitorozáshoz <br/>&bull;Támogatja a nagyobb adatkészleteket <br/>&bull;Mivel ez egy harmadik féltől származó eszköz, azt meg kell vásárolni a piactérről, és telepíteni kell a felhasználó környezetében|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API
|**Áttelepítés típusa**|**Megoldás**|**Megfontolások**|
|---------|---------|---------|
|Offline|[Adatáttelepítési eszköz](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Egyszerűen beállítható és több forrást támogat <br/>&bull;Nem alkalmas nagy adatkészletek|
|Offline|[Azure-adatgyár](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Egyszerűen beállítható és több forrást támogat <br/>&bull;Az Azure Cosmos DB tömeges végrehajtó könyvtárát használja <br/>&bull;Nagy adatkészletekhez alkalmas <br/>&bull;Az ellenőrzőpontok hiánya azt jelenti, hogy a migráció során felmerülő bármely probléma a teljes áttelepítési folyamat újraindítását tenné szükségessé<br/>&bull;A kézbesítetlen levelek várólistájának hiánya azt jelentené, hogy néhány hibás fájl leállíthatja a teljes áttelepítési folyamatot <br/>&bull;Egyéni kódszükséges bizonyos adatforrások olvasási átviteli igényének növeléséhez|
|Offline|[Meglévő Mongo Tools (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Könnyen beállítható és integrálható <br/>&bull;Egyéni kezelést igényel a fojtószelepek hez|
|Online|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;Az Azure Cosmos DB tömeges végrehajtó könyvtárát használja <br/>&bull;Nagy adatkészletek esetén is alkalmas, és gondoskodik az élő módosítások replikálásáról <br/>&bull;Csak más MongoDB forrásokkal működik|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API
|**Áttelepítés típusa**|**Megoldás**|**Megfontolások**|
|---------|---------|---------|
|Offline|[cqlsh COPY parancs](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;Könnyen beállítható <br/>&bull;Nem alkalmas nagy adatkészletek <br/>&bull;Csak akkor működik, ha a forrás Cassandra asztal|
|Offline|[Tábla másolása a Sparkkal](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;A Spark-képességek segítségével párhuzamosíthatja az átalakítást és a lenyelést <br/>&bull;A szabályozások kezeléséhez egyéni újrapróbálkozási házirenddel rendelkező konfigurációra van szükség|
|Online|[Striim (az Oracle DB/Apache Cassandra-tól)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;Sokféle forrással működik, mint az Oracle, DB2, SQL Server <br/>&bull;Könnyen felépíthető ETL-csővezetékek és irányítópultot biztosít a monitorozáshoz <br/>&bull;Támogatja a nagyobb adatkészleteket <br/>&bull;Mivel ez egy harmadik féltől származó eszköz, azt meg kell vásárolni a piactérről, és telepíteni kell a felhasználó környezetében|
|Online|[Blitzz (az Oracle DB/Apache Cassandra-tól)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;Támogatja a nagyobb adatkészleteket <br/>&bull;Mivel ez egy harmadik féltől származó eszköz, azt meg kell vásárolni a piactérről, és telepíteni kell a felhasználó környezetében|

## <a name="other-apis"></a>Egyéb API-k
Az SQL API-tól, a Mongo API-tól és a Cassandra API-tól eltérő API-k hoz különböző eszközök vannak az API meglévő ökoszisztémái által támogatott. 

**Tábla API** 
* [Adatáttelepítési eszköz](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin API**
* [Graph tömeges végrehajtó könyvtára](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Szörnyecskék szikra](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>További lépések

* További információ: a [.NET](bulk-executor-dot-net.md) és Java tömeges végrehajtó könyvtárát használó mintaalkalmazások [kipróbálásával.](bulk-executor-java.md) 
* A tömeges végrehajtó könyvtár integrálva van a Cosmos DB Spark-összekötő, további információkért tekintse meg [az Azure Cosmos DB Spark-összekötő](spark-connector.md) cikket.  
* Lépjen kapcsolatba az Azure Cosmos DB termékcsapatával egy támogatási jegy megnyitásával az "Általános tanácsadó" problématípusa és a "Nagy (TB+) áttelepítések" probléma altípusa alatt további segítséget a nagyméretű áttelepítések esetén.
