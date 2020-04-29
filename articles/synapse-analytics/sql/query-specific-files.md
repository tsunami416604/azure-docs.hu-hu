---
title: Fájl metaadatainak használata a lekérdezésekben
description: A OPENROWSET függvény fájl-és elérési útvonalat biztosít a lekérdezésben használt összes fájlról az adatok fájlnév és/vagy mappa elérési útján történő szűréséhez és elemzéséhez.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431552"
---
# <a name="using-file-metadata-in-queries"></a>Fájl metaadatainak használata a lekérdezésekben

Az SQL igény szerinti lekérdezési szolgáltatása több fájlt és mappát is képes kezelni a [lekérdezési mappák és a több fájl](query-folders-multiple-csv-files.md) című cikkben leírtak szerint. Ebből a cikkből megtudhatja, hogyan használhatja a lekérdezésekben a fájl-és mappanevek metaadat-információit.

Előfordulhat, hogy tudnia kell, hogy melyik fájl-vagy mappa-forrás korrelál az eredményhalmaz egy adott sorával.

Használhatja a függvényt `filepath` , `filename` és visszaállíthatja a fájlneveket és/vagy az elérési utat az eredményhalmazban. Vagy használhatja őket az adatszűréshez a fájlnév és/vagy a mappa elérési útja alapján. Ezeket a függvényeket a szintaxis szakasz [filename függvénye](develop-storage-files-overview.md#filename-function) és a [filepath függvény](develop-storage-files-overview.md#filepath-function)ismerteti. Alább rövid leírásokat talál a minták mentén.

## <a name="prerequisites"></a>Előfeltételek

A cikk további részének beolvasása előtt tekintse át a következő előfeltételeket:

- [Első beállítás](query-data-storage.md#first-time-setup)
- [Előfeltételek](query-data-storage.md#prerequisites)

## <a name="functions"></a>Functions

### <a name="filename"></a>Filename

Ez a függvény azt a fájlnevet adja vissza, amelyből a sor származik.

A következő minta beolvassa a NYC Yellow taxi adatfájljait az 2017-as utolsó három hónapban, és visszaadja a másodpercenkénti túrák számát. A lekérdezés OPENROWSET része határozza meg, hogy mely fájlok lesznek beolvasva.

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

Az alábbi példa azt szemlélteti, hogyan használható a *filename ()* a WHERE záradékban az olvasni kívánt fájlok szűréséhez. A lekérdezés OPENROWSET részében a teljes mappát éri el, a WHERE záradékban pedig szűri a fájlokat.

Az eredmények az előző példával megegyezőek lesznek.

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

### <a name="filepath"></a>Filepath

A filepath függvény teljes vagy részleges elérési utat ad vissza:

- Ha paraméter nélkül hívja meg a metódust, az a teljes fájl elérési útját adja vissza, amelyből a sor származik.
- Ha paraméterrel hívja meg a metódust, az az elérési út azon részét adja vissza, amely megfelel a paraméterben megadott pozícióban szereplő helyettesítő karakternek. Például az 1. paraméter értéke az első helyettesítő karakternek megfelelő elérési út egy részét fogja visszaadni.

A következő minta a NYC Yellow taxi adatfájljait olvassa be az elmúlt három hónapban 2017-as értékben. A fájlok elérési útjának számát adja vissza. A lekérdezés OPENROWSET része határozza meg, hogy mely fájlok lesznek beolvasva.

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

Az alábbi példa azt szemlélteti, hogyan használható a *filepath ()* a WHERE záradékban az olvasni kívánt fájlok szűréséhez.

Használhatja a lekérdezés OPENROWSET részében található helyettesítő karaktereket, és szűrheti a WHERE záradékban található fájlokat. Az eredmények az előző példával megegyezőek lesznek.

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

A következő cikkben megtudhatja, hogyan lehet [lekérdezni a parketta-fájlokat](query-parquet-files.md).
