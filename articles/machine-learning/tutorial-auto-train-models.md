---
title: 'Regressziós oktatóanyag: automatizált ML'
titleSuffix: Azure Machine Learning
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhatja elő a gépi tanulási modellt az automatizált gépi tanulás használatával. A Azure Machine Learning az adatok előfeldolgozását, az algoritmus kiválasztását és a hiperparaméter kiválasztását automatizált módon végezheti el.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.custom: tracking-python
ms.openlocfilehash: 595440dc727f3faf1fa475266825a671f00d9153
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86143618"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Oktatóanyag: automatikus gépi tanulás használata a taxi viteldíjak előrejelzéséhez
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben az oktatóanyagban a Azure Machine Learning automatizált gépi tanulást használ egy regressziós modell létrehozásához a New York-i taxi díjszabásának előrejelzéséhez. Ez a folyamat fogadja az oktatási és konfigurációs beállításokat, és automatikusan megismétli a különböző funkció-normalizálás/szabványosítási módszerek, modellek és hiperparaméter-beállítások kombinációit, hogy a legjobb modellt lehessen megérkezni.

![Folyamatábra](./media/tutorial-auto-train-models/flow2.png)

Ez az oktatóanyag a következő feladatokat ismerteti:

> [!div class="checklist"]
> * Az Azure Open-adatkészletek használatával letöltheti, átalakíthatja és megtisztíthatja az adatokat
> * Automatizált gépi tanulási regressziós modell betanítása
> * Modell pontosságának kiszámítása

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a Azure Machine Learning [ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="prerequisites"></a>Előfeltételek

* Ha még nem rendelkezik Azure Machine Learning munkaterülettel vagy notebook virtuális géppel, fejezze be a [telepítési útmutatót](tutorial-1st-experiment-sdk-setup.md) .
* A telepítési oktatóanyag befejezése után nyissa meg az *oktatóanyagok/Regression-automl-NYC-taxi-Data/Regression-Automated-ml. ipynb* notebookot ugyanazzal a notebook-kiszolgálóval.

Ez az oktatóanyag a [githubon](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) is elérhető, ha saját [helyi környezetben](how-to-configure-environment.md#local)szeretné futtatni. Futtassa `pip install azureml-sdk[automl] azureml-opendatasets azureml-widgets` a parancsot a szükséges csomagok beszerzéséhez.

## <a name="download-and-prepare-data"></a>Az adatgyűjtés letöltése és előkészítése

Importálja a szükséges csomagokat. A nyílt adatkészletek csomag az egyes adatforrásokat jelképező osztályt tartalmaz ( `NycTlcGreen` például) a letöltés előtt egyszerűen szűrheti a dátum-paramétereket.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Először hozzon létre egy dataframe a taxi-adattároláshoz. Ha nem Spark-környezetben dolgozik, a nyílt adatkészletek csak egy hónapos adat letöltését teszik lehetővé bizonyos osztályokkal, hogy elkerülje `MemoryError` a nagyméretű adatkészleteket.

A taxi-adatok letöltéséhez egyszerre egy hónapot iteratív le, és mielőtt hozzáfűzi, hogy `green_taxi_df` véletlenszerűen mintát 2 000 rekordokat minden hónapból, hogy elkerülje a dataframe felduzzadása. Ezután tekintse meg az adatmegjelenítést.


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

<div>
<style scoped>. dataframe TBODY TR th: csak típus {függőleges igazítás: középső;}. dataframe TBODY TR th {Vertical-igazítás: Top;}. dataframe thead th {Text-igazítás: Right;}</style>
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
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4,84</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,88</td>
      <td>40,84</td>
      <td>– 73,94</td>
      <td>...</td>
      <td>2</td>
      <td>15,00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>16,30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0,69</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,96</td>
      <td>40,81</td>
      <td>– 73,96</td>
      <td>...</td>
      <td>2</td>
      <td>4,50</td>
      <td>1,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6,30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0,45</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,92</td>
      <td>40,76</td>
      <td>– 73,91</td>
      <td>...</td>
      <td>2</td>
      <td>4,00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>4,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0,00</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,81</td>
      <td>40,70</td>
      <td>– 73,82</td>
      <td>...</td>
      <td>2</td>
      <td>12,50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,92</td>
      <td>40,76</td>
      <td>– 73,92</td>
      <td>...</td>
      <td>2</td>
      <td>4,00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>5.00</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1.10</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,96</td>
      <td>40,72</td>
      <td>– 73,95</td>
      <td>...</td>
      <td>2</td>
      <td>6,50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>7,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,88</td>
      <td>40,76</td>
      <td>– 73,87</td>
      <td>...</td>
      <td>2</td>
      <td>6,00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3,30</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,96</td>
      <td>40,72</td>
      <td>– 73,91</td>
      <td>...</td>
      <td>2</td>
      <td>12,50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1,19</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,94</td>
      <td>40,71</td>
      <td>– 73,95</td>
      <td>...</td>
      <td>1</td>
      <td>7,00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>1.75</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>9,55</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0,65</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,94</td>
      <td>40,71</td>
      <td>– 73,94</td>
      <td>...</td>
      <td>2</td>
      <td>5.00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6,30</td>
      <td>1,00</td>
    </tr>
  </tbody>
</table>
<p>10 sor × 23 oszlop</p>
</div>


Most, hogy a kezdeti adatok betöltődik, Definiáljon egy függvényt, amely különböző időalapú szolgáltatásokat hoz létre a pickup datetime mezőből. Ez új mezőket hoz létre a hónap, a hónap napja, a hét napja és a nap órájában, és lehetővé teszi a modell időalapú szezonális felszámítását. Használja a `apply()` függvényt a dataframe, hogy a iteratív alkalmazza a `build_time_features()` függvényt a taxi összes sorára.

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

<div>
<style scoped>. dataframe TBODY TR th: csak típus {függőleges igazítás: középső;}. dataframe TBODY TR th {Vertical-igazítás: Top;}. dataframe thead th {Text-igazítás: Right;}</style>
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
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4,84</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,88</td>
      <td>40,84</td>
      <td>– 73,94</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>16,30</td>
      <td>1,00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0,69</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,96</td>
      <td>40,81</td>
      <td>– 73,96</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6,30</td>
      <td>1,00</td>
      <td>1</td>
      <td>20</td>
      <td>1</td>
      <td>16</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0,45</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,92</td>
      <td>40,76</td>
      <td>– 73,91</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>4,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0,00</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,81</td>
      <td>40,70</td>
      <td>– 73,82</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>17</td>
      <td>5</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,92</td>
      <td>40,76</td>
      <td>– 73,92</td>
      <td>...</td>
      <td>0</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>5.00</td>
      <td>1,00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1.10</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,96</td>
      <td>40,72</td>
      <td>– 73,95</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>7,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>4</td>
      <td>6</td>
      <td>19</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,88</td>
      <td>40,76</td>
      <td>– 73,87</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
      <td>12</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3,30</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,96</td>
      <td>40,72</td>
      <td>– 73,91</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>9</td>
      <td>4</td>
      <td>23</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1,19</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,94</td>
      <td>40,71</td>
      <td>– 73,95</td>
      <td>...</td>
      <td>0.3</td>
      <td>1.75</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>9,55</td>
      <td>1,00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>17</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0,65</td>
      <td>Nincsenek</td>
      <td>Nincsenek</td>
      <td>– 73,94</td>
      <td>40,71</td>
      <td>– 73,94</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6,30</td>
      <td>1,00</td>
      <td>1</td>
      <td>22</td>
      <td>3</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
<p>10 sor × 27 oszlop</p>
</div>

Távolítson el néhány olyan oszlopot, amelyre nem lesz szüksége a betanításhoz vagy a további funkciók létrehozásához.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Az adattisztítás

Futtassa a `describe()` függvényt az új dataframe, és tekintse meg az egyes mezők összegző statisztikáit.

```python
green_taxi_df.describe()
```

<div>
<style scoped>. dataframe TBODY TR th: csak típus {függőleges igazítás: középső;}. dataframe TBODY TR th {Vertical-igazítás: Top;}. dataframe thead th {Text-igazítás: Right;}</style>
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
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
      <td>48000,00</td>
    </tr>
    <tr>
      <th>középérték</th>
      <td>1,78</td>
      <td>1,37</td>
      <td>2,87</td>
      <td>– 73,83</td>
      <td>40,69</td>
      <td>– 73,84</td>
      <td>40,70</td>
      <td>14,75</td>
      <td>6,50</td>
      <td>15,13</td>
      <td>3,27</td>
      <td>13,52</td>
    </tr>
    <tr>
      <th>Standard</th>
      <td>0.41</td>
      <td>1.04</td>
      <td>2.93</td>
      <td>2,76</td>
      <td>1.52</td>
      <td>2,61</td>
      <td>1,44</td>
      <td>12,08</td>
      <td>3,45</td>
      <td>8,45</td>
      <td>1,95</td>
      <td>6,83</td>
    </tr>
    <tr>
      <th>p</th>
      <td>1,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>– 74,66</td>
      <td>0,00</td>
      <td>– 74,66</td>
      <td>0,00</td>
      <td>– 300,00</td>
      <td>1,00</td>
      <td>1,00</td>
      <td>0,00</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2.00</td>
      <td>1,00</td>
      <td>1.06</td>
      <td>– 73,96</td>
      <td>40,70</td>
      <td>– 73,97</td>
      <td>40,70</td>
      <td>7,80</td>
      <td>3,75</td>
      <td>8,00</td>
      <td>2.00</td>
      <td>9,00</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.00</td>
      <td>1,00</td>
      <td>1,90</td>
      <td>– 73,94</td>
      <td>40,75</td>
      <td>– 73,94</td>
      <td>40,75</td>
      <td>11,30</td>
      <td>6,50</td>
      <td>15,00</td>
      <td>3.00</td>
      <td>15,00</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.00</td>
      <td>1,00</td>
      <td>3,60</td>
      <td>– 73,92</td>
      <td>40,80</td>
      <td>– 73,91</td>
      <td>40,79</td>
      <td>17,80</td>
      <td>9,25</td>
      <td>22,00</td>
      <td>5.00</td>
      <td>19,00</td>
    </tr>
    <tr>
      <th>Max</th>
      <td>2.00</td>
      <td>9,00</td>
      <td>97,57</td>
      <td>0,00</td>
      <td>41,93</td>
      <td>0,00</td>
      <td>41,94</td>
      <td>450,00</td>
      <td>12,00</td>
      <td>30,00</td>
      <td>6,00</td>
      <td>23,00</td>
    </tr>
  </tbody>
</table>
</div>


Az összegző statisztikában láthatja, hogy több olyan mező van, amely kiugró vagy értékkel rendelkezik, ami csökkenti a modell pontosságát. Először szűrje a Lat/Long mezőket a Manhattan terület határain belülre. Ezzel kiszűri a hosszú taxi-utakat vagy-utazásokat, amelyek a más funkciókkal való kapcsolatuk tekintetében kiugró módon jelennek meg.

Emellett szűrheti a `tripDistance` mezőt úgy, hogy nullánál nagyobb legyen, de kevesebb, mint 31 mérföld (a haversine távolság a két lat/hosszú pár között). Ezzel kiküszöbölheti a hosszú kiugró utakat, amelyekben inkonzisztens utazási díj szerepel.

Végül a `totalAmount` mező negatív értékeket tartalmaz a taxi viteldíjak esetében, amelyek nem ésszerűek a modell kontextusában, és a `passengerCount` mező hibás adatokkal rendelkezik, és a minimális érték nulla.

Szűrje ki ezeket a rendellenességeket a Query functions használatával, majd távolítsa el az utolsó néhány oszlopot, amely nem szükséges a betanításhoz.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Hívja `describe()` meg újra az adatvesztést, hogy a takarítás a várt módon működött. Most már készen áll a gépi tanulási modell betanításához használt, a taxi, a nyaralás és az időjárási idő előkészítésére.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Munkaterület konfigurálása

Hozzon létre egy munkaterület-objektumot a meglévő munkaterületről. A [munkaterület](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) egy olyan osztály, amely elfogadja az Azure-előfizetést és az erőforrás-információkat. Létrehoz egy felhőalapú erőforrást is a modell futtatásának figyelésére és nyomon követésére. `Workspace.from_config()`beolvassa a (z) **config.js** fájlt, és betölti a hitelesítési adatokat egy nevű objektumba `ws` . A `ws` a kód további részében használható ebben az oktatóanyagban.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Az adat felosztása a vonatra és a tesztelési csoportokra

Ossza fel az adatgyűjtést a betanítási és tesztelési készletekbe a `train_test_split` könyvtárban található függvény használatával `scikit-learn` . Ez a függvény elkülöníti az adatait az x (**szolgáltatások**) adatkészletbe a modell betanításához, valamint az y (**előre jelzett értékek**) adatkészletet a teszteléshez.

A `test_size` paraméter határozza meg a teszteléshez lefoglalható adatmennyiség százalékos arányát. A `random_state` paraméter állítja be a magot a véletlenszerű generátorba, hogy a determinisztikus legyenek kiosztva.

```python
from sklearn.model_selection import train_test_split

x_train, x_test = train_test_split(final_df, test_size=0.2, random_state=223)
```

Ennek a lépésnek a célja, hogy az adatpontokkal tesztelje a modell betanítására nem használt befejezett modellt a valódi pontosság mérése érdekében.

Más szóval a jól betanított modellnek képesnek kell lennie arra, hogy a már nem látott adatok alapján pontosan elvégezze az előrejelzések készítését. Most már készen áll a gépi tanulási modell automatikus betanítására.

## <a name="automatically-train-a-model"></a>Modell automatikus betanítása

A modellek automatikus betanításához hajtsa végre a következő lépéseket:
1. Adja meg a kísérlet futtatásának beállításait. Csatolja a betanítási adatait a konfigurációhoz, és módosítsa a betanítási folyamatot szabályozó beállításokat.
1. A kísérlet beküldése a modell finomhangolására. A kísérlet elküldése után a folyamat különböző gépi tanulási algoritmusokon és hiperparaméter-beállításokon keresztül megismétli a megadott megkötéseket. A pontosság mérőszámának optimalizálásával kiválasztja a legjobban illeszkedő modellt.

### <a name="define-training-settings"></a>Képzési beállítások megadása

A kísérlet paraméter és a modell beállításainak megadása a betanításhoz. Tekintse meg a [Beállítások](how-to-configure-auto-train.md)teljes listáját. Ha a kísérletet ezekkel az alapértelmezett beállításokkal küldi el, a rendszer körülbelül 5-20 percig tart, de ha rövidebb futási időt szeretne, csökkentse a `experiment_timeout_minutes` paramétert.

|Tulajdonság| Az oktatóanyagban szereplő érték |Leírás|
|----|----|---|
|**iteration_timeout_minutes**|2|Az egyes iterációk időkorlátja percben kifejezve. Csökkentse ezt az értéket a teljes futtatókörnyezet csökkentése érdekében.|
|**experiment_timeout_minutes**|20|Az a maximális időtartam (percben), ameddig az összes iteráció a kísérlet megszakítása előtt elvégezhető.|
|**enable_early_stopping**|True (Igaz)|Annak a jelzője, amely lehetővé teszi a korai megszakítást, ha a pontszám rövid távon nem javul.|
|**primary_metric**| spearman_correlation | Az optimalizálni kívánt metrika. A legjobban illeszkedő modell a metrika alapján lesz kiválasztva.|
|**featurization**| automatikus | Az **automatikus**használatával a kísérlet elődolgozhatja a bemeneti adatokat (a hiányzó adatok kezelésével, a szöveg és a numerikus szöveggé való átalakítással stb.).|
|**részletességi**| logging.INFO | A naplózási szint szabályozása.|
|**n_cross_validations**|5|Az ellenőrzési adatok megadásakor végrehajtandó, több ellenőrzési felosztások száma.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 2,
    "experiment_timeout_minutes": 20,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

A megadott tanítási beállításokat `**kwargs` paraméterként használhatja egy `AutoMLConfig` objektumhoz. Továbbá adja meg a betanítási adatait és a modell típusát, amely `regression` ebben az esetben.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data=x_train,
                             label_column_name="totalAmount",
                             **automl_settings)
```

> [!NOTE]
> Az automatizált gépi tanulás előfeldolgozásának lépései (a funkciók normalizálása, a hiányzó adatkezelés, a szöveg konvertálása a numerikus formátumba stb.) az alapul szolgáló modell részévé válnak. A modell előrejelzésekhez való használatakor a betanítás során alkalmazott azonos előfeldolgozási lépéseket a rendszer automatikusan alkalmazza a bemeneti adatokra.

### <a name="train-the-automatic-regression-model"></a>Az automatikus regressziós modell betanítása

Hozzon létre egy kísérlet objektumot a munkaterületen. Egy kísérlet tárolóként működik az egyes futtatásokhoz. Adja át a definiált `automl_config` objektumot a kísérletnek, és állítsa be a kimenetet, `True` hogy megtekintse az előrehaladást a Futtatás során.

A kísérlet elindítása után a kimenet a kísérlet futtatásakor élőben jelenik meg. Minden egyes iterációnál megjelenik a modell típusa, a Futtatás időtartama és a képzés pontossága. A mező a `BEST` metrikák típusától függően a legjobb futó tanítási pontszámot követi nyomon.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "taxi-experiment")
local_run = experiment.submit(automl_config, show_output=True)
```

```output
Running on local machine
Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
Current status: DatasetFeaturization. Beginning to featurize the dataset.
Current status: DatasetEvaluation. Gathering dataset statistics.
Current status: FeaturesGeneration. Generating features for the dataset.
Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
Current status: ModelSelection. Beginning model selection.

****************************************************************************************************
ITERATION: The iteration being evaluated.
PIPELINE: A summary description of the pipeline being evaluated.
DURATION: Time taken for the current iteration.
METRIC: The result of computing score on the fitted pipeline.
BEST: The best observed score thus far.
****************************************************************************************************

 ITERATION   PIPELINE                                       DURATION      METRIC      BEST
         0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
         1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
         2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
         3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
         4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
         5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
         6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
         7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
         8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
         9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
        10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
        11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
        12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
        13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
        14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
        15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
        16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
        17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
        18   VotingEnsemble                                 0:00:23       0.9471    0.9471
        19   StackEnsemble                                  0:00:27       0.9463    0.9471
```

## <a name="explore-the-results"></a>Az eredmények vizsgálata

Ismerje meg az automatikus képzés eredményeit egy [Jupyter widgettel](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). A widget lehetővé teszi, hogy megtekintse az egyes futtatási ismétlések gráfját és táblázatát, valamint a betanítási pontossági mérőszámokat és metaadatokat. Emellett a legördülő választóval szűrheti az elsődleges metrikától eltérő pontossági mérőszámokat is.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter widget-Futtatás részletei ](./media/tutorial-auto-train-models/automl-dash-output.png)
 ![ Jupyter widget Plot](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>A legjobb modell lekérése

Válassza ki az iterációk legjobb modelljét. A `get_output` függvény a legjobb futtatást adja vissza, az utolsó illeszkedési meghíváshoz tartozó modellt. A túlterhelések bekapcsolásával lekérheti `get_output` az összes naplózott metrika és egy adott iteráció esetében a legjobb futtatási és a beépített modellt.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>A legjobb modell pontosságának tesztelése

A legjobb modell segítségével előrejelzéseket futtathat a tesztelési adatkészleten a taxi viteldíjak előrejelzéséhez. A függvény `predict` a legjobb modellt használja, és az adatkészletből megbecsüli az y és az **utazás költségeit** `x_test` . Nyomtassa ki az első 10 előre jelzett költségadatok értékét a következőből: `y_predict` .

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Az `root mean squared error` eredmények kiszámítása. Alakítsa át a `y_test` dataframe egy listára, hogy összehasonlítsa az előre jelzett értékeket. A függvény `mean_squared_error` két tömb értéket vesz igénybe, és kiszámítja a közöttük lévő átlagos négyzetes hibát. Az eredmény négyzet gyökerének megadásával az y változóval megegyező egységekben talál **hibát.** Nagyjából azt jelzi, hogy a taxi díjszabása milyen messzire kerül a tényleges díjszabás alapján.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

A teljes `y_actual` és az adatkészletek használatával a következő kód alapján számíthatja ki az átlagos abszolút százalékos hibát (mape) `y_predict` . Ez a mérőszám az összes előre jelzett és tényleges érték közötti abszolút különbséget számítja ki, és összegzi az összes különbséget. Ezt követően a tényleges értékek összegének százalékában kifejezi az összeget.

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

```output
Model MAPE:
0.14353867606052823

Model Accuracy:
0.8564613239394718
```


A két előrejelzési pontossági mérőszámból láthatja, hogy a modell elég jó, ha az adatkészlet szolgáltatásaiból, jellemzően a +-$4,00-es és körülbelül 15%-os hiba miatt megjósolja a taxi viteldíjait.

A hagyományos gépi tanulási modell fejlesztési folyamata nagy erőforrás-igényes, és jelentős tartományi ismereteket és időigényes befektetéseket igényel a több tucat modell eredményeinek összehasonlításához. Az automatizált gépi tanulás nagyszerű módja annak, hogy gyorsan tesztelje a forgatókönyv számos különböző modelljét.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ne hajtsa végre ezt a szakaszt, ha más Azure Machine Learning oktatóanyagok futtatását tervezi.

### <a name="stop-the-compute-instance"></a>A számítási példány leállítása

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Mindent törölni

Ha nem tervezi a létrehozott erőforrások használatát, törölje őket, így nem számítunk fel díjat.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen.
1. Válassza ki a listában az Ön által létrehozott erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Adja meg az erőforráscsoport nevét. Ezután válassza a **Törlés** elemet.

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet is. Jelenítse meg a munkaterület tulajdonságait, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az automatizált gépi tanulási oktatóanyagban a következő feladatokat hajtotta végre:

> [!div class="checklist"]
> * Egy kísérlethez konfigurált egy munkaterületet és egy előkészített adatkészletet.
> * Automatikus regressziós modell használatával, helyi egyéni paraméterekkel.
> * A betanítási eredmények megismerése és felülvizsgálata.

[A modell üzembe helyezése](tutorial-deploy-models-with-aml.md) Azure Machine Learningsal.
