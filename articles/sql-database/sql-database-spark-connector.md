---
title: Spark-összekötő Azure SQL Database és SQL Server
description: Megtudhatja, hogyan használhatja a Spark-összekötőt Azure SQL Database és SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: b22ec475c0281a54d65921bc450b35723aa23219
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471652"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Gyorsítsa fel a valós idejű big data elemzéseket a Spark-összekötővel Azure SQL Database és SQL Server

A Azure SQL Database és SQL Server Spark-összekötője lehetővé teszi az SQL-adatbázisok, például a Azure SQL Database és a SQL Server számára, hogy bemeneti adatforrásként vagy kimeneti adatfogadóként működjenek a Spark-feladatokhoz. Lehetővé teszi a valós idejű tranzakciós adatmennyiségek kihasználása big data elemzésekben, és az alkalmi lekérdezések vagy jelentések eredményeinek megőrzése. A beépített JDBC-összekötőhöz képest ez az összekötő lehetővé teszi, hogy az SQL-adatbázisokba tömegesen helyezzen be adatbevitelt. Az adatsorok sorba állítása a sor beszúrása után 10x-vel gyorsabb teljesítményt nyújt. A Azure SQL Database és SQL Server Spark-összekötője a HRE-hitelesítést is támogatja. Lehetővé teszi az Azure SQL Database-adatbázishoz való biztonságos csatlakozást Azure Databricks a HRE-fiók használatával. Hasonló felületeket biztosít a beépített JDBC-összekötővel. A meglévő Spark-feladatok egyszerűen áttelepíthetők az új összekötő használatára.

## <a name="download"></a>Letöltés
Első lépésként töltse le a Sparkot az SQL DB-összekötőre az [Azure-sqldb-Spark adattárból](https://github.com/Azure/azure-sqldb-spark) a githubon.

## <a name="official-supported-versions"></a>Hivatalos támogatott verziók

| Összetevő                            | Verzió                  |
| :----------------------------------- | :----------------------- |
| Apache Spark                         | 2.0.2 vagy újabb           |
| Scala                                | 2,10 vagy újabb            |
| Microsoft JDBC-illesztőprogram a SQL Serverhoz | 6,2 vagy újabb             |
| Microsoft SQL Server                 | SQL Server 2008 vagy újabb |
| Azure SQL Database                   | Támogatott                |

A Azure SQL Database és SQL Server Spark-összekötője a Microsoft JDBC-illesztőt használja a SQL Serverhoz a Spark Worker-csomópontok és az SQL-adatbázisok közötti adatáthelyezéshez:
 
A adatfolyam a következő:
1. A Spark főcsomópontja összekapcsolja a SQL Server, Azure SQL Database vagy egy adott táblából vagy egy adott SQL-lekérdezés használatával tölti be az adatait.
2. A Spark-főkiszolgáló a feldolgozó csomópontok számára az átalakításhoz az adatok elosztását végzi. 
3. A feldolgozó csomópont csatlakozik SQL Serverhoz, vagy Azure SQL Database és ír az adatbázisba. A felhasználó dönthet úgy, hogy sor-sor beszúrást vagy tömeges beszúrást használ.

Az alábbi ábra az adatfolyamot ábrázolja.

   ![architektúra](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>A Spark felépítése az SQL DB-összekötőbe
Az összekötő projekt jelenleg a mavent használja. Ha függőségek nélkül szeretné felépíteni az összekötőt, futtassa a következőt:

- MVN tiszta csomag
- Töltse le a JAR legújabb verzióit a kiadási mappából.
- Az SQL DB Spark JAR belefoglalása

## <a name="connect-spark-to-sql-db-using-the-connector"></a>A Spark és az SQL DB összekötése az összekötő használatával
A Spark-feladatokhoz Azure SQL Database vagy SQL Server csatlakozhat, illetve olvasási vagy írási feladatokat is elhelyezhet. Egy DML-vagy DDL-lekérdezést futtathat egy Azure SQL Database-adatbázisban vagy SQL Server adatbázisban is.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Adatok beolvasása Azure SQL Database vagy SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Adatok olvasása Azure SQL Database vagy SQL Server a megadott SQL-lekérdezéssel
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Adatírás a Azure SQL Databaseba vagy az SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>DML-vagy DDL-lekérdezés futtatása Azure SQL Database vagy SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>A Spark és a Azure SQL Database összekötése a HRE-hitelesítés használatával
Azure Active Directory (HRE) hitelesítés használatával csatlakozhat Azure SQL Databasehoz. Az HRE-hitelesítés használatával központilag kezelheti az adatbázis-felhasználók identitásait, és a SQL Server hitelesítés alternatívájaként is.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Csatlakozás a ActiveDirectoryPassword hitelesítési móddal
#### <a name="setup-requirement"></a>Telepítési követelmény
Ha a ActiveDirectoryPassword hitelesítési módot használja, le kell töltenie az [Azure-ActiveDirectory-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) -t és annak függőségeit, és fel kell vennie őket a Java Build elérési útjába.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Kapcsolódás hozzáférési jogkivonat használatával
#### <a name="setup-requirement"></a>Telepítési követelmény
Ha a hozzáférési jogkivonat-alapú hitelesítési módot használja, le kell töltenie az [Azure-ActiveDirectory-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) -t és annak függőségeit, és fel kell vennie azokat a Java-Build elérési útjába.

Lásd: [Azure Active Directory hitelesítés használata a hitelesítéshez SQL Database](sql-database-aad-authentication.md) segítségével megtudhatja, hogyan szerezhet be hozzáférési tokent az Azure SQL Database-be.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Adatírás az Azure SQL Database-be vagy SQL Server tömeges Beszúrás használatával
A hagyományos JDBC-összekötő az Azure SQL Database-be vagy a SQL Server sor-sor beszúrásával ír be adatot. A Spark és az SQL DB Connector használatával az SQL Database-be tömeges beszúrással írhat adatbevitelt. Nagy mértékben javítja az írási teljesítményt nagyméretű adathalmazok betöltésekor, vagy az adattöltést olyan táblákba, ahol az oszlopok tárolására szolgáló index van használatban.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Következő lépések
Ha még nem tette meg, töltse le a Spark-összekötőt Azure SQL Database és SQL Server az [Azure-sqldb-Spark GitHub adattárból](https://github.com/Azure/azure-sqldb-spark) , és ismerkedjen meg a tárház további erőforrásaival:

- [Minta Azure Databricks notebookok](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Mintául szolgáló parancsfájlok (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Érdemes áttekinteni a [Apache Spark SQL, a DataFrames és az adatkészletek útmutatóját](https://spark.apache.org/docs/latest/sql-programming-guide.html) , valamint a [Azure Databricks dokumentációját](https://docs.microsoft.com/azure/azure-databricks/)is.

