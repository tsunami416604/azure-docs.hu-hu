---
title: Az Azure Data Lake az Azure az adatkezelő segítségével adatokat kérdezhet le
description: Ismerje meg, hogyan kérdezhet le adatokat az Azure Data Lake az Azure az adatkezelő segítségével.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: d6a58d144482e17f7e4b615134115d1da46af6f0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453176"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Az Azure Data Lake az Azure Data Explorer (előzetes verzió) használatával adatokat lekérdezni

Az Azure Data Lake Storage egy hatékonyan méretezhető és költséghatékony data lake megoldás big data-elemzés. Egy nagy teljesítményű fájlrendszer, a hatalmas méret és a gazdaságosság ötvözésével segít gyorsabban hozzájutni az elemzési eredményekhez. A Data Lake Storage Gen2 túlmutat az Azure Blob Storage képességein, és elemzési feladatokra van optimalizálva.
 
Az Azure Data Explorer integrálható az Azure Blob Storage és az Azure Data Lake Storage Gen2, gyors, így a gyorsítótárba, és indexeli a Lake adatokhoz való hozzáférést. Elemezheti és kérdezhet le adatokat a előzetes feltöltése az Azure Data Explorer nélküli Lake. A feldolgozott és uningested natív lake data egyszerre is lekérdezésére.  

> [!TIP]
> A legjobb lekérdezési teljesítmény az adatok feltöltése az Azure Data Explorer szükségessé teszi. A funkció használatával adatokat lekérdezni az Azure Data Lake Storage Gen2 előzetes betöltési nélkül csak használandó előzményadatok vagy a ritkán lekérdezett adatok.
 
## <a name="optimize-query-performance-in-the-lake"></a>A Lake a lekérdezési teljesítmény optimalizálása 

* Adatok particionálása a nagyobb teljesítmény és optimalizált lekérdezési idő.
* Jobb teljesítmény (a legjobb tömörítési gzip, a legjobb teljesítmény érdekében lz4) adatok tömörítése.
* Az Azure Blob Storage vagy Azure Data Lake Storage Gen2 és az Azure Data Explorer fürt ugyanabban a régióban. 

## <a name="create-an-external-table"></a>Külső tábla létrehozása

1. Használja a `.create external table` külső tábla létrehozása az Azure Data Explorer parancsot. Külső tábla további parancsok, mint `.show`, `.drop`, és `.alter` vannak dokumentálva [külső tábla parancsok](/azure/kusto/management/externaltables).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```

    Ez a lekérdezés hoz létre a napi partíciók *container1/yyyy/MM/dd/all_exported_blobs.csv*. Nagyobb teljesítmény várható a részletesebb particionálási. Például a napi partíciók, például a fenti, a külső táblákon végrehajtott lekérdezések jobb teljesítmény érdekében ezeket a lekérdezéseket, havi particionált táblák lesz.

    > [!NOTE]
    > Jelenleg támogatott tárfiókok az Azure Blob Storage vagy az Azure Data Lake Storage Gen2. Jelenleg támogatott formátumok a következők: csv, tsv és txt.

1. A külső tábla jelenik meg a bal oldali panelen, a webes felhasználói felület

    ![külső tábla a webes felhasználói felületen](media/data-lake-query-data/external-tables-web-ui.png)
 
### <a name="external-table-permissions"></a>Külső tábla engedélyek
 
* Az adatbázis-felhasználót egy külső táblát hozhat létre. A tábla létrehozója automatikusan lesz tábla rendszergazdája.
* A fürt, adatbázis vagy táblázat rendszergazda szerkesztheti a meglévő tábla.
* Bármilyen adatbázis-felhasználót vagy olvasó lekérdezheti a külső tábla.
 
## <a name="query-an-external-table"></a>A külső tábla lekérdezése
 
A külső tábla lekérdezése, használja a `external_table()` függvényt, és adja meg a táblanevet függvény argumentumaként. A lekérdezés többi általános Kusto-lekérdezés nyelvi.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Az IntelliSense jelenleg nem támogatott a külső tábla lekérdezések.

## <a name="query-external-and-ingested-data-together"></a>Együtt a külső és a feldolgozott adatok lekérdezése

Külső táblák és a betöltött táblák ugyanabból a lekérdezés lekérdezheti. Ön [ `join` ](/azure/kusto/query/joinoperator) vagy [ `union` ](/azure/kusto/query/unionoperator) a külső tábla az adatkezelőt az Azure, SQL Server-kiszolgálók vagy egyéb forrásokból származó további adatokkal. Használja a [ `let( ) statement` ](/azure/kusto/query/letstatement) gyorsírás nevét hozzárendelése egy külső tábla hivatkozást.

Az alábbi példában *termékek* betöltött táblázat és *ArchivedProducts* van egy külső táblát, amely az Azure Data Lake Storage Gen2 adatokat tartalmazza:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Lekérdezés *TaxiRides* a súgófürtben lévő külső tábla

A *TaxiRides* minta adatkészlet tartalmazza a New York City-i taxik adatait [NYC Taxi és Limousine Bizottság](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Külső tábla létrehozása *TaxiRides* 

> [!NOTE]
> Ez a szakasz a létrehozásához használt lekérdezés ábrázolja a *TaxiRides* a külső tábla a *súgó* fürt. Mivel ez a tábla már létre van hozva kihagyhatja ezt a szakaszt, és hajtsa végre [lekérdezés *TaxiRides* külső táblák adatainak](#query-taxirides-external-table-data). 

1. A következő lekérdezést a külső tábla létrehozásához használt *TaxiRides* a súgófürtben lévő. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
    h@'https://externalkustosamples.blob.core.windows.net/taxiridesbyday?st=2019-06-18T14%3A59%3A00Z&se=2029-06-19T14%3A59%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=yEaO%2BrzFHzAq7lvd4d9PeQ%2BTi3AWnho8Rn8hGU0X30M%3D'
    )
    ```
1. Az eredményül kapott tábla sikeresen létrehozva a *súgó* fürt:

    ![Külső tábla TaxiRides](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Lekérdezés *TaxiRides* külső tábla adatait 

Jelentkezzen be a [ https://dataexplorer.azure.com/clusters/help/databases/Samples ](https://dataexplorer.azure.com/clusters/help/databases/Samples) lekérdezéshez a *TaxiRides* külső tábla. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Lekérdezés *TaxiRides* külső tábla particionálása nélkül

[A lekérdezés futtatására](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) a külső tábla *TaxiRides* ábrázolhatja a hét naponként fel a teljes adatkészlet között. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Ez a lekérdezés megjeleníti a legforgalmasabb a hét napja. Az adatok nem particionált, mivel ez a lekérdezés az eredmények (akár több is perc) hosszú időt vehet igénybe.

![a lekérdezés nem particionált megjelenítése](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Lekérdezés TaxiRides külső tartalmazó tábla particionálása 

[A lekérdezés futtatására](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) a külső tábla *TaxiRides* megjelenítő taxi cab típusok (sárga vagy zöld), a 2017 január használt. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Ez a lekérdezés használ a particionálás, ami optimalizálja a lekérdezési idő és teljesítmény. A lekérdezés egy particionált (pickup_datetime) oszlop alapján szűri, és pár másodpercen belül eredményeket ad vissza.

![a particionált lekérdezés megjelenítése](media/data-lake-query-data/taxirides-with-partition.png)
  
A külső tábla futtathatók további lekérdezéseket írhat *TaxiRides* és további információ az adatokat. 

## <a name="next-steps"></a>További lépések

Az Azure Data Lake az Azure az adatkezelő segítségével az adatok lekérdezése. Ismerje meg, hogyan [lekérdezéseket írni](write-queries.md) és további betekintést szeretne nyerni az adatokból.