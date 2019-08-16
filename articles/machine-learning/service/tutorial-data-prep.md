---
title: 'A regressziós modell oktatóanyaga: Adatok előkészítése'
titleSuffix: Azure Machine Learning service
description: Az oktatóanyag első részében megismerheti, hogyan készítheti elő az adatelemzést a Pythonban a regressziós modellezéshez a Azure Machine Learning SDK használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: MayMSFT
ms.author: sihhu
ms.reviewer: trbye
ms.date: 07/16/2019
ms.custom: seodec18
ms.openlocfilehash: cc243fea67c479879f25e4f39e3cd1c5e0237869
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534364"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Oktatóanyag: Adatelőkészítés a regressziós modellezéshez

Ebből az oktatóanyagból megtudhatja, hogyan készítheti elő a regressziós modellezés adatait a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)adatelőkészítési [csomagjának](https://aka.ms/data-prep-sdk) használatával. Különböző átalakításokat futtat két különböző New York-i taxi-adathalmaz szűrésére és egyesítésére.

Ez az oktatóanyag **egy kétrészes sorozat első része**. Az oktatóanyag-Sorozat elvégzése után előre megjósolhatja a taxik árát, ha betanít egy modellt az adatfunkciókra. A funkciók közé tartozik a felvételi nap és az idő, az utasok száma és a felvételi hely.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Python-környezet beállítása és csomagok importálása.
> * Helyezzen be két különböző mezőneveket tartalmazó adatkészletet.
> * Az adatsérülések eltávolítására szolgáló Adattisztítás.
> * Az adatátalakítást intelligens átalakítások segítségével hozhatja létre új funkciók létrehozásához.
> * Mentse a adatfolyam-objektumot egy regressziós modellben való használatra.

## <a name="prerequisites"></a>Előfeltételek

* Fejezze be [az oktatóanyagot: Ismerkedjen meg az első ml-](tutorial-1st-experiment-sdk-setup.md) kísérlet létrehozásával:
    * Munkaterület létrehozása
    * Felhőalapú notebook-kiszolgáló létrehozása
    * A Jupyter notebook irányítópultjának elindítása

* A Jupyter notebook irányítópultjának elindítása után nyissa meg az **oktatóanyagok/Regression-part1-Data-PREP. ipynb** notebookot.

Ez az oktatóanyag a githubon [](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) is elérhető, ha saját [helyi környezetében](how-to-configure-environment.md#local)szeretné használni.  Győződjön meg arról, hogy telepítette a `azureml-dataprep` csomagot a Azure Machine learning SDK-ból.


## <a name="start"></a>A fejlesztési környezet beállítása

Egy Python-notebookban a fejlesztési munka összes beállítása megadható. A telepítő a következő műveleteket tartalmazza:

* Az SDK telepítése
* Python-csomagok importálása

### <a name="install-and-import-packages"></a>Csomagok telepítése és importálása

Ha még nem rendelkezik velük, a következő paranccsal telepítheti a szükséges csomagokat.

```shell
pip install "azureml-dataprep[pandas]>=1.1.0,<1.2.0"
```

Importálja a csomagot.

```python
import azureml.dataprep as dprep
```

> [!IMPORTANT]
> Győződjön meg arról, hogy a legújabb azureml. adatelőkészítés csomag verzióját telepíti. Ez az oktatóanyag nem fog működni a 1.1.0-nél alacsonyabb verziószámmal

## <a name="load-data"></a>Adatok betöltése

Töltsön le két különböző New York-i taxi-adatkészletet a adatfolyam Objects szolgáltatásba. Az adathalmazok kis mértékben különböző mezőkkel rendelkeznek. A `auto_read_file()` metódus automatikusan felismeri a bemeneti fájl típusát.

```python
from IPython.display import display

green_path = "https://dprepdata.blob.core.windows.net/demo/green-small/*"
yellow_path = "https://dprepdata.blob.core.windows.net/demo/yellow-small/*"

# (optional) Download and view a subset of the data: https://dprepdata.blob.core.windows.net/demo/green-small/green_tripdata_2013-08.csv

green_df_raw = dprep.read_csv(
    path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file automatically identifies and parses the file type, which is useful when you don't know the file type.
yellow_df_raw = dprep.auto_read_file(path=yellow_path)

display(green_df_raw.head(5))
display(yellow_df_raw.head(5))
```

Egy `Dataflow` objektum hasonló egy dataframe, és az adatokra vonatkozó, lustán kiértékelt, nem módosítható műveletek sorozatát jelöli. A műveletek hozzáadhatók a különböző transzformációs és szűrési módszerek meghívásával. Egy művelet `Dataflow` hozzáadásának eredménye mindig egy új `Dataflow` objektum.

## <a name="cleanse-data"></a>Adatok megtisztítása

Most feltölt néhány változót a parancsikon-átalakításokkal, hogy az összes adatfolyamok vonatkozzon. A `drop_if_all_null` változó a rekordok törlésére szolgál, ha az összes mező null értékű. A `useful_columns` változó az egyes adatfolyam tárolt oszlopok leírásának tömbjét tartalmazza.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(
    dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Először a zöld taxival kell dolgoznia, hogy egy érvényes alakba kerüljön, amely kombinálható a sárga taxi-adattal. `replace_na()`A, `drop_nulls()`a és`keep_columns()` a függvények meghívása a létrehozott parancsikon-átalakítási változók használatával. Továbbá nevezze át a dataframe összes oszlopát, hogy egyezzen a `useful_columns` változóban található nevekkel.


```python
green_df = (green_df_raw
            .replace_na(columns=all_columns)
            .drop_nulls(*drop_if_all_null)
            .rename_columns(column_pairs={
                "VendorID": "vendor",
                "lpep_pickup_datetime": "pickup_datetime",
                "Lpep_dropoff_datetime": "dropoff_datetime",
                "lpep_dropoff_datetime": "dropoff_datetime",
                "Store_and_fwd_flag": "store_forward",
                "store_and_fwd_flag": "store_forward",
                "Pickup_longitude": "pickup_longitude",
                "Pickup_latitude": "pickup_latitude",
                "Dropoff_longitude": "dropoff_longitude",
                "Dropoff_latitude": "dropoff_latitude",
                "Passenger_count": "passengers",
                "Fare_amount": "cost",
                "Trip_distance": "distance"
            })
            .keep_columns(columns=useful_columns))
display(green_df.head(5))
```

<div>
<style scoped> .dataframe pedig tbody tr th: csak a-típusú {függőleges igazítás: középre;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Szállító</th>
      <th>pickup_datetime</th>
      <th>dropoff_datetime</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>az utasok</th>
      <th>távolságskála</th>
      <th>költség</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01-08:14:37</td>
      <td>2013-08-01-09:09:06</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>21,25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01-09:13:00</td>
      <td>2013-08-01 11:38:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>.00</td>
      <td>74.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-01-09:48:00</td>
      <td>2013-08-01-09:49:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-01-10:38:35</td>
      <td>2013-08-01-10:38:51</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>3.25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-01 11:51:45</td>
      <td>2013-08-01-12:03:52</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>8.5</td>
    </tr>
  </tbody>
</table>
</div>

Futtassa ugyanazokat az átalakítási lépéseket a sárga taxi-adathalmazon. Ezek a függvények biztosítják, hogy a rendszer nullával távolítsa el az adatkészletből a nullát, ami segít a gépi tanulási modell pontosságának növelésében.

```python
yellow_df = (yellow_df_raw
             .replace_na(columns=all_columns)
             .drop_nulls(*drop_if_all_null)
             .rename_columns(column_pairs={
                 "vendor_name": "vendor",
                 "VendorID": "vendor",
                 "vendor_id": "vendor",
                 "Trip_Pickup_DateTime": "pickup_datetime",
                 "tpep_pickup_datetime": "pickup_datetime",
                 "Trip_Dropoff_DateTime": "dropoff_datetime",
                 "tpep_dropoff_datetime": "dropoff_datetime",
                 "store_and_forward": "store_forward",
                 "store_and_fwd_flag": "store_forward",
                 "Start_Lon": "pickup_longitude",
                 "Start_Lat": "pickup_latitude",
                 "End_Lon": "dropoff_longitude",
                 "End_Lat": "dropoff_latitude",
                 "Passenger_Count": "passengers",
                 "passenger_count": "passengers",
                 "Fare_Amt": "cost",
                 "fare_amount": "cost",
                 "Trip_Distance": "distance",
                 "trip_distance": "distance"
             })
             .keep_columns(columns=useful_columns))
display(yellow_df.head(5))
```

Hívja meg `append_rows()` a függvényt a zöld taxi-adaton a Yellow taxi-adat hozzáfűzéséhez. Létrejön egy új kombinált dataframe.

```python
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>A konvertálás típusok és -szűrő

Vizsgálja meg a begyűjtés és Gyűjtőtár koordinálja az összefoglaló statisztikák hogyan oszlik el az adatok megtekintéséhez. Először Definiáljon egy `TypeConverter` objektumot, amely a szélességi és a hosszúsági mezőket decimális típusra módosítja. Ezután hívja meg a `keep_columns()` függvényt, hogy a kimenetet csak a szélességi és hosszúsági mezőkre `get_profile()` korlátozza, majd hívja meg a függvényt. Ezek a függvények meghívja a adatfolyam tömör nézetét, hogy csak a Lat/Long mezőket jelenítsék meg, így könnyebben kiértékelheti a hiányzó vagy a hatókörön kívüli koordinátákat.


```python
decimal_type = dprep.TypeConverter(data_type=dprep.FieldType.DECIMAL)
combined_df = combined_df.set_column_types(type_conversions={
    "pickup_longitude": decimal_type,
    "pickup_latitude": decimal_type,
    "dropoff_longitude": decimal_type,
    "dropoff_latitude": decimal_type
})
combined_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>Max</th>
      <th>Darabszám</th>
      <th>Hiányzó száma</th>
      <th>Nem hiányzó száma</th>
      <th>Hiányzó százalék</th>
      <th>Hibák száma</th>
      <th>Üres száma</th>
      <th>0,1 % ki osztóérték</th>
      <th>1 % ki osztóérték</th>
      <th>5 %-os ki osztóérték</th>
      <th>25 %-os ki osztóérték</th>
      <th>50 %-os ki osztóérték</th>
      <th>75 %-os ki osztóérték</th>
      <th>95 %-os ki osztóérték</th>
      <th>99 %-os ki osztóérték</th>
      <th>99,9 %-os ki osztóérték</th>
      <th>Szórás</th>
      <th>középérték</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-88.114046</td>
      <td>-73.961840</td>
      <td>-73.961964</td>
      <td>-73.947693</td>
      <td>-73.922097</td>
      <td>-73.846670</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.792672</td>
      <td>-68.833579</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>40.919121</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.682889</td>
      <td>40.675541</td>
      <td>40.721075</td>
      <td>40.756159</td>
      <td>40.803909</td>
      <td>40.849406</td>
      <td>40.870681</td>
      <td>40.891244</td>
      <td>10.345967</td>
      <td>37.936742</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-87.699611</td>
      <td>-73.984734</td>
      <td>-73.985777</td>
      <td>-73.956250</td>
      <td>-73.928948</td>
      <td>-73.866208</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.696526</td>
      <td>-68.896978</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>41.008934</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.662763</td>
      <td>40.654851</td>
      <td>40.717821</td>
      <td>40.756534</td>
      <td>40.784688</td>
      <td>40.852437</td>
      <td>40.879289</td>
      <td>40.937291</td>
      <td>10.290780</td>
      <td>37.963774</td>
    </tr>
  </tbody>
</table>



Az összefoglaló statisztikák kimenetében láthatja, hogy hiányoznak a New York Cityben nem található koordináták és koordináták (ez a szubjektív elemzésből van meghatározva). Kiszűri a város szegélyén kívüli helyszínek koordinátáit. Az oszlop szűrő parancsainak láncolása `filter()` a függvényen belül, és az egyes mezők minimális és maximális határának megadása. Ezután hívja meg `get_profile()` újra a függvényt az átalakítás ellenőrzéséhez.


```python
latlong_filtered_df = (combined_df
                       .drop_nulls(
                           columns=["pickup_longitude", "pickup_latitude",
                                    "dropoff_longitude", "dropoff_latitude"],
                           column_relationship=dprep.ColumnRelationship(
                               dprep.ColumnRelationship.ANY)
                       )
                       .filter(dprep.f_and(
                           dprep.col("pickup_longitude") <= -73.72,
                           dprep.col("pickup_longitude") >= -74.09,
                           dprep.col("pickup_latitude") <= 40.88,
                           dprep.col("pickup_latitude") >= 40.53,
                           dprep.col("dropoff_longitude") <= -73.72,
                           dprep.col("dropoff_longitude") >= -74.09,
                           dprep.col("dropoff_latitude") <= 40.88,
                           dprep.col("dropoff_latitude") >= 40.53
                       )))
latlong_filtered_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>Max</th>
      <th>Darabszám</th>
      <th>Hiányzó száma</th>
      <th>Nem hiányzó száma</th>
      <th>Hiányzó százalék</th>
      <th>Hibák száma</th>
      <th>Üres száma</th>
      <th>0,1 % ki osztóérték</th>
      <th>1 % ki osztóérték</th>
      <th>5 %-os ki osztóérték</th>
      <th>25 %-os ki osztóérték</th>
      <th>50 %-os ki osztóérték</th>
      <th>75 %-os ki osztóérték</th>
      <th>95 %-os ki osztóérték</th>
      <th>99 %-os ki osztóérték</th>
      <th>99,9 %-os ki osztóérték</th>
      <th>Szórás</th>
      <th>középérték</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.078156</td>
      <td>-73.736481</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.076314</td>
      <td>-73.962542</td>
      <td>-73.962893</td>
      <td>-73.948975</td>
      <td>-73.927856</td>
      <td>-73.866662</td>
      <td>-73.830438</td>
      <td>-73.823160</td>
      <td>-73.769750</td>
      <td>0.048711</td>
      <td>-73.913865</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.575485</td>
      <td>40.879852</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632884</td>
      <td>40.713105</td>
      <td>40.711600</td>
      <td>40.721403</td>
      <td>40.758142</td>
      <td>40.805145</td>
      <td>40.848855</td>
      <td>40.867567</td>
      <td>40.877690</td>
      <td>0.048348</td>
      <td>40.765226</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.085747</td>
      <td>-73.720871</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.078828</td>
      <td>-73.985650</td>
      <td>-73.985813</td>
      <td>-73.959041</td>
      <td>-73.936681</td>
      <td>-73.884846</td>
      <td>-73.815507</td>
      <td>-73.776697</td>
      <td>-73.733471</td>
      <td>0.055961</td>
      <td>-73.920718</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.583530</td>
      <td>40.879734</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.597741</td>
      <td>40.695376</td>
      <td>40.695115</td>
      <td>40.727549</td>
      <td>40.758160</td>
      <td>40.788378</td>
      <td>40.850372</td>
      <td>40.867968</td>
      <td>40.878586</td>
      <td>0.050462</td>
      <td>40.759487</td>
    </tr>
  </tbody>
</table>

### <a name="split-and-rename-columns"></a>Felosztott és oszlopok átnevezése

Tekintse meg a data-profilt a a `store_forward` oszlop. Ez a mező egy logikai jelző, amely `Y` akkor van, ha a taxi nem kapcsolódott a kiszolgálóhoz az utazás után, és így a memóriában kellett tárolnia az utazási adatmennyiséget, majd később továbbítja azt a kiszolgálónak a csatlakozáskor.


```python
latlong_filtered_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>Max</th>
      <th>Darabszám</th>
      <th>Hiányzó száma</th>
      <th>Nem hiányzó száma</th>
      <th>Hiányzó százalék</th>
      <th>Hibák száma</th>
      <th>Üres száma</th>
      <th>0,1 % ki osztóérték</th>
      <th>1 % ki osztóérték</th>
      <th>5 %-os ki osztóérték</th>
      <th>25 %-os ki osztóérték</th>
      <th>50 %-os ki osztóérték</th>
      <th>75 %-os ki osztóérték</th>
      <th>95 %-os ki osztóérték</th>
      <th>99 %-os ki osztóérték</th>
      <th>99,9 %-os ki osztóérték</th>
      <th>Szórás</th>
      <th>középérték</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>I</td>
      <td>7059.0</td>
      <td>99.0</td>
      <td>6960.0</td>
      <td>0.014025</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>



Figyelje meg `store_forward` , hogy az oszlop adatprofil kimenete azt mutatja, hogy az adatok inkonzisztensek, és hiányoznak vagy Null értékek. A `replace()` és`fill_nulls()` a függvények használatával cserélje le ezeket az értékeket az "N" karakterláncra:


```python
replaced_stfor_vals_df = latlong_filtered_df.replace(
    columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Hajtsa `replace` végre a függvényt `distance` a mezőben. A függvény újraformázza a nem megfelelően címkézett `.00`távolsági értékeket, és nullával kitölti az összes null értéket. Konvertálja a `distance` mező numerikus formátumban. Ezek a helytelen adatpontok valószínűleg rendellenességek a taxik adatgyűjtési rendszerében.


```python
replaced_distance_vals_df = replaced_stfor_vals_df.replace(
    columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
replaced_distance_vals_df = replaced_distance_vals_df.to_number(["distance"])
```

Ossza szét a felvételi és lemorzsolódási datetime értékeket a megfelelő dátum és idő oszlopba. `split_column_by_example()` A függvény használatával végezze el a felosztást. Ebben az esetben a `example` `split_column_by_example()` függvény opcionális paramétere nincs megadva. Ezért a függvény automatikusan meghatározza a felosztás helyét az adat alapján.


```python
time_split_df = (replaced_distance_vals_df
                 .split_column_by_example(source_column="pickup_datetime")
                 .split_column_by_example(source_column="dropoff_datetime"))
time_split_df.head(5)
```

<div>
<style scoped> .dataframe pedig tbody tr th: csak a-típusú {függőleges igazítás: középre;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Szállító</th>
      <th>pickup_datetime</th>
      <th>pickup_datetime_1</th>
      <th>pickup_datetime_2</th>
      <th>dropoff_datetime</th>
      <th>dropoff_datetime_1</th>
      <th>dropoff_datetime_2</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>az utasok</th>
      <th>távolságskála</th>
      <th>költség</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>2013-08-01</td>
      <td>17:24:00</td>
      <td>2013-08-01 17:25-ös: 00</td>
      <td>2013-08-01</td>
      <td>17:25-ös: 00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06-06:51:19</td>
      <td>2013-08-06</td>
      <td>06:51:19</td>
      <td>2013-08-06-06:51:36</td>
      <td>2013-08-06</td>
      <td>06:51:36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>2013-08-06</td>
      <td>13:26:34</td>
      <td>2013-08-06 13:26:57</td>
      <td>2013-08-06</td>
      <td>13:26:57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>2013-08-06</td>
      <td>13:27:53</td>
      <td>2013-08-06 13:28:08</td>
      <td>2013-08-06</td>
      <td>13:28:08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Nevezze át a `split_column_by_example()` függvény által generált oszlopokat az értelmes nevek használatára.

```python
renamed_col_df = (time_split_df
                  .rename_columns(column_pairs={
                      "pickup_datetime_1": "pickup_date",
                      "pickup_datetime_2": "pickup_time",
                      "dropoff_datetime_1": "dropoff_date",
                      "dropoff_datetime_2": "dropoff_time"
                  }))
renamed_col_df.head(5)
```

A `get_profile()` függvény meghívásával megtekintheti a teljes összesítő statisztikát az összes tisztítási lépés után.

```python
renamed_col_df.get_profile()
```

## <a name="transform-data"></a>Adatok átalakítása

A felvétel és a lemorzsolódási dátumának felosztása a hét napjára, a hónap napjára, valamint a hónap értékére. A hét napja értékének lekéréséhez használja a `derive_column_by_example()` függvényt. A függvény a bemeneti adatokat definiáló objektumok, valamint az előnyben részesített kimenet tömb paraméterét veszi át. A függvény automatikusan meghatározza az előnyben részesített átalakítást. A felvételi és a lemorzsolódási időoszlopok esetében az időt az óra, perc és másodperc értékre kell bontani a `split_column_by_example()` példa nélküli függvény használatával.

Az új funkciók `drop_columns()` létrehozása után a függvénnyel törölheti az eredeti mezőket, ahogy az újonnan létrehozott funkciók előnyben részesítettek. Nevezze át a többi mezőt az értelmes leírások használatára.

Az adatok ily módon történő átalakítása új, időalapú funkciók létrehozásával javíthatja a gépi tanulási modell pontosságát. Például a hétköznap új funkciójának létrehozása segít a hét napja és a taxi viteldíja közötti kapcsolat létrehozásában, ami gyakran drágább a hét bizonyos napjain a nagy kereslet miatt.


```python
transformed_features_df = (renamed_col_df
                           .derive_column_by_example(
                               source_columns="pickup_date",
                               new_column_name="pickup_weekday",
                               example_data=[
                                   ("2009-01-04", "Sunday"), ("2013-08-22", "Thursday")]
                           )
                           .derive_column_by_example(
                               source_columns="dropoff_date",
                               new_column_name="dropoff_weekday",
                               example_data=[
                                   ("2013-08-22", "Thursday"), ("2013-11-03", "Sunday")]
                           )

                           .split_column_by_example(source_column="pickup_time")
                           .split_column_by_example(source_column="dropoff_time")
                           # The following two calls to split_column_by_example reference the column names generated from the previous two calls.
                           .split_column_by_example(source_column="pickup_time_1")
                           .split_column_by_example(source_column="dropoff_time_1")
                           .drop_columns(columns=[
                               "pickup_date", "pickup_time", "dropoff_date", "dropoff_time",
                               "pickup_date_1", "dropoff_date_1", "pickup_time_1", "dropoff_time_1"
                           ])

                           .rename_columns(column_pairs={
                               "pickup_date_2": "pickup_month",
                               "pickup_date_3": "pickup_monthday",
                               "pickup_time_1_1": "pickup_hour",
                               "pickup_time_1_2": "pickup_minute",
                               "pickup_time_2": "pickup_second",
                               "dropoff_date_2": "dropoff_month",
                               "dropoff_date_3": "dropoff_monthday",
                               "dropoff_time_1_1": "dropoff_hour",
                               "dropoff_time_1_2": "dropoff_minute",
                               "dropoff_time_2": "dropoff_second"
                           }))

transformed_features_df.head(5)
```

<div>
<style scoped> .dataframe pedig tbody tr th: csak a-típusú {függőleges igazítás: középre;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Szállító</th>
      <th>pickup_datetime</th>
      <th>pickup_weekday</th>
      <th>pickup_hour</th>
      <th>pickup_minute</th>
      <th>pickup_second</th>
      <th>dropoff_datetime</th>
      <th>dropoff_weekday</th>
      <th>dropoff_hour</th>
      <th>dropoff_minute</th>
      <th>dropoff_second</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>az utasok</th>
      <th>távolságskála</th>
      <th>költség</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>Csütörtök</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>2013-08-01 17:22:00</td>
      <td>Csütörtök</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>Csütörtök</td>
      <td>17</td>
      <td>24</td>
      <td>00</td>
      <td>2013-08-01 17:25-ös: 00</td>
      <td>Csütörtök</td>
      <td>17</td>
      <td>25</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06-06:51:19</td>
      <td>Kedd</td>
      <td>06</td>
      <td>51</td>
      <td>19</td>
      <td>2013-08-06-06:51:36</td>
      <td>Kedd</td>
      <td>06</td>
      <td>51</td>
      <td>36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>Kedd</td>
      <td>13</td>
      <td>26</td>
      <td>34</td>
      <td>2013-08-06 13:26:57</td>
      <td>Kedd</td>
      <td>13</td>
      <td>26</td>
      <td>57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>Kedd</td>
      <td>13</td>
      <td>27</td>
      <td>53</td>
      <td>2013-08-06 13:28:08</td>
      <td>Kedd</td>
      <td>13</td>
      <td>28</td>
      <td>08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Figyelje meg, hogy az adatok azt mutatják, hogy a származtatott átalakításokból előállított felvételi és lemorzsolódási dátum és idő összetevők helyesek. A és `pickup_datetime` az `dropoff_datetime` oszlopok eldobása, mert már nem szükségesek (a modell betanításához hasznosak lehetnek a részletes időpontok, például az óra, a perc és a második).


```python
processed_df = transformed_features_df.drop_columns(
    columns=["pickup_datetime", "dropoff_datetime"])
```

A típus következtetésekhez funkciójának használata automatikusan megkeresi a minden mező adattípusát, és a következtetésekhez eredmények megjelenítéséhez.


```python
type_infer = processed_df.builders.set_column_types()
type_infer.learn()
type_infer
```

Az eredményül kapott `type_infer` kimenet a következő:.

    Column types conversion candidates:
    'pickup_weekday': [FieldType.STRING],
    'pickup_hour': [FieldType.DECIMAL],
    'pickup_minute': [FieldType.DECIMAL],
    'pickup_second': [FieldType.DECIMAL],
    'dropoff_hour': [FieldType.DECIMAL],
    'dropoff_minute': [FieldType.DECIMAL],
    'dropoff_second': [FieldType.DECIMAL],
    'store_forward': [FieldType.STRING],
    'pickup_longitude': [FieldType.DECIMAL],
    'dropoff_longitude': [FieldType.DECIMAL],
    'passengers': [FieldType.DECIMAL],
    'distance': [FieldType.DECIMAL],
    'vendor': [FieldType.STRING],
    'dropoff_weekday': [FieldType.STRING],
    'pickup_latitude': [FieldType.DECIMAL],
    'dropoff_latitude': [FieldType.DECIMAL],
    'cost': [FieldType.DECIMAL]

A következtetések eredményei az adatok alapján helyesek. Most alkalmazza a típus konverzióját a adatfolyam.


```python
type_converted_df = type_infer.to_dataflow()
type_converted_df.get_profile()
```

A adatfolyam csomagolása előtt futtasson két végső szűrőt az adatkészleten. A helytelenül rögzített adatpontok eltávolításához szűrje a adatfolyam olyan rekordokon `cost` , `distance` ahol a és a változó értéke nagyobb nullánál. Ez a lépés jelentősen javítja a gépi tanulási modell pontosságát, mivel a nulla értékű vagy távolságú adatpontok jelentős kiugró értékeket jelentenek, amelyek kiváltják az előrejelzési pontosságot.

```python
final_df = type_converted_df.filter(dprep.col("distance") > 0)
final_df = final_df.filter(dprep.col("cost") > 0)
```

Most már rendelkezik egy teljes mértékben átalakított és előkészített adatfolyam-objektummal a Machine learning-modellben való használatra. Az SDK tartalmaz egy objektum-szerializálási funkciót, amely az alábbi kódban látható módon használható.

```python
import os

file_path = os.path.join(os.getcwd(), "dflows.dprep")
final_df.save(file_path)
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag második részének folytatásához a **dflows. dprep** fájlra van szükség az aktuális könyvtárban.

Ha nem tervezi a második rész folytatását, törölje a **dflows. dprep** fájlt az aktuális könyvtárában. Törölje ezt a fájlt, hogy helyileg vagy Azure Notebookson futtatja [](https://notebooks.azure.com/)-e a végrehajtást.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egyik részben meg:

> [!div class="checklist"]
> * Állítsa be a fejlesztési környezetet.
> * Betöltött és megtisztított adathalmazok.
> * Az intelligens átalakítók használatával előre megjósolhatja a logikát egy példa alapján.
> * Egyesített és csomagolt adatkészletek a gépi tanulási képzéshez.

Készen áll az oktatóanyag második részében szereplő betanítási adatmennyiség használatára:

> [!div class="nextstepaction"]
> [Oktatóanyag (második rész): A regressziós modell betanítása](tutorial-auto-train-models.md)
