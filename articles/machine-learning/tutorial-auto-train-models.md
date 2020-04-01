---
title: 'Regressziós oktatóanyag: Automatikus ml'
titleSuffix: Azure Machine Learning
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy gépi tanulási modell t automatizált gépi tanulás használatával. Az Azure Machine Learning automatizált módon végezheti el az adatok előfeldolgozását, az algoritmusok kiválasztását és a hiperparaméter-kiválasztást.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: 75e61ea3f4fa6c2b346f912a9effd66ad94e7e93
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77116447"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Oktatóanyag: Automatikus gépi tanulás használata a taxidíjak előrejelzéséhez
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben az oktatóanyagban az Azure Machine Learning automatikus gépi tanuláshasználatával regressziós modellt hozhat létre a NYC taxi árak előrejelzéséhez. Ez a folyamat elfogadja a betanítási adatokat és a konfigurációs beállításokat, és automatikusan iterálja a különböző funkciók normalizálási/szabványosítási módszereinek, modelljeinek és hiperparaméter-beállításainak kombinációjával, hogy a legjobb modellt találja.

![Folyamatábra](./media/tutorial-auto-train-models/flow2.png)

Ebben a tutorial megtanulod a következő feladatokat:

> [!div class="checklist"]
> * Adatok letöltése, átalakítása és tisztítása az Azure Open adatkészleteivel
> * Automatikus gépi tanulási regressziós modell betanítása
> * Modell pontosságának kiszámítása

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot, mielőtt elkezdené. Próbálja ki még ma az Azure Machine Learning [ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

## <a name="prerequisites"></a>Előfeltételek

* Töltse ki a [telepítő oktatóanyagot,](tutorial-1st-experiment-sdk-setup.md) ha még nem rendelkezik Azure Machine Learning-munkaterülettel vagy virtuális notebookszámítógéppel.
* Miután befejezte a telepítő oktatóanyag, nyissa meg az *oktatóanyagok/regresszió-automl-nyc-tax-data/regresszió-automated-ml.ipynb* notebook ugyanazt a notebook szerver.

Ez az oktatóanyag a [GitHubon](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) is elérhető, ha saját [helyi környezetben](how-to-configure-environment.md#local)szeretné futtatni. Fuss, `pip install azureml-sdk[automl] azureml-opendatasets azureml-widgets` hogy a szükséges csomagokat.

## <a name="download-and-prepare-data"></a>Adatok letöltése és előkészítése

Importálja a szükséges csomagokat. Az Open Datasets csomag tartalmaz egy`NycTlcGreen` osztályt, amely minden adatforrást jelöl (például), hogy a letöltés előtt könnyen szűrje a dátumparamétereket.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Kezdje azzal, hogy létrehoz egy adatkeretet a taxi adatok tárolására. Nem Spark-környezetben végzett munka esetén az open datasets csak lehetővé teszi az `MemoryError` adatok egy hónapnyi letöltését bizonyos osztályokmal, hogy nagy adatkészletekkel elkerülhető legyen.

Taxiadatok letöltéséhez iteratív módon egyszerre egy hónapot kell letöltenie, és mielőtt hozzáfűzi őket, hogy `green_taxi_df` véletlenszerűen 2000 rekordot mintavételezhet minden hónapból, hogy elkerülje az adatkeret puffadását. Ezután tekintse meg az adatok előnézetét.


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
<style scoped>.dataframe tbody tr th:only-of-type { vertical-align: middle; }

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
      <th>szállítóazonosító</th>
      <th>lpepPickupDate idő</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount (utasszáma)</th>
      <th>tripTávolság</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupHosszúság</th>
      <th>pickupSzélesség</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>kifizetéstípusa</th>
      <th>fareÖsszeg</th>
      <th>Extra</th>
      <th>mtaTax</th>
      <th>javításPótdíj</th>
      <th>tippÖsszeg</th>
      <th>útdíjösszeg</th>
      <th>ehailFee között</th>
      <th>összesen</th>
      <th>tripType (utazástípusa)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4.84</td>
      <td>None</td>
      <td>None</td>
      <td>-73.88</td>
      <td>40.84</td>
      <td>-73.94</td>
      <td>...</td>
      <td>2</td>
      <td>15.00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>16.30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0.69</td>
      <td>None</td>
      <td>None</td>
      <td>-73.96</td>
      <td>40.81</td>
      <td>-73.96</td>
      <td>...</td>
      <td>2</td>
      <td>4.50</td>
      <td>1,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0.45</td>
      <td>None</td>
      <td>None</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.91</td>
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
      <td>None</td>
      <td>None</td>
      <td>-73.81</td>
      <td>40.70</td>
      <td>-73.82</td>
      <td>...</td>
      <td>2</td>
      <td>12.50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>None</td>
      <td>None</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.92</td>
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
      <td>None</td>
      <td>None</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.95</td>
      <td>...</td>
      <td>2</td>
      <td>6.50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>7.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>None</td>
      <td>None</td>
      <td>-73.88</td>
      <td>40.76</td>
      <td>-73.87</td>
      <td>...</td>
      <td>2</td>
      <td>6.00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3.30</td>
      <td>None</td>
      <td>None</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.91</td>
      <td>...</td>
      <td>2</td>
      <td>12.50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1.19</td>
      <td>None</td>
      <td>None</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.95</td>
      <td>...</td>
      <td>1</td>
      <td>7.00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>1.75</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>9.55</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0.65</td>
      <td>None</td>
      <td>None</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.94</td>
      <td>...</td>
      <td>2</td>
      <td>5.00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
      <td>1,00</td>
    </tr>
  </tbody>
</table>
<p>10 sor × 23 oszlop</p>
</div>


Most, hogy a kezdeti adatok betöltődnek, definiáljon egy függvényt, amely különböző időalapú funkciókat hoz létre a felvételi datetime mezőből. Ez új mezőket hoz létre a hónap számához, a hónap napához, a hét napához és a nap óráihoz, és lehetővé teszi, hogy a modell figyelembe vegye az időalapú szezonalitást. Használja `apply()` a függvényt a dataframe iteratív módon alkalmazza a `build_time_features()` függvényt minden sorban a taxi adatok.

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
<style scoped>.dataframe tbody tr th:only-of-type { vertical-align: middle; }

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
      <th>szállítóazonosító</th>
      <th>lpepPickupDate idő</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount (utasszáma)</th>
      <th>tripTávolság</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupHosszúság</th>
      <th>pickupSzélesség</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>javításPótdíj</th>
      <th>tippÖsszeg</th>
      <th>útdíjösszeg</th>
      <th>ehailFee között</th>
      <th>összesen</th>
      <th>tripType (utazástípusa)</th>
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
      <td>4.84</td>
      <td>None</td>
      <td>None</td>
      <td>-73.88</td>
      <td>40.84</td>
      <td>-73.94</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>16.30</td>
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
      <td>0.69</td>
      <td>None</td>
      <td>None</td>
      <td>-73.96</td>
      <td>40.81</td>
      <td>-73.96</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
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
      <td>0.45</td>
      <td>None</td>
      <td>None</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.91</td>
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
      <td>None</td>
      <td>None</td>
      <td>-73.81</td>
      <td>40.70</td>
      <td>-73.82</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
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
      <td>None</td>
      <td>None</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.92</td>
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
      <td>None</td>
      <td>None</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.95</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>7.80</td>
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
      <td>None</td>
      <td>None</td>
      <td>-73.88</td>
      <td>40.76</td>
      <td>-73.87</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.80</td>
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
      <td>3.30</td>
      <td>None</td>
      <td>None</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.91</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
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
      <td>1.19</td>
      <td>None</td>
      <td>None</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.95</td>
      <td>...</td>
      <td>0.3</td>
      <td>1.75</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>9.55</td>
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
      <td>0.65</td>
      <td>None</td>
      <td>None</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.94</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
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

Távolítson el néhány olyan oszlopot, amelynem lesz szüksége a betanításhoz vagy a további szolgáltatásépítéshez.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Adatok tisztítása

Futtassa a `describe()` függvényt az új adatkereten az egyes mezők összesítő statisztikáinak megtekintéséhez.

```python
green_taxi_df.describe()
```

<div>
<style scoped>.dataframe tbody tr th:only-of-type { vertical-align: middle; }

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
      <th>szállítóazonosító</th>
      <th>passengerCount (utasszáma)</th>
      <th>tripTávolság</th>
      <th>pickupHosszúság</th>
      <th>pickupSzélesség</th>
      <th>dropoffLongitude</th>
      <th>dropoffSzélesség</th>
      <th>összesen</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
    </tr>
    <tr>
      <th>középérték</th>
      <td>1.78</td>
      <td>1.37</td>
      <td>2.87</td>
      <td>-73.83</td>
      <td>40.69</td>
      <td>-73.84</td>
      <td>40.70</td>
      <td>14.75</td>
      <td>6.50</td>
      <td>15.13</td>
      <td>3.27</td>
      <td>13.52</td>
    </tr>
    <tr>
      <th>Std</th>
      <td>0.41</td>
      <td>1.04</td>
      <td>2.93</td>
      <td>2.76</td>
      <td>1.52</td>
      <td>2.61</td>
      <td>1.44</td>
      <td>12.08</td>
      <td>3.45</td>
      <td>8.45</td>
      <td>1.95</td>
      <td>6.83</td>
    </tr>
    <tr>
      <th>p</th>
      <td>1,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>-74.66</td>
      <td>0,00</td>
      <td>-74.66</td>
      <td>0,00</td>
      <td>-300.00</td>
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
      <td>-73.96</td>
      <td>40.70</td>
      <td>-73.97</td>
      <td>40.70</td>
      <td>7.80</td>
      <td>3,75</td>
      <td>8.00</td>
      <td>2.00</td>
      <td>9.00</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.00</td>
      <td>1,00</td>
      <td>1.90</td>
      <td>-73.94</td>
      <td>40.75</td>
      <td>-73.94</td>
      <td>40.75</td>
      <td>11.30</td>
      <td>6.50</td>
      <td>15.00</td>
      <td>3.00</td>
      <td>15.00</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.00</td>
      <td>1,00</td>
      <td>3,60</td>
      <td>-73.92</td>
      <td>40.80</td>
      <td>-73.91</td>
      <td>40.79</td>
      <td>17.80</td>
      <td>9.25</td>
      <td>22.00</td>
      <td>5.00</td>
      <td>19.00</td>
    </tr>
    <tr>
      <th>Max</th>
      <td>2.00</td>
      <td>9.00</td>
      <td>97.57</td>
      <td>0,00</td>
      <td>41.93</td>
      <td>0,00</td>
      <td>41.94</td>
      <td>450.00</td>
      <td>12.00</td>
      <td>30.00</td>
      <td>6.00</td>
      <td>23.00</td>
    </tr>
  </tbody>
</table>
</div>


Az összegző statisztikákból kiderül, hogy számos olyan mező van, amelynek kiugró értékei vagy értékei csökkentik a modell pontosságát. Először szűrje ki a lat/hosszú mezőket, hogy a Manhattan terület határain belül legyenek. Ez kiszűri a hosszabb taxi utakat vagy utakat, amelyek kiugróak a más funkciókkal való kapcsolatuk tekintetében.

Továbbá szűrjük `tripDistance` a mezőt, hogy nagyobb, mint nulla, de kevesebb, mint 31 mérföld (a haversine közötti távolság a két lat / hosszú pár). Ez kiküszöböli a hosszú kiugró utak, amelyek következetlen utazási költség.

Végül, a `totalAmount` mező negatív értékeket a taxi viteldíjak, amelyek nincs értelme az összefüggésben a modell, és a `passengerCount` mező rossz adatokat a minimális értékek nulla.

Ezeket az anomáliákat lekérdezési függvények segítségével szűrje ki, majd távolítsa el a betanításhoz felesleges utolsó néhány oszlopot.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Hívja `describe()` újra az adatokat, hogy biztosítsa a tisztítást a várt módon. Most már rendelkezik egy előkészített és megtisztított taxi-, üdülési és időjárási adatokkal, amelyeket gépi tanulási modell betanításához használhat.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Munkaterület konfigurálása

Hozzon létre egy munkaterület-objektumot a meglévő munkaterületről. A [munkaterület](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) olyan osztály, amely elfogadja az Azure-előfizetési és erőforrás-adatokat. Emellett létrehoz egy felhőalapú erőforrást a modellfuttatások figyeléséhez és nyomon követéséhez. `Workspace.from_config()`beolvassa a **config.json** fájlt, és betölti a hitelesítésrészleteit egy objektumba. `ws` A `ws` a kód további részében használható ebben az oktatóanyagban.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Az adatok felosztása vonat- és tesztkészletekre

Az adatok felosztása betanítási `train_test_split` és tesztkészletek a függvény a `scikit-learn` tárban. Ez a függvény elkülöníti az adatokat az x (**funkciók**) adatkészlet a modell betanítása és az y (**értékek előrejelzésére**) adatkészlet teszteléshez.

A `test_size` paraméter határozza meg a teszteléshez lefoglalandó adatok százalékos arányát. A `random_state` paraméter beállítja a mag a véletlen generátor, úgy, hogy a vonat-teszt hasít determinisztikus.

```python
from sklearn.model_selection import train_test_split

y_df = final_df.pop("totalAmount")
x_df = final_df

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
```

Ennek a lépésnek az a célja, hogy adatpontokat teszteljen a kész modell, amely nem használt ák a modell betanításához, a valós pontosság mérése érdekében.

Más szóval egy jól betanított modellnek képesnek kell lennie arra, hogy pontosan előrejelzéseket készítsen olyan adatokból, amelyeket még nem látott. Most már készített adatokat a gépi tanulási modell automatikus betanításához.

## <a name="automatically-train-a-model"></a>Modell automatikus betanítása

A modell automatikus betanításához tegye a következő lépéseket:
1. Adja meg a kísérlet futtatásának beállításait. Csatolja a betanítási adatokat a konfigurációhoz, és módosítsa a betanítási folyamatot vezérlő beállításokat.
1. Küldje el a kísérletet a modell hangolásához. A kísérlet elküldése után a folyamat végighalad a különböző gépi tanulási algoritmusok és a hiperparaméter-beállítások, betapadva a megadott megkötések. A pontossági metrika optimalizálásával választja ki a legjobban illeszkedő modellt.

### <a name="define-training-settings"></a>Betanítási beállítások megadása

Adja meg a kísérleti paraméter és a modell beállításait a betanításhoz. A [beállítások](how-to-configure-auto-train.md)teljes listájának megtekintése . A kísérlet beküldése ezekkel az alapértelmezett beállításokkal körülbelül 5-20 min lesz, `experiment_timeout_minutes` de ha rövidebb futási időt szeretne, csökkentse a paramétert.

|Tulajdonság| Az oktatóanyagban szereplő érték |Leírás|
|----|----|---|
|**iteration_timeout_minutes**|2|Időkorlát percben minden egyes iteráció. Csökkentse ezt az értéket a teljes futásidő csökkentéséhez.|
|**experiment_timeout_minutes**|20|Az összes ismétlés együttes en-percben eltöltött maximális idő a kísérlet befejezéséhez.|
|**enable_early_stopping**|True (Igaz)|Jelző a korai megszüntetés engedélyezéséhez, ha a pontszám rövid távon nem javul.|
|**primary_metric**| spearman_correlation | Az optimalizálni kívánt metrika. A legjobban illeszkedő modell e mérőszám alapján kerül kiválasztásra.|
|**featurization (jellemző)**| Auto | Az **automatikus**használatával a kísérlet előre feldolgozhatja a bemeneti adatokat (a hiányzó adatok kezelése, szöveg átalakítása numerikussá stb.)|
|**Bőbeszédűség**| logging.INFO | A naplózás szintjét szabályozza.|
|**n_cross_validations**|5|Az érvényesítési adatok megadása esetén végrehajtandó keresztérvényesítési felosztások száma.|

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

A megadott betanítási `**kwargs` beállításokat `AutoMLConfig` használja egy objektum paramétereként. Emellett adja meg a betanítási adatokat `regression` és a modell típusát, amely ebben az esetben.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             X=x_train.values,
                             y=y_train.values.flatten(),
                             **automl_settings)
```

> [!NOTE]
> Az automatizált gépi tanulás előfeldolgozási lépései (funkciónormalizálás, hiányzó adatok kezelése, szöveg átalakítása numerikussá stb.) az alapul szolgáló modell részévé válnak. Ha a modell t használja az előrejelzések, ugyanazokat az előzetes feldolgozási lépéseket alkalmazott betanítása során automatikusan alkalmazza a bemeneti adatokat.

### <a name="train-the-automatic-regression-model"></a>Az automatikus regressziós modell betanítása

Hozzon létre egy kísérleti objektumot a munkaterületen. Egy kísérlet az egyes futtatások tárolójaként működik. Adja át `automl_config` a definiált objektumot a `True` kísérletnek, és állítsa be a kimenetet a futás közbeni előrehaladás megtekintéséhez.

A kísérlet elindítása után a kimeneti megjelenített frissítések élőben futnak a kísérlet futása közben. Minden egyes iteráció, láthatja a modell típusát, a futtatás időtartamát és a betanítási pontosságot. A `BEST` mező nyomon követi a legjobb futó képzési pontszám alapján a metrika típusát.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "taxi-experiment")
local_run = experiment.submit(automl_config, show_output=True)
```

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

## <a name="explore-the-results"></a>Az eredmények vizsgálata

Fedezze fel az automatikus képzés eredményeit egy [Jupyter widgetsegítségével.](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) A widget lehetővé teszi, hogy egy grafikont és táblázatot az összes egyes futtatási ismétlések, valamint a betanítási pontosság metrikák és metaadatok. Emellett az elsődleges metrikátaz elsődleges metrikátaz elsődleges metrikát mutató különböző pontossági mutatókra szűrhet a legördülő választóval.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter widget](./media/tutorial-auto-train-models/automl-dash-output.png)
![futtatása részleteket Jupyter widget telek](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>A legjobb modell lekérése

Válassza ki a legjobb modellt az iterációk közül. A `get_output` függvény a legjobb futást és a felszerelt modellt adja vissza az utolsó illesztéshez. A túlterhelések `get_output`használatával a , akkor a legjobb futtatási és felszerelt modell bármely naplózott metrika vagy egy adott iteráció.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>A legjobb modellpontosság tesztelése

Használja a legjobb modellt előrejelzések futtatásához a tesztadatkészleten a taxiviteldíjak előrejelzéséhez. A `predict` függvény a legjobb modellt használja, és előre jelzi `x_test` az y, az utazási **költség**értékét az adatkészletből. Az első 10 előre jelzett `y_predict`költségérték nyomtatása ebből.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

`root mean squared error` Számítsa ki az eredményeket. Az `y_test` adatkeret átalakítása listává az előre jelzett értékekkel való összehasonlításhoz. A `mean_squared_error` függvény két értéktömböt vesz fel, és kiszámítja a köztük lévő átlagos négyzethibát. Figyelembe a négyzetgyök az eredmény ad egy hiba ugyanabban az egységben, mint az y változó, **költség**. Azt jelzi, nagyjából milyen messze a taxi viteldíj előrejelzések a tényleges viteldíjak.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Futtassa a következő kódot az átlagos abszolút százalékos `y_actual` hiba `y_predict` (MAPE) kiszámításához a teljes és az adatkészletek használatával. Ez a metrika kiszámítja az egyes előre jelzett és a tényleges érték közötti abszolút különbséget, és összegzi az összes különbséget. Ezután ezt az összeget a tényleges értékek teljes összegének százalékaként fejezi ki.

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
    0.14353867606052823

    Model Accuracy:
    0.8564613239394718


A két előrejelzési pontossági metrikák, láthatja, hogy a modell meglehetősen jó előre taxi viteldíjak az adatkészlet funkciói, általában belül +- $ 4,00, és körülbelül 15%-os hiba.

A hagyományos gépi tanulási modell fejlesztési folyamat a rendkívül erőforrás-igényes, és jelentős tartományi ismeretek és idő befektetés futtatásához és összehasonlításához több tucat modell. Az automatizált gépi tanulás segítségével gyorsan tesztelheti a forgatókönyv számos különböző modelljét.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ne fejezze be ezt a szakaszt, ha más Azure Machine Learning-oktatóanyagok futtatását tervezi.

### <a name="stop-the-compute-instance"></a>A számítási példány leállítása

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Mindent töröljön

Ha nem tervezi a létrehozott erőforrások használatát, törölje azokat, így nem kell fizetnie.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen.
1. Válassza ki a listában az Ön által létrehozott erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Adja meg az erőforráscsoport nevét. Ezután válassza a **Törlés** elemet.

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet. Jelenítse meg a munkaterület tulajdonságait, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az automatikus gépi tanulási oktatóanyagban a következő feladatokat végezte el:

> [!div class="checklist"]
> * Munkaterület konfigurálása és adatok előkészítése egy kísérlethez.
> * Automatikus regressziós modell helyi használatával, egyéni paraméterekkel betanítása.
> * A képzési eredmények feltárása és áttekintése.

[Telepítse a modellt az](tutorial-deploy-models-with-aml.md) Azure Machine Learning használatával.
