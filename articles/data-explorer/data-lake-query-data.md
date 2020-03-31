---
title: Adatok lekérdezése az Azure Data Lake szolgáltatásban az Azure Data Explorer használatával
description: Ismerje meg, hogyan kérdezheti le az adatokat az Azure Data Lake-ben az Azure Data Explorer használatával.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 8240b1a01aa39e53b9ae41f73543ccf9774290b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161749"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Adatok lekérdezése az Azure Data Lake szolgáltatásban az Azure Data Explorer használatával

Az Azure Data Lake Storage egy nagymértékben skálázható és költséghatékony data lake megoldás a big data-elemzéshez. Egyesíti a nagy teljesítményű fájlrendszer erejét a masszív méretekkel és gazdaságossággal, hogy segítsen felgyorsítani az időt a bepillantásra. A Data Lake Storage Gen2 kibővíti az Azure Blob Storage képességeit, és elemzési számítási feladatokra van optimalizálva.
 
Az Azure Data Explorer integrálható az Azure Blob Storage és az Azure Data Lake Storage (Gen1 és Gen2), amely gyors, gyorsítótárazott és indexelt hozzáférést biztosít a tóban lévő adatokhoz. A tóban lévő adatok at az Azure Data Explorer előzetes betöltése nélkül elemezheti és lekérdezheti. A betöltési és nem ingetsztő natív tó adatok egyszerre is lekérdezheti.  

> [!TIP]
> A legjobb lekérdezési teljesítmény szükségessé teszi az adatok betöltését az Azure Data Explorerbe. A külső adatok előzetes betöltés nélküli lekérdezésének képessége csak olyan előzményadatokhoz vagy adatokhoz használható, amelyeket ritkán kérdeznek le. [Optimalizálja a lekérdezési teljesítményt a tóban a](#optimize-your-query-performance) legjobb eredmény érdekében.
 

## <a name="create-an-external-table"></a>Külső tábla létrehozása

 > [!NOTE]
 > A jelenleg támogatott tárfiókok az Azure Blob Storage vagy az Azure Data Lake Storage (Gen1 és Gen2).

1. A `.create external table` parancs segítségével hozzon létre egy külső táblát az Azure Data Explorerben. További külső táblázatparancsok, `.show`például a `.drop`, és `.alter` a Külső táblázat [parancsok](/azure/kusto/management/externaltables)ban vannak dokumentálva.

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
    > * Nagyobb teljesítmény várható a részletesebb particionálás. Például a napi partíciókkal rendelkező külső táblákon keresztül i. lekérdezések teljesítménye jobb lesz, mint a havi particionált táblákkal rendelkező lekérdezések.
    > * Ha partíciókkal rendelkező külső táblát határoz meg, a tárolószerkezetnek azonosnak kell lennie.
Ha például a tábla yyyy/MM/dd formátumú DateTime partícióval van definiálva (alapértelmezett), az URI-tárolófájl elérési útjának *tárolónak1/yyyy/MM/dd/all_exported_blobs*all_exported_blobs . 
    > * Ha a külső táblát egy datetime oszlop particionálja, a lekérdezésben mindig szerepeljen egy `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` lezárt tartomány időszűrője (például `ArchivedProducts | where Timestamp > ago(1h)` a lekérdezés - - jobban kell teljesítenie, mint ez (megnyitott tartomány) egy - ). 
    > * Az összes [támogatott betöltési formátum](ingest-data-overview.md#supported-data-formats) lekérdezhető külső táblákkal.

1. A külső tábla a webes felhasználói felület bal oldali ablaktáblájában látható

    ![külső tábla a webes felhasználói felületen](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Külső táblázat létrehozása json formátummal

Létrehozhat egy külső táblázatot json formátummal. További információt a [Külső táblázatparancsok](/azure/kusto/management/externaltables) című témakörben talál.

1. A `.create external table` paranccsal hozzon létre egy *ExternalTableJson*nevű táblát:

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
 
1. A Json formátum az oszlopokhoz való hozzárendelés második lépését teszi szükségessé az alábbiak szerint. A következő lekérdezésben hozzon létre egy adott json-leképezést, amelynek neve *mappingName:*

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Külső tábla engedélyek
 
* Az adatbázis-felhasználó létrehozhat egy külső táblát. A táblalétrehozó automatikusan a tábla rendszergazdája lesz.
* A fürt, az adatbázis vagy a tábla rendszergazdája szerkesztheti a meglévő táblát.
* Bármely adatbázis-felhasználó vagy -olvasó lekérdezhet egy külső táblát.
 
## <a name="query-an-external-table"></a>Külső tábla lekérdezése
 
Külső tábla lekérdezéséhez használja `external_table()` a függvényt, és adja meg a tábla nevét függvényargumentumként. A lekérdezés többi része szabványos Kusto lekérdezési nyelv.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Az Intellisense jelenleg nem támogatott a külső táblalekérdezéseknél.

### <a name="query-an-external-table-with-json-format"></a>Külső tábla lekérdezése json formátumban

Ha json formátumú külső táblát `external_table()` szeretne lekérdezni, használja a függvényt, és adja meg függvényargumentumként a tábla nevét és a leképezés nevét is. Az alábbi lekérdezésben, ha nincs megadva *a mappingName,* a rendszer a korábban létrehozott leképezést fogja használni.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Külső és bevitt adatok lekérdezése együtt

Lekérdezheti a külső táblákat és a bevitt adattáblákat is ugyanazon a lekérdezésen belül. Ön [`join`](/azure/kusto/query/joinoperator) [`union`](/azure/kusto/query/unionoperator) vagy a külső tábla az Azure Data Explorer, AZ SQL-kiszolgálók vagy más forrásokból származó további adatokkal. [`let( ) statement`](/azure/kusto/query/letstatement) A segítségével gyorsírásnevet rendelhet egy külső táblahivatkozáshoz.

Az alábbi példában a *Termékek* egy bevitt adattábla, az *ArchivedProducts* pedig egy külső tábla, amely az Azure Data Lake Storage Gen2-ben lévő adatokat tartalmazza:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>*A TaxiRides* külső tábla lekérdezése a súgófürtben

A *TaxiRides* minta adatsor tartalmazza a New York-i taxi adatok [AT NYC Taxi és Limuzin Bizottság](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Külső tábla létrehozása *TaxiRides* 

> [!NOTE]
> Ez a szakasz a *TaxiRides* külső tábla létrehozásához használt lekérdezést ábrázolja a *súgófürtben.* Mivel ez a tábla már létrejött, kihagyhatja ezt a szakaszt, és végrehajthatja [a *TaxiRides* külső táblaadatok lekérdezését.](#query-taxirides-external-table-data) 

1. A következő lekérdezést használták a *taxirides* külső tábla létrehozásához a súgófürtben. 

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
1. Az eredményül kapott tábla a *súgófürtben* jött létre:

    ![TaxiRides külső tábla](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>*TaxiRides* lekérdezése külső táblaadatok 

Jelentkezzen be [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) a *TaxiRides* külső tábla lekérdezéséhez. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>*TaxiRides* lekérdezése külső tábla particionálás nélkül

[Futtassa ezt](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) a lekérdezést a *taxirides* külső táblán, hogy a hét minden napjára, a teljes adatkészletre vonatkozó fuvarokat ábrázoljon. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Ez a lekérdezés a hét legforgalmasabb napját mutatja. Mivel az adatok nincsenek particionálva, ez a lekérdezés hosszú időt vehet igénybe az eredmények visszaadása (akár néhány percig).

![nem particionált lekérdezés renderelése](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>TaxiRides lekérdezése particionálással rendelkező külső tábla 

[Futtassa ezt](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) a lekérdezést a 2017 januárjában használt *taxitípusokat* (sárga vagy zöld) megjelenítő taxialkalmazások külső tábláján. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Ez a lekérdezés particionálást használ, amely optimalizálja a lekérdezési időt és teljesítményt. A lekérdezés szűri a particionált oszlop (pickup_datetime), és visszaadja az eredményeket néhány másodpercen belül.

![particionált lekérdezés renderelése](media/data-lake-query-data/taxirides-with-partition.png)
  
Írhat további lekérdezéseket a *TaxiRides* külső táblán való futtatáshoz, és többet is megtudhat az adatokról. 

## <a name="optimize-your-query-performance"></a>A lekérdezési teljesítmény optimalizálása

Optimalizálja a lekérdezési teljesítményt a tóban az alábbi ajánlott eljárások használatával a külső adatok lekérdezéséhez. 
 
### <a name="data-format"></a>Adatformátum
 
Oszlopos formátumot használ az analitikus lekérdezésekhez, mivel:
* Csak a lekérdezéshez tartozó oszlopok olvashatók. 
* Az oszlopkódolási technikák jelentősen csökkenthetik az adatok méretét.  
Az Azure Data Explorer támogatja a Parquet és az ORC oszlopos formátumokat. Parketta formátum javasolt miatt optimalizált végrehajtását. 
 
### <a name="azure-region"></a>Azure-régió 
 
Győződjön meg arról, hogy a külső adatok ugyanabban az Azure-régióban találhatók, mint az Azure Data Explorer-fürt. Ez csökkenti a költségeket és az adatok lehívási idejét.
 
### <a name="file-size"></a>Fájlméret
 
Az optimális fájlméret fájlonként több száz Mb (legfeljebb 1 Gb). Kerülje a szükségtelen többletterhelést igénylő kis fájlokat, például a lassabb fájlszámbavételi folyamatot és az oszlopos formátum korlátozott használatát. Vegye figyelembe, hogy a fájlok száma nagyobbnak kell lennie, mint az Azure Data Explorer-fürt processzormagok száma. 
 
### <a name="compression"></a>Tömörítés
 
A tömörítés segítségével csökkentse a távtárolóból beolvasott adatok mennyiségét. A Parketta formátumban használja a belső parketta tömörítési mechanizmust, amely külön tömöríti az oszlopcsoportokat, így lehetővé teszi azok külön olvasását. A tömörítési mechanizmus használatának ellenőrzéséhez ellenőrizze, hogy<filename>a fájlok neve a következő: " .gz.parquet" vagy "<filename>.snappy.parquet " a "<filename>.parquet.gz" helyett). 
 
### <a name="partitioning"></a>Particionálás
 
Az adatok rendszerezése "mappa" partíciók használatával, amelyek lehetővé teszik, hogy a lekérdezés kihagyja a lényegtelen elérési utakat. A particionálás tervezésekor vegye figyelembe a fájlméretet és a gyakori szűrőket a lekérdezésekben, például az időbélyegben vagy a bérlői azonosítóban.
 
### <a name="vm-size"></a>Virtuális gép mérete
 
Válassza ki a virtuális gép sk-ek több mag és a nagyobb hálózati átviteli (memória kevésbé fontos). További információ: [Válassza ki a megfelelő virtuálisgép-termékváltozatot az Azure Data Explorer-fürthöz.](manage-cluster-choose-sku.md)

## <a name="next-steps"></a>További lépések

Az Azure Data Explorer használatával lekérdezheti az adatokat az Azure Data Lake-ben. Ismerje meg a [lekérdezések írását,](write-queries.md) és további elemzéseket nyerhet az adatokból.
