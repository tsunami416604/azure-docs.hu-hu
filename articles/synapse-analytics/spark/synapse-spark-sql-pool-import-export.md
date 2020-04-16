---
title: Adatok importálása és exportálása Spark-készletek (előzetes verzió) és SQL-készletek között
description: Ez a cikk arról nyújt tájékoztatást, hogyan használhatja az egyéni összekötőt az SQL-készletek és a Spark-készletek közötti adatok oda-vissza mozgatására (előzetes verzió).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424292"
---
# <a name="introduction"></a>Introduction (Bevezetés)

A Spark SQL Analytics-összekötő célja, hogy hatékonyan továbbítsa az adatokat a Spark-készlet (előzetes verzió) és az SQL-készletek között az Azure Synapse-ban. A Spark SQL Analytics-összekötő csak SQL-készleteken működik, nem működik az SQL on-Demand.

## <a name="design"></a>Tervezés

Az adatok átvitele a Spark-készletek és az SQL-készletek között a JDBC használatával végezhető el. Azonban mivel két elosztott rendszerek, mint a Spark és az SQL-készletek, JDBC általában szűk keresztmetszetet a soros adatátvitel.

A Spark-készletek az SQL Analytics-összekötő egy adatforrás-megvalósításaz Apache Spark. Az Azure Data Lake Storage Gen 2 és az SQL-készletekben lévő Polybase segítségével hatékonyan továbbítja az adatokat a Spark-fürt és az SQL Analytics-példány között.

![Összekötő architektúrája](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Hitelesítés az Azure Synapse Analytics szolgáltatásban

A rendszerek közötti hitelesítés zökkenőmentes az Azure Synapse Analytics szolgáltatásban. Van egy token szolgáltatás, amely csatlakozik az Azure Active Directoryhoz a tárfiók vagy az adattárház-kiszolgáló elérésekor használható biztonsági jogkivonatok beszerzéséhez. Emiatt nincs szükség hitelesítő adatok létrehozására vagy adja meg őket az összekötő API-ban, amíg a Tárfiók és az adattárház-kiszolgáló AAD-Auth konfigurálva van. Ha nem, az SQL Auth megadható. További részleteket a [Használat](#usage) szakaszban talál.

## <a name="constraints"></a>Korlátozások

- Ez az összekötő csak a Scala esetén működik.

## <a name="prerequisites"></a>Előfeltételek

- Legyen **db_exporter** szerepköre abban az adatbázisban/SQL-készletben, amelyről adatokat szeretne átvinni.

Felhasználók létrehozásához csatlakozzon az adatbázishoz, és kövesse az alábbi példákat:

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Szerepkör hozzárendelése:

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Használat

Az importálási utasítások nem kell megadni, azokat előre importálják a notebook-élmény.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Adatok átvitele a munkaterülettel csatolt logikai kiszolgáló (DW-példány) SQL-készletére vagy onnan

> [!NOTE]
> **Nem szükséges az importálás a jegyzetfüzet-élményben**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API olvasása

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

A fenti API-t belső (felügyelt) és külső táblák az SQL-készletben.

#### <a name="write-api"></a>API írása

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

ahol a TableType lehet Constants.INTERNAL vagy Constants.EXTERNAL

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

A storage és az SQL Server hitelesítése befejeződött

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>Ha adatokat továbbít egy logikai kiszolgálón lévő SQL-készletbe vagy adatbázisba a munkaterületen kívül

> [!NOTE]
> Nem szükséges az importálás a jegyzetfüzet-élményben

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

### <a name="using-sql-auth-instead-of-aad"></a>SQL Auth használata az AAD helyett

#### <a name="read-api"></a>API olvasása

Jelenleg az összekötő nem támogatja a jogkivonat-alapú hitelesítési egy SQL-készlet, amely kívül esik a munkaterületen. Az SQL Auth-ot kell használnia.

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

### <a name="using-the-pyspark-connector"></a>A PySpark-összekötő használata

> [!NOTE]
> Ez a példa csak a jegyzetfüzet-élményt tartja szem előtt tartva.

Tegyük fel, hogy van egy dataframe "pyspark_df", hogy szeretne írni a DW.

Ideiglenes tábla létrehozása a PySpark adatkeretének használatával

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Scala-cella futtatása a PySpark-noteszgépben varázslatokkal

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
Hasonlóképpen az olvasási forgatókönyvben olvassa be az adatokat a Scala használatával, és írja be egy ideiglenes táblába, és a Spark SQL a PySpark használatával lekérdezi az ideiglenes táblát egy adatkeretbe.

## <a name="next-steps"></a>További lépések

- [SQL-készlet létrehozása]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Hozzon létre egy új Apache Spark-készletet egy Azure Synapse Analytics-munkaterülethez](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 