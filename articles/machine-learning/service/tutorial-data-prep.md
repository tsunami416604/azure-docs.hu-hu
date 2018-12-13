---
title: 'Regressziós modell oktatóanyag: adatok előkészítése'
titleSuffix: Azure Machine Learning service
description: Ez az oktatóanyag első részében meg fogjuk útmutató előkészítheti az adatokat a Python, az Azure Machine Learning SDK-val regressziós modellezéshez.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: cforbe
ms.author: cforbe
ms.reviewer: trbye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 14be60977ef0be3e88a0cb01356446b59ca374e2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53076440"
---
# <a name="tutorial-part-1-prepare-data-for-regression-modeling"></a>(1. rész) oktatóanyag: adatok előkészítése az regressziós modellezéshez

Ebben az oktatóanyagban elsajátíthatja, hogyan előkészítheti az adatokat a regressziós modellezéshez, az Azure Machine Learning Data Prep SDK használatával. Különféle átalakításokat szűrését, és kombinálja a két különböző NYC Taxi adatkészletet. A végső cél, ez az oktatóanyag beállított taxi belépőt költsége alapján a data-funkciókat, beleértve a felvételi óra, a modell képes előre jelezni, nap, hét, az utasok és a koordináták száma. Ebben az oktatóanyagban a kétrészes oktatóanyag-sorozat része.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Csomagok importálásához és a egy Python-környezet beállítása
> * Két adatkészletet különböző mezőneveket betöltése
> * Rendellenességek eltávolítása adatok megtisztítása
> * Adatok átalakítása intelligens átalakítások használatával hozhat létre új funkciók
> * Egy regressziós modellt használja, az adatfolyam objektum mentése

Az adatok a Python használatával készítheti elő a [az Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk).

## <a name="get-the-notebook"></a>A notebook beszerzése

Az Ön kényelme érdekében ez az oktatóanyag [Jupyter-notebookként](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/regression-part1-data-prep.ipynb) is elérhető. A `regression-part1-data-prep.ipynb` notebook az Azure Notebooks szolgáltatásban vagy a saját Jupyter-notebookkiszolgálóján futtatható.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="import-packages"></a>Csomagok importálása

Először importálja az SDK-t.


```python
import azureml.dataprep as dprep
```

## <a name="load-data"></a>Adatok betöltése

Töltse le a két különböző NYC Taxi adatkészletek adatfolyamot objektumokba.  Ezek az adatkészletek eltérő mezők tartalmaznak. A metódus `auto_read_file()` automatikusan felismeri a bemeneti fájl típusa.


```python
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df = dprep.read_csv(path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file will automatically identify and parse the file type, and is useful if you don't know the file type
yellow_df = dprep.auto_read_file(path=yellow_path)

display(green_df.head(5))
display(yellow_df.head(5))
```

## <a name="cleanse-data"></a>Adatok megtisztítása

Most töltse fel az összes adatfolyamok érvényes helyi átalakítások bizonyos változókat. A változó `drop_if_all_null` használandó rekordok törléséhez, az összes mező kitöltése null értékű. A változó `useful_columns` oszlop leírását, amely kerülnek be az egyes adatfolyamot tömbjét tartalmazza.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Először a zöld taxi adatokkal dolgozik, és formázhatja őket egy érvényes, amely get kombinálva, a sárga i taxik adatait. Hozzon létre egy ideiglenes adatfolyamot `tmp_df`. Hívja a `replace_na()`, `drop_nulls()`, és `keep_columns()` függvények használatával a helyi átalakítása létrehozott változókat. Ezenkívül nevezze át a nevek megfelelően az adathalmaz összes oszlop `useful_columns`.


```python
tmp_df = (green_df
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
tmp_df.head(5)
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

Írja felül a `green_df` változó, a végrehajtott átalakítások `tmp_df` az előző lépésben.

```python
green_df = tmp_df
```

Hajtsa végre a sárga i taxik adatait az azonos Adatátalakítási lépéseket.


```python
tmp_df = (yellow_df
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
tmp_df.head(5)
```

Ismét felülírása `yellow_df` a `tmp_df`, majd hívja a `append_rows()` funkciót, a zöld taxi adatok hozzáfűzése a sárga i taxik adatait, hozzon létre egy új adathalmaz kombinált.


```python
yellow_df = tmp_df
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>A konvertálás típusok és -szűrő 

Vizsgálja meg a begyűjtés és Gyűjtőtár koordinálja az összefoglaló statisztikák hogyan oszlik el az adatok megtekintéséhez. Első adjon meg egy `TypeConverter` módosítani a szél/hosszú mezők decimális típusú objektum. Ezután hívja a `keep_columns()` kimeneti korlátozhatja a csak a szél/hosszú mezők függvényt, és ezután hívja meg `get_profile()`.


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
      <th>Típus</th>
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



Az összefoglaló statisztikák kimenetében láthatja, hogy nincsenek hiányzó koordináták, és koordináták, amelyek nem a New York City. Nem található az városa szegély koordinátái meg szűrés láncolási oszlop szűrő belül lévő parancsok a `filter()` függvényt, és az egyes mezőkhöz tartozó minimális és maximális rozsah meghatározása. Ezután hívja meg `get_profile()` az átalakítás megerősítése érdekében.


```python
tmp_df = (combined_df
    .drop_nulls(
        columns=["pickup_longitude", "pickup_latitude", "dropoff_longitude", "dropoff_latitude"],
        column_relationship=dprep.ColumnRelationship(dprep.ColumnRelationship.ANY)
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
tmp_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude", 
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Típus</th>
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



Írja felül `combined_df` az elvégzett átalakítások `tmp_df`.


```python
combined_df = tmp_df
```

### <a name="split-and-rename-columns"></a>Felosztott és oszlopok átnevezése

Tekintse meg a data-profilt a a `store_forward` oszlop.


```python
combined_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Típus</th>
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



Adatok profil kimenetéből származó `store_forward`, látni, hogy az adatok inkonzisztens és a hiányzó/null érték. Ezeket az értékeket cserélje le a `replace()` és `fill_nulls()` működik, és mindkét esetben módosítsa a karakterlánc "N".


```python
combined_df = combined_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Hajtsa végre egy másik `replace` működnek majd, ezúttal a `distance` mező. Ez újraformázza távolság értékek, amelyek nem megfelelően van-e jelölve, `.00`, és bármely nullák nullákkal tölti ki. Konvertálja a `distance` mező numerikus formátumban.


```python
combined_df = combined_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
combined_df = combined_df.to_number(["distance"])
```

A kivételezést felosztani a, és dobja el a megfelelő dátum és idő oszlopokra, időpontok ki. Használat `split_column_by_example()` a felosztás végrehajtásához. Ebben az esetben, az opcionális `example` paraméterében `split_column_by_example()` van hagyva. Ezért a függvény automatikusan meghatározza, hol található adatok alapján felosztani.


```python
tmp_df = (combined_df
    .split_column_by_example(source_column="pickup_datetime")
    .split_column_by_example(source_column="dropoff_datetime"))
tmp_df.head(5)
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


Nevezze át az oszlopokat által generált `split_column_by_example()` be adjon kifejező nevet.


```python
tmp_df_renamed = (tmp_df
    .rename_columns(column_pairs={
        "pickup_datetime_1": "pickup_date",
        "pickup_datetime_2": "pickup_time",
        "dropoff_datetime_1": "dropoff_date",
        "dropoff_datetime_2": "dropoff_time"
    }))
tmp_df_renamed.head(5)
```

Írja felül `combined_df` az végrehajtott átalakítások, majd hívást `get_profile()` minden átalakítás után megtekintheti a teljes összefoglaló statisztikák.


```python
combined_df = tmp_df_renamed
combined_df.get_profile()
```

## <a name="transform-data"></a>Adatok átalakítása

További a begyűjtés és Gyűjtőtár dátum ossza fel a hét napja, a hónap és a hónap napját. Hét napja lekéréséhez használja a `derive_column_by_example()` függvény. Ezt a funkciót használja például egy tömb objektumainak, amely definiálja a bemeneti adatokat és a kívánt kimeneti paraméterként. A függvény ezután automatikusan meghatározza a kívánt átalakítási műveletet. Begyűjtést és Gyűjtőtár idő oszlopok, ossza fel óra, perc és másodperc használatával a `split_column_by_example()` függvény paraméterek nélkül példa.

Új funkciók hoztak létre, ha törli az eredeti mezők értéke az újonnan létrehozott funkciók `drop_columns()`. Nevezze át az összes többi mező pontos ismertetését.


```python
tmp_df = (combined_df
    .derive_column_by_example(
        source_columns="pickup_date", 
        new_column_name="pickup_weekday", 
        example_data=[("2009-01-04", "Sunday"), ("2013-08-22", "Thursday")]
    )
    .derive_column_by_example(
        source_columns="dropoff_date",
        new_column_name="dropoff_weekday",
        example_data=[("2013-08-22", "Thursday"), ("2013-11-03", "Sunday")]
    )
          
    .split_column_by_example(source_column="pickup_time")
    .split_column_by_example(source_column="dropoff_time")
    # the following two split_column_by_example calls reference the generated column names from the above two calls
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

tmp_df.head(5)
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

A fenti adatokból láthatja, hogy helyesek-e a begyűjtés és Gyűjtőtár dátum és időpont összetevőket a származtatott átalakítások előállítása. Dobja el a `pickup_datetime` és `dropoff_datetime` oszlopok azok már nem szükséges.


```python
tmp_df = tmp_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

A típus következtetésekhez funkciójának használata automatikusan megkeresi a minden mező adattípusát, és a következtetésekhez eredmények megjelenítéséhez.


```python
type_infer = tmp_df.builders.set_column_types()
type_infer.learn()
type_infer
```

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

Az eredmények megfelelő hely az adatok alapján következtetésekhez most már a típusátalakítás az adatfolyamot a alkalmazni.


```python
tmp_df = type_infer.to_dataflow()
tmp_df.get_profile()
```

A csomagolás az adatfolyamot, mielőtt hajtsa végre az adatkészlet két végső szűrők. Helytelen adatpontok kiküszöböléséhez, az adatfolyamot, a rekordok szűrése ahol mind a `cost` és `distance` nagyobb, mint nulla.

```python
tmp_df = tmp_df.filter(dprep.col("distance") > 0)
tmp_df = tmp_df.filter(dprep.col("cost") > 0)
```

Ezen a ponton rendelkezik egy teljes körűen átalakított és előkészített adatfolyam-objektumot a gépi tanulási modellek használata. Az SDK objektum szerializációs funkciót tartalmaz, amely a következőképpen használja.

```python
import os
file_path = os.path.join(os.getcwd(), "dflows.dprep")

dflow_prepared = tmp_df
package = dprep.Package([dflow_prepared])
package.save(file_path)
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje a fájlt `dflows.dprep` (akár helyileg vagy az Azure-jegyzetfüzetekben futtat) az aktuális könyvtárban található, ha nem szeretné folytatni a második rész az oktatóanyag. Ha továbbra is be a második, szüksége lesz a `dflows.dprep` fájlt az aktuális könyvtárban található.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egyik részben meg:

> [!div class="checklist"]
> * A fejlesztési környezet beállítása
> * A betöltött és tisztított adatkészletek
> * Az intelligens átalakítások használt előre jelezni a logika egy példa alapján
> * A machine learning betanítási egyesített és csomagolt adatkészletek

Ez az oktatóanyag-sorozat következő részére, a betanítási adatok használandó készen áll:

> [!div class="nextstepaction"]
> [#2. oktatóanyag: Regressziós modell betanításához.](tutorial-auto-train-models.md)
