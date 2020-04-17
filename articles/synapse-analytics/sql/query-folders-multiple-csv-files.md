---
title: Lekérdezési mappák és több CSV-fájl az SQL igény szerinti használatával (előzetes verzió)
description: Az SQL igény szerinti (előzetes verzió) támogatja több fájl/mappa olvasását helyettesítő karakterek használatával, amelyek hasonlóak a Windows operációs rendszerben használt helyettesítő karakterekhez.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8f8af7fab7113e38b91c3f5f1bcc41b4e4fba2c1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457365"
---
# <a name="query-folders-and-multiple-csv-files"></a>Mappák és több CSV-fájl lekérdezése  

Ebben a cikkben megtudhatja, hogyan írhat lekérdezést az SQL igény szerinti (előzetes verzió) használatával az Azure Synapse Analytics szolgáltatásban.

Az SQL igény szerinti használata több fájl/mappa olvasását támogatja helyettesítő karakterek használatával, amelyek hasonlóak a Windows operációs rendszerben használt helyettesítő karakterekhez. Azonban nagyobb rugalmasság van jelen, mivel több helyettesítő karakter is megengedett.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elolvasná a cikk többi részét, győződjön meg róla, hogy áttekinti az alább felsorolt cikkeket:

- [Első beállítás](query-data-storage.md#first-time-setup)
- [Előfeltételek](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Több fájl olvasása a mappában

A *csv/taxi* mappát fogja használni a mintalekérdezések követésére. A NYC Taxi - Yellow Taxi Trip Records adatokat tartalmaz 2016 júliusa és 2018 júniusa között.

A *csv/taxi* fájljai az év és a hónap után vannak elnevezve:

- yellow_tripdata_2016-07.csv
- yellow_tripdata_2016-08.csv
- yellow_tripdata_2016-09.csv
- ...
- yellow_tripdata_2018-04.csv
- yellow_tripdata_2018-05.csv
- yellow_tripdata_2018-06.csv

Minden fájl a következő struktúrával rendelkezik:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>A mappában lévő összes fájl olvasása
    
Az alábbi példa beolvassa az összes NYC Yellow Taxi adatfájlt a *csv/taxi* mappából, és visszaadja az utasok és a túrák éves számát. Azt is mutatja, használata összesített függvények.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/*.*',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
           trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
           payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Az egyetlen OPENROWSET-sel elért összes fájlnak azonos szerkezetűnek kell lennie (azaz az oszlopok számával és adattípusaival).

### <a name="read-subset-of-files-in-folder"></a>A mappában lévő fájlok részhalmazának olvasása

Az alábbi példa beolvassa a 2017-es NYC Yellow Taxi adatfájlokat a *csv/taxi* mappából egy helyettesítő karakter használatával, és visszaadja a teljes viteldíj összegét fizetési típusonként.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Az egyetlen OPENROWSET-sel elért összes fájlnak azonos szerkezetűnek kell lennie (azaz az oszlopok számával és adattípusaival).

## <a name="read-folders"></a>Mappák olvasása

Az OPENROWSET elérési útja egy mappa elérési útja is lehet. A következő szakaszok ezeket a lekérdezéstípusokat tartalmazzák.

### <a name="read-all-files-from-specific-folder"></a>Az összes fájl olvasása adott mappából

A mappában lévő összes fájlt elolvashatja a fájlszintű helyettesítő karakter használatával, ahogy az a [mappában lévő összes fájl olvasása](#read-all-files-in-folder)című részben látható. De van egy módja annak, hogy lekérdezni egy mappát, és felhasználja az összes fájlt a mappában.

Ha az OPENROWSET-ben megadott elérési út egy mappára mutat, a rendszer a mappában lévő összes fájlt a lekérdezés forrásaként fogja használni. A következő lekérdezés a *csv/taxi* mappában lévő összes fájlt beolvassa.

> [!NOTE]
> Megjegyzés: létezik a / végén az elérési út a lekérdezés alatt. Egy mappát jelöl. Ha a / nincs megadva, a lekérdezés egy *taxi* nevű fájlt céloz meg.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Az egyetlen OPENROWSET-sel elért összes fájlnak azonos szerkezetűnek kell lennie (azaz az oszlopok számával és adattípusaival).

### <a name="read-all-files-from-multiple-folders"></a>Az összes fájl olvasása több mappából

Egy helyettesítő karakter segítségével több mappából is lehet fájlokat olvasni. A következő lekérdezés beolvassa az összes fájlt az összes mappát található a *csv* mappát, amelyek nevét kezdve *t* és végződő *i*.

> [!NOTE]
> Megjegyzés: létezik a / végén az elérési út a lekérdezés alatt. Egy mappát jelöl. Ha a / nincs megadva, a lekérdezés célzott fájlokat nevű *t&ast;i* helyett.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/', 
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Az egyetlen OPENROWSET-sel elért összes fájlnak azonos szerkezetűnek kell lennie (azaz az oszlopok számával és adattípusaival).

Mivel csak egy olyan mappája van, amely megfelel a feltételeknek, a lekérdezés eredménye megegyezik a [mappában lévő összes fájl olvasásával.](#read-all-files-in-folder)

## <a name="multiple-wildcards"></a>Több helyettesítő karakter

Különböző elérési utakon több helyettesítő karaktert is használhat. Például gazdagíthatja az előző lekérdezést, hogy csak a 2017-es adatokkal rendelkező fájlokat olvasson, az összes olyan mappából, amely a nevek *t-vel* kezdődnek és *az i-vel*végződnek.

> [!NOTE]
> Megjegyzés: létezik a / végén az elérési út a lekérdezés alatt. Egy mappát jelöl. Ha a / nincs megadva, a lekérdezés célzott fájlokat nevű *t&ast;i* helyett.
> Lekérdezésenként legfeljebb 10 helyettesítő karakter lehet.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Az egyetlen OPENROWSET-sel elért összes fájlnak azonos szerkezetűnek kell lennie (azaz az oszlopok számával és adattípusaival).

Mivel csak egy olyan mappája van, amely megfelel a feltételeknek, a lekérdezés eredménye megegyezik a [mappában lévő fájlok olvasása](#read-subset-of-files-in-folder) és [az összes fájl olvasása adott mappából](#read-all-files-from-specific-folder). A [lekérdezési parkettafájlok](query-parquet-files.md)összetettebb helyettesítő karakteres használati forgatókönyveket is lefednek.

## <a name="next-steps"></a>További lépések

További információ a Query [adott fájlok](query-specific-files.md) ról szóló cikkben található.
