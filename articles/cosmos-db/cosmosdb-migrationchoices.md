---
title: Cosmos DB áttelepítési lehetőségek
description: Ez a dokumentum ismerteti a helyszíni vagy a Felhőbeli adatAzure Cosmos DBre való Migrálás különböző lehetőségeit.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 4de6d4ba019af75b0f6179b2794ddb6c1e35e0c1
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030072"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>A helyszíni vagy a Felhőbeli adatátviteli beállítások Azure Cosmos DB

A különböző adatforrásokból származó adatok betölthetők Azure Cosmos DBba. Mivel Azure Cosmos DB több API-t támogat, a célok lehetnek a meglévő API-k. A következő forgatókönyvek az adatok Azure Cosmos DBba való áttelepíthetők:

* Az adatok áthelyezése egy Azure Cosmos-tárolóból egy másik tárolóba ugyanabba az adatbázisba vagy egy másik adatbázisba. s
* Az adatáthelyezés dedikált tárolók között a megosztott adatbázis-tárolók között.
* Adatok áthelyezése egy terület1 található Azure Cosmos-fiókból egy másik Azure Cosmos-fiókba ugyanabban vagy egy másik régióban.
* Adatok áthelyezése egy forrásból, például az Azure Blob Storage-ból, egy JSON-fájlból, egy Oracle-adatbázisból, egy Couchbase, a DynamoDB-ből Azure Cosmos DB.

Ahhoz, hogy támogassa a különböző forrásokból származó áttelepítési útvonalakat a különböző Azure Cosmos DB API-kkal, több megoldás is van, amelyek speciális kezelést biztosítanak az egyes áttelepítési útvonalakhoz. Ez a dokumentum felsorolja az elérhető megoldásokat, valamint ismerteti azok előnyeit és korlátozásait.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Az áttelepítési eszköz kiválasztását befolyásoló tényezők

A következő tényezők határozzák meg az áttelepítési eszköz kiválasztását:

* **Online vagy offline áttelepítés**: számos áttelepítési eszköz biztosít egy elérési utat, amely csak egyszeri áttelepítést tesz lehetővé. Ez azt jelenti, hogy az adatbázishoz hozzáférő alkalmazások leállási időszakot tapasztalhatnak. Egyes áttelepítési megoldások lehetővé teszik az élő áttelepítést, ahol a forrás és a cél közötti replikációs folyamat van beállítva.

* **Adatforrás**: a meglévő adatforrások lehetnek olyan különböző adatforrások, mint például az Oracle DB2, a Datastax Cassanda, a Azure SQL Database, a PostgreSQL stb. Az adattípusok egy meglévő Azure Cosmos DB fiókban is lehetnek, és a Migrálás célja lehet az adatmodell módosítása vagy egy másik partíciós kulccsal rendelkező tárolóban lévő adatparticionálás.

* **Azure Cosmos db API**: a Azure Cosmos db SQL API-hoz számos olyan eszközt fejlesztettünk ki, amelyek a különböző áttelepítési forgatókönyvek keretében támogatást nyújtanak a Azure Cosmos db csapat számára. Az összes többi API-nak saját, a Közösség által fejlesztett és karbantartott eszközei vannak. Mivel a Azure Cosmos DB támogatja ezeket az API-kat a vezetékes protokoll szintjén, ezeknek az eszközöknek ugyanúgy kell működniük, amikor az Azure Cosmos DB is áttelepíti az adatátvitelt. Előfordulhat azonban, hogy egyéni kezelést igényelnek a szabályozáshoz, mivel ez a fogalom Azure Cosmos DBre vonatkozik.

* **Adatméret**: a legtöbb áttelepítési eszköz nagyon jól működik a kisebb adatkészletek esetében. Ha az adathalmaz meghaladja a néhány száz gigabájtot, az áttelepítési eszközök lehetőségei korlátozottak. 

* **Várható áttelepítési időtartam**: a Migrálás úgy konfigurálható, hogy lassú, növekményes ütemben történjen, amely kevesebb átviteli sebességet használ, vagy felhasználja a cél Azure Cosmos db tárolóban kiosztott teljes átviteli sebességet, és az áttelepítést kevesebb idő alatt végrehajtja.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API

|Áttelepítés típusa|Megoldás|Támogatott források|Támogatott célok|Megfontolandó szempontok|
|---------|---------|---------|---------|---------|
|Offline|[Adatáttelepítési eszköz](import-data.md)| &bull;JSON-/CSV-fájlok<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;AWS DynamoDB<br/>&bull;Azure-Blob Storage|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB Tables API<br/>&bull;JSON-fájlok |&bull; Egyszerűen beállíthatja és támogathatja a több forrást. <br/>&bull; Nagyméretű adathalmazokhoz nem alkalmas.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON-/CSV-fájlok<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB-hez készült Azure Cosmos DB API<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure-Blob Storage <br/> <br/>Tekintse meg az egyéb támogatott források [Azure Data Factoryét](../data-factory/connector-overview.md) ismertető cikket.|&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB-hez készült Azure Cosmos DB API<br/>&bull;JSON-fájlok <br/><br/> További támogatott célokért tekintse meg a [Azure Data Factory](../data-factory/connector-overview.md) cikket. |&bull; Egyszerűen beállíthatja és támogathatja a több forrást.<br/>&bull; A Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé. <br/>&bull; Nagyméretű adatkészletekhez használható. <br/>&bull; Az ellenőrzőpontok hiánya – ez azt jelenti, hogy ha probléma merül fel az áttelepítés során, újra kell indítania a teljes áttelepítési folyamatot.<br/>&bull; A kézbesítetlen levelek várólistájának hiánya – ez azt jelenti, hogy néhány hibás fájl leállíthatja a teljes áttelepítési folyamatot.|
|Offline|[Azure Cosmos DB Spark-összekötő](spark-connector.md)|Azure Cosmos DB SQL API-t. <br/><br/>Más forrásokat is használhat a Spark-ökoszisztéma további összekötőit használva.| Azure Cosmos DB SQL API-t. <br/><br/>Más célokat a Spark-ökoszisztéma további összekötői is használhatnak.| &bull; A Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé. <br/>&bull; Nagyméretű adatkészletekhez használható. <br/>&bull; Egyéni Spark-telepítőre van szüksége. <br/>&bull; A Spark érzékeny a séma következetlenségére, és ez problémát okozhat az áttelepítés során. |
|Offline|[Egyéni eszköz Cosmos DB tömeges végrehajtó könyvtárral](migrate-cosmosdb-data.md)| A forrás az egyéni kódból függ | Azure Cosmos DB SQL API| &bull; Ellenőrzőpont-és kézbesítetlen levelekre vonatkozó képességeket biztosít, amelyek növelik az áttelepítés rugalmasságát. <br/>&bull; Nagyon nagy adatkészletekhez alkalmas (10 TB +).  <br/>&bull; Ehhez az eszközhöz App Serviceként futó egyéni beállítás szükséges. |
|Online|[Cosmos DB Functions + ChangeFeed API](change-feed-functions.md)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; Egyszerűen beállítható. <br/>&bull; Csak akkor működik, ha a forrás egy Azure Cosmos DB tároló. <br/>&bull; Nagyméretű adathalmazokhoz nem alkalmas. <br/>&bull; A nem rögzíti a törlést a forrás tárolóból. |
|Online|[Egyéni áttelepítési szolgáltatás a ChangeFeed használatával](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; A folyamat nyomon követését teszi lehetővé. <br/>&bull; Csak akkor működik, ha a forrás egy Azure Cosmos DB tároló. <br/>&bull; Nagyobb adatkészletek esetén is működik.<br/>&bull; A felhasználónak be kell állítania egy App Service a változási csatorna processzorának üzemeltetéséhez. <br/>&bull; A nem rögzíti a törlést a forrás tárolóból.|
|Online|[Striim](cosmosdb-sql-api-migrate-data-striim.md)| &bull;Oracle <br/>&bull;Apache Cassandra<br/><br/> Más támogatott forrásokért tekintse meg a [Striim webhelyet](https://www.striim.com/sources-and-targets/) . |&bull;Azure Cosmos DB SQL API <br/>&bull; Azure Cosmos DB Cassandra API<br/><br/> További támogatott célokért tekintse meg a [Striim webhelyet](https://www.striim.com/sources-and-targets/) . | &bull; Sokféle forrással működik, például Oracle, DB2 SQL Server.<br/>&bull; Egyszerűen hozhat létre ETL-folyamatokat, és irányítópultot biztosít a figyeléshez. <br/>&bull; Nagyobb adatkészleteket támogat. <br/>&bull; Mivel ez egy harmadik féltől származó eszköz, azt a piactéren kell megvásárolni, és telepíteni kell a felhasználó környezetében.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API

|Áttelepítés típusa|Megoldás|Támogatott források|Támogatott célok|Megfontolandó szempontok|
|---------|---------|---------|---------|---------|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|MongoDB-hez készült Azure Cosmos DB API |&bull; A Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé. <br/>&bull; Nagyméretű adatkészletekhez alkalmas, és gondoskodik az élő módosítások replikálásáról. <br/>&bull; Csak más MongoDB-forrásokkal működik.|
|Offline|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| MongoDB-hez készült Azure Cosmos DB API| &bull; A Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé. <br/>&bull; Nagyméretű adatkészletekhez alkalmas, és gondoskodik az élő módosítások replikálásáról. <br/>&bull; Csak más MongoDB-forrásokkal működik.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON-/CSV-fájlok<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB-hez készült Azure Cosmos DB API <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure-Blob Storage <br/><br/> Tekintse meg az egyéb támogatott források [Azure Data Factoryét](../data-factory/connector-overview.md) ismertető cikket. | &bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB-hez készült Azure Cosmos DB API <br/>&bull; JSON-fájlok <br/><br/> További támogatott célokért tekintse meg a [Azure Data Factory](../data-factory/connector-overview.md) cikket.| &bull; Egyszerűen beállíthatja és támogathatja a több forrást. <br/>&bull; A Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé. <br/>&bull; Nagyméretű adatkészletekhez használható. <br/>&bull; Az ellenőrzőpontok hiánya azt jelenti, hogy az áttelepítés során felmerülő problémákhoz a teljes áttelepítési folyamat újraindítására lenne szükség.<br/>&bull; A kézbesítetlen levelek várólistájának hiánya azt jelenti, hogy néhány hibás fájl leállíthatja a teljes áttelepítési folyamatot. <br/>&bull; Az egyes adatforrások olvasási teljesítményének növeléséhez egyéni kód szükséges.|
|Offline|[Meglévő Mongo-eszközök (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|MongoDB | MongoDB-hez készült Azure Cosmos DB API| &bull; Egyszerűen beállítható és integrálható. <br/>&bull; Egyéni kezelést igényel a szabályozáshoz.|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API

|Áttelepítés típusa|Megoldás|Támogatott források|Támogatott célok|Megfontolandó szempontok|
|---------|---------|---------|---------|---------|
|Offline|[cqlsh MÁSOLÁSi parancs](cassandra-import-data.md#migrate-data-using-cqlsh-copy-command)|CSV-fájlok | Azure Cosmos DB Cassandra API| &bull; Egyszerűen beállítható. <br/>&bull; Nagyméretű adathalmazokhoz nem alkalmas. <br/>&bull; Csak akkor működik, ha a forrás egy Cassandra-tábla.|
|Offline|[Táblázat másolása a Sparktal](cassandra-import-data.md#migrate-data-using-spark) | &bull;Apache Cassandra<br/>&bull;Azure Cosmos DB Cassandra API| Azure Cosmos DB Cassandra API | &bull; A Spark-funkciók használatával integrálással az átalakítást és a betöltést. <br/>&bull; A szabályozások kezeléséhez egyéni újrapróbálkozási szabályzattal rendelkező konfigurációra van szükség.|
|Online|[Striim (Oracle DB/Apache Cassandra-ból)](cosmosdb-cassandra-api-migrate-data-striim.md)| &bull;Oracle<br/>&bull;Apache Cassandra<br/><br/> Más támogatott forrásokért tekintse meg a [Striim webhelyet](https://www.striim.com/sources-and-targets/) .|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB Cassandra API <br/><br/> További támogatott célokért tekintse meg a [Striim webhelyet](https://www.striim.com/sources-and-targets/) .| &bull; Sokféle forrással működik, például Oracle, DB2 SQL Server. <br/>&bull; Egyszerűen hozhat létre ETL-folyamatokat, és irányítópultot biztosít a figyeléshez. <br/>&bull; Nagyobb adatkészleteket támogat. <br/>&bull; Mivel ez egy harmadik féltől származó eszköz, azt a piactéren kell megvásárolni, és telepíteni kell a felhasználó környezetében.|
|Online|[Blitzz (Oracle DB/Apache Cassandra-ból)](oracle-migrate-cosmos-db-blitzz.md)|&bull;Oracle<br/>&bull;Apache Cassandra<br/><br/>Más támogatott forrásokért tekintse meg a [Blitzz webhelyet](https://www.blitzz.io/) . |Azure Cosmos DB Cassandra API. <br/><br/>További támogatott célokért tekintse meg a [Blitzz webhelyet](https://www.blitzz.io/) . | &bull; Nagyobb adatkészleteket támogat. <br/>&bull; Mivel ez egy harmadik féltől származó eszköz, azt a piactéren kell megvásárolni, és telepíteni kell a felhasználó környezetében.|

## <a name="other-apis"></a>Egyéb API-k

Az SQL API-t, a Mongo API-t és a Cassandra API-t nem használó API-k esetében az API meglévő ökoszisztémái különböző eszközöket támogatnak. 

**Table API** 

* [Adatáttelepítési eszköz](table-import.md#data-migration-tool)
* [AzCopy](table-import.md#migrate-data-by-using-azcopy)

**Gremlin API**

* [Gráf tömeges végrehajtó könyvtára](bulk-executor-graph-dotnet.md)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Következő lépések

* További információ: a [.net](bulk-executor-dot-net.md) és a [Java](bulk-executor-java.md)szolgáltatásban a tömeges végrehajtó függvénytárat használó minta alkalmazások kipróbálása. 
* A tömeges végrehajtó függvénytár integrálva van a Cosmos DB Spark-összekötőbe, és további információt a [Azure Cosmos db Spark-összekötő](spark-connector.md) című cikkben talál.  
* A nagyméretű áttelepítéssel kapcsolatos további segítségért lépjen kapcsolatba a Azure Cosmos DB termék csapatával, és nyisson meg egy támogatási jegyet az "általános tanácsadó" probléma típusa és a "nagy (TB +) Migrálás" problémás altípusban.
