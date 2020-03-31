---
title: Spark Connector az Azure SQL Database és az SQL Server alkalmazással
description: A Spark Connector azure-beli SQL Database és AZ SQL Server használatához való használatának megismerése
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471652"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Gyorsítsa fel a valós idejű big data-elemzést az Azure SQL Database és az SQL Server Spark-összekötőjével

A Spark-összekötő az Azure SQL Database és az SQL Server lehetővé teszi, hogy az SQL-adatbázisok, beleértve az Azure SQL Database és az SQL Server, a Spark-feladatok bemeneti adatforrásként vagy kimeneti adatgyűjtőként működjenek. Lehetővé teszi, hogy valós idejű tranzakciós adatokat használjon a big data-elemzésben, és megőrizje az eredményeket az adhoc lekérdezésekhez vagy jelentésekhez. A beépített JDBC-összekötővel összehasonlítva ez az összekötő lehetővé teszi az SQL-adatbázisokba történő adatok tömeges beszúrását. A 10-20-szor gyorsabb teljesítménnyel képes soronként jobban teljesíteni. A Spark-összekötő az Azure SQL Database és az SQL Server is támogatja az AAD-hitelesítést. Lehetővé teszi, hogy biztonságosan csatlakozzon az Azure DATABricks-ből az Azure Databricks-ből az AAD-fiók használatával. Hasonló interfészeket biztosít a beépített JDBC csatlakozóval. Az új összekötő használatához könnyen áttelepítheti a meglévő Spark-feladatokat.

## <a name="download"></a>Letöltés
A kezdéshez töltse le a Spark SQL DB-összekötőt az [azure-sqldb-spark tárházból](https://github.com/Azure/azure-sqldb-spark) a GitHubon.

## <a name="official-supported-versions"></a>Hivatalosan támogatott verziók

| Összetevő                            | Verzió                  |
| :----------------------------------- | :----------------------- |
| Apache Spark                         | 2.0.2 vagy újabb           |
| Scala                                | 2.10 vagy újabb            |
| Microsoft JDBC-illesztőprogram SQL Serverhez | 6.2 vagy újabb             |
| Microsoft SQL Server                 | SQL Server 2008 vagy újabb |
| Azure SQL Database                   | Támogatott                |

Az Azure SQL Database és AZ SQL Server Spark-összekötője a Microsoft JDBC Driver for SQL Server segítségével mozgatja az adatokat a Spark feldolgozócsomópontjai és az SQL-adatbázisok között:
 
Az adatfolyam a következő:
1. A Spark-főcsomópont csatlakozik az SQL Server vagy az Azure SQL Database szolgáltatáshoz, és adatokat tölt be egy adott táblából vagy egy adott SQL-lekérdezés használatával
2. A Spark-fő csomópont adatokat oszt ki a munkavégző csomópontok átalakításra. 
3. A munkavégző csomópont csatlakozik az SQL Server vagy az Azure SQL Database, és adatokat ír az adatbázisba. A felhasználó választhat, hogy soronkénti beszúrást vagy tömeges beszúrást használ.

Az alábbi ábra az adatfolyamot mutatja be.

   ![architektúra](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>A Spark-SQL DB összekötő létrehozása
Jelenleg az összekötő projekt maven-t használ. Az összekötő függőségek nélküli létrehozásához futtathatja a következőket:

- mvn tiszta csomag
- A JAR legújabb verzióinak letöltése a kiadási mappából
- Az SQL DB Spark JAR felvétele

## <a name="connect-spark-to-sql-db-using-the-connector"></a>A Spark csatlakoztatása az SQL DB-hez az összekötő használatával
Az Azure SQL Database vagy az SQL Server a Spark-feladatokból csatlakozhat, adatok olvasása vagy írása. DML- vagy DDL-lekérdezést Is futtathat egy Azure SQL-adatbázisban vagy SQL Server-adatbázisban.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Adatok olvasása az Azure SQL Database vagy az SQL Server kiszolgálóról

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
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Adatok olvasása az Azure SQL Database vagy az SQL Server rendszerből a megadott SQL-lekérdezéssel
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

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Adatok írása az Azure SQL Database vagy az SQL Server rendszerbe
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

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>DML- vagy DDL-lekérdezés futtatása Az Azure SQL Database vagy az SQL Server rendszerben
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

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>A Spark csatlakoztatása az Azure SQL-adatbázishoz AAD-hitelesítéssel
Az Azure Active Directory (AAD) hitelesítésével csatlakozhat az Azure SQL Database-hez. Az AAD-hitelesítés sel központilag kezelheti az adatbázis-felhasználók identitását, és az SQL Server-hitelesítés alternatívájaként.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Csatlakozás ActiveDirectoryPassword hitelesítési módhasználatával
#### <a name="setup-requirement"></a>Telepítési követelmény
Ha ActiveDirectoryPassword hitelesítési módot használ, le kell töltenie [az azure-activedirectory-library-for-java-t](https://github.com/AzureAD/azure-activedirectory-library-for-java) és annak függőségeit, és be kell illesztenie őket a Java build elérési útba.

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

### <a name="connecting-using-access-token"></a>Csatlakozás hozzáférési jogkivonat használatával
#### <a name="setup-requirement"></a>Telepítési követelmény
Ha a hozzáférési jogkivonat-alapú hitelesítési módot használja, le kell töltenie [az azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) és annak függőségeit, és fel kell építenie őket a Java build elérési útba.

Az [Azure Active Directory-hitelesítés használata az SQL-adatbázissal való hitelesítéshez](sql-database-aad-authentication.md) című témakörben megtudhatja, hogyan kaphat hozzáférési jogkivonatot az Azure SQL-adatbázisához.

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

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Adatok írása Az Azure SQL-adatbázisba vagy az SQL Server kiszolgálóra a Tömeges beszúrás használatával
A hagyományos jdbc-összekötő adatokat ír az Azure SQL-adatbázisba vagy az SQL Serverbe soronkénti beszúrással. A Spark-SQL DB-összekötő segítségével adatokat írhat az SQL-adatbázisba tömeges beszúrással. Jelentősen javítja az írási teljesítményt nagy adatkészletek betöltésekor vagy adatok betöltésekor olyan táblákba, ahol oszloptároló indexet használnak.

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

## <a name="next-steps"></a>További lépések
Ha még nem tette meg, töltse le a Spark-összekötőt az Azure SQL Database és az SQL Server számára az [azure-sqldb-spark GitHub-tárházból,](https://github.com/Azure/azure-sqldb-spark) és fedezze fel a további erőforrásokat a tárházban:

- [Minta Azure Databricks notebookok](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Mintaparancsfájlok (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Érdemes lehet áttekinteni az [Apache Spark SQL, DataFrames és adatkészletek útmutató](https://spark.apache.org/docs/latest/sql-programming-guide.html) és az Azure [Databricks dokumentációt.](https://docs.microsoft.com/azure/azure-databricks/)

