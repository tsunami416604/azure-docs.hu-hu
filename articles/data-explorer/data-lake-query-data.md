---
title: Azure Data Lake lekérdezése az Azure-Adatkezelő használatával
description: Megtudhatja, hogyan kérdezheti le az Azure Data Lake az Azure Adatkezelő használatával.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: ef4dfc4370c71eac1978a6f3535b571a5e6009b5
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950142"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Azure Data Lake lekérdezése az Azure Adatkezelő használatával (előzetes verzió)

A Azure Data Lake Storage egy rugalmasan méretezhető és költséghatékony, big data elemzésre szolgáló adatkezelési megoldás. Egy nagy teljesítményű fájlrendszer, a hatalmas méret és a gazdaságosság ötvözésével segít gyorsabban hozzájutni az elemzési eredményekhez. A Data Lake Storage Gen2 túlmutat az Azure Blob Storage képességein, és elemzési feladatokra van optimalizálva.
 
Az Azure Adatkezelő integrálható az Azure Blob Storage és Azure Data Lake Storage Gen2 szolgáltatással, amely gyors, gyorsítótárazott és indexelt hozzáférést biztosít a Lake-beli adatkezeléshez. Az Azure Adatkezelő-ba való előzetes betöltés nélkül elemezheti és lekérdezheti a tóban lévő adatot. Lekérdezheti a betöltött és a nem feldolgozott natív Lake-adatmennyiségeket is egyszerre.  

> [!TIP]
> A legjobb lekérdezési teljesítmény az Azure Adatkezelőba való adatfeldolgozást teszi szükségessé. Az Azure Data Lake Storage Gen2 előzetes betöltés nélküli lekérdezésének képességét csak olyan múltbeli vagy a ritkán lekérdezett adatmennyiségek esetében kell használni.
 
## <a name="optimize-query-performance-in-the-lake"></a>A lekérdezés teljesítményének optimalizálása a tóban 

* Adatok particionálása a jobb teljesítmény és az optimalizált lekérdezési idő tekintetében.
* Az adatok tömörítése a jobb teljesítmény érdekében (a legjobb tömörítés érdekében a lz4 a legjobb teljesítmény érdekében).
* Az Azure Blob Storage vagy az Azure Adatkezelő-fürttel azonos régióval Azure Data Lake Storage Gen2. 

## <a name="create-an-external-table"></a>Külső tábla létrehozása

 > [!NOTE]
 > Jelenleg támogatott Storage-fiókok az Azure Blob Storage vagy Azure Data Lake Storage Gen2. A jelenleg támogatott adatformátumok a JSON, a CSV, a TSV és a txt.

1. A `.create external table` paranccsal létrehozhat egy külső táblát az Azure Adatkezelőban. A külső táblák további parancsai, `.show`például `.drop`a, `.alter` a és a [külső táblák parancsaiban](/azure/kusto/management/externaltables)vannak dokumentálva.

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * Nagyobb teljesítmény várható részletesebb particionálással. Többek között a napi partíciókat tartalmazó külső táblák lekérdezései jobb teljesítményt kapnak, mint a havonta particionált táblákkal rendelkező lekérdezések.
    > * Ha partíciót tartalmazó külső táblát határoz meg, a tárolási struktúra megegyeznek.
Ha például a tábla egy DateTime partícióval van definiálva éééé/hh/nn formátumban (alapértelmezett), az URI-tár elérési útja csak *container1/éééé/hh/nn/all_exported_blobs*lehet. 

1. A külső tábla látható a webes felhasználói felület bal oldali ablaktábláján.

    ![külső tábla a webes felületen](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Külső tábla létrehozása JSON-formátummal

Létrehozhat egy JSON formátumú külső táblát is. További információ: [külső táblák parancsai](/azure/kusto/management/externaltables)

1. A `.create external table` parancs használatával hozzon létre egy *ExternalTableJson*nevű táblázatot:

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. A JSON formátuma egy második lépést tesz szükségessé az oszlopok leképezésének létrehozásához az alább látható módon. A következő lekérdezésben hozzon létre egy *mappingName*nevű megadott JSON-leképezést:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Külső tábla engedélyei
 
* Az adatbázis-felhasználó létrehozhat egy külső táblát. A tábla létrehozója automatikusan lesz a tábla rendszergazdája.
* A fürt, az adatbázis vagy a tábla rendszergazdája szerkesztheti a meglévő táblákat.
* Bármely adatbázis-felhasználó vagy-olvasó lekérdezheti a külső táblákat.
 
## <a name="query-an-external-table"></a>Külső tábla lekérdezése
 
Egy külső tábla lekérdezéséhez használja a `external_table()` függvényt, és adja meg a tábla nevét függvény argumentumként. A lekérdezés többi része a standard Kusto lekérdezési nyelv.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Az IntelliSense jelenleg nem támogatott külső táblákon futó lekérdezésekben.

### <a name="query-an-external-table-with-json-format"></a>Külső tábla lekérdezése JSON-formátummal

Ha JSON formátumú külső táblát szeretne lekérdezni, használja `external_table()` a függvényt, és adja meg a tábla nevét és a hozzárendelés nevét a függvény argumentumként. Ha az alábbi lekérdezésben nincs megadva a *mappingName* , a rendszer a korábban létrehozott leképezést fogja használni.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Külső és betöltött adatmennyiség lekérdezése együtt

Ugyanazon lekérdezésen belül is lekérdezheti a külső táblákat és a betöltött adattáblákat. Ön [`join`](/azure/kusto/query/joinoperator) [vagy`union`](/azure/kusto/query/unionoperator) a külső tábla, amely az Azure adatkezelő, az SQL Server vagy más forrásból származó további adatokkal rendelkezik. [`let( ) statement`](/azure/kusto/query/letstatement) A használatával rövidített nevet rendelhet egy külső tábla hivatkozásához.

Az alábbi példában a *termékek* egy betöltött adattábla, a *ArchivedProducts* pedig egy külső tábla, amely a Azure Data Lake Storage Gen2ban lévő adatot tartalmaz:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Külső tábla *TaxiRides* lekérdezése a Súgó fürtben

A *TaxiRides* -minta adatkészlete New York-i taxi-adathalmazt tartalmaz a [NYC taxi és a limuzin commissionból](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Külső tábla *TaxiRides* létrehozása 

> [!NOTE]
> Ez a szakasz a *TaxiRides* külső tábla létrehozásához használt lekérdezést mutatja be a *Súgó* fürtben. Mivel ez a tábla már létrejött, kihagyhatja ezt a szakaszt, és elvégezheti a [lekérdezés *TaxiRides* külső tábla adatain](#query-taxirides-external-table-data). 

1. A következő lekérdezés a külső tábla *TaxiRides* létrehozására szolgál a Súgó fürtben. 

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
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. Az eredményül kapott tábla a következő *Súgó* fürtben lett létrehozva:

    ![TaxiRides külső tábla](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Külső *TaxiRides* lekérdezése 

A TaxiRides külső tábla lekérdezéséhez jelentkezzen be. [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Külső tábla lekérdezése particionálás nélkül *TaxiRides*

[Futtassa ezt a lekérdezést](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) a külső táblán, hogy a hét minden napján a teljes adathalmazon *TaxiRides* a túrákat. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Ez a lekérdezés a hét legforgalmasabb napját jeleníti meg. Mivel az adatmennyiség nincs particionálva, a lekérdezés hosszú időt vehet igénybe az eredmények visszaküldéséhez (akár több percig is).

![nem particionált lekérdezés megjelenítése](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>TaxiRides külső táblájának lekérdezése particionálással 

[Futtassa ezt a lekérdezést](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) a külső tábla *TaxiRides* , amely a 2017 januárjában használt taxisofőr-típusokat (sárga vagy zöld) jeleníti meg. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Ez a lekérdezés particionálást használ, amely optimalizálja a lekérdezési időt és a teljesítményt. A lekérdezés szűrői egy particionált oszlopon (pickup_datetime), és néhány másodpercen belül visszaadja az eredményeket.

![particionált lekérdezés renderelése](media/data-lake-query-data/taxirides-with-partition.png)
  
További lekérdezéseket is írhat a külső tábla *TaxiRides* való futtatáshoz, és további információkat tudhat meg az adatkezelésről. 

## <a name="next-steps"></a>További lépések

Az Azure Adatkezelő használatával kérdezheti le adatait a Azure Data Lake. Megtudhatja, hogyan [írhat lekérdezéseket](write-queries.md) , és hogyan hozhatja ki az adatokból származó további információkat.
