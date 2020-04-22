---
title: Tárolófájlok lekérdezése az SQL igény szerinti (előzetes verzió) használatával a Synapse SQL-ben
description: A synapse SQL-en belüli SQL-erőforrások használatával történő tárfájlok lekérdezése.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2126996620d6f891dde4e7530c057d2c7f31a996
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676671"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Tárolófájlok lekérdezése sql-alapú (előzetes verziójú) erőforrások használatával a Synapse SQL-ben

SQL igény szerinti (előzetes verzió) lehetővé teszi az adatok lekérdezését az adattóban. Kínál egy T-SQL lekérdezési felület, amely befogadja a félig strukturált és strukturálatlan adatlekérdezések.

A lekérdezéshez a következő T-SQL szempontok támogatottak:

- Teljes [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) felület, beleértve az SQL függvények, operátorok és így tovább többsége.
- HOZZON LÉTRE KÜLSŐ TÁBLA AS SELECT[(CETAS](develop-tables-cetas.md)) létrehoz egy [külső táblát,](develop-tables-external-tables.md) majd ezzel párhuzamosan exportálja a Transact-SQL SELECT utasítás eredményeit az Azure Storage-ba.

További információ arról, hogy mi vs. mi jelenleg nem támogatott, olvassa el az [SQL igény szerinti áttekintése](on-demand-workspace-overview.md) cikket.

Amikor az Azure AD-felhasználók lekérdezéseket futtatnak, az alapértelmezett az Azure AD áthaladási hitelesítési protokoll használatával elérhető tárfiókok elérése. Így a felhasználók megszemélyesítése és az engedélyek ellenőrzése a tárolási szinten lesz. Igény [szerint szabályozhatja a tárhelyhez való hozzáférést.](develop-storage-files-storage-access-control.md)

## <a name="extensions"></a>Bővítmények

Az Azure Storage-fájlokban található adatok azonnali lekérdezésének zökkenőmentes működéséhez az SQL igény szerinti funkciója az [OPENROWSET](develop-openrowset.md) függvényt további képességekkel használja:

- [Több fájl vagy mappa lekérdezése](#query-multiple-files-or-folders)
- [PARQUET fájlformátum](#parquet-file-format)
- [A tagolt szöveggel való munka további lehetőségei (mezőterminátor, sorlesek, escape karakter)](#additional-options-for-working-with-delimited-text)
- [Oszlopok kiválasztott részhalmazának olvasása](#read-a-chosen-subset-of-columns)
- [Séma-következtetés](#schema-inference)
- [fájlnév függvény](#filename-function)
- [filepath függvény](#filepath-function)
- [Összetett típusokkal és beágyazott vagy ismétlődő adatstruktúrákkal végzett munka](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Több fájl vagy mappa lekérdezése

Ha t-SQL-lekérdezést szeretne futtatni egy mappán vagy mappacsoporton belüli fájlok on keresztül, miközben egyetlen entitásként vagy sorhalmazként kezeli őket, adjon meg egy elérési utat egy mappához vagy egy mintához (helyettesítő karakterek használatával) fájlok vagy mappák egy készlete felett.

A következő szabályok érvényesek:

- A minták megjelenhetnek a könyvtár elérési útjának egy részében vagy egy fájlnévben.
- Ugyanabban a könyvtárlépésben vagy fájlnévben több minta is megjelenhet.
- Ha több helyettesítő karakter van, akkor az összes egyező elérési úton lévő fájlok szerepelni fognak az eredményül kapott fájlkészletben.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

Használati [példákat a Lekérdezésmappák és a több fájl](query-folders-multiple-csv-files.md) hivatkozik.

### <a name="parquet-file-format"></a>PARQUET fájlformátum

A Parketta forrásadatainak lekérdezéséhez használja a FORMAT = 'PARQUET' parancsot

```syntaxsql
OPENROWSET
(
    { BULK 'data_file' ,
    { FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
)
AS table_alias(column_alias,...n)
<bulk_options> ::=
...
[ , FORMAT = {'CSV' | 'PARQUET'} ]
```

Tekintse át a [Lekérdezési parkettafájlok cikket](query-parquet-files.md) használati példákért.

### <a name="additional-options-for-working-with-delimited-text"></a>A tagolt szöveggel való munka további lehetőségei

Ezek a további paraméterek kerülnek bevezetésre a CSV (tagolt szöveg) fájlokkal való munkához:

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = 'char' Megadja a fájlban azt a karaktert, amely a fájl kikerülésére szolgál, és a fájl összes határolóértékét. Ha az escape karaktert nem önmaga vagy a határolóértékek bármelyike követi, az escape karakter elesik az érték olvasásakor.
A ESCAPE_CHAR paraméter attól függetlenül alkalmazásra kerül, hogy a FIELDQUOTE engedélyezve van-e vagy sem. Nem fogja használni, hogy elkerülje az idéző karakter. Az idéző karakter az Excel CSV viselkedésével összhangban dupla idézőjelekkel kerül ki.
- FIELDTERMINATOR ='field_terminator' A használandó mezőterminátort adja meg. Az alapértelmezett mezőterminátor vessző ("**,**")
- ROWTERMINATOR ='row_terminator' A használandó sorterminátort adja meg. Az alapértelmezett sorterminátor egy új sorkarakter: **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Oszlopok kiválasztott részhalmazának olvasása

Az elolvasni kívánt oszlopok megadásához az OPENROWSET utasításban megadhat egy választható WITH záradékot.

- Ha vannak CSV-adatfájlok, az összes oszlop olvasásához adja meg az oszlopneveket és azok adattípusait. Ha az oszlopok egy részét szeretné használni, sorszámokkal válassza ki az oszlopokat az eredeti adatfájlokból sorszám szerint. Az oszlopokat a sormegjelölés köti.
- Ha vannak Parketta adatfájlok, adja meg azokat az oszlopneveket, amelyek megfelelnek az eredeti adatfájlok oszlopnevének. Az oszlopokat név köti.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

Mintákat a [CSV-fájlok olvasása az összes oszlop megadása nélkül](query-single-csv-file.md#returning-subset-of-columns)olvassa el.

### <a name="schema-inference"></a>Séma-következtetés

Ha kihagyja a WITH záradékot az OPENROWSET utasításból, utasíthatja a szolgáltatást, hogy automatikusan észlelje (következtetje ki) a sémát az alapul szolgáló fájlokból.

> [!NOTE]
> Ez jelenleg csak parquet fájlformátum esetén működik.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

### <a name="filename-function"></a>Fájlnév függvény

Ez a függvény azt a fájlnevet adja vissza, amelyből a sor származik.

Adott fájlok lekérdezéséhez olvassa el a Fájlnév szakaszt a [Query adott fájlokról](query-specific-files.md#filename) szóló cikkben.

### <a name="filepath-function"></a>Filepath függvény

Ez a függvény teljes elérési utat vagy az elérési út egy részét adja vissza:

- Paraméter nélkül történő hívás esetén a sor teljes fájlelérési útját adja vissza.
- Ha paraméterrel hívják meg, az elérési út egy részét adja vissza, amely megegyezik a paraméterben megadott beosztásban lévő helyettesítő karakterrel. Az 1-es paraméterérték például az első helyettesítő karakternek megfelelő elérési út egy részét adja vissza.

További információt a [Query adott fájlokról](query-specific-files.md#filepath) szóló cikk Filepath szakaszában talál.

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Összetett típusokkal és beágyazott vagy ismétlődő adatstruktúrákkal végzett munka

A beágyazott vagy ismétlődő adattípusokban , például [a Parquet-fájlokban](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) tárolt adatokkal való munka zökkenőmentes működéséhez az SQL igény szerinti bővítményei hozzáadták az alábbi bővítményeket.

#### <a name="project-nested-or-repeated-data"></a>Projekt beágyazott vagy ismétlődő adatai

Adatok kivetítéséhez futtasson SELECT utasítást a beágyazott adattípusok oszlopait tartalmazó Parketta fájlon. Kimenetkor a beágyazott értékek szerializálódnak a JSON-ba, és varchar(8000) SQL-adattípusként jelennek meg.

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

További információt a [Lekérdezési parketta beágyazott típusok](query-parquet-nested-types.md#project-nested-or-repeated-data) című cikk Project beágyazott vagy ismétlődő részében talál.

#### <a name="access-elements-from-nested-columns"></a>Elemek elérése beágyazott oszlopokból

A beágyazott oszlopok, például a Struct beágyazott elemeinek eléréséhez használja a "dot notation" kifejezést a mezőnevek nek az elérési útba való összefűzéséhez. Adja meg az elérési utat az OPENROWSET függvény WITH záradékában column_name.

A szintaktikai töredék példája a következő:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Alapértelmezés szerint az OPENROWSET függvény a forrásmező nevét és elérési útját a WITH záradékban megadott oszlopnevekkel egyezteti. Az ugyanazon forrásparquet fájlon belül különböző beágyazási szinteken lévő elemek a WITH záradékon keresztül érhetők el.

**Visszaadott értékek**

- A függvény a megadott elemből és a megadott görbéről származó skaláris értéket ad eredményül, például int, decimális és varchar, minden olyan parkettatípusra, amely nem szerepel a Beágyazott típus csoportban.
- Ha a görbe beágyazott típusú elemre mutat, a függvény egy JSON-töredéket ad vissza, amely a megadott görbe legfelső elemétől indul. A JSON-töredék varchar(8000) típusú.
- Ha a tulajdonság nem található a megadott column_name, a függvény hibát ad vissza.
- Ha a tulajdonság nem található a megadott column_path, a [Path módtól](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)függően a függvény hibát ad vissza, ha szigorú módban van, vagy null módban, ha laza módban van.

Lekérdezésminták esetén tekintse át a [Query Parquet beágyazott típusok](query-parquet-nested-types.md#access-elements-from-nested-columns) cikkbeágyazott oszlopokból származó Access-elemeket.

#### <a name="access-elements-from-repeated-columns"></a>Elemek elérése ismétlődő oszlopokból

Az ismétlődő oszlopok elemeinek, például egy tömb vagy térkép elemeinek eléréséhez használja a [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) függvényt minden kivetendő és a következő höz:

- Beágyazott vagy ismétlődő oszlop, mint az első paraméter
- [Egy JSON elérési út,](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) amely második paraméterként megadja a hozzáféréshez használt elemet vagy tulajdonságot

Ha nem skaláris elemeket szeretne elérni egy ismétlődő oszlopból, használja a [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) függvényt minden olyan nem skaláris elemhez, amelyet ki kell vetíteni, és a következőket biztosítja:

- Beágyazott vagy ismétlődő oszlop, mint az első paraméter
- [Egy JSON elérési út,](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) amely második paraméterként megadja a hozzáféréshez használt elemet vagy tulajdonságot

Lásd az alábbi szintaxistöredéket:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

A [Query Parquet beágyazott típusok](query-parquet-nested-types.md#access-elements-from-repeated-columns) cikkben lekérdezésmintákat találhat az ismétlődő oszlopok elemeinek eléréséhez.

## <a name="next-steps"></a>További lépések

A különböző fájltípusok lekérdezéséről, valamint a nézetek létrehozásáról és használatáról az alábbi cikkekben talál további információt:

- [Egyetlen CSV-fájl lekérdezése](query-single-csv-file.md)
- [Parquet-fájlok lekérdezése](query-parquet-files.md)
- [JSON-fájlok lekérdezése](query-json-files.md)
- [A Parquet beágyazott típusainak lekérdezése](query-parquet-nested-types.md)
- [Mappák és több CSV-fájl lekérdezése](query-folders-multiple-csv-files.md)
- [Fájlmetaadatok használata lekérdezésekben](query-specific-files.md)
- [Nézetek létrehozása és használata](create-use-views.md)
