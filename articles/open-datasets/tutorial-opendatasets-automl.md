---
title: 'Oktatóanyag: Automatikus gépi tanulási modell gazdagítása'
titleSuffix: Azure Open Datasets
description: Ismerje meg, hogyan hasznosíthatja az Azure Open-adatkészletek kényelmét, valamint a Azure Machine Learning szolgáltatás hatékonyságát, hogy regressziós modellt hozzon létre a New York-i taxi díjszabásának előrejelzéséhez.
services: open-datasets
ms.service: open-datasets
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 05/02/2019
ms.openlocfilehash: 0218221426351b0b508021fc74693557f200b4e8
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309264"
---
# <a name="tutorial-build-a-regression-model-with-automated-machine-learning-and-open-datasets"></a>Oktatóanyag: Regressziós modell létrehozása automatizált gépi tanulással és nyitott adatkészletekkel

Ebben az oktatóanyagban kihasználja az Azure Open-adatkészletek kényelmét, valamint a Azure Machine Learning szolgáltatás hatékonyságát egy regressziós modell létrehozásához a New York-i taxi díjszabásának előrejelzéséhez. Egyszerűen letöltheti a nyilvánosan elérhető taxit, az ünnepnapokat és az időjárási adatgyűjtést, és konfigurálhat egy automatizált gépi tanulási kísérletet Azure Machine Learning szolgáltatás használatával. Ez a folyamat fogadja az oktatási és konfigurációs beállításokat, és automatikusan megismétli a különböző funkció-normalizálás/szabványosítási módszerek, modellek és hiperparaméter-beállítások kombinációit, hogy a legjobb modellt lehessen megérkezni.

Ez az oktatóanyag a következő feladatokat ismerteti:

- Az Azure Machine Learning szolgáltatás munkaterület konfigurálása
- Helyi Python-környezet beállítása
- Az Azure Open-adatkészletek használatával elérheti, átalakíthatja és csatlakoztathatja az adatokat
- Automatizált gépi tanulási regressziós modell betanítása
- Modell pontosságának kiszámítása

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következő előfeltételek szükségesek.

* Egy Azure Machine Learning szolgáltatás munkaterülete
* Python 3,6-környezet

### <a name="create-a-workspace"></a>Munkaterület létrehozása

Ha még nem rendelkezik ilyennel, kövesse az [utasításokat](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-workspace) , és hozzon létre egy munkaterületet a Azure Portalon keresztül. A létrehozás után jegyezze fel a munkaterület nevét, az erőforráscsoport nevét és az előfizetés AZONOSÍTÓját.

### <a name="create-a-python-environment"></a>Python-környezet létrehozása

Ez a példa egy Jupyter-jegyzetfüzetekkel rendelkező anaconda-környezetet használ, de ezt a kódot bármely 3.6. x környezetben, bármilyen szövegszerkesztővel vagy IDE-vel futtathatja. Új fejlesztési környezet létrehozásához kövesse az alábbi lépéseket.

1. Ha még nem tette meg, [töltse le](https://www.anaconda.com/distribution/) és telepítse a anacondat, és válassza a **Python 3,7 verziót**.
1. Nyisson meg egy Anaconda-parancssort, és hozzon létre egy új környezetet. A környezet létrehozása több percet is igénybe vehet, miközben az összetevők és csomagok letöltődnek.
    ```
    conda create -n tutorialenv python=3.6.5
    ```
1. Aktiválja a környezetet.
    ```
    conda activate tutorialenv
    ```
1. Környezet-specifikus IPython-kernelek engedélyezése.
    ```
    conda install notebook ipykernel
    ```
1. Hozza létre a kernelt.
    ```
    ipython kernel install --user
    ```
1. Telepítse az oktatóanyaghoz szükséges csomagokat. Ezek a csomagok nagy méretűek, és 5-10 percet vesznek igénybe.
    ```
    pip install azureml-sdk[automl] azureml-opendatasets
    ```
1. Indítsa el a notebook kernelt a környezetből.
    ```
    jupyter notebook
    ```

A lépések elvégzése után klónozott a [megnyitott adatkészletek notebook](https://github.com/Azure/OpenDatasetsNotebooks) -tárházát, és megnyitja az **oktatóanyagok/taxi-automl/01-tutorial-opendatasets-automl. ipynb** notebookot a futtatásához.

## <a name="download-and-prepare-data"></a>Az adatgyűjtés letöltése és előkészítése

Importálja a szükséges csomagokat. A nyílt adatkészletek csomag az egyes adatforrásokat jelképező`NycTlcGreen` osztályt tartalmaz (például) a letöltés előtt egyszerűen szűrheti a dátum-paramétereket.


```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Először hozzon létre egy dataframe a taxi-adattároláshoz. Ha nem Spark-környezetben dolgozik, a nyílt adatkészletek csak egy hónapos adat letöltését teszik lehetővé bizonyos osztályokkal, hogy elkerülje `MemoryError` a nagyméretű adatkészleteket. A taxi-adatok egy évig való letöltéséhez egyszerre egy hónapot iteratív le, és mielőtt hozzáfűzi azt az `green_taxi_df` egyes hónapok véletlenszerűen vett 2000-rekordjaihoz, hogy elkerülje a dataframe felduzzadása. Ezután tekintse meg az adatmegjelenítést.

>[!NOTE]
> A nyitott adatkészletek tükröző osztályokkal rendelkeznek a Spark-környezetekben való munkához, ahol az adatméret és a memória nem jelent gondot.

```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016", "%m/%d/%Y")
end = datetime.strptime("1/31/2016", "%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
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
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>paymentType</th>
      <th>fareAmount</th>
      <th>extra</th>
      <th>mtaTax</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0,98</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>...</td>
      <td>2.0</td>
      <td>7.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>8.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3,08</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.979973</td>
      <td>40,677071</td>
      <td>-73.934349</td>
      <td>...</td>
      <td>2.0</td>
      <td>11,5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.863045</td>
      <td>40,882923</td>
      <td>-73.839836</td>
      <td>...</td>
      <td>2.0</td>
      <td>12.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2,87</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>...</td>
      <td>1.0</td>
      <td>12.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0,50</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.942589</td>
      <td>40,841423</td>
      <td>-73.943672</td>
      <td>...</td>
      <td>2.0</td>
      <td>4.5</td>
      <td>0.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>5.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.830894</td>
      <td>40.759434</td>
      <td>-73.842422</td>
      <td>...</td>
      <td>2.0</td>
      <td>10.5</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1,93</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.927109</td>
      <td>40.762848</td>
      <td>-73.909302</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0,80</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.881195</td>
      <td>40,741779</td>
      <td>– 73,872086</td>
      <td>...</td>
      <td>2.0</td>
      <td>6.5</td>
      <td>0.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1,04</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.954376</td>
      <td>40,805729</td>
      <td>-73.939117</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11,3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2,82</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,845200</td>
      <td>40.722134</td>
      <td>– 73,810638</td>
      <td>...</td>
      <td>1.0</td>
      <td>13.0</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16,3</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>10 sor × 23 oszlop</p>
</div>



Most, hogy a kezdeti adatok betöltődik, Definiáljon egy függvényt, amely különböző időalapú szolgáltatásokat hoz létre a pickup datetime mezőből. Ez új mezőket hoz létre a hónap, a hónap napja, a hét napja és a nap órájában, és lehetővé teszi a modell időalapú szezonális felszámítását. A függvény egy statikus funkciót is feltesz az országkód számára az üdülési adathoz való csatlakozáshoz. Használja a `build_time_features()` függvényt a dataframe, hogy a iteratív alkalmazza a függvényt a taxi összes sorára. `apply()`


```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour
    country_code = "US"

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day, country_code))


green_taxi_df[["month_num", "day_of_month", "day_of_week", "hour_of_day", "country_code"]
              ] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
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
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0,98</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>8.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3,08</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.979973</td>
      <td>40,677071</td>
      <td>-73.934349</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.863045</td>
      <td>40,882923</td>
      <td>-73.839836</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2,87</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0,50</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.942589</td>
      <td>40,841423</td>
      <td>-73.943672</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>5.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.830894</td>
      <td>40.759434</td>
      <td>-73.842422</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>29</td>
      <td>4</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1,93</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.927109</td>
      <td>40.762848</td>
      <td>-73.909302</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>14</td>
      <td>3</td>
      <td>0</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0,80</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.881195</td>
      <td>40,741779</td>
      <td>– 73,872086</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>9</td>
      <td>5</td>
      <td>14</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1,04</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>-73.954376</td>
      <td>40,805729</td>
      <td>-73.939117</td>
      <td>...</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>25</td>
      <td>0</td>
      <td>18</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2,82</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,845200</td>
      <td>40.722134</td>
      <td>– 73,810638</td>
      <td>...</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>24</td>
      <td>6</td>
      <td>20</td>
      <td>USA</td>
    </tr>
  </tbody>
</table>
<p>10 sor × 28 oszlop</p>
</div>

Távolítson el néhány olyan oszlopot, amelyre nem lesz szüksége modellezéshez vagy további funkciók létrehozásához. Nevezze át a felvételi idő időmezőjét, és az időpontot is konvertálja éjfélre a használatával `pandas.Series.dt.normalize`. Ezt minden alkalommal megteheti, hogy a DateTime összetevő később is használható legyen kulcsként, amikor az adatkészleteket napi szinten, részletességgel csatlakoztatja.

```python
columns_to_remove = ["lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                     ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df = green_taxi_df.rename(
    columns={"lpepPickupDatetime": "datetime"})
green_taxi_df["datetime"] = green_taxi_df["datetime"].dt.normalize()
green_taxi_df.head(5)
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
      <th>vendorID</th>
      <th>datetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0,98</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>40.761257</td>
      <td>8.8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3,08</td>
      <td>-73.979973</td>
      <td>40,677071</td>
      <td>-73.934349</td>
      <td>40.671654</td>
      <td>12,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73.863045</td>
      <td>40,882923</td>
      <td>-73.839836</td>
      <td>40,868336</td>
      <td>13,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2,87</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>40,694248</td>
      <td>13,8</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0,50</td>
      <td>-73.942589</td>
      <td>40,841423</td>
      <td>-73.943672</td>
      <td>40,834396</td>
      <td>5.3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>USA</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-holiday-data"></a>Gazdagítsa az üdülési adatgyűjtést

Most, hogy már letöltötte a taxi-adatletöltést, és durván előkészített, adja hozzá az üdülési adatkészletet további funkciókként. A vakációra jellemző funkciók segítik a modell pontosságát, mivel a nagy ünnepek olyan időpontok, amikor a taxi igénye jelentősen megnő, és a kínálat korlátozott lesz. Az üdülési adatkészlet viszonylag kicsi, ezért a teljes készletet az `PublicHolidays` osztály konstruktorának használatával, szűrési paraméterek nélkül kell beolvasni. A formátum vizsgálatához tekintse meg az adatelemzést.

```python
from azureml.opendatasets import PublicHolidays
# call default constructor to download full dataset
holidays_df = PublicHolidays().to_pandas_dataframe()
holidays_df.head(5)
```

    ActivityStarted, to_pandas_dataframe
    Looking for parquet files...
    Reading them into Pandas dataframe...
    Reading Processed/part-00000-tid-1353805596865908763-9ee4e95b-0d55-4292-addd-a0e19d7c32cb-3559-c000.snappy.parquet under container holidaydatacontainer
    Done.
    ActivityCompleted: Activity=to_pandas_dataframe, HowEnded=Success, Duration=1799.89 [ms]

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
      <th>countryOrRegion</th>
      <th>holidayName</th>
      <th>isPaidTimeOff</th>
      <th>countryRegionCode</th>
      <th>normalizeHolidayName</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>40688</th>
      <td>Albánia</td>
      <td>Új év napja</td>
      <td>Nincsenek</td>
      <td>AL</td>
      <td>Új év napja</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40689</th>
      <td>Algéria</td>
      <td>Új év napja</td>
      <td>Nincsenek</td>
      <td>DZ</td>
      <td>Új év napja</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40690</th>
      <td>Andorra</td>
      <td>Új év napja</td>
      <td>Nincsenek</td>
      <td>AD</td>
      <td>Új év napja</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40691</th>
      <td>Angola</td>
      <td>Új év napja</td>
      <td>Nincsenek</td>
      <td>AO</td>
      <td>Új év napja</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40692</th>
      <td>Argentína</td>
      <td>Új év napja</td>
      <td>Nincsenek</td>
      <td>AR</td>
      <td>Új év napja</td>
      <td>2008-01-01</td>
    </tr>
  </tbody>
</table>
</div>



Nevezze át `countryRegionCode` az `date` és az oszlopokat, hogy azok megfeleljenek a taxi adataiban található megfelelő mezőnevek és az idő normalizálása érdekében, hogy kulcsként lehessen használni. Ezután csatlakoztassa az üdülési adatokhoz a taxi-adatokhoz egy bal oldali illesztést a pandák `merge()` függvény használatával. Ez megőrzi a összes rekordját `green_taxi_df`, de a hozzá tartozó, a megfelelő `datetime` , és `country_code`a jelen esetben mindig `"US"`az adott időponthoz tartozó adatokat is hozzáadja. Tekintse meg az adatelemzést, és győződjön meg arról, hogy helyesen lettek-e egyesítve.

```python
holidays_df = holidays_df.rename(
    columns={"countryRegionCode": "country_code", "date": "datetime"})
holidays_df["datetime"] = holidays_df["datetime"].dt.normalize()
holidays_df.pop("countryOrRegion")
holidays_df.pop("holidayName")

taxi_holidays_df = pd.merge(green_taxi_df, holidays_df, how="left", on=[
                            "datetime", "country_code"])
taxi_holidays_df.head(5)
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
      <th>vendorID</th>
      <th>datetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
      <th>isPaidTimeOff</th>
      <th>normalizeHolidayName</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0,98</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>40.761257</td>
      <td>8.8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>USA</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3,08</td>
      <td>-73.979973</td>
      <td>40,677071</td>
      <td>-73.934349</td>
      <td>40.671654</td>
      <td>12,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>USA</td>
      <td>True</td>
      <td>Új év napja</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73.863045</td>
      <td>40,882923</td>
      <td>-73.839836</td>
      <td>40,868336</td>
      <td>13,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>USA</td>
      <td>True</td>
      <td>Új év napja</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2,87</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>40,694248</td>
      <td>13,8</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>USA</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0,50</td>
      <td>-73.942589</td>
      <td>40,841423</td>
      <td>-73.943672</td>
      <td>40,834396</td>
      <td>5.3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>USA</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-weather-data"></a>Gazdagítás időjárási adattal

Most fűzze hozzá a NOAA Surface időjárási adatszolgáltatásait a Taxihoz és az üdülési szolgáltatáshoz. Használjon hasonló módszert az időjárási adatgyűjtéshez, ha egyszerre egy hónapot tölt le iteratív. Emellett a `cols` paramétert a letölteni kívánt oszlopok szűrésére szolgáló karakterláncok tömbje alapján is megadhatja. Ez egy nagyon nagy adatkészlet, amely a világ minden tájáról származó időjárási felületi adatokat tartalmaz, ezért az egyes hónapok hozzáfűzését megelőzően a dataframe `query()` függvény használatával szűrheti a szél/hosszú mezőket a NYC közelében. Ezzel biztosítható, `weather_df` hogy az ne legyen túl nagy.

```python
from azureml.opendatasets import NoaaIsdWeather

weather_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016", "%m/%d/%Y")
end = datetime.strptime("1/31/2016", "%m/%d/%Y")

for sample_month in range(12):
    tmp_df = NoaaIsdWeather(cols=["temperature", "precipTime", "precipDepth", "snowDepth"], start_date=start + relativedelta(months=sample_month), end_date=end + relativedelta(months=sample_month))\
        .to_pandas_dataframe()
    print("--weather downloaded--")

    # filter out coordinates not in NYC to conserve memory
    tmp_df = tmp_df.query("latitude>=40.53 and latitude<=40.88")
    tmp_df = tmp_df.query("longitude>=-74.09 and longitude<=-73.72")
    print("--filtered coordinates--")
    weather_df = weather_df.append(tmp_df)

weather_df.head(10)
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
      <th>wban</th>
      <th>precipTime</th>
      <th>snowDepth</th>
      <th>hőmérséklet</th>
      <th>Szélesség</th>
      <th>precipDepth</th>
      <th>Hosszúság</th>
      <th>datetime</th>
      <th>USAF</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1754979</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>7.2</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>– 74,061</td>
      <td>2016-01-01 00:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754980</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>– 74,061</td>
      <td>2016-01-01 01:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754981</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>– 74,061</td>
      <td>2016-01-01 02:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754982</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.1</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>– 74,061</td>
      <td>2016-01-01 03:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754983</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>– 74,061</td>
      <td>2016-01-01 04:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754984</th>
      <td>94741</td>
      <td>24,0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40,85</td>
      <td>5.0</td>
      <td>– 74,061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754985</th>
      <td>94741</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40,85</td>
      <td>NaN</td>
      <td>– 74,061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754986</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>– 74,061</td>
      <td>2016-01-01 05:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754987</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>– 74,061</td>
      <td>2016-01-01 06:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754988</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>– 74,061</td>
      <td>2016-01-01 07:51:00</td>
      <td>725025</td>
    </tr>
  </tbody>
</table>
</div>

Ismét hívja `pandas.Series.dt.normalize` `datetime` meg a mezőt az időjárási adatsorokban, hogy az `taxi_holidays_df`megfeleljen a (z) értékének. Törölje a szükségtelen oszlopokat, és szűrje ki azokat a rekordokat, ahol `NaN`a hőmérséklet van.

A következő csoport az időjárási adatokat, hogy napi összesített időjárási értékeket lehessen. Definiáljon egy nevű `aggregations` dict, amely meghatározza, hogyan összesíthető az egyes mezők napi szinten. `precipTime` `precipDepth` A és a`temperature` , a és a esetében pedig a napi maximumot használja. `snowDepth` Használja a `groupby()` függvényt az összesítésekkel együtt az adat csoportosításához. Tekintse meg az adatok előnézetét, és győződjön meg róla, hogy naponta egy rekord van.

```python
weather_df["datetime"] = weather_df["datetime"].dt.normalize()
weather_df.pop("usaf")
weather_df.pop("wban")
weather_df.pop("longitude")
weather_df.pop("latitude")

# filter out NaN
weather_df = weather_df.query("temperature==temperature")

# group by datetime
aggregations = {"snowDepth": "mean", "precipTime": "max",
                "temperature": "mean", "precipDepth": "max"}
weather_df_grouped = weather_df.groupby("datetime").agg(aggregations)
weather_df_grouped.head(10)
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
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>hőmérséklet</th>
      <th>precipDepth</th>
    </tr>
    <tr>
      <th>datetime</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-01-01</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>5,197345</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-02</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>2.567857</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-03</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>3.846429</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-04</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>0,123894</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-05</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>– 7,206250</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-06</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>– 0,896396</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-07</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>3.180645</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-08</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>4.384091</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-09</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>6,710274</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>2016-01-10</th>
      <td>NaN</td>
      <td>24,0</td>
      <td>10,943655</td>
      <td>254,0</td>
    </tr>
  </tbody>
</table>
</div>

> [!NOTE]
> Az oktatóanyagban szereplő példák a Panda functions és az egyéni összesítések használatával egyesítik az adatokat, de a nyílt adatkészletek SDK olyan osztályokat tartalmaz, amelyekkel egyszerűen egyesíthető és bővíthetők az adatkészletek. Tekintse meg a [jegyzetfüzetben](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) a tervezési mintákhoz tartozó példákat.

### <a name="cleanse-data"></a>Adatok megtisztítása

Egyesítse a taxit és az új időjárási adatszolgáltatással készített ünnepnapokat. Ezúttal csak a `datetime` kulcsra van szükség, és ismét végre kell hajtania az adatok bal oldali illesztését. Futtassa a `describe()` függvényt az új dataframe, és tekintse meg az egyes mezők összegző statisztikáit.

```python
taxi_holidays_weather_df = pd.merge(
    taxi_holidays_df, weather_df_grouped, how="left", on=["datetime"])
taxi_holidays_weather_df.describe()
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
      <th>vendorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>hőmérséklet</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>1671.000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
    </tr>
    <tr>
      <th>középérték</th>
      <td>1.786583</td>
      <td>6,576208</td>
      <td>1,582588</td>
      <td>20,505491</td>
      <td>84,936413</td>
      <td>-36.232825</td>
      <td>21.723144</td>
      <td>7,863018</td>
      <td>6,500000</td>
      <td>15.113708</td>
      <td>3,240250</td>
      <td>13.664125</td>
      <td>11.764141</td>
      <td>13,258875</td>
      <td>13,903524</td>
      <td>1056,644458</td>
    </tr>
    <tr>
      <th>Standard</th>
      <td>0,409728</td>
      <td>9,086857</td>
      <td>2.418177</td>
      <td>108,847821</td>
      <td>70,678506</td>
      <td>37,650276</td>
      <td>19.104384</td>
      <td>10,648766</td>
      <td>3,452124</td>
      <td>8,485155</td>
      <td>1,956895</td>
      <td>6,650676</td>
      <td>15,651884</td>
      <td>10,339720</td>
      <td>9.474396</td>
      <td>2815.592754</td>
    </tr>
    <tr>
      <th>perc</th>
      <td>1,000000</td>
      <td>– 60,000000</td>
      <td>– 1,000000</td>
      <td>-74.179482</td>
      <td>0.000000</td>
      <td>-74.190704</td>
      <td>0.000000</td>
      <td>– 52,800000</td>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>– 13,379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>0,330000</td>
      <td>– 73,946680</td>
      <td>40.717712</td>
      <td>-73.945429</td>
      <td>1.770000</td>
      <td>1,000000</td>
      <td>3.750000</td>
      <td>8,000000</td>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>6,620773</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2,000000</td>
      <td>4,000000</td>
      <td>0,830000</td>
      <td>1,500000</td>
      <td>40,814129</td>
      <td>0,500000</td>
      <td>21,495000</td>
      <td>2,000000</td>
      <td>6,500000</td>
      <td>15,000000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>4,428571</td>
      <td>6,000000</td>
      <td>13,090753</td>
      <td>10,000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>1,870000</td>
      <td>89,000000</td>
      <td>129,000000</td>
      <td>1,000000</td>
      <td>40.746146</td>
      <td>11,300000</td>
      <td>9,250000</td>
      <td>22,000000</td>
      <td>5,000000</td>
      <td>19,000000</td>
      <td>12,722222</td>
      <td>24,000000</td>
      <td>22,944737</td>
      <td>132,000000</td>
    </tr>
    <tr>
      <th>max.</th>
      <td>2,000000</td>
      <td>460,000000</td>
      <td>51,950000</td>
      <td>265,000000</td>
      <td>265,000000</td>
      <td>6,000000</td>
      <td>58,600000</td>
      <td>498,000000</td>
      <td>12,000000</td>
      <td>30,000000</td>
      <td>6,000000</td>
      <td>23,000000</td>
      <td>67,090909</td>
      <td>24,000000</td>
      <td>31,303665</td>
      <td>9999,000000</td>
    </tr>
  </tbody>
</table>
</div>

Az összegző statisztikában láthatja, hogy több olyan mező van, amely kiugró vagy értékkel rendelkezik, ami csökkenti a modell pontosságát. Először szűrje a Lat/Long mezőket úgy, hogy az időjárási adat szűréséhez használt azonos határokon belül legyenek. A `tripDistance` mezőnek van néhány rossz adathalmaza, mert a minimális érték negatív. A `passengerCount` mező értéke helytelen, és a maximális érték 210 utas. Végül a `totalAmount` mező negatív értékeket tartalmaz, amelyek nem ésszerűek a modell kontextusában.

Szűrje ki ezeket a rendellenességeket a Query functions használatával, majd távolítsa el az utolsó néhány oszlopot, amely nem szükséges a betanításhoz.

```python
final_df = taxi_holidays_weather_df.query(
    "pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query(
    "pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>0 and tripDistance<75")
final_df = final_df.query("passengerCount>0 and passengerCount<100")
final_df = final_df.query("totalAmount>0")

columns_to_remove_for_training = ["datetime", "pickupLongitude",
                                  "pickupLatitude", "dropoffLongitude", "dropoffLatitude", "country_code"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Hívja `describe()` meg újra az adatvesztést, hogy a takarítás a várt módon működött. Most már készen áll a gépi tanulási modell betanításához használt, a taxi, a nyaralás és az időjárási idő előkészítésére.

```python
final_df.describe()
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
      <th>vendorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>hőmérséklet</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>1490,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
    </tr>
    <tr>
      <th>középérték</th>
      <td>1.786910</td>
      <td>1,343476</td>
      <td>2,848488</td>
      <td>14,689039</td>
      <td>3,499788</td>
      <td>14,948916</td>
      <td>3,234254</td>
      <td>13,647344</td>
      <td>12,508581</td>
      <td>11.855929</td>
      <td>10,301433</td>
      <td>208.432384</td>
    </tr>
    <tr>
      <th>Standard</th>
      <td>0,409508</td>
      <td>1,001232</td>
      <td>2.895960</td>
      <td>10,289832</td>
      <td>1.707865</td>
      <td>8.442438</td>
      <td>1,958477</td>
      <td>6.640280</td>
      <td>16,203195</td>
      <td>10,125701</td>
      <td>8,553512</td>
      <td>1284.892832</td>
    </tr>
    <tr>
      <th>perc</th>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0,010000</td>
      <td>3,300000</td>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>– 13,379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>1,070000</td>
      <td>8.160000</td>
      <td>2,000000</td>
      <td>8,000000</td>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>3,504580</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>1,900000</td>
      <td>11,300000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>4,250000</td>
      <td>6,000000</td>
      <td>10,168182</td>
      <td>3,000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>3,550000</td>
      <td>17,800000</td>
      <td>5,000000</td>
      <td>22,000000</td>
      <td>5,000000</td>
      <td>19,000000</td>
      <td>15,647059</td>
      <td>24,000000</td>
      <td>16,966923</td>
      <td>41,000000</td>
    </tr>
    <tr>
      <th>max.</th>
      <td>2,000000</td>
      <td>6,000000</td>
      <td>51,950000</td>
      <td>150,300000</td>
      <td>6,000000</td>
      <td>30,000000</td>
      <td>6,000000</td>
      <td>23,000000</td>
      <td>67,090909</td>
      <td>24,000000</td>
      <td>26,524107</td>
      <td>9999,000000</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="train-a-model"></a>Modell betanítása

Most az előkészített adattípust használja egy automatizált gépi tanulási modell betanításához. Először a szolgáltatások ( `final_df` X értékek) és a címkék (y érték) felosztásával kezdje el, amely ebben a modellben a taxi viteldíjának díja.

```python
y_df = final_df.pop("totalAmount")
x_df = final_df
```

Most az adat kiosztását betanítási és tesztelési csoportokra bontja `train_test_split()` a `scikit-learn` könyvtárban található függvény használatával. A `test_size` paraméter határozza meg az adatok tesztelését lefoglalni aránya. A `random_state` paraméter beállítja a magot a véletlenszám-generátorra, így a vonat-teszt felosztás determinisztikus.


```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    x_df, y_df, test_size=0.2, random_state=222)
```

### <a name="load-workspace-and-configure-experiment"></a>Munkaterület betöltése és kísérlet konfigurálása

Töltse be a Azure Machine learning szolgáltatás munkaterületét az előfizetéséhez és a munkaterület adataihoz tartozó `get()` függvény használatával. Hozzon létre egy kísérletet a munkaterületen a modell futtatásának tárolásához és figyeléséhez.


```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment

workspace = Workspace.get(subscription_id="<your-subscription-id>",
                          name="<your-workspace-name>", resource_group="<your-resource-group>")
experiment = Experiment(workspace, "opendatasets-ml")
```

Hozzon létre egy konfigurációs objektumot a kísérlethez `AutoMLConfig` a osztály használatával. Csatolja a betanítási adatait, és adja meg azokat a beállításokat és paramétereket, amelyek a betanítási folyamatot vezérlik. A paraméterek a következők:

* `task`: a futtatni kívánt kísérlet típusa.
* `X`: képzési funkciók.
* `y`: betanítási címkék.
* `iterations`: a futtatandó Ismétlések száma. Minden iteráció különböző funkció-normalizálás/szabványosítási módszerek kombinációit kísérli meg, és különböző modelleket használ több hiperparaméter-beállítás használatával.
* `primary_metric`: a modell betanítása során optimalizálni kívánt elsődleges metrika. A legjobb illeszkedési modell ezen metrika alapján lesz kiválasztva.
* `preprocess`: azt határozza meg, hogy a kísérlet képes-e elődolgozni a bemeneti adatokat (hiányzó adatok kezelésére, szöveg konvertálása numerikusra stb.).
* `n_cross_validations`: Az ellenőrzési adatok megadásakor végrehajtandó, több ellenőrzési felosztások száma.


```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task="regression",
                             X=X_train.values,
                             y=y_train.values.flatten(),
                             iterations=20,
                             primary_metric="spearman_correlation",
                             preprocess=True,
                             n_cross_validations=5
                             )
```

### <a name="submit-experiment"></a>Kísérlet elküldése

A kísérlet beküldése a betanításhoz. A kísérlet elküldése után a folyamat különböző gépi tanulási algoritmusokon és hiperparaméter-beállításokon keresztül megismétli a megadott megkötéseket. A legjobban illeszkedő modellt a megadott pontossági metrika optimalizálásával választja ki. Adja át `automl_config` az objektumot a kísérletnek. Állítsa be a kimenetet `True` , hogy megtekintse az előrehaladást a kísérlet során.

A kísérlet elküldése után a betanítási folyamat élő kimenete jelenik meg. Minden egyes iteráció esetében megjelenik a modell típusa és a szolgáltatás normalizálása/szabványosítási módszer, a Futtatás időtartama és a betanítási pontosság. A mező `BEST` a metrikák típusától függően a legjobb futó tanítási pontszámot követi nyomon.

```python
training_run = experiment.submit(automl_config, show_output=True)
```

    Running on local machine
    Parent Run ID: AutoML_5c35f2a7-e479-4e7f-a131-ed4cb51e29d1
    Current status: DatasetEvaluation. Gathering dataset statistics.
    Current status: FeaturesGeneration. Generating features for the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: ModelSelection. Beginning model selection.

    ****************************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ****************************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler RandomForest                      0:00:07       0.9081    0.9081
             1   StandardScalerWrapper DecisionTree             0:00:05       0.9121    0.9121
             2   StandardScalerWrapper LightGBM                 0:00:04       0.9318    0.9318
             3   StandardScalerWrapper LightGBM                 0:00:07       0.9286    0.9318
             4   MaxAbsScaler LightGBM                          0:00:05       0.9246    0.9318
             5   MaxAbsScaler LightGBM                          0:00:05       0.9199    0.9318
             6   MaxAbsScaler RandomForest                      0:00:07       0.9327    0.9327
             7   StandardScalerWrapper ElasticNet               0:00:04       0.9371    0.9371
             8   MaxAbsScaler LightGBM                          0:00:05       0.9327    0.9371
             9   MaxAbsScaler SGD                               0:00:04       0.9077    0.9371
            10   MaxAbsScaler LightGBM                          0:00:04       0.9340    0.9371
            11   StandardScalerWrapper LightGBM                 0:00:04       0.8301    0.9371
            12   MaxAbsScaler DecisionTree                      0:00:05       0.9214    0.9371
            13   StandardScalerWrapper DecisionTree             0:00:04       0.9201    0.9371
            14   MaxAbsScaler DecisionTree                      0:00:05       0.9179    0.9371
            15   MaxAbsScaler ExtremeRandomTrees                0:00:05       0.9052    0.9371
            16   StandardScalerWrapper DecisionTree             0:00:04       0.9282    0.9371
            17   StandardScalerWrapper ElasticNet               0:00:04       0.9319    0.9371
            18   VotingEnsemble                                 0:00:16       0.9380    0.9380
            19   StackEnsemble                                  0:00:17       0.9376    0.9380

### <a name="retrieve-the-fitted-model"></a>A beépített modell beolvasása

Az összes betanítási ismétlés végén az automatizált gépi tanulási folyamat létrehoz egy Ensemble-algoritmust minden egyes futtatásból, akár a poggyászt, akár a halmozást. Kérje le a beépített együttest a `fitted_model`változóba, és a legjobb egyéni futtatást `best_run`a változóba.

```python
best_run, fitted_model = training_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-model-accuracy"></a>Modell pontosságának tesztelése

A beépített Ensemble-modell segítségével előrejelzéseket futtathat a tesztelési adatkészleten a taxi viteldíjak előrejelzéséhez. A függvény `predict()` a beillesztett modellt használja, és előre jelzi az y, a taxi viteldíjának értékét az `X_test` adatkészlethez.


```python
y_predict = fitted_model.predict(X_test.values)
```

Kiszámítja az eredmények legfelső szintű, négyzetes hibáját. Használja a `y_test` dataframe, és alakítsa át egy listára `y_actual` , és hasonlítsa össze az előre jelzett értékeket. A függvény `mean_squared_error` két tömb értéket vesz igénybe, és kiszámítja a közöttük lévő átlagos négyzetes hibát. Az eredmény négyzet gyökerének megadásával az y változóval megegyező egységekben talál hibát. Nagyjából azt jelzi, hogy a taxi viteldíjai milyen mértékben vannak kiértékelve a tényleges díjaktól, ugyanakkor nagy mértékben súlyozzák a nagyméretű hibákat.


```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```




    4.178568987067901



Futtassa a következő kódot a teljes `y_actual` és `y_predict` adatkészletek használatával a középérték abszolút százalékának (mape) kiszámításához. Ez a mérőszám az összes előre jelzett és tényleges érték közötti abszolút különbséget számítja ki, és összegzi az összes különbséget. Ezt követően a tényleges értékek összegének százalékában kifejezi az összeget.


```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.14923619644924357

    Model Accuracy:
    0.8507638035507564

Mivel a teljes adatkészlethez (n = 11748) képest meglehetősen kis adatminta volt, a modell pontossága meglehetősen magas, 85%-nál, és a GYÖKÁTLAGOS-$4,00 hiba történt a taxi viteldíj-árának előrejelzése során. Lehetséges, hogy a következő lépés a pontosság javítása, térjen vissza a jegyzetfüzet második cellájába, és növelje a minta méretét havonta 2 000 rekordra, majd futtassa újra a teljes kísérletet a modell újbóli betanításához több adattal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem tervezi a létrehozott erőforrások használatát, törölje őket, így nem számítunk fel díjat.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen.
1. Válassza ki a listában az Ön által létrehozott erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Adja meg az erőforráscsoport nevét. Ezután válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

* További példákért tekintse meg az Azure Open adatkészletek [jegyzetfüzeteit](https://github.com/Azure/OpenDatasetsNotebooks) .
* A Azure Machine Learning szolgáltatásban a gépi tanulással kapcsolatos további információkért kövesse az [útmutató](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train) című témakört.
