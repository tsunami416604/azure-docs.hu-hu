---
title: A Spark-készletek (előzetes verzió) és az SQL-készletek közötti adatimportálás és-exportálás
description: Ez a cikk azt ismerteti, hogyan használható az egyéni összekötő az adatoknak az SQL-készletek és a Spark-készletek (előzetes verzió) közötti áthelyezéséhez.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: ebf948fdb1df76cb7bcb03ee5d85f581d856524f
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85918723"
---
# <a name="introduction"></a>Bevezetés

Az Azure szinapszis Apache Spark a szinapszis SQL connectorhoz az Azure szinapszisban az adatoknak a Spark-készletek (előzetes verzió) és az SQL-készletek közötti hatékony átvitelét szolgálja. Az Azure szinapszis Apache Spark a szinapszis SQL-összekötő csak az SQL-készleteken működik, az SQL igény szerint nem működik.

## <a name="design"></a>Tervezés

A Spark-készletek és az SQL-készletek közötti adatátvitel a JDBC használatával végezhető el. Azonban a két elosztott rendszer, például a Spark és az SQL-készletek miatt a JDBC általában szűk keresztmetszetet jelent a soros adatátvitel során.

Az Azure szinapszis Apache Spark Pool to szinapszis SQL Connector a Apache Spark adatforrások implementációja. Az SQL-készletekben a Azure Data Lake Storage Gen2t és a bázist használja az adatok hatékony átviteléhez a Spark-fürt és a szinapszis SQL-példány között.

![Összekötő-architektúra](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Hitelesítés az Azure szinapszis Analyticsben

A rendszerek közötti hitelesítés zökkenőmentesen elérhető az Azure szinapszis Analytics szolgáltatásban. Létezik egy jogkivonat-szolgáltatás, amely összekapcsolja a Azure Active Directory-mel a Storage-fiók vagy az adatraktár-kiszolgáló eléréséhez szükséges biztonsági jogkivonatok beszerzéséhez.

Emiatt nem kell hitelesítő adatokat létrehoznia, vagy megadnia azokat az összekötő API-ban, ha a HRE-Auth konfigurálva van a Storage-fiókban és az adatraktár-kiszolgálón. Ha nem, akkor megadható az SQL-hitelesítés. További részleteket a [használati](#usage) szakaszban talál.

## <a name="constraints"></a>Korlátozások

- Ez az összekötő csak a Scalaben működik.

## <a name="prerequisites"></a>Előfeltételek

- **Db_exporter** szerepkör tagjának kell lennie abban az adatbázisban/SQL-készletben, amelybe be kívánja vinni az adatátvitelt.
- A Storage blob adatközreműködői szerepkör tagjának kell lennie az alapértelmezett Storage-fiókban.

Felhasználók létrehozásához kapcsolódjon az SQL-készlet adatbázisához, és kövesse az alábbi példákat:

```sql
--SQL User
CREATE USER Mary FROM LOGIN Mary;

--Azure Active Directory User
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Szerepkör társítása:

```sql
--SQL User
EXEC sp_addrolemember 'db_exporter', 'Mary';

--Azure Active Directory User
EXEC sp_addrolemember 'db_exporter',[mike@contoso.com]
```

## <a name="usage"></a>Használat

Az importálási utasítások nem szükségesek, ezeket a rendszer előre importálja a jegyzetfüzet felhasználói felületén.

### <a name="transferring-data-to-or-from-a-sql-pool-attached-with-the-workspace"></a>Adatok átvitele a munkaterülethez csatolt SQL-készletbe vagy-ból

> [!NOTE]
> **A notebook-élményben nem szükséges importálás**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API olvasása

```scala
val df = spark.read.sqlanalytics("<DBName>.<Schema>.<TableName>")
```

A fenti API a belső (felügyelt) és az SQL-készletben található külső táblák esetében is működik.

#### <a name="write-api"></a>API írása

```scala
df.write.sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

Az írási API létrehozza a táblát az SQL-készletben, majd meghívja a albase-et az adatok betöltéséhez.  A tábla nem létezhet az SQL-készletben, és a rendszer hibaüzenetet küld, amely szerint a "már létezik, és az objektum neve."

TableType-értékek

- Állandók. belső felügyelt tábla az SQL-készletben
- Konstansok. külső külső tábla az SQL-készletben

SQL-készlet által felügyelt tábla

```scala
df.write.sqlanalytics("<DBName>.<Schema>.<TableName>", Constants.INTERNAL)
```

SQL Pool – külső tábla

SQL-készlet külső táblájába való íráshoz egy külső adatforrást és egy külső FÁJLFORMÁTUMot kell létrehozni az SQL-készleten.  További információért olvassa el a [külső adatforrás](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) és az SQL-készlet külső [fájlformátumainak](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) létrehozása című témakört.  Az alábbiakban a külső adatforrás és a külső fájlformátumok SQL-készletben való létrehozására mutatunk példákat.

```sql
--For an external table, you need to pre-create the data source and file format in SQL pool using SQL queries:
CREATE EXTERNAL DATA SOURCE <DataSourceName>
WITH
  ( LOCATION = 'abfss://...' ,
    TYPE = HADOOP
  ) ;

CREATE EXTERNAL FILE FORMAT <FileFormatName>
WITH (  
    FORMAT_TYPE = PARQUET,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);
```

A külső HITELESÍTŐADAT-objektum nem szükséges, ha Azure Active Directory átmenő hitelesítést használ a Storage-fiókhoz.  Győződjön meg arról, hogy a Storage-fiókban a "Storage blob adatközreműködői" szerepkör tagja.

```scala

df.write.
    option(Constants.DATA_SOURCE, <DataSourceName>).
    option(Constants.FILE_FORMAT, <FileFormatName>).
    sqlanalytics("<DBName>.<Schema>.<TableName>", Constants.EXTERNAL)

```

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-outside-the-workspace"></a>Ha a munkaterületen kívüli SQL-készletbe vagy-adatbázisba küldi át az adatátvitelt

> [!NOTE]
> A notebook-élményben nem szükséges importálás

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API olvasása

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API írása

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="using-sql-auth-instead-of-aad"></a>SQL-hitelesítés használata a HRE helyett

#### <a name="read-api"></a>API olvasása

Az összekötő jelenleg nem támogatja a jogkivonat-alapú hitelesítést a munkaterületen kívüli SQL-készletre. Az SQL-hitelesítést kell használnia.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API írása

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="using-the-pyspark-connector"></a>Az PySpark-összekötő használata

> [!NOTE]
> Ez a példa csak a jegyzetfüzettel kapcsolatos, szem előtt tartott felülettel van megadva.

Tegyük fel, hogy rendelkezik egy "pyspark_df" dataframe, amelyet szeretne írni a DW-be.

Hozzon létre egy ideiglenes táblát a PySpark dataframe használatával:

```py
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

A PySpark notebookon a Magics használatával futtasson egy Scala-cellát:

```scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

Hasonlóképpen, az olvasási forgatókönyvben olvassa el az adataikat a Scala használatával, majd írja be egy ideiglenes táblába, és használja a Spark SQL-et a PySpark-ben, hogy lekérdezze a temp táblát egy dataframe.

## <a name="allowing-other-users-to-use-the-azure-synapse-apache-spark-to-synapse-sql-connector-in-your-workspace"></a>Az Azure szinapszis Apache Spark használatának engedélyezése más felhasználók számára a munkaterületen található, az SQL-összekötők számára

A munkaterülethez csatlakoztatott ADLS Gen2 Storage-fiókban tárolnia kell a blob-adattulajdonost, hogy mások ne tudják módosítani a hiányzó engedélyeket. Ellenőrizze, hogy a felhasználó rendelkezik-e hozzáféréssel a munkaterülethez és a jegyzetfüzetek futtatásához szükséges engedélyekhez.

### <a name="option-1"></a>1\. lehetőség

- A felhasználó tárolási blob-adatközreműködőinek/tulajdonosának tétele

### <a name="option-2"></a>2\. lehetőség

- A következő ACL-eket kell megadnia a mappa struktúrájához:

| Mappa | / | szinapszis | munkaterületek  | \<workspacename> | sparkpools | \<sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Hozzáférési engedélyek | --X | --X | --X | --X | --X | --X | – WX |
| Alapértelmezett engedélyek | ---| ---| ---| ---| ---| ---| ---|

- Az összes mappát le kell tudnia adni a "szinapszis" mappából, és a Azure Portal lefelé haladva. A root "/" mappa ACL-jéhez kövesse az alábbi utasításokat.

- Csatlakozás a munkaterülethez csatlakoztatott Storage-fiókhoz Storage Explorer a HRE használatával
- Válassza ki a fiókját, és adja meg a munkaterület ADLS Gen2 URL-címét és alapértelmezett fájlrendszerét
- Ha megjelenik a felsorolt Storage-fiók, kattintson a jobb gombbal a Listázás munkaterületre, és válassza a "hozzáférés kezelése" lehetőséget.
- Adja hozzá a felhasználót a/mappához a "végrehajtás" hozzáférési engedéllyel. Válassza az OK lehetőséget

> [!IMPORTANT]
> Ügyeljen arra, hogy ne válassza az "alapértelmezett" lehetőséget, ha nem kívánja.

## <a name="next-steps"></a>További lépések

- [SQL-készlet létrehozása a Azure Portal használatával](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Új Apache Spark-készlet létrehozása a Azure Portal használatával](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
