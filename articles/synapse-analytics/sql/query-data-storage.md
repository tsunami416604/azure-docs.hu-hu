---
title: Áttekintés – adatlekérdezés a Storage-ban az SQL on-demand használatával (előzetes verzió)
description: Ez a szakasz azokat a lekérdezéseket tartalmazza, amelyekkel kipróbálhatja az Azure-beli SQL on-demand (előzetes verzió) erőforrást az Azure szinapszis Analyticsen belül.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e18fc765385e6d703e735a1ca15c539c32f36e93
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116247"
---
# <a name="overview-query-data-in-storage"></a>Áttekintés: adatlekérdezés a Storage szolgáltatásban

Ez a szakasz azokat a lekérdezéseket tartalmazza, amelyekkel kipróbálhatja az Azure-beli SQL on-demand (előzetes verzió) erőforrást az Azure szinapszis Analyticsen belül.
A jelenleg támogatott fájlok a következők: 
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Előfeltételek

A lekérdezések kibocsátásához szükséges eszközök:

- Tetszőleges SQL-ügyfél:
    - Azure szinapszis Studio (előzetes verzió)
    - Azure Data Studio
    - SQL Server Management Studio

Emellett a paraméterek a következők:

| Paraméter                                 | Leírás                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Igény szerinti SQL-szolgáltatás végpontjának címe    | A kiszolgáló neveként lesz használva.                                   |
| Igény szerinti SQL-szolgáltatás végpontjának régiója     | A rendszer a mintákban használt tárterület meghatározására szolgál. |
| A végpontok hozzáférésének felhasználóneve és jelszava | A végpont eléréséhez lesz használva.                               |
| A nézetek létrehozásához használni kívánt adatbázis     | Ezt az adatbázist a rendszer kiindulási pontként fogja használni a mintákhoz.       |

## <a name="first-time-setup"></a>Első beállítás

A jelen cikk későbbi részében szereplő minták használata előtt két lépésből áll:

- Hozzon létre egy adatbázist a nézetekhez (ha a nézeteket szeretné használni)
- A tárolóban lévő fájlok eléréséhez igény szerint az SQL igény szerint használandó hitelesítő adatok létrehozása

### <a name="create-database"></a>Adatbázis létrehozása

A nézetek létrehozásához adatbázisra van szükség. Ezt az adatbázist fogja használni a dokumentációban szereplő példák némelyikére.

> [!NOTE]
> Az adatbázisokat csak a metaadatok megtekintésére használják, nem a tényleges adatokhoz.  Jegyezze fel a használni kívánt adatbázis nevét, később szüksége lesz rá.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Hitelesítő adatok létrehozása

A lekérdezések futtatása előtt létre kell hoznia a hitelesítő adatokat. Ezt a hitelesítő adatot az SQL igény szerinti szolgáltatása fogja használni a tárolóban lévő fájlok eléréséhez.

> [!NOTE]
> Az ebben a szakaszban található útmutató sikeres futtatásához az SAS-tokent kell használnia.
>
> Az SAS-tokenek használatának megkezdéséhez el kell dobnia a UserIdentity, amelyet az alábbi [cikkben](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through)ismertetünk.
>
> Alapértelmezés szerint az SQL igény szerinti használata mindig a HRE-továbbítást használja.

A tárterület-hozzáférés-vezérlés kezelésével kapcsolatos további információkért olvassa el ezt a [hivatkozást](develop-storage-files-storage-access-control.md).

A CSV-, JSON-és parketta-tárolók hitelesítő adatainak létrehozásához futtassa az alábbi kódot:

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
```

## <a name="provided-demo-data"></a>Megadott bemutatói információk

A demo-adat a következő adatkészleteket tartalmazza:

- New York-i taxi – Yellow taxi Trip Records – a Public NYC adathalmazának része
  - CSV-formátum
  - Parquet formátum
- Populációs adatkészlet
  - CSV-formátum
- Parketta-fájlok beágyazott oszlopokkal
  - Parquet formátum
- Könyvek JSON
  - JSON formátum

| Mappa elérési útja                                                  | Leírás                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| CSV                                                        | CSV formátumú adatszülő mappa                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Különböző CSV formátumú adatfájlokkal rendelkező mappák. |
| /csv/taxi/                                                   | A New York-i nyilvános adatfájlok mappája CSV formátumban              |
| Parquet                                                    | Az adatszülő mappa a parketta formátumban                     |
| /parquet/taxi                                                | A New York-i nyilvános adatfájlok parkettás formátumban, évről évre, a kaptár/Hadoop particionálási sémával particionálva. |
| /parquet/nested/                                             | Parketta-fájlok beágyazott oszlopokkal                     |
| JSON                                                       | JSON formátumú adatszülő mappa                        |
| /json/books/                                                 | Könyvekből származó JSON-fájlok                                   |

## <a name="validation"></a>Ellenőrzés

Hajtsa végre a következő három lekérdezést, és ellenőrizze, hogy a hitelesítő adatok megfelelően lettek-e létrehozva.

> [!NOTE]
> A mintavételi lekérdezések összes URI-je az észak-európai Azure-régióban található Storage-fiókot használja. Győződjön meg arról, hogy a megfelelő hitelesítő adatokat hozta létre. Futtassa az alábbi lekérdezést, és győződjön meg arról, hogy a Storage-fiók szerepel a listában.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Ha nem találja a megfelelő hitelesítő adatokat, tekintse meg az [első alkalommal történő telepítést](#first-time-setup).

### <a name="sample-query"></a>Mintalekérdezés

Az érvényesítés utolsó lépése a következő lekérdezés végrehajtása:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

A fenti lekérdezésnek a következő számot kell visszaadnia: **8945574**.

## <a name="next-steps"></a>További lépések

Most már készen áll a folytatásra a következő cikkekkel:

- [Egyetlen CSV-fájl lekérdezése](query-single-csv-file.md)

- [Mappák és több CSV-fájl lekérdezése](query-folders-multiple-csv-files.md)

- [Adott fájlok lekérdezése](query-specific-files.md)

- [Parquet-fájlok lekérdezése](query-parquet-files.md)

- [A Parquet beágyazott típusainak lekérdezése](query-parquet-nested-types.md)

- [JSON-fájlok lekérdezése](query-json-files.md)

- [Nézetek létrehozása és használata](create-use-views.md)
