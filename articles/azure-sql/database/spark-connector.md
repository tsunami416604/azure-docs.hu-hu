---
title: A Spark-összekötő használata Microsoft Azure SQL és SQL Server használatával
description: Megtudhatja, hogyan használhatja a Spark-összekötőt Microsoft Azure SQL-sel és SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: b2e042f2c3a7c6e1528ff96fb4fb96f392274855
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041226"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>Valós idejű big data elemzések felgyorsítása a Spark-összekötő használatával 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Spark-összekötő lehetővé teszi, hogy a Azure SQL Database, az Azure SQL felügyelt példányai és a SQL Server adatbázisok bemeneti adatforrásként vagy kimeneti adatfogadóként működjenek a Spark-feladatokhoz. Lehetővé teszi a valós idejű tranzakciós adatmennyiségek kihasználása big data elemzésekben, és az alkalmi lekérdezések vagy jelentések eredményeinek megőrzése. A beépített JDBC-összekötőhöz képest ez az összekötő lehetővé teszi az adatbevitelt Microsoft Azure SQL-és SQL Server-adatbázisba. Az adatsorok sorba állítása a sor beszúrása után 10x-vel gyorsabb teljesítményt nyújt. A Spark-összekötő támogatja az HRE hitelesítést az Azure SQL Database-adatbázisokhoz való csatlakozáshoz. Lehetővé teszi az Azure SQL Database-adatbázishoz való biztonságos csatlakozást Azure Databricks a HRE-fiók használatával. Hasonló felületeket biztosít a beépített JDBC-összekötővel. A meglévő Spark-feladatok egyszerűen áttelepíthetők az új összekötő használatára.

## <a name="download-and-build-spark-connector"></a>Spark-összekötő letöltése és létrehozása

Első lépésként töltse le a Spark-összekötőt az [Azure-sqldb-Spark adattárból](https://github.com/Azure/azure-sqldb-spark) a githubon.

### <a name="official-supported-versions"></a>Hivatalos támogatott verziók

| Összetevő                             | Verzió                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 vagy újabb           |
| Scala                                 | 2,10 vagy újabb            |
| Microsoft JDBC-illesztőprogram SQL Serverhez  | 6,2 vagy újabb             |
| Microsoft SQL Server                  | SQL Server 2008 vagy újabb |
| Azure SQL Database                    | Támogatott                |
| Felügyelt Azure SQL-példány            | Támogatott                |

A Spark-összekötő a Microsoft JDBC-illesztőt használja a SQL Serverhoz az adatáthelyezéshez a Spark Worker-csomópontok és az SQL-adatbázisok között:

A adatfolyam a következő:

1. A Spark-főkiszolgáló egy Azure SQL-vagy SQL Server-adatbázishoz csatlakozik, és egy adott táblából vagy egy adott SQL-lekérdezés használatával tölti be az adatait
2. A Spark-főkiszolgáló a feldolgozó csomópontok számára az átalakításhoz az adatok elosztását végzi.
3. A Worker csomópont egy Azure SQL-vagy SQL Server-adatbázishoz csatlakozik, és adatot ír az adatbázisba. A felhasználó dönthet úgy, hogy sor-sor beszúrást vagy tömeges beszúrást használ.

Az alábbi ábra az adatfolyamot ábrázolja.

   ![architektúra](./media/spark-connector/architecture.png)

### <a name="build-the-spark-connector"></a>A Spark-összekötő létrehozása

Az összekötő projekt jelenleg a mavent használja. Ha függőségek nélkül szeretné felépíteni az összekötőt, futtassa a következőt:

- MVN tiszta csomag
- Töltse le a JAR legújabb verzióit a kiadási mappából.
- A SQL Database Spark JAR belefoglalása

## <a name="connect-and-read-data-using-the-spark-connector"></a>Csatlakozás és adatolvasás a Spark-összekötő használatával

Egy Spark-feladatokból egy Azure SQL-vagy SQL Server-adatbázishoz kapcsolódhat, illetve olvasható vagy írható. Az Azure SQL-ben és a SQL Server-ban is futtathat DML-vagy DDL-lekérdezést az adatbázisban.

### <a name="read-data-from-azure-sql-and-sql-server"></a>Adatok beolvasása az Azure SQL-ből és SQL Server

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

### <a name="read-data-from-azure-sql-and-sql-server-with-specified-sql-query"></a>Adatok beolvasása az Azure SQL-ből és SQL Server a megadott SQL-lekérdezéssel

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

### <a name="write-data-to-azure-sql-and-sql-server"></a>Adatírás az Azure SQL-be és SQL Server

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

### <a name="run-dml-or-ddl-query-in-azure-sql-and-sql-server"></a>DML-vagy DDL-lekérdezés futtatása az Azure SQL-ben és SQL Server

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

## <a name="connect-from-spark-to-azure-sql-using-aad-authentication"></a>Kapcsolódás a Spark-ból az Azure SQL-be a HRE-hitelesítés használatával

Azure Active Directory (HRE) hitelesítés használatával kapcsolódhat az Azure SQL-hez. Az HRE-hitelesítés használatával központilag kezelheti az adatbázis-felhasználók identitásait, és a SQL Server hitelesítés alternatívájaként is.

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

Lásd: [Azure Active Directory hitelesítés használata hitelesítéshez](authentication-aad-overview.md) , amelyből megtudhatja, hogyan kérhet hozzáférési tokent az adatbázishoz Azure SQL Database vagy az Azure SQL felügyelt példányain.

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

## <a name="write-data-to-azure-sql-and-sql-server-using-bulk-insert"></a>Adatírás az Azure SQL-be és a SQL Server tömeges beszúrással

A hagyományos JDBC-összekötő az Azure SQL-be és a SQL Server sor-sor beszúrással írja be az adatot. A Spark Connector használatával az Azure SQL és a SQL Server tömeges Beszúrás használatával írhat adatírást. Nagy mértékben javítja az írási teljesítményt nagyméretű adathalmazok betöltésekor, vagy az adattöltést olyan táblákba, ahol az oszlopok tárolására szolgáló index van használatban.

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

Ha még nem tette meg, töltse le a Spark-összekötőt az [Azure-sqldb-Spark GitHub adattárból](https://github.com/Azure/azure-sqldb-spark) , és fedezze fel a további forrásokat a tárházban:

- [Minta Azure Databricks notebookok](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Mintául szolgáló parancsfájlok (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Érdemes áttekinteni a [Apache Spark SQL, a DataFrames és az adatkészletek útmutatóját](https://spark.apache.org/docs/latest/sql-programming-guide.html) , valamint a [Azure Databricks dokumentációját](https://docs.microsoft.com/azure/azure-databricks/)is.
