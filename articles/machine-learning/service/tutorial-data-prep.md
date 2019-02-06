---
title: 'Regressziós modell oktatóanyag: Adatok előkészítése'
titleSuffix: Azure Machine Learning service
description: Ez az oktatóanyag első részét elsajátíthatja a Pythonban adatok előkészítése az Azure Machine Learning SDK használatával a modellezési regressziós.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cforbe
ms.author: cforbe
ms.reviewer: trbye
ms.date: 02/04/2018
ms.custom: seodec18
ms.openlocfilehash: 7be1bf8c003315fc4dbed449283f7c92850edced
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752041"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Oktatóanyag: Adatok előkészítése az regressziós modellezéshez

Ebben az oktatóanyagban elsajátíthatja, hogyan előkészíti az adatokat az regressziós modellezés az Azure Machine Learning Data Prep SDK használatával. Szűrését, és kombinálja a két különböző NYC taxi adatkészletek különféle átalakításokat futtat.

Ez az oktatóanyag **egy kétrészes sorozat első része**. Miután elvégezte az oktatóanyag-sorozat, akkor is alapján képes előre jelezni taxi belépőt költsége funkciókat az egy modell. Ezek a funkciók közé tartozik a felvételi nap és idő, számától és a felvétel helyére.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre egy Python-környezetet, és csomagok importálásához.
> * Töltse be a különböző mezőneveket két adatkészletet.
> * Adatok eltávolítása a rendellenességek megtisztításához.
> * Adatok átalakítása intelligens átalakítások használatával hozhat létre új funkciókat.
> * Mentse az adatfolyamot objektumot a regressziós modellek használata.

## <a name="prerequisites"></a>Előfeltételek

Ugrás a [a fejlesztési környezet beállítása](#start) olvassa végig a notebook lépéseket, vagy használja az alábbi utasításokat a notebook beszerzése és az Azure notebookok vagy a saját notebook server futtatásához. A jegyzetfüzet futtatásához szüksége lesz:

* Egy Python 3.6-os notebook kiszolgálót a következőkkel:
    * Az Azure Machine Learning Python SDK Adatelőkészítéshez
* Az oktatóanyag notebook

Ezekről az előfeltételekről le az alábbi szakaszok egyikét.

* Használat [Azure notebookok](#azure)
* Használat [saját notebook server](#server)

### <a name="azure"></a>Az Azure notebookok használata: Ingyenes Jupyter notebookok a felhőben

Nem kell mást Azure notebookok használatának megkezdéséhez. A [az Azure Machine Learning Data Prep SDK Pythonhoz készült](https://aka.ms/data-prep-sdk) már telepítve és konfigurálva van az Ön számára [Azure notebookok](https://notebooks.azure.com/). A telepítés és a jövőbeli frissítések automatikusan felügyelt Azure-szolgáltatások használatával.

Után kövesse az alábbi lépéseket, futtassa a **oktatóanyagok/regressziós-1-adatok – prep.ipynb** jegyzetfüzetet a **bevezetés** projekt.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

### <a name="server"></a>A saját Jupyter notebook server használata

Ezek a lépések használatával hozzon létre egy helyi Jupyter Notebook kiszolgálót a számítógépen.  Miután végrehajtotta a lépéseket, futtassa a **oktatóanyagok/regressziós-1-adatok – prep.ipynb** notebookot.

1. Végezze el a [Azure Machine Learning Python rövid](quickstart-create-workspace-with-python.md) Miniconda környezet kialakításához.  Nyugodtan hagyja ki a **hozzon létre egy munkaterületet** szakaszt, ha kívánja, de szükség lesz rá a [2. rész](tutorial-auto-train-models.md) az oktatóanyag-sorozat.
1. Az adatok előkészítő SDK telepítése az a környezet használatával `pip install azureml-dataprep`.
1. Klónozza [a GitHub-adattárat](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.

    ```shell
    jupyter notebook

## <a name="start"></a>Set up your development environment

All the setup for your development work can be accomplished in a Python notebook. Setup includes the following actions:

* Install the SDK
* Import Python packages

### Install and import packages

Use the following to install necessary packages if you don't already have them.

```shell
pip install azureml-dataprep
```

Importálja az SDK-t.

```python
import azureml.dataprep as dprep
```

## <a name="load-data"></a>Adatok betöltése

Töltse le a két különböző NYC taxi adatkészletek adatfolyamot objektumokba. Az adatkészletek eltérő mezővel rendelkezik. A `auto_read_file()` metódus automatikusan felismeri a bemeneti fájl típusa.

```python
from IPython.display import display
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df_raw = dprep.read_csv(path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file automatically identifies and parses the file type, which is useful when you don't know the file type.
yellow_df_raw = dprep.auto_read_file(path=yellow_path)

display(green_df_raw.head(5))
display(yellow_df_raw.head(5))
```

A `Dataflow` objektum dataframe hasonló, és ráérősen értékeli ki, nem módosítható elküldhessenek adatokon jelöli. Műveletek meghívása a különböző Adatátalakítási és szűrés által választható módszerek adhat hozzá. A művelet hozzáadásának eredményét egy `Dataflow` mindig van egy új `Dataflow` objektum.

## <a name="cleanse-data"></a>Adatok megtisztítása

Most töltse fel néhány változót a helyi átalakítások összes adatfolyamot a alkalmazni. A `drop_if_all_null` változójával rekordok törléséhez, az összes mező kitöltése null értékű. A `useful_columns` változó tárolja az oszlopok leírása való minden egyes adatfolyamot tömbjét.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Először dolgozik, a zöld taxi adatokkal kombinálva, a sárga i taxik adatait érvényes alakzat alakítja. Hívja a `replace_na()`, `drop_nulls()`, és `keep_columns()` funkciók segítségével a helyi átalakítása létrehozott változókat. Ezenkívül nevezze át az adathalmaz megfelelően a nevek az összes oszlopot a `useful_columns` változó.


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
green_df.head(5)
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

Futtassa az Adatátalakítási lépéseket a sárga i taxik adatait. Ezek a függvények győződjön meg arról, hogy null adat törlődik az adatkészlet, amelyek segítségével növelheti a machine learning pontosság modellezésére.

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
yellow_df.head(5)
```

Hívja a `append_rows()` funkciót, a zöld taxi adatok hozzáfűzése a sárga i taxik adatait. Új kombinált dataframe jön létre.

```python
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>A konvertálás típusok és -szűrő

Vizsgálja meg a begyűjtés és Gyűjtőtár koordinálja az összefoglaló statisztikák hogyan oszlik el az adatok megtekintéséhez. Először határozzon meg egy `TypeConverter` módosítani a szélesség és hosszúság mezők decimális típusú objektum. Ezután hívja a `keep_columns()` kimeneti korlátozhatja a csak a szélesség és hosszúság mezők függvényt, és ezután hívja meg a `get_profile()` függvény. Az ezen függvényhívásokat az adatfolyamot, hogy megmutassam a a szél/hosszú mezők, ami jó hír kiértékelheti, hogy hiányzó vagy Hatókörön kívüli koordinátákat sűrített nézetének létrehozása.


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
      <th>Typo</th>
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



Az összefoglaló statisztikák kimenetében láthatja létezik koordináták és a nem található (Ez határozza meg a szubjektív analysis) New York City koordinátái hiányzik. Szűrje ki az városa szegély kívül eső helyeken koordinátáit. Lánc az Oszlopszűrő belüli parancsokat a `filter()` funkciót, és adja meg az egyes mezőkhöz tartozó minimális és maximális határán. Ezután hívja meg a `get_profile()` függvény az átalakítás megerősítése érdekében.


```python
latlong_filtered_df = (combined_df
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
latlong_filtered_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Typo</th>
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

Tekintse meg a data-profilt a a `store_forward` oszlop. Ez a mező egy logikai jelzőt, amely akkor `Y` amikor a taxi nincs út után a kapcsolat a kiszolgálóval, és ezért kellett az útadatok tárolása a memóriában, és később továbbítja azt a kiszolgáló használatakor.


```python
latlong_filtered_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Typo</th>
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



Figyelje meg, hogy az adatprofil a kimenetet a `store_forward` az oszlopban látható, hogy az adatok inkonzisztens, és találkozhat hiányzó vagy a null értékeket. Használja a `replace()` és `fill_nulls()` ezeket az értékeket cserélje le a karakterlánc "N" függvények:


```python
replaced_stfor_vals_df = latlong_filtered_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Hajtsa végre a `replace` működnek a `distance` mező. A függvény újraformázza távolság értékek, amelyek nem megfelelően van-e jelölve, `.00`, és bármely nullák nullákkal tölti ki. Konvertálja a `distance` mező numerikus formátumban. Ezek helytelen adatok pontok valószínűleg anomolies a taxi kabinetfájlok adatok gyűjtése rendszerben.


```python
replaced_distance_vals_df = replaced_stfor_vals_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
replaced_distance_vals_df = replaced_distance_vals_df.to_number(["distance"])
```

A begyűjtés és dropoff dátum/idő értékek felosztása a megfelelő dátum és idő oszlopokra. Használja a `split_column_by_example()` , hogy a split függvény. Ebben az esetben, az opcionális `example` paraméterében a `split_column_by_example()` funkció ki van hagyva. Ezért a függvény automatikusan meghatározza, hogy hol található adatok alapján felosztani.


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

Nevezze át az oszlopokat által generált a `split_column_by_example()` adjon kifejező nevet használni kívánt függvény.

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

Hívja a `get_profile()` függvényt, hogy a teljes összefoglaló statisztikáit, amikor az összes adattisztítási műveleteket.

```python
renamed_col_df.get_profile()
```

## <a name="transform-data"></a>Adatok átalakítása

Ossza fel a begyűjtés és dropoff dátum további, a nap, hét, nap, hónap és havi értékeket. A nap, hét értékének lekéréséhez használja a `derive_column_by_example()` függvény. A függvény példa objektumok, amelyek meghatározzák a bemeneti adatokat, és az elsődleges kimenet egy tömb paramétert vesz igénybe. A függvény automatikusan határozza meg a kívánt átalakítást. A begyűjtés és dropoff idő oszlopok felosztása az idő az óra, perc és másodperc használatával a `split_column_by_example()` függvény paraméterek nélkül példa.

Miután az új funkciók generál, a `drop_columns()` függvény az eredeti mezők törölni, mivel az újonnan létrehozott szolgáltatások használata javasolt. Nevezze át a többi jelentéssel bíró leírások használandó mezőt.

Ezzel a módszerrel új időalapú funkciók létrehozása az adatok átalakítása javul a gépi tanulási modell pontosságát. Például egy új szolgáltatás létrehozása, a hét napja segítséget nyújt a hét napja és a taxi diszkont ár közötti kapcsolatot hozhat létre, amelyek gyakran több nagy kereslet miatt a hét bizonyos napjain drága.


```python
transformed_features_df = (renamed_col_df
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

Figyelje meg, hogy helyesek-e az adatok azt mutatja, hogy a begyűjtés és dropoff dátum és időpont összetevőket előállított a származtatott átalakításokat. Dobja el a `pickup_datetime` és `dropoff_datetime` oszlopok, mert már nem szükséges (részletes időpontja szolgáltatásai, óra, perc és másodperc hasznosak több modell betanítása).


```python
processed_df = transformed_features_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

A típus következtetésekhez funkciójának használata automatikusan megkeresi a minden mező adattípusát, és a következtetésekhez eredmények megjelenítéséhez.


```python
type_infer = processed_df.builders.set_column_types()
type_infer.learn()
type_infer
```

Az eredményül kapott kimenetének `type_infer` következő.

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

Következtetésekhez eredmények meg helyes-e az adatok alapján. Most már a alkalmazni az adatfolyamot, a típusátalakítás.


```python
type_converted_df = type_infer.to_dataflow()
type_converted_df.get_profile()
```

Mielőtt az adatfolyamot becsomagolását, hogy az adatkészlet futtassa két végső szűrők. Helytelenül rögzített adatpontok kiküszöböléséhez, szűrheti a rögzíti az adatfolyamot, mind a `cost` és `distance` változók értékei csak nullánál nagyobb. Ebben a lépésben jelentősen növelhető a gépi tanulási modell pontosságát, mivel adatpontok esetében költség vagy távolság jelölik fő kiugró értékek, amelyek a throw ki előrejelzés pontosságát.

```python
final_df = type_converted_df.filter(dprep.col("distance") > 0)
final_df = final_df.filter(dprep.col("cost") > 0)
```

Most már rendelkezik egy teljes körűen átalakított és előkészített adatfolyam-objektumot a gépi tanulási modellek használata. Az SDK objektum szerializációs funkciót tartalmaz, amely használja az alábbi kódban látható módon.

```python
import os
file_path = os.path.join(os.getcwd(), "dflows.dprep")

package = dprep.Package([final_df])
package.save(file_path)
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag második részében folytatásához van szüksége a **dflows.dprep** fájlt az aktuális könyvtárban található.

Ha nem folytatja a második, törölje a **dflows.dprep** fájlt az aktuális könyvtárban található. Törli ezt a fájlt, hogy helyileg futtatja a végrehajtási, vagy a [Azure notebookok](https://notebooks.azure.com/).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egyik részben meg:

> [!div class="checklist"]
> * A fejlesztési környezet beállítása.
> * A betöltött és tisztított adatkészletek.
> * Az intelligens átalakítások segítségével előre jelezni a logika egy példa alapján.
> * Machine learning betanítási egyesített és csomagolt adathalmaz.

Készen áll a betanítási adatok használatára az oktatóanyag második részében:

> [!div class="nextstepaction"]
> [Oktatóanyag (második): A regressziós modell betanítása](tutorial-auto-train-models.md)
