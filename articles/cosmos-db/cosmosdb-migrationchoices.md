---
title: Cosmos DB áttelepítési lehetőségek
description: Ez a dokumentum ismerteti a helyszíni vagy a Felhőbeli adatAzure Cosmos DBre való Migrálás különböző lehetőségeit.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 9111193bb441487b9e3c49bc9ee1a296d49f8a31
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882386"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>A helyszíni vagy a Felhőbeli adatátviteli beállítások Azure Cosmos DB

A különböző adatforrásokból származó adatok betölthetők Azure Cosmos DBba. Emellett mivel Azure Cosmos DB több API-t támogat, a célok lehetnek a meglévő API-k. Ahhoz, hogy támogassa a különböző forrásokból származó áttelepítési útvonalakat a különböző Azure Cosmos DB API-kkal, több megoldás is van, amelyek speciális kezelést biztosítanak az egyes áttelepítési útvonalakhoz. Ez a dokumentum felsorolja az elérhető megoldásokat, valamint ismerteti azok előnyeit és korlátozásait.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Az áttelepítési eszköz kiválasztását befolyásoló tényezők

A következő tényezők határozzák meg az áttelepítési eszköz kiválasztását:
* **Online vagy offline áttelepítés**: számos áttelepítési eszköz biztosít egy elérési utat, amely csak egyszeri áttelepítést tesz lehetővé. Ez azt jelenti, hogy az adatbázishoz hozzáférő alkalmazások leállási időszakot tapasztalhatnak. Egyes áttelepítési megoldások lehetővé teszik az élő áttelepítést, ahol a forrás és a cél közötti replikációs folyamat van beállítva.

* **Adatforrás**: a meglévő információk különböző adatforrásokban lehetnek, például az Oracle DB2, a Datastax Cassanda, az Azure SQL Server, a PostgreSQL stb. Az adattípusok egy meglévő Azure Cosmos DB fiókban is lehetnek, és a Migrálás célja lehet az adatmodell módosítása vagy egy másik partíciós kulccsal rendelkező tárolóban lévő adatparticionálás.

* **Azure Cosmos db API**: a Azure Cosmos db SQL API-hoz számos olyan eszközt fejlesztettünk ki, amelyek a különböző áttelepítési forgatókönyvek keretében támogatást nyújtanak a Azure Cosmos db csapat számára. Az összes többi API-nak saját, a Közösség által fejlesztett és karbantartott eszközei vannak. Mivel a Azure Cosmos DB támogatja ezeket az API-kat a vezetékes protokoll szintjén, ezeknek az eszközöknek ugyanúgy kell működniük, amikor az Azure Cosmos DB is áttelepíti az adatátvitelt. Előfordulhat azonban, hogy egyéni kezelést igényelnek a szabályozáshoz, mivel ez a fogalom Azure Cosmos DBre vonatkozik.

* **Adatméret**: a legtöbb áttelepítési eszköz nagyon jól működik a kisebb adatkészletek esetében. Ha az adathalmaz meghaladja a néhány száz gigabájtot, az áttelepítési eszközök lehetőségei korlátozottak. 

* **Várható áttelepítési időtartam**: a Migrálás úgy konfigurálható, hogy lassú, növekményes ütemben történjen, amely kevesebb átviteli sebességet használ, vagy felhasználja a cél Azure Cosmos db tárolóban kiosztott teljes átviteli sebességet, és az áttelepítés elvégzése kevesebb időt vesz igénybe idő.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
|**Áttelepítés típusa**|**Megoldás**|**Megfontolások**|
|---------|---------|---------|
|Offline|[Adatáttelepítési eszköz](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; könnyen beállítható és támogatott több forrás <br/>a &bull; nagyméretű adatkészletekhez nem alkalmas|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; könnyen beállítható és támogatott több forrás <br/>a &bull; a Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé <br/>nagyméretű adatkészletekhez megfelelő &bull; <br/>az ellenőrzőpontok hiánya &bull; – ez azt jelenti, hogy ha probléma merül fel az áttelepítés során, újra kell indítania a teljes áttelepítési folyamatot.<br/>&bull; a kézbesítetlen levelek várólistájának hiánya – ez azt jelenti, hogy néhány hibás fájl leállíthatja a teljes áttelepítési folyamatot.|
|Offline|[Azure Cosmos DB Spark-összekötő](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|a &bull; a Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé <br/>nagyméretű adatkészletekhez megfelelő &bull; <br/>&bull; egyéni Spark-telepítőre van szüksége <br/>a &bull; Spark érzékeny a séma következetlenségére, és ez problémát okozhat az áttelepítés során |
|Offline|[Egyéni eszköz Cosmos DB tömeges végrehajtó könyvtárral](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|a &bull; olyan ellenőrzőpont-és kézbesítő-betűjeli képességeket biztosít, amelyek növelik az áttelepítés rugalmasságát <br/>&bull; a nagyon nagy adatkészletekhez megfelelő (10 TB +)  <br/>a &bull; az eszköz egyéni telepítését igényli App Service |
|Online|[Cosmos DB Functions + ChangeFeed API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|Egyszerűen beállítható &bull; <br/>a &bull; csak akkor működik, ha a forrás egy Azure Cosmos DB tároló <br/>a &bull; nagyméretű adatkészletekhez nem alkalmas <br/>a &bull; nem rögzíti a törlést a forrás tárolóból. |
|Online|[Egyéni áttelepítési szolgáltatás a ChangeFeed használatával](https://aka.ms/CosmosDBMigrationSample)|a &bull; a folyamat nyomon követését teszi lehetővé <br/>a &bull; csak akkor működik, ha a forrás egy Azure Cosmos DB tároló <br/>&bull; a nagyobb adatkészletek esetében is működik <br/>a &bull; beállításnál a felhasználónak be kell állítania egy App Service a módosítási hírcsatorna processzorának üzemeltetéséhez <br/>a &bull; nem rögzíti a törlést a forrás tárolóból.|
|Online|[Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|a &bull; számos különböző forrással működik, mint például az Oracle, a DB2, a SQL Server <br/>&bull; könnyedén készíthet ETL-folyamatokat, és irányítópultot biztosít a figyeléshez <br/>a &bull; támogatja a nagyobb adatkészleteket <br/>&bull; mivel ez egy külső gyártótól származó eszköz, azt a piactéren kell megvásárolni, és telepíteni kell a felhasználó környezetében.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API
|**Áttelepítés típusa**|**Megoldás**|**Megfontolások**|
|---------|---------|---------|
|Offline|[Adatáttelepítési eszköz](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; könnyen beállítható és támogatott több forrás <br/>a &bull; nagyméretű adatkészletekhez nem alkalmas|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; könnyen beállítható és támogatott több forrás <br/>a &bull; a Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé <br/>nagyméretű adatkészletekhez megfelelő &bull; <br/>&bull; ellenőrzőpontok hiánya azt jelenti, hogy az áttelepítés során esetlegesen felmerülő problémákhoz a teljes áttelepítési folyamat újraindítása szükséges.<br/>&bull; a kézbesítetlen levelek várólistájának hiánya azt jelenti, hogy néhány hibás fájl leállíthatja a teljes áttelepítési folyamatot <br/>&bull; egyéni kóddal kell rendelkeznie az olvasási teljesítmény növeléséhez bizonyos adatforrások esetén|
|Offline|[Meglévő Mongo-eszközök (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; egyszerűen beállítható és integrálható <br/>&bull; egyéni kezelést igényel a szabályozáshoz|
|Online|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|a &bull; a Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé <br/>&bull; a nagyméretű adatkészletek számára megfelelő, és gondoskodik az élő módosítások replikálásáról <br/>a &bull; csak más MongoDB-forrásokkal működik|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API
|**Áttelepítés típusa**|**Megoldás**|**Megfontolások**|
|---------|---------|---------|
|Offline|[cqlsh MÁSOLÁSi parancs](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|Egyszerűen beállítható &bull; <br/>a &bull; nagyméretű adatkészletekhez nem alkalmas <br/>a &bull; csak akkor működik, ha a forrás egy Cassandra-tábla|
|Offline|[Táblázat másolása a Sparktal](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |a &bull; a Spark-képességeket a integrálással átalakításához és betöltéséhez használhatja <br/>a szabályozások kezeléséhez a &bull; egyéni újrapróbálkozási szabályzattal kell konfigurálni|
|Online|[Striim (Oracle DB/Apache Cassandra-ból)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|a &bull; számos különböző forrással működik, mint például az Oracle, a DB2, a SQL Server <br/>&bull; könnyedén készíthet ETL-folyamatokat, és irányítópultot biztosít a figyeléshez <br/>a &bull; támogatja a nagyobb adatkészleteket <br/>&bull; mivel ez egy külső gyártótól származó eszköz, azt a piactéren kell megvásárolni, és telepíteni kell a felhasználó környezetében.|
|Online|[Blitzz (Oracle DB/Apache Cassandra-ból)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>a &bull; támogatja a nagyobb adatkészleteket <br/>&bull; mivel ez egy külső gyártótól származó eszköz, azt a piactéren kell megvásárolni, és telepíteni kell a felhasználó környezetében.|

## <a name="other-apis"></a>Egyéb API-k
Az SQL API-t, a Mongo API-t és a Cassandra API-t nem használó API-k esetében az API meglévő ökoszisztémái különböző eszközöket támogatnak. 

**Tábla API** 
* [Adatáttelepítési eszköz](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin API**
* [Gráf tömeges végrehajtó könyvtára](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Következő lépések

* További információ: a [.net](bulk-executor-dot-net.md) és a [Java](bulk-executor-java.md)szolgáltatásban a tömeges végrehajtó függvénytárat használó minta alkalmazások kipróbálása. 
* A tömeges végrehajtó függvénytár integrálva van a Cosmos DB Spark-összekötőbe, és további információt a [Azure Cosmos db Spark-összekötő](spark-connector.md) című cikkben talál.  
* A nagyméretű áttelepítéssel kapcsolatos további segítségért lépjen kapcsolatba a Azure Cosmos DB termék csapatával, és nyisson meg egy támogatási jegyet az "általános tanácsadó" probléma típusa és a "nagy (TB +) Migrálás" problémás altípusban.
