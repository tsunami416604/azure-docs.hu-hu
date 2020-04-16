---
title: Az Azure Synapse Analytics megosztott metaadat-táblái
description: Az Azure Synapse Analytics egy megosztott metaadat-modellt biztosít, ahol egy tábla létrehozása az Apache Sparkban elérhetővé teszi az SQL on-demand (előzetes verzió) és az SQL-készletmotorok között az adatok megkettőzése nélkül.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 7c1951c772dcd2f49f4f7c09021f69193af0a87e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424579"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Az Azure Synapse Analytics megosztott metaadat-táblái

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Az Azure Synapse Analytics lehetővé teszi, hogy a különböző munkaterületi számítási motorok adatbázisokat és parketta-támogatott táblákat osszanak meg az Apache Spark-készletek (előzetes verzió), az SQL on-demand (előzetes verzió) motor és az SQL-készletek között.

Miután egy Spark-feladat létrehozott egy adatbázist, táblákat hozhat létre benne a Spark használatával, amelyek a Parkettát használják tárolási formátumként. Ezek a táblák azonnal elérhetővé válnak az Azure Synapse-munkaterület Spark-készletek bármelyikének lekérdezéséhez. A Spark-feladatok bármelyikéből is használhatók engedélyekhez kötötten.

A Spark létrehozott, felügyelt és külső táblák is elérhetővé külső táblák azonos nevű a megfelelő szinkronizált adatbázis `$`SQL on-demand és a megfelelő -előtagsémák az SQL-készletek, amelyek a metaadat-szinkronizálás engedélyezve van. [A Spark-tábla SQL-ben való felfedése](#exposing-a-spark-table-in-sql) részletesebb en a tábla szinkronizálása.

Mivel a táblák az igény szerinti SQL-fájllal vannak szinkronizálva, és az SQL-készletek aszinkron módon szinkronizálódnak, késleltetni fogják, amíg meg nem jelennek.

Táblák hozzárendelése külső táblákhoz, adatforrásokhoz és fájlformátumokhoz.

## <a name="manage-a-spark-created-table"></a>Spark által létrehozott tábla kezelése

A Spark segítségével kezelheti a Spark által létrehozott adatbázisokat. Például törölje egy Spark-készlet feladat, és hozzon létre táblákat a Sparkból.

Ha ilyen adatbázisban objektumokat hoz létre az SQL igény szerinti rendszerből, vagy megpróbálja eldobni az adatbázist, a művelet sikeres lesz, de az eredeti Spark-adatbázis nem módosul.

Ha megpróbálja eldobni a szinkronizált sémát egy SQL-készletbe, vagy megpróbál létrehozni benne egy táblát, az Azure hibát ad vissza.

## <a name="exposing-a-spark-table-in-sql"></a>Spark-tábla felfedése sql-ben

### <a name="which-spark-tables-are-shared"></a>Mely Spark-táblák vannak megosztva

A Spark kétféle táblát biztosít, amelyeket az Azure Synapse automatikusan elérhetővé tetszetik az SQL-ben:

- Felügyelt táblák

  A Spark számos lehetőséget kínál az adatok felügyelt táblákban való tárolására, például szöveg, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA és LIBSVM. Ezek a fájlok általában `warehouse` abban a könyvtárban tárolódnak, ahol a felügyelt táblaadatokat tárolják.

- Külső táblák

  A Spark külső táblákat is létrehozhat a meglévő `LOCATION` adatokhoz, akár a beállítás megadásával, akár a Hive formátum használatával. Az ilyen külső táblák többféle adatformátumon is áteshetnek, beleértve a Parquet-et is.

Az Azure Synapse jelenleg csak a felügyelt és a külső Spark-táblákat osztja meg, amelyek parketta formátumban tárolják az adataikat az SQL-motorokkal. A más formátumokkal támogatott táblázatok szinkronizálása nem történik meg automatikusan. Előfordulhat, hogy az ilyen táblákat explicit módon szinkronizálhatja külső táblaként a saját SQL-adatbázisában, ha az SQL motor támogatja a tábla alapjául szolgáló formátumot.

### <a name="how-are-spark-tables-shared"></a>Hogyan osztják meg a Spark-táblákat?

Az SQL-motorokban külső táblákként elérhető, megosztható megosztott felügyelt és külső Spark-táblák a következő tulajdonságokkal:

- Az SQL külső tábla adatforrása a Spark-tábla helymappáját képviselő adatforrás.
- Az SQL külső tábla fájlformátuma Parketta.
- Az SQL külső tábla hozzáférési hitelesítő adatai áthaladási.

Mivel az összes Spark-táblanév érvényes SQL-táblanév, és az összes Spark oszlopnév érvényes SQL oszlopnév, a Spark-tábla és az oszlopnevek az SQL külső táblához lesznek használva.

A Spark-táblák a Szinapszis SQL-motoroktól eltérő adattípusokat biztosítanak. Az alábbi táblázat leképezi a Spark-tábla adattípusait az SQL-típusokhoz:

| Szikra-adattípus | SQL-adattípus | Megjegyzések |
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
| `string`    |    `varchar(max)`   | A rendezés`Latin1_General_CP1_CI_AS_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Szerializálja a JSON-t rendezéssel`Latin1_General_CP1_CI_AS_UTF8` |
| `map`       |    `varchar(max)`   | Szerializálja a JSON-t rendezéssel`Latin1_General_CP1_CI_AS_UTF8` |
| `struct`    |    `varchar(max)`   | Szerializálja a JSON-t rendezéssel`Latin1_General_CP1_CI_AS_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Biztonsági modell

A Spark-adatbázisok és táblák, valamint azok szinkronizált ábrázolásaaz SQL-motorok lesznek biztosítva az alapul szolgáló tárolási szinten. Mivel jelenleg nem rendelkeznek engedéllyel magukhoz az objektumokhoz, az objektumok az objektumkezelőben láthatók.

A felügyelt táblát létrehozó rendszerbiztonsági tag a tábla tulajdonosának minősül, és rendelkezik a táblához, valamint az alapul szolgáló mappákhoz és fájlokhoz fűződő összes jogosultsággal. Ezenkívül az adatbázis tulajdonosa automatikusan társtulajdonosa lesz a táblának.

Ha létrehoz egy Spark vagy SQL külső táblát hitelesítési áthárítással, az adatok csak a mappa- és fájlszinteken lesznek biztosítva. Ha valaki lekérdezi az ilyen típusú külső táblát, a lekérdezésbeküldő biztonsági identitása átkerül a fájlrendszerbe, amely ellenőrzi a hozzáférési jogokat.

A mappákra és fájlokra vonatkozó engedélyek beállításáról az [Azure Synapse Analytics megosztott adatbázisában](database.md)talál további információt.

## <a name="examples"></a>Példák

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>Parketta a Sparkban és lekérdezés létrehozása az SQL-ből igény szerint

Ebben az esetben egy Spark-adatbázis neve `mytestdb`van. Lásd: [& csatlakozás a Spark-adatbázishoz – SQL on-demand](database.md#create--connect-to-spark-database---sql-on-demand).

Hozzon létre egy felügyelt Spark-táblát a SparkSQL-el a következő parancs futtatásával:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

Ezzel létrehozza `myParquetTable` a táblát `mytestdb`az adatbázisban. Rövid késleltetés után a tábla igény szerinti SQL-ben jelenik meg. Például futtassa a következő utasítást az SQL igény szerinti.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Ellenőrizze, `myParquetTable` hogy szerepel-e az eredményekben.

>[!NOTE]
>Azok a táblázatok, amelyek nem a Parkettát használják tárolási formátumként, nem lesznek szinkronizálva.

Ezután helyezzen be néhány értéket a sparkból a táblába, például a következő C# Spark-utasításokkal egy C# jegyzetfüzetben:

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

Most már az SQL igény szerinti adatait a következőképpen olvashatja el:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

Ennek eredményeképpen a következő sort kell kapnia:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="creating-an-external-table-backed-by-parquet-in-spark-and-querying-it-from-sql-on-demand"></a>Külső tábla létrehozása a Spark parquet-jével, és lekérdezés az SQL-ből igény szerint

Ebben a példában hozzon létre egy külső Spark-táblát a Parketta adatfájlok, amelyek létreadták az előző példában a felügyelt tábla.

Például a SparkSQL futtatása esetén:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Cserélje le `<fs>` a helyőrzőt a munkaterület alapértelmezett fájlrendszerének nevét, a helyőrzőt `<synapse_ws>` pedig a példa futtatásához használt szinapszáz munkaterület nevére.

Az előző példa `myExtneralParquetTable` létrehozza a `mytestdb`táblát az adatbázisban. Rövid késleltetés után a tábla igény szerinti SQL-ben jelenik meg. Például futtassa a következő utasítást az SQL igény szerinti.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Ellenőrizze, `myExternalParquetTable` hogy szerepel-e az eredményekben.

Most már az SQL igény szerinti adatait a következőképpen olvashatja el:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

Ennek eredményeképpen a következő sort kell kapnia:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="querying-spark-tables-in-a-sql-pool"></a>Spark-táblák lekérdezése SQL-készletben

Az előző példákban létrehozott táblákkal most hozzon létre `mysqlpool` egy SQL-készletet a munkaterületen, amely lehetővé teszi a metaadatok szinkronizálását (vagy használja a már létrehozott készletet [a Spark-adatbázis SQL-készletben való felfedéséből.](database.md#exposing-a-spark-database-in-a-sql-pool)

Futtassa a `mysqlpool` következő utasítást az SQL-készleten:

```sql
SELECT * FROM sys.tables;
```

Ellenőrizze, hogy `myParquetTable` `myExternalParquetTable` a táblák láthatók-e a `$mytestdb`sémában.

Most már az SQL igény szerinti adatait a következőképpen olvashatja el:

```sql
SELECT * FROM [$mytestdb].myParquetTable WHERE name = 'Alice';
SELECT * FROM [$mytestdb].myExternalParquetTable WHERE name = 'Alice';
```

Ugyanazt az eredményt kell kapnia, mint a fenti SQL-ek esetén.

## <a name="next-steps"></a>További lépések

- [További információ az Azure Synapse Analytics megosztott metaadatairól](overview.md)
- [További információ az Azure Synapse Analytics megosztott metaadat-táblázatairól](table.md)


