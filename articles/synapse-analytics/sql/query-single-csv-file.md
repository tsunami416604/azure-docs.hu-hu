---
title: CSV-fájlok lekérdezése az SQL on-demand használatával (előzetes verzió)
description: Ebből a cikkből megtudhatja, hogyan kérdezheti le az önálló CSV-fájlokat különböző fájlformátumokban az SQL on-demand (előzetes verzió) használatával.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 63755616bb524226d3c40d32b9695f4b787860d9
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489707"
---
# <a name="query-csv-files"></a>CSV-fájlok lekérdezése

Ebből a cikkből megtudhatja, hogyan kérdezheti le egyetlen CSV-fájlt az SQL on-demand (előzetes verzió) használatával az Azure szinapszis Analyticsben. A CSV-fájlok formátuma eltérő lehet: 

- Fejléc-sorral és anélkül
- Vesszővel és tabulátorral tagolt értékek
- Windows-és Unix-stílusú sorok vége
- Nem idézett és idézett értékek, valamint Escape-karakterek

Az alábbiakban az összes fenti változatot tárgyaljuk.

## <a name="quickstart-example"></a>Rövid útmutató – példa

`OPENROWSET`a függvény lehetővé teszi a CSV-fájl tartalmának olvasását a fájl URL-címének megadásával.

### <a name="read-a-csv-file"></a>CSV-fájl olvasása

A fájl tartalmának megtekintésére a legegyszerűbb módszer, `CSV` Ha a fájl URL-címét megadja a `OPENROWSET` függvénynek, a CSV-t és a 2,0-et adja meg `FORMAT` `PARSER_VERSION` . Ha a fájl nyilvánosan elérhető, vagy ha az Azure AD-identitása hozzáfér ehhez a fájlhoz, akkor az alábbi példában látható módon láthatja a fájl tartalmát:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    format = 'csv',
    parser_version = '2.0',
    firstrow = 2 ) as rows
```

Ezzel a beállítással lehet `firstrow` kihagyni a CSV-fájl első olyan sorát, amely ebben az esetben a fejlécet jelöli. Győződjön meg arról, hogy el tudja érni ezt a fájlt. Ha a fájlt SAS-kulccsal vagy egyéni identitással védi, a [kiszolgáló szintű hitelesítő adatokat kell beállítania az SQL-bejelentkezéshez](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

### <a name="data-source-usage"></a>Adatforrás használata

Az előző példa a fájl teljes elérési útját használja. Alternatív megoldásként létrehozhat egy külső adatforrást is, amelynek a helye a tároló gyökérkönyvtárára mutat:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
```

Miután létrehozta az adatforrást, használhatja azt az adatforrást és a fájl relatív elérési útját a következő `OPENROWSET` függvényben:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) as rows
```

Ha egy adatforrás SAS-kulccsal vagy egyéni identitással védett, az [adatforrást adatbázis-hatókörű hitelesítő](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)adatokkal is konfigurálhatja.

### <a name="explicitly-specify-schema"></a>Séma explicit meghatározása

`OPENROWSET`lehetővé teszi explicit módon megadhatja, hogy mely oszlopokat szeretné beolvasni a fájlból a `WITH` záradék használatával:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) with (
        date_rep date 1,
        cases int 5,
        geo_id varchar(6) 8
    ) as rows
```

A záradékban szereplő adattípusú számok a `WITH` CSV-fájlban lévő oszlop indexét jelölik.

A következő részekben láthatja, hogyan lehet lekérdezni különböző típusú CSV-fájlokat.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként létre kell **hoznia egy adatbázist** , amelybe a táblákat létre kívánja hozni. Ezután inicializálja az objektumokat a [telepítési parancsfájl](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) végrehajtásával az adatbázison. Ez a telepítési parancsfájl létrehozza az adatforrásokat, az adatbázis-hatókörrel rendelkező hitelesítő adatokat, valamint az ezekben a mintákban használt külső fájlformátumokat.

## <a name="windows-style-new-line"></a>Windows stílusú új sor

A következő lekérdezés azt mutatja be, hogyan lehet beolvasni egy olyan CSV-fájlt, amely egy fejlécsor nélkül, egy Windows-stílusú új sorral és vesszővel tagolt oszlopokkal rendelkezik.

Fájl előnézete:

![A CSV-fájl első 10 sora fejléc nélkül, Windows stílusú új sor.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Unix stílusú új sor

A következő lekérdezés azt mutatja be, hogyan lehet egy olyan fájlt beolvasni, amely nem tartalmaz fejlécet, Unix stílusú új sorral és vesszővel tagolt oszlopokkal. Figyelje meg a fájl különböző helyét a többi példához képest.

Fájl előnézete:

![A CSV-fájl első 10 sora fejlécsor nélkül és Unix stílusú új sorral.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Fejlécsor

A következő lekérdezés azt mutatja be, hogyan lehet egy fejlécet tartalmazó olvasási fájl egy Unix stílusú új sorral és vesszővel tagolt oszlopokkal. Figyelje meg a fájl különböző helyét a többi példához képest.

Fájl előnézete:

![A CSV-fájl első 10 sora a fejléc sorával és Unix stílusú új sorral.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Egyéni idézőjel karakter

A következő lekérdezés azt mutatja be, hogyan lehet beolvasni egy fejlécet tartalmazó fájlt egy Unix-stílusú új vonallal, vesszővel tagolt oszlopokkal és az idézőjeles értékekkel. Figyelje meg a fájl különböző helyét a többi példához képest.

Fájl előnézete:

![A CSV-fájl első 10 sora a fejléc sorával, valamint a Unix stílusú új sor és az idézett értékekkel.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> A lekérdezés ugyanezeket az eredményeket adja vissza, ha kihagyta a FIELDQUOTE paramétert, mivel a FIELDQUOTE alapértelmezett értéke dupla idézőjel.

## <a name="escape-characters"></a>Escape-karakterek

A következő lekérdezés azt mutatja be, hogyan lehet beolvasni egy fejlécet tartalmazó fájlt egy Unix-stílusú új sorral, vesszővel tagolt oszlopokkal, valamint egy Escape-karaktert, amely az értékeken belüli elválasztó (vessző) mezőkhöz használható. Figyelje meg a fájl különböző helyét a többi példához képest.

Fájl előnézete:

![A CSV-fájl első 10 sora a fejléc sorával és a mező elválasztójának Unix stílusú, új vonallal és Escape karakterrel való használatával.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Ez a lekérdezés meghiúsul, ha a ESCAPECHAR nincs megadva, mert a "Slov, enia" vessző nem az ország/régió neve részeként lesz kezelve. A "Slov, enia" két oszlopként lesz kezelve. Ezért az adott sornak több oszlopa is lehet, mint a többi sor, és egy oszlop a WITH záradékban megadott értékkel.

### <a name="escape-quoting-characters"></a>Escape-idézetek karakterei

A következő lekérdezés azt mutatja be, hogyan lehet beolvasni egy fejlécet tartalmazó fájlt egy Unix-stílusú új sorral, vesszővel tagolt oszlopokkal és egy megszökött idézőjeles karakterrel az értékeken belül. Figyelje meg a fájl különböző helyét a többi példához képest.

Fájl előnézete:

![A következő lekérdezés azt mutatja be, hogyan lehet beolvasni egy fejlécet tartalmazó fájlt egy Unix-stílusú új sorral, vesszővel tagolt oszlopokkal és egy megszökött idézőjeles karakterrel az értékeken belül.](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Az idézőjel karakternek egy másik idézőjel karakterrel kell megszöknie. Az idézőjel karakter csak akkor szerepelhet az oszlop értékén belül, ha az érték idézőjelekkel van ellátva.

## <a name="tab-delimited-files"></a>Tabulátorral tagolt fájlok

A következő lekérdezés azt mutatja be, hogyan lehet beolvasni egy fejlécet tartalmazó fájlt egy Unix stílusú új sorral és tabulátorral tagolt oszlopokkal. Figyelje meg a fájl különböző helyét a többi példához képest.

Fájl előnézete:

![A CSV-fájl első 10 sora a fejléc sorával, valamint a Unix stílusú új sor és tabulátor elválasztóval.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="return-a-subset-of-columns"></a>Oszlopok részhalmazának visszaadása

Eddig a CSV-fájl sémáját a és az összes oszlop listázásával adtuk meg. A lekérdezésben ténylegesen szükséges oszlopokat csak sorszám alapján adhatja meg a szükséges oszlopokhoz. Emellett az oszlopok nem érdeklik.

A következő lekérdezés egy fájl különböző ország-/régióinak nevét adja vissza, csak a szükséges oszlopokat adja meg:

> [!NOTE]
> Tekintse meg a WITH záradékot az alábbi lekérdezésben, és vegye figyelembe, hogy a sor végén található "2" (idézőjelek nélkül) a (z) *[country_name]* oszlopot határozza meg. Ez azt jelenti, hogy a *[country_name]* oszlop a fájl második oszlopa. A lekérdezés figyelmen kívül hagyja a fájl összes oszlopát, kivéve a másodikat.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>További lépések

A következő cikkek a következőkre mutatnak:

- [Parquet-fájlok lekérdezése](query-parquet-files.md)
- [Mappák és több fájl lekérdezése](query-folders-multiple-csv-files.md)
