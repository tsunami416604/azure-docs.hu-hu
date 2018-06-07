---
title: Spark az Azure SQL Database és SQL Server-összekötő |} Microsoft Docs
description: A Spark Connector használata az Azure SQL Database és SQL Server
services: sql-database
author: allenwux
manager: craigg
ms.service: sql-database
ms.custom: ''
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: xiwu
ms.openlocfilehash: a422f65097466e4bbe5740c449d3ccf88701802b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650162"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Spark-összekötővel valós idejű big data elemzés érdekében az Azure SQL Database és SQL Server

Az Azure SQL Database és SQL Server Spark-összekötő lehetővé teszi, hogy az SQL-adatbázisok, beleértve az Azure SQL Database és az SQL Server, bemeneti adatokat forrás vagy a kimeneti adatokat fogadó Spark-feladatok nevében járhasson el. Ez lehetővé teszi a big data elemzés valós idejű tranzakciós adatok használatára, és az ad hoc lekérdezéseket vagy a jelentés az eredménye megmaradnak. A beépített JDBC-összekötő képest, ez az összekötő lehetővé teszi a tömeges beszúrás adatokat az SQL-adatbázisok. Azt is outperform soronként beszúrási 10 x 20 x gyorsabb teljesítmény. A Spark-összekötő az Azure SQL Database és SQL Server is támogatja az AAD-hitelesítés. Lehetővé teszi a biztonságos kapcsolódás az Azure SQL database az Azure Databricks AAD-fiókjával. Hasonló felületek biztosít a beépített JDBC-összekötővel együtt. Akkor is könnyen áttelepítése a meglévő Spark feladatok az új összekötő használatára.

## <a name="download"></a>Letöltés
A kezdéshez töltse le a Spark SQL DB-összekötőt a [azure-sqldb-külső tárház](https://github.com/Azure/azure-sqldb-spark) a Githubon.

## <a name="official-supported-versions"></a>Hivatalos támogatott verziója

| Összetevő                            |Verzió                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 vagy újabb verzió           |
| Scala                                |2,10 vagy újabb verzió            |
| Microsoft JDBC-illesztőprogram SQL Serverhez |6.2 vagy újabb             |
| Microsoft SQL Server                 |SQL Server 2008 vagy újabb |
| Azure SQL Database                   |Támogatott                |

Az Azure SQL Database és SQL Server Spark-összekötőt használja a Microsoft Spark munkavégző csomópontokhoz és SQL-adatbázisok közötti áthelyezni az adatokat az SQL Server JDBC-illesztőt:
 
Az adatfolyamban a következőképpen történik:
1. A Spark főcsomópont SQL Server vagy az Azure SQL Database csatlakozik, és adatokat tölt a bizonyos táblájához vagy egy adott SQL-lekérdezés
2. A Spark főcsomópont elosztása az adatok munkavégző csomópontokhoz az átalakításhoz. 
3. A munkavégző csomópont csatlakozik az SQL Server vagy az Azure SQL Database, és adatokat ír az adatbázis. Felhasználó soronként-beszúrási vagy tömeges beszúrás választhat.

A következő ábra szemlélteti az adatfolyam.

   ![architektúra](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>A Spark SQL DB összekötőre összeállítása
Az összekötő projekt jelenleg maven használja. Az összekötő függőségek nélkül létrehozásához futtathatja:

- mvn tiszta csomag
- Töltse le a JAR legújabb verzióját a kiadási mappából
- Az SQL DB külső JAR tartalmazza

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Spark csatlakozni az összekötővel SQL-adatbázis
Csatlakozás az Azure SQL Database vagy az SQL Server Spark feladatokból, olvassa el, vagy adatok írása. Azure SQL-adatbázisban vagy SQL Server-adatbázis is futtathatja egy DML vagy DDL-lekérdezés.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Az Azure SQL Database vagy az SQL Server-adatok olvasása

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
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Adatok beolvasása az Azure SQL Database vagy az SQL Server megadott SQL-lekérdezés
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

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Adatok írása az Azure SQL Database vagy az SQL Server
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

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Az Azure SQL Database vagy az SQL Server DML vagy DDL-lekérdezés futtatása
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

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Spark csatlakozzon az Azure SQL Database AAD-hitelesítés használatával
Azure SQL Database szolgáltatáshoz Azure Active Directory (AAD) hitelesítési kapcsolódhat. AAD-hitelesítés segítségével központilag kezelheti az identitásokat az adatbázis-felhasználók és az SQL Server-hitelesítés helyett.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>ActiveDirectoryPassword hitelesítési móddal
#### <a name="setup-requirement"></a>A telepítő követelmény
Ha a ActiveDirectoryPassword hitelesítési módot használ, le kell töltenie [azure-Active Directoryban – könyvtár-az-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) és függőségeit, és azokat a Java build elérési útja.

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

### <a name="connecting-using-access-token"></a>Kapcsolódás a hozzáférési jogkivonat használatával
#### <a name="setup-requirement"></a>A telepítő követelmény
Ha a hozzáférési jogkivonat-alapú hitelesítési módot használ, le kell töltenie [azure-Active Directoryban – könyvtár-az-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) és függőségeit, és azokat a Java build elérési útja.

Lásd: [Azure Active Directory hitelesítés használata a hitelesítés az SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) megtudhatja, hogyan férhetnek hozzá az Azure SQL-adatbázis tokenjét.

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

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Adatok írása az Azure SQL database vagy az SQL Server Bulk Insert használatával
A hagyományos jdbc-összekötő írja az adatokat az Azure SQL database vagy az SQL Server-soronként beszúrási használatával. Spark SQL DB összekötőre segítségével adatok írása az SQL-adatbázis a tömeges beszúrás. Jelentősen javítja a írási teljesítmény, oszlopcentrikus indexszel helyének táblákba nagy adatkészletek vagy az adatok betöltésekor betöltése közben.

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
Ha még nem tette meg, töltse le a Spark-összekötő az Azure SQL Database és SQL Server a [azure-sqldb-spark GitHub-tárházban](https://github.com/Azure/azure-sqldb-spark) és vizsgálja meg a tárházban további források:

-   [A minta Azure Databricks notebookok](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Mintaparancsfájlok (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Is érdemes áttekinteni a [Apache Spark SQL, DataFrames és adatkészletek útmutató](http://spark.apache.org/docs/latest/sql-programming-guide.html) és a [Azure Databricks dokumentáció](https://docs.microsoft.com/azure/azure-databricks/).

