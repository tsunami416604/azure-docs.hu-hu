---
title: Fájlmetaadatok használata lekérdezésekben
description: Az OPENROWSET függvény fájl- és elérésiút-adatokat biztosít a lekérdezésben használt összes fájlról az adatok szűrésére vagy elemzésére a fájlnév és/vagy mappa elérési útja alapján.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431552"
---
# <a name="using-file-metadata-in-queries"></a>Fájlmetaadatok használata lekérdezésekben

Az SQL igény szerinti lekérdezési szolgáltatás a [Query mappákban és](query-folders-multiple-csv-files.md) több fájlról szóló cikkben leírtak szerint több fájlt és mappát is meg tud címezni. Ebből a cikkből megtudhatja, hogy miként használhatja a fájl- és mappanevek metaadat-adatait a lekérdezésekben.

Néha előfordulhat, hogy tudnia kell, hogy melyik fájl- vagy mappaforrás kapcsolódik az eredményhalmaz egy adott sorához.

Használhatja a `filepath` `filename` függvényt, és visszaadhatja a fájlneveket és/vagy az elérési utat az eredményhalmazban. Vagy használhatja őket az adatok szűrésére a fájl név és/vagy mappa elérési útja alapján. Ezeket a függvényeket a szintaktikai szakasz [fájlnév és](develop-storage-files-overview.md#filename-function) [filepath függvény](develop-storage-files-overview.md#filepath-function)ismerteti. Az alábbiakban rövid leírásokat talál a minták mentén.

## <a name="prerequisites"></a>Előfeltételek

A cikk többi részének elolvasása előtt tekintse át az alábbi előfeltételeket:

- [Első beállítás](query-data-storage.md#first-time-setup)
- [Előfeltételek](query-data-storage.md#prerequisites)

## <a name="functions"></a>Functions

### <a name="filename"></a>Fájlnév

Ez a függvény azt a fájlnevet adja vissza, amelyből a sor származik.

A következő minta beolvassa a NYC Yellow Taxi adatfájljait 2017 utolsó három hónapjában, és visszaadja a fájlonkénti fuvarok számát. A lekérdezés OPENROWSET része határozza meg, hogy mely fájlokat olvassa be a program.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET') AS [r]
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

A következő példa bemutatja, hogyan használható a *fájlnév()* a WHERE záradékban az olvasandó fájlok szűréséhez. A lekérdezés OPENROWSET részében lévő teljes mappához hozzáfér, és szűri a WHERE záradékban lévő fájlokat.

Az eredmények ugyanazok lesznek, mint az előző példában.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
    FORMAT='PARQUET') AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.parquet', 'yellow_tripdata_2017-11.parquet', 'yellow_tripdata_2017-12.parquet')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Fájlelérési út

A filepath függvény teljes vagy részleges elérési utat ad vissza:

- Ha paraméter nélkül hívják meg, a teljes fájlelérési utat adja vissza, amelyből a sor származik.
- Ha egy paraméterrel hívják meg, az elérési út egy részét adja vissza, amely megegyezik a paraméterben megadott beosztásban lévő helyettesítő karakterrel. Az 1-es paraméterérték például az első helyettesítő karakternek megfelelő elérési út egy részét adja vissza.

A következő minta a NYC Yellow Taxi adatfájljait olvassa fel 2017 utolsó három hónapjában. A fájlok elérési útjánkénti fuvarok számát adja vissza. A lekérdezés OPENROWSET része határozza meg, hogy mely fájlokat olvassa be a program.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-1*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT,
        pickup_datetime DATETIME2,
        dropoff_datetime DATETIME2,
        passenger_count SMALLINT,
        trip_distance FLOAT,
        rate_code SMALLINT,
        store_and_fwd_flag SMALLINT,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type SMALLINT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

A következő példa bemutatja, hogyan használható a *filepath()* a WHERE záradékban az olvasandó fájlok szűréséhez.

Használhatja a helyettesítő karaktereket a lekérdezés OPENROWSET részében, és szűrheti a FÁJLOKAT a WHERE záradékban. Az eredmények ugyanazok lesznek, mint az előző példában.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_*-*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
WITH (
    vendor_id INT,
    pickup_datetime DATETIME2,
    dropoff_datetime DATETIME2,
    passenger_count SMALLINT,
    trip_distance FLOAT,
    rate_code SMALLINT,
    store_and_fwd_flag SMALLINT,
    pickup_location_id INT,
    dropoff_location_id INT,
    payment_type SMALLINT,
    fare_amount FLOAT,
    extra FLOAT,
    mta_tax FLOAT,
    tip_amount FLOAT,
    tolls_amount FLOAT,
    improvement_surcharge FLOAT,
    total_amount FLOAT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>További lépések

A következő cikkben megtudhatja, hogyan [lehet lekérdezni a Parkettafájlokat.](query-parquet-files.md)
