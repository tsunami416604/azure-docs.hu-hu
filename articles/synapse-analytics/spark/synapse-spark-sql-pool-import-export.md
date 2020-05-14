---
title: A Spark-készletek (előzetes verzió) és az SQL-készletek közötti adatimportálás és-exportálás
description: Ez a cikk azt ismerteti, hogyan használható az egyéni összekötő az adatoknak az SQL-készletek és a Spark-készletek (előzetes verzió) közötti áthelyezéséhez.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f562c195e90f2356568530b9b618ae9e6610fa56
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201461"
---
# <a name="introduction"></a>Introduction (Bevezetés)

A Spark SQL Analytics-összekötő úgy lett kialakítva, hogy hatékonyan vigyen át adatátvitelt a Spark Pool (előzetes verzió) és az SQL-készletek között az Azure szinapszis A Spark SQL Analytics-összekötő csak az SQL-készleteken működik, az SQL-on igény szerint nem működik.

## <a name="design"></a>Kialakítás

A Spark-készletek és az SQL-készletek közötti adatátvitel a JDBC használatával végezhető el. Azonban a két elosztott rendszer, például a Spark és az SQL-készletek miatt a JDBC általában szűk keresztmetszetet jelent a soros adatátvitel során.

Az SQL Analytics-összekötőhöz készült Spark-készletek a Apache Spark adatforrások általi implementációja. A Azure Data Lake Storage Gen 2, az SQL-készletekben pedig a Base-t használja az adatok hatékony átviteléhez a Spark-fürt és az SQL Analytics-példány között.

![Összekötő-architektúra](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Hitelesítés az Azure szinapszis Analyticsben

A rendszerek közötti hitelesítés zökkenőmentesen elérhető az Azure szinapszis Analytics szolgáltatásban. Létezik egy jogkivonat-szolgáltatás, amely összekapcsolja a Azure Active Directory-mel a Storage-fiók vagy az adatraktár-kiszolgáló eléréséhez szükséges biztonsági jogkivonatok beszerzéséhez. Emiatt nem kell hitelesítő adatokat létrehoznia, vagy megadnia azokat az összekötő API-ban, ha a HRE-Auth konfigurálva van a Storage-fiókban és az adatraktár-kiszolgálón. Ha nem, akkor megadható az SQL-hitelesítés. További részleteket a [használati](#usage) szakaszban talál.

## <a name="constraints"></a>Korlátozások

- Ez az összekötő csak a Scalaben működik.

## <a name="prerequisites"></a>Előfeltételek

- **Db_exporter** szerepkört kell megadnia abban az adatbázisban/SQL-készletben, amelybe be kívánja vinni az adatátvitelt.

Felhasználók létrehozásához kapcsolódjon az adatbázishoz, és kövesse az alábbi példákat:

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Szerepkör társítása:

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Használat

Az importálási utasítások nem szükségesek, ezeket a rendszer előre importálja a jegyzetfüzet felhasználói felületén.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Adatok átvitele a munkaterülethez csatolt logikai kiszolgáló (DW-példány) és egy SQL-készletbe

> [!NOTE]
> **A notebook-élményben nem szükséges importálás**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API olvasása

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

A fenti API a belső (felügyelt) és az SQL-készletben található külső táblák esetében is működik.

#### <a name="write-api"></a>API írása

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

ahol a TableType konstans lehet. belső vagy állandó. külső

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

A Storage és a SQL Server hitelesítése befejeződött

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>Ha a munkaterületen kívüli logikai kiszolgálón található SQL-készletből vagy-adatbázisból továbbít adatátvitelt.

> [!NOTE]
> A notebook-élményben nem szükséges importálás

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API olvasása

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API írása

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>SQL-hitelesítés használata a HRE helyett

#### <a name="read-api"></a>API olvasása

Az összekötő jelenleg nem támogatja a jogkivonat-alapú hitelesítést a munkaterületen kívüli SQL-készletre. SQL-hitelesítést kell használnia.

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API írása

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>Az PySpark-összekötő használata

> [!NOTE]
> Ez a példa csak a jegyzetfüzettel kapcsolatos, szem előtt tartott felülettel van megadva.

Tegyük fel, hogy rendelkezik egy "pyspark_df" dataframe, amelyet szeretne írni a DW-be.

Ideiglenes tábla létrehozása a PySpark dataframe használatával

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Scala-cella futtatása a PySpark notebookon a Magics használatával

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

Hasonlóképpen, az olvasási forgatókönyvben olvassa el az adataikat a Scala használatával, majd írja be egy ideiglenes táblába, és használja a Spark SQL-et a PySpark-ben, hogy lekérdezze a temp táblát egy dataframe.

## <a name="allowing-other-users-to-use-the-dw-connector-in-your-workspace"></a>A DW-összekötő használatának engedélyezése más felhasználók számára a munkaterületen

Mások számára a hiányzó engedélyek módosításához a ADLS Gen2 Storage-fiókban a munkaterülethez csatlakoztatott Storage blob-adattulajdonost kell megadni. Ellenőrizze, hogy a felhasználó rendelkezik-e hozzáféréssel a munkaterülethez és a jegyzetfüzetek futtatásához szükséges engedélyekhez.

### <a name="option-1"></a>1. lehetőség

- A felhasználó tárolási blob-adatközreműködőinek/tulajdonosának tétele

### <a name="option-2"></a>2. lehetőség

- A következő ACL-eket kell megadnia a mappa struktúrájához:

| Mappa | / | szinapszis | munkaterületek  | <workspacename> | sparkpools | <sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Hozzáférési engedélyek |--X |--X |--X |--X |--X |--X |– WX |
| Alapértelmezett engedélyek |---|---|---|---|---|---|---|

- Az összes mappát le kell tudnia adni a "szinapszis" mappából, és a Azure Portal lefelé haladva. A root "/" mappa ACL-jéhez kövesse az alábbi utasításokat.

- Csatlakozás a munkaterülethez csatlakoztatott Storage-fiókhoz Storage Explorer a HRE használatával
- Válassza ki a fiókját, és adja meg a munkaterület ADLS Gen2 URL-címét és alapértelmezett fájlrendszerét
- Ha megjelenik a felsorolt Storage-fiók, kattintson a jobb gombbal a Listázás munkaterületre, és válassza a "hozzáférés kezelése" lehetőséget.
- Adja hozzá a felhasználót a/mappához a "végrehajtás" hozzáférési engedéllyel. Válassza az OK lehetőséget

**Ügyeljen arra, hogy ne válassza az "alapértelmezett" lehetőséget, ha nem kívánja**

## <a name="next-steps"></a>Következő lépések

- [SQL-készlet létrehozása](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Új Apache Spark-készlet létrehozása az Azure szinapszis Analytics-munkaterülethez](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 