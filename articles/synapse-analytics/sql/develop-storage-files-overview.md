---
title: Tárolási fájlok lekérdezése az SQL on-demand (előzetes verzió) használatával a szinapszis SQL-en belül
description: Leírja a tárolási fájlok lekérdezését az SQL on-demand (előzetes verzió) típusú, a szinapszis SQLon belüli erőforrásainak használatával.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2126996620d6f891dde4e7530c057d2c7f31a996
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81676671"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Storage-fájlok lekérdezése az SQL on-demand (előzetes verzió) erőforrásain belül a szinapszis SQL-ben

Az SQL on-demand (előzetes verzió) segítségével lekérdezheti az adatait az adattóban. Egy T-SQL-lekérdezési felületet kínál, amely a félig strukturált és a strukturálatlan adatlekérdezéseket is tartalmazza.

A lekérdezéshez a következő T-SQL-szempontok támogatottak:

- Teljes [kijelölési](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) felület, beleértve az SQL-függvények többségét, a kezelőket stb.
- KÜLSŐ tábla létrehozása SELECT ([CETAS](develop-tables-cetas.md)) – létrehoz egy [külső táblát](develop-tables-external-tables.md) , majd párhuzamosan exportálja a Transact-SQL SELECT utasítás eredményeit az Azure Storage-ba.

Ha többet szeretne megtudni arról, hogy mi is a jelenleg nem támogatott, olvassa el az [SQL igény szerinti áttekintését](on-demand-workspace-overview.md) ismertető cikket.

Amikor az Azure AD-felhasználók lekérdezéseket futtatnak, az alapértelmezett érték a Storage-fiókok eléréséhez az Azure AD átmenő hitelesítési protokoll használatával. Ennek megfelelően a rendszer megszemélyesíti a felhasználókat, és a tárolási szinten ellenőrzi az engedélyeket. Az igényeinek megfelelően [szabályozhatja a tárterület-hozzáférést](develop-storage-files-storage-access-control.md) .

## <a name="extensions"></a>Bővítmények

Az Azure Storage-fájlokban található adatlekérdezés zökkenőmentes működésének támogatásához az SQL on-demand a [OpenRowset](develop-openrowset.md) függvényt használja további képességekkel:

- [Több fájl vagy mappa lekérdezése](#query-multiple-files-or-folders)
- [PARKETTA fájlformátum](#parquet-file-format)
- [A tagolt szöveg (mező lezáró, sor lezáró, escape-karakter) használatának további lehetőségei](#additional-options-for-working-with-delimited-text)
- [Oszlopok kiválasztott részhalmazának olvasása](#read-a-chosen-subset-of-columns)
- [Séma-következtetés](#schema-inference)
- [filename függvény](#filename-function)
- [filepath függvény](#filepath-function)
- [Összetett típusok és beágyazott vagy ismétlődő adatstruktúrák használata](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Több fájl vagy mappa lekérdezése

Ha T-SQL-lekérdezést szeretne futtatni egy mappa vagy mappák készletén belül, miközben egyetlen entitásként vagy sorhalmazként kezeli őket, adjon meg egy mappát vagy egy mintát (helyettesítő karakterek használatával) egy fájlon vagy mappán keresztül.

A következő szabályok érvényesek:

- A minták a könyvtár elérési útjának vagy a fájlnevek egy részében is megjelenhetnek.
- Több minta is szerepelhet ugyanabban a címtárbeli lépésben vagy fájlnévben.
- Ha több helyettesítő karakter is van, akkor az összes egyező elérési úton található fájlok szerepelni fognak az eredményül kapott fájlban.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

A használati példákat a [lekérdezési mappákban és több fájlban](query-folders-multiple-csv-files.md) találja.

### <a name="parquet-file-format"></a>PARKETTA fájlformátum

A parketta-forrásadatok lekérdezéséhez használja a FORMAT = "PARQUEt"

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

A használati példákért tekintse át a [lekérdezési parketta fájljait](query-parquet-files.md) ismertető cikket.

### <a name="additional-options-for-working-with-delimited-text"></a>A tagolt szöveg használatának további lehetőségei

Ezek a további paraméterek a CSV (tagolt szöveg) fájlok használatához szükségesek:

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- A ESCAPE_CHAR = "char" a fájlban szereplő karaktert határozza meg, amely a fájlban lévő összes elválasztó érték kiszökésére szolgál. Ha a Escape-karaktert a saját maga vagy az elválasztó értékek egyike követi, az escape-karakter el lesz dobva az érték beolvasása során.
A ESCAPE_CHAR paraméter attól függetlenül lesz alkalmazva, hogy a FIELDQUOTE vagy nincs-e engedélyezve. A rendszer nem használja fel az idézett karakter kiszökésére. Az idézőjeles karaktert dupla idézőjelek között kell megszökni az Excel CSV-viselkedéssel való igazításhoz.
- A FIELDTERMINATOR = ' field_terminator ' meghatározza a használni kívánt lezáró mezőt. Az alapértelmezett lezáró mező egy vessző ("**,**")
- A ROWTERMINATOR = ' row_terminator ' megadja a használandó sort. Az alapértelmezett sor lezáró egy sortörési karakter: **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Oszlopok kiválasztott részhalmazának olvasása

Az olvasni kívánt oszlopok megadásához a OPENROWSET utasításban megadhat egy opcionális záradékot.

- Ha CSV-adatfájlok vannak, az összes oszlop olvasásához adja meg az oszlopnevek és az adattípusok nevét. Ha az oszlopok egy részhalmazát szeretné használni, a sorszámok használatával válassza ki az oszlopokat a származó adatfájlokból a sorszám alapján. Az oszlopokat a sorszám megjelölése fogja kötni.
- Ha vannak parketta-adatfájlok, adjon meg olyan oszlopnevek, amelyek egyeznek a kezdeményező adatfájlokban lévő oszlopnevek. Az oszlopok név szerint lesznek kötve.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

Minták esetében tekintse meg a [CSV-fájlok olvasása az összes oszlop megadása nélkül](query-single-csv-file.md#returning-subset-of-columns)című témakört.

### <a name="schema-inference"></a>Séma-következtetés

Ha kihagyja a WITH záradékot a OPENROWSET utasításból, utasíthatja a szolgáltatást, hogy automatikusan érzékelje (következteti) a sémát az alapul szolgáló fájlokból.

> [!NOTE]
> Ez jelenleg csak a PARQUEt fájlformátum esetében működik.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

### <a name="filename-function"></a>Filename függvény

Ez a függvény annak a fájlnak a nevét adja vissza, amelyből a sor származik.

Adott fájlok lekérdezéséhez olvassa el a fájl- [specifikus fájlok lekérdezése](query-specific-files.md#filename) című cikket.

### <a name="filepath-function"></a>Filepath függvény

Ez a függvény teljes elérési utat vagy az elérési út egy részét adja vissza:

- Ha paraméter nélkül hívja meg, a a fájl teljes elérési útját adja vissza, amelyből a sor származik.
- Ha paraméterrel hívja meg a metódust, az az elérési út azon részét adja vissza, amely megfelel a paraméterben megadott helyettesítő karakternek. Például az 1. paraméter értéke az elérési út azon részét fogja visszaadni, amely megfelel az első helyettesítő karakternek.

További információért olvassa el az [adott fájlok lekérdezése](query-specific-files.md#filepath) című cikk filepath című szakaszát.

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Összetett típusok és beágyazott vagy ismétlődő adatstruktúrák használata

Ha a beágyazott vagy ismétlődő adattípusokban (például a [parketta](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) -fájlokban) tárolt adatfeldolgozás során zavartalan működést szeretne végezni, az SQL on-demand az alábbi bővítményeket adta hozzá.

#### <a name="project-nested-or-repeated-data"></a>Beágyazott vagy ismétlődő projekt

Az adatok kivetítéséhez futtasson egy SELECT utasítást a beágyazott adattípusú oszlopokat tartalmazó parketta-fájlon. A kimenetben a beágyazott értékek a JSON-be lesznek szerializálva, és varchar (8000) SQL-adattípusként lesznek visszaadva.

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Részletesebb információkért tekintse meg a [lekérdezési parketta beágyazott típusai](query-parquet-nested-types.md#project-nested-or-repeated-data) című cikket a projekt beágyazott vagy ismétlődő adatok szakaszában.

#### <a name="access-elements-from-nested-columns"></a>Elemek elérése beágyazott oszlopokból

Egy beágyazott oszlop (például a struct) beágyazott elemeinek eléréséhez használja a "dot jelölést", hogy összefűzse a mezőneveket az elérési útra. Adja meg az elérési utat column_nameként a OPENROWSET függvény WITH záradékában.

A szintaxis kódrészlet példája a következő:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Alapértelmezés szerint a OPENROWSET függvény megegyezik a forrás mező nevével és elérési útjával a WITH záradékban megadott oszlopnevek alapján. Az azonos forrásban lévő parketta-fájl különböző beágyazási szintjein található elemek a WITH záradék használatával érhetők el.

**Visszaadott értékek**

- A függvény egy skaláris értéket ad vissza, például int, decimális és varchar, a megadott elemből és a megadott elérési úton, a beágyazott típusú csoportba nem tartozó összes fatípushoz.
- Ha az elérési út egy beágyazott típusú elemre mutat, a függvény egy JSON-töredéket ad vissza a megadott elérési út felső elemétől kezdve. A JSON-töredék varchar (8000) típusú.
- Ha a tulajdonság nem található a megadott column_name, a függvény hibát ad vissza.
- Ha a tulajdonság nem található a megadott column_pathban, az [elérési út módjától](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)függően, a függvény hibát ad vissza, ha a szigorú vagy a null értéket adja meg LAX módban.

A lekérdezési minták esetében tekintse át a beágyazott oszlopok hozzáférési elemeit a [lekérdezési parketta beágyazott típusai](query-parquet-nested-types.md#access-elements-from-nested-columns) című cikkben.

#### <a name="access-elements-from-repeated-columns"></a>Elemek elérése ismétlődő oszlopokból

Ha ismétlődő oszlop elemeit szeretné elérni, például egy tömb vagy Térkép elemét, használja a [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) függvényt minden olyan skaláris elemnél, amelyet a projekthez és a biztosításához szükséges:

- Beágyazott vagy ismétlődő oszlop, az első paraméterként
- Egy [JSON-elérési út](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) , amely megadja az elérni kívánt elemet vagy tulajdonságot második paraméterként

A nem skaláris elemek ismétlődő oszlopokból való eléréséhez használja a [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) függvényt minden olyan nem skaláris elemhez, amelyet a projekthez és a biztosításához szükséges:

- Beágyazott vagy ismétlődő oszlop, az első paraméterként
- Egy [JSON-elérési út](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) , amely megadja az elérni kívánt elemet vagy tulajdonságot második paraméterként

Lásd az alábbi szintaxist:

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

Lekérdezési mintákat talál az elemek az ismétlődő oszlopokból való eléréséhez a [lekérdezési parketta beágyazott típusai](query-parquet-nested-types.md#access-elements-from-repeated-columns) cikkben.

## <a name="next-steps"></a>További lépések

A különböző fájltípusok lekérdezésével, valamint a nézetek létrehozásával és használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Egyetlen CSV-fájl lekérdezése](query-single-csv-file.md)
- [Parquet-fájlok lekérdezése](query-parquet-files.md)
- [JSON-fájlok lekérdezése](query-json-files.md)
- [A Parquet beágyazott típusainak lekérdezése](query-parquet-nested-types.md)
- [Mappák és több CSV-fájl lekérdezése](query-folders-multiple-csv-files.md)
- [Fájl metaadatainak használata a lekérdezésekben](query-specific-files.md)
- [Nézetek létrehozása és használata](create-use-views.md)
