---
title: Megosztott metaadatok táblái
description: Az Azure szinapszis Analytics olyan megosztott metaadat-modellt biztosít, amelyben egy tábla létrehozása Apache Spark az adatok duplikálása nélkül elérhetővé válik az SQL on-demand (előzetes verzió) és az SQL Pool Engine szolgáltatásból.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 28f666fe295b2b49fb6795306e9fad489c867517
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387217"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Az Azure szinapszis Analytics megosztott metaadatait tartalmazó táblái

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Az Azure szinapszis Analytics lehetővé teszi, hogy a különböző munkaterület-számítási motorok megosszák az adatbázisokat és a parketta által támogatott táblákat a Apache Spark készletek (előzetes verzió) és az SQL on-demand (előzetes verzió) motorja között.

Miután létrehozta az adatbázist egy Spark-feladatokkal, a Sparkban létrehozhat olyan táblákat, amelyek tárolási formátumként használják a parketta-t. Ezek a táblák azonnal elérhetővé válnak az Azure szinapszis-munkaterület Spark-készletei bármelyikének lekérdezéséhez. Ezek bármelyik Spark-feladatból is felhasználhatók, az engedélyek alá tartoznak.

A Spark által létrehozott, felügyelt és külső táblákat külső táblákként is elérhetővé teszi a megfelelő szinkronizált adatbázisban az SQL igény szerint. Az [SQL-ben a Spark-táblázat](#expose-a-spark-table-in-sql) további részleteket tartalmaz a tábla szinkronizálásával kapcsolatban.

Mivel a táblákat aszinkron módon szinkronizálják az SQL-be, a rendszer késést okoz, amíg meg nem jelenik.

## <a name="manage-a-spark-created-table"></a>Spark-létrehozott tábla kezelése

A Spark segítségével kezelheti a Spark által létrehozott adatbázisokat. Például törölheti azt egy Spark-készlet feladatokon keresztül, és létrehozhat táblákat a Sparkból.

Ha ilyen adatbázisban hoz létre objektumokat az SQL on-demand vagy az adatbázis eldobására, akkor a művelet sikeres lesz, de az eredeti Spark-adatbázis nem módosul.

## <a name="expose-a-spark-table-in-sql"></a>Spark-táblázat közzététele az SQL-ben

### <a name="shared-spark-tables"></a>Megosztott Spark-táblák

A Spark kétféle táblázatot biztosít az Azure szinapszisok számára az SQL automatikus elérhetővé tétele érdekében:

- Felügyelt táblák

  A Spark számos lehetőséget biztosít a felügyelt táblákban tárolt adattároláshoz, például szöveg, CSV, JSON, JDBC, Parque, ork, kaptár, DELTA és LIBSVM. Ezeket a fájlokat általában abban a könyvtárban tárolja a rendszer, `warehouse` ahol a felügyelt tábla tárolta az adattárolót.

- Külső táblák

  A Spark lehetővé teszi, hogy külső táblákat is hozzon létre a meglévő adatain keresztül, akár a beállítás megadásával, akár `LOCATION` a kaptár formátumának használatával. Az ilyen külső táblák különböző adatformátumok, például a parketta használatával is lehetnek.

Az Azure szinapszis jelenleg csak azokat a felügyelt és külső Spark-táblákat osztja meg, amelyek az SQL-motorokkal együtt tárolják az adattárakat. A más formátumok által támogatott táblákat a rendszer nem szinkronizálja automatikusan. Előfordulhat, hogy ezeket a táblákat saját SQL-adatbázisában lévő külső táblázatként is szinkronizálja, ha az SQL-motor támogatja a tábla alapjául szolgáló formátumot.

### <a name="share-spark-tables"></a>Spark-táblázatok megosztása

Az SQL-motorban az alábbi tulajdonságokkal rendelkező, megosztható felügyelt és külső Spark-táblák külső táblákként jelennek meg:

- Az SQL külső tábla adatforrása a Spark-tábla Location mappáját jelképező adatforrás.
- Az SQL külső táblázat fájlformátuma a parketta.
- Az SQL külső tábla hozzáférési hitelesítő adatai továbbítva vannak.

Mivel az összes Spark-táblázat neve érvényes SQL-táblanév, és az összes Spark-oszlop neve érvényes SQL-oszlopnevek, a Spark-tábla és az oszlopnevek az SQL külső táblához lesznek használva.

A Spark-táblázatok különböző adattípusokat biztosítanak, mint a szinapszis SQL-hajtóművek. A következő táblázat a Spark Table-adattípusokat képezi le az SQL-típusokhoz:

| Spark adattípus | SQL-adattípus | Megjegyzések |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | Rendezéssel`Latin1_General_CP1_CI_AS_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Szerializálás a JSON-ba rendezéssel`Latin1_General_CP1_CI_AS_UTF8` |
| `map`       |    `varchar(max)`   | Szerializálás a JSON-ba rendezéssel`Latin1_General_CP1_CI_AS_UTF8` |
| `struct`    |    `varchar(max)`   | Szerializálás a JSON-ba rendezéssel`Latin1_General_CP1_CI_AS_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Biztonsági modell

A Spark-adatbázisok és-táblák, valamint az SQL Engine-ben szinkronizált ábrázolások a mögöttes tárolási szinten lesznek biztosítva. Mivel jelenleg nem rendelkeznek engedélyekkel az objektumokhoz, az objektumok az Object Explorerben láthatók.

A felügyelt táblát létrehozó rendszerbiztonsági tag a tábla tulajdonosa, és rendelkezik a táblához, valamint a mögöttes mappákhoz és fájlokhoz szükséges jogokkal. Emellett az adatbázis tulajdonosa automatikusan a tábla társtulajdonosa lesz.

Ha olyan Spark-vagy SQL-alapú külső táblázatot hoz létre, amelynek a hitelesítése átmenő, az adatai csak a mappák és a fájlok szintjén biztonságosak. Ha valaki lekérdezi az ilyen típusú külső táblákat, a rendszer a lekéréses küldő biztonsági identitását a fájlrendszerre továbbítja, amely a hozzáférési jogosultságokat fogja megkeresni.

A mappákra és fájlokra vonatkozó engedélyek beállításával kapcsolatos további információkért lásd: az [Azure szinapszis Analytics megosztott adatbázisa](database.md).

## <a name="examples"></a>Példák

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>Hozzon létre egy felügyelt táblázatot a parketta használatával a Sparkban, és kérdezze le az SQL igény szerinti lekérdezését

Ebben az esetben egy nevű Spark-adatbázissal rendelkezik `mytestdb` . Lásd: [Létrehozás és kapcsolódás egy Spark-adatbázishoz SQL-igény szerint](database.md#create-and-connect-to-spark-database-with-sql-on-demand).

Hozzon létre egy felügyelt Spark-táblázatot a SparkSQL a következő parancs futtatásával:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

Ez létrehozza a táblát `myParquetTable` az adatbázisban `mytestdb` . Rövid késleltetés után megtekintheti a táblázatot az SQL on-demand használatával. Futtassa például az alábbi utasítást az SQL igény szerint.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Ellenőrizze, hogy `myParquetTable` szerepel-e az eredmények között.

>[!NOTE]
>Nem lesz szinkronizálva olyan tábla, amely nem használ parkettát, mert a tárolási formátuma nem.

Ezután szúrjon be néhány értéket a Sparkból a táblába, például a következő C# Spark-utasításokkal egy C# jegyzetfüzetben:

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myParquetTable");
```

Az SQL igény szerinti adatait az alábbi módon olvashatja:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

A következő sort kell beolvasnia az eredményként:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>Külső tábla létrehozása a Sparkban, és az SQL igény szerinti lekérdezése

Ebben a példában hozzon létre egy külső Spark-táblázatot a kezelt tábla előző példájában létrehozott parketta-adatfájlok felett.

Például a SparkSQL futtatásával:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

A helyőrzőt cserélje le `<fs>` a fájlrendszer nevére, amely a munkaterület alapértelmezett fájlrendszere, a helyőrző pedig annak a `<synapse_ws>` szinapszis-munkaterületnek a neve, amelyet a példa futtatásához használ.

Az előző példa létrehozza a táblát `myExtneralParquetTable` az adatbázisban `mytestdb` . Rövid késleltetés után megtekintheti a táblázatot az SQL on-demand használatával. Futtassa például az alábbi utasítást az SQL igény szerint.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Ellenőrizze, hogy `myExternalParquetTable` szerepel-e az eredmények között.

Az SQL igény szerinti adatait az alábbi módon olvashatja:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

A következő sort kell beolvasnia az eredményként:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>Következő lépések

- [További információ az Azure szinapszis Analytics megosztott metaadatairól](overview.md)
- [További információ az Azure szinapszis Analytics megosztott metaadat-adatbázisáról](database.md)


