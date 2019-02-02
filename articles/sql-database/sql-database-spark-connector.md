---
title: A Spark az Azure SQL Database és az SQL Server-összekötő |} A Microsoft Docs
description: A Spark-összekötő használata az Azure SQL Database és SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 927b887dbe9214867c0a694a611ca9fd9dc647a2
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565292"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Gyorsítsa fel a valós idejű big data analytics és a Spark-összekötő az Azure SQL Database és SQL Server

A Spark-összekötő az Azure SQL Database és SQL Server lehetővé teszi, hogy az SQL-adatbázisok, beleértve az Azure SQL Database és az SQL Server, a bemeneti forrás vagy a kimeneti adatok fogadására szolgáló Spark-feladatok funkcionálni. Lehetővé teszi a big data-elemzés a valós idejű tranzakciós adatok, és a megőrzéséhez eredményeket ad hoc ad hoc lekérdezéseket és jelentéseket. A beépített JDBC-összekötő képest, ez az összekötő lehetővé teszi a tömeges beszúrás adatokat az SQL Database-adatbázisok. Azt is teljesítményben felülmúlják soronként beszúrási 10 x a 20 x nagyobb teljesítményhez. A Spark-összekötő az Azure SQL Database és SQL Server az AAD-hitelesítést is támogatja. Lehetővé teszi a biztonságos kapcsolódás az Azure SQL database használatával az AAD-fiók Azure databricksből. Hasonló felületeket biztosít a beépített JDBC-összekötővel. Fontos egyszerűvé tenni a létező Spark-feladatok az új összekötő használatához.

## <a name="download"></a>Letöltés
Első lépésként töltse le a Spark SQL DB-összekötő a [azure-sqldb-spark tárház](https://github.com/Azure/azure-sqldb-spark) a Githubon.

## <a name="official-supported-versions"></a>Hivatalos támogatott verziók

| Összetevő                            |Verzió                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2-es vagy újabb           |
| Scala                                |2,10 vagy újabb            |
| Microsoft JDBC-illesztőprogram SQL Serverhez |6.2-es vagy újabb             |
| Microsoft SQL Server                 |Az SQL Server 2008 vagy újabb |
| Azure SQL Database                   |Támogatott                |

A Spark-összekötő az Azure SQL Database és SQL Server használja a Microsoft JDBC-illesztőprogram SQL Serverhez az SQL-adatbázisok és a Spark munkavégző csomópontok közötti:
 
Az adatfolyam a következőképpen történik:
1. A Spark-főkiszolgáló csomópont csatlakozik az SQL Server- vagy Azure SQL Database, és adatokat tölt be egy adott táblához vagy egy adott SQL-lekérdezés használatával
2. A Spark-főkiszolgáló csomópont elosztja az adatokat a munkavégző csomópontokhoz átalakításkor. 
3. A munkavégző csomópont csatlakozik az SQL Server vagy az Azure SQL Database, és adatokat ír az adatbázis. Felhasználó dönthet a sor-sor beszúrása vagy tömeges beszúrás.

A következő ábra szemlélteti az adatfolyam.

   ![architektúra](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>A Spark, az SQL DB-összekötő létrehozása
Az összekötő projekt jelenleg a mavent használja. Az összekötő függőségek nélkül hozhat létre, futtathatja:

- mvn tiszta csomag
- Töltse le a fájlt a legújabb kiadás mappájában
- Az SQL DB Spark JAR belefoglalása

## <a name="connect-spark-to-sql-db-using-the-connector"></a>A Spark csatlakozni az összekötővel SQL-adatbázis
Spark-feladatok az Azure SQL Database vagy SQL Server elérését, olvassa el, vagy adatokat írni. Az Azure SQL database vagy SQL Server-adatbázis egy DML vagy DDL-lekérdezés is futtathatja.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Azure SQL Database vagy SQL Server adatainak beolvasása

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients"
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>A megadott SQL-lekérdezést az Azure SQL Database vagy SQL Server-adatok olvasása
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
val collection = sqlContext.read.sqlDb(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Adatokat írni az Azure SQL Database vagy SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients"
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Az Azure SQL Database vagy SQL Server DML vagy DDL-lekérdezés futtatása
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.SqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Csatlakozás a Spark az Azure SQL Database AAD-hitelesítés használatával
Azure SQL Database, Azure Active Directory (AAD) hitelesítés használatával csatlakozhat. AAD-hitelesítés segítségével központilag kezelheti az identitásokat az adatbázis-felhasználók és az SQL Server-hitelesítés helyett.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Kapcsolódás a ActiveDirectoryPassword hitelesítési mód használatával
#### <a name="setup-requirement"></a>A telepítő követelmény
Ha a ActiveDirectoryPassword hitelesítési módszerét használja, le kell töltenie [azure-activedirectory-erőforrástár-az-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) és annak függőségeit, és ezeket a Java-fordítási utat a.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username ",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Kapcsolódás a hozzáférési Token használatával
#### <a name="setup-requirement"></a>A telepítő követelmény
Ha a hozzáférési jogkivonat-alapú hitelesítési módot használja, le kell töltenie [azure-activedirectory-erőforrástár-az-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) és annak függőségeit, és ezeket a Java build elérési útja.

Lásd: [használata az Azure Active Directory-hitelesítéssel hitelesítés az SQL Database](sql-database-aad-authentication.md) megtudhatja, hogyan lehet beszerezni a hozzáférési jogkivonatot az Azure SQL Database-adatbázishoz.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token ",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Adatokat írni az Azure SQL database vagy SQL Server Bulk Insert használatával
A hagyományos jdbc-összekötő az Azure SQL database vagy SQL Server használatával a sor-sor beszúrása írja az adatokat. Az SQL DB-összekötő a Spark segítségével az adatok írása az SQL database-hez a tömeges beszúrás. Jelentős mértékben javítja a írási teljesítményt, ha nagy méretű adatkészletekhez, vagy az adatok betöltésekor betöltése a táblákba, ahol az oszlopcentrikus index használja.

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
  "databaseName"      -> "zeqisql",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>További lépések
Ha még nem tette, töltse le a Spark-összekötő az Azure SQL Database és SQL Server- [GitHub-tárházat az azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) és a tárházban további források:

-   [Minta Azure Databricks-jegyzetfüzetek](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Mintaszkriptek (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Emellett érdemes áttekinteni a [adatkészletek útmutató, Apache Spark SQL és DataFrames](http://spark.apache.org/docs/latest/sql-programming-guide.html) és a [Azure Databricks dokumentációja](https://docs.microsoft.com/azure/azure-databricks/).

