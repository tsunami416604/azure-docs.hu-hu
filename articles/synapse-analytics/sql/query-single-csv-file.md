---
title: CSV-fájlok lekérdezése az IGÉNY szerinti SQL használatával (előzetes verzió)
description: Ebből a cikkből megtudhatja, hogyan lehet lekérdezni egyetlen CSV-fájlokat különböző fájlformátumokkal az SQL on-demand (előzetes verzió) használatával.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431591"
---
# <a name="query-csv-files"></a>CSV-fájlok lekérdezése

Ebben a cikkben megtudhatja, hogyan lehet lekérdezni egyetlen CSV-fájlt az SQL igény szerinti (előzetes verzió) használatával az Azure Synapse Analytics szolgáltatásban. A CSV-fájlok különböző formátumúak lehetnek: 

- Fejlécsorral és anélkül
- Vesszővel és tabulátorral tagolt értékek
- Windows és Unix stílusvonal végződések
- Nem jegyzett és idézett értékek, valamint kiáramló karakterek

A fenti változatok mindegyikét az alábbiakban fedjük le.

## <a name="prerequisites"></a>Előfeltételek

A cikk többi részének elolvasása előtt tekintse át a következő cikkeket:

- [Első beállítás](query-data-storage.md#first-time-setup)
- [Előfeltételek](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Windows stílusú új vonal

A következő lekérdezés bemutatja, hogyan lehet fejlécsor nélküli CSV-fájlt olvasni windowsos stílusú új sorral és vesszővel tagolt oszlopokkal.

Fájl előnézete:

![A CSV fájl első 10 sora fejléc nélkül, a Windows új sort stílusa.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
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

## <a name="unix-style-new-line"></a>Unix stílusú új vonal

A következő lekérdezés bemutatja, hogyan lehet fejlécsor nélküli fájlt olvasni Unix stílusú új sorral és vesszővel tagolt oszlopokkal. Figyelje meg a fájl különböző helyét a többi példához képest.

Fájl előnézete:

![A CSV-fájl első 10 sora fejlécsor nélkül és Unix-Style új sorral.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT = 'CSV',
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

A következő lekérdezés bemutatja, hogyan lehet fejlécsorral, Unix stílusú új sorral és vesszővel tagolt oszlopokkal olvasni a fájlt. Figyelje meg a fájl különböző helyét a többi példához képest.

Fájl előnézete:

![A CSV-fájl első 10 sora fejlécsorral és Unix-Style új sorral.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr/population.csv',
        FORMAT = 'CSV',
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

## <a name="custom-quote-character"></a>Egyéni idézőjel

A következő lekérdezés bemutatja, hogyan lehet fejlécsorral, Unix stílusú új sorral, vesszővel tagolt oszlopokkal és idézett értékekkel olvasni. Figyelje meg a fájl különböző helyét a többi példához képest.

Fájl előnézete:

![A CSV-fájl első 10 sora fejlécsorral és Unix-Style új sorral és idézett értékekkel.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-quoted/population.csv',
        FORMAT = 'CSV',
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
> Ez a lekérdezés ugyanazt az eredményt adja vissza, ha kihagyta a FIELDQUOTE paramétert, mivel a FIELDQUOTE alapértelmezett értéke dupla idézőjel.

## <a name="escaping-characters"></a>Menekülő karakterek

A következő lekérdezés bemutatja, hogyan lehet fejlécsorral, Unix stílusú új sorral, vesszővel tagolt oszlopokkal és az értékeken belüli mezőhatárolóhoz (vesszőhöz) használt escape-karakterrel olvasni. Figyelje meg a fájl különböző helyét a többi példához képest.

Fájl előnézete:

![A CSV-fájl első 10 sora fejlécsorral és Unix-Style új sorral és a mezőhatárolóhoz használt escape karakterrel.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-escape/population.csv',
        FORMAT = 'CSV',
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
    country_name = 'Slov,enia';
```

> [!NOTE]
> Ez a lekérdezés sikertelen lenne, ha az ESCAPECHAR nincs megadva, mivel a "Slov,enia" vesszője mezőhatárolóként lenne kezelve az országnév része helyett. "Slov, enia" lenne kezelni, mint két oszlop. Ezért az adott sor egy oszlopával több, mint a többi, és egy oszlopmal több, mint amit a WITH záradékban meghatározott.

## <a name="tab-delimited-files"></a>Tabulátorral tagolt fájlok

A következő lekérdezés bemutatja, hogyan lehet fejlécsorral, Unix stílusú új sorral és tabulátorral tagolt oszlopokkal olvasni a fájlt. Figyelje meg a fájl különböző helyét a többi példához képest.

Fájl előnézete:

![A CSV-fájl első 10 sora fejlécsorral és Unix-Style új vonallal és tabulátorral.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-tsv/population.csv',
        FORMAT = 'CSV',
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

## <a name="returning-subset-of-columns"></a>Oszlopok részhalmazának visszaadása

Eddig a CSV-fájlsémát a WITH használatával adta meg, és felsorolja az összes oszlopot. A lekérdezésben csak akkor adhat meg oszlopokat, amelyekre ténylegesen szüksége van, ha minden szükséges oszlophoz sorszámot használ. Akkor is kihagyja oszlopok nem érdekel.

A következő lekérdezés a fájlban lévő különböző országnevek számát adja vissza, és csak a szükséges oszlopokat adja meg:

> [!NOTE]
> Vessen egy pillantást az alábbi lekérdezés WITH záradékára, és vegye figyelembe, hogy a sor végén a sor végén "2" (idézőjelek nélkül) található, ahol megadhatja a *[country_name]* oszlopot. Ez azt jelenti, hogy a *[country_name]* oszlop a fájl második oszlopa. A lekérdezés a második kivételével figyelmen kívül hagyja a fájl összes oszlopát.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>További lépések

A következő cikkek megmutatják, hogyan:

- [Parquet-fájlok lekérdezése](query-parquet-files.md)
- [Mappák és több fájl lekérdezése](query-folders-multiple-csv-files.md)
