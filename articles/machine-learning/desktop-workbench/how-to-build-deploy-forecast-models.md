---
title: Hozhat létre és helyezhet üzembe egy előrejelzési modellt előrejelzés az Azure Machine Learning-csomag használatával.
description: Ismerje meg, hogyan hozhat létre, betanítását, tesztelése és üzembe helyezése az Azure Machine Learning csomag segítségével az előrejelzés előrejelzési modell.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: mattcon
author: matthewconners
ms.date: 07/13/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 06613ed1eac43ebe865666f85235de74903b1d5c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953598"
---
# <a name="build-and-deploy-forecasting-models-with-azure-machine-learning"></a>Hozhat létre, és az Azure Machine Learning előrejelzési modellek üzembe helyezése

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Ebből a cikkből megtudhatja, hogyan használható **Azure Machine Learning-csomagja előrejelzés** (AMLPF) gyorsan készíthet és helyezhet üzembe egy előrejelzési modellt. A munkafolyamat a következőképpen történik:

1. Betölteni és az adatok megismerése
2. Funkciók létrehozása
3. Betanítása, és válassza ki a legjobb modellt
4. A modell üzembe helyezése, és a webszolgáltatás használata

Tekintse át a [csomag dokumentációja](https://aka.ms/aml-packages/forecasting) transzformátorok és modellek, valamint a részletesen ismertetjük az egyes modul és osztály teljes listáját.

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

1. A következő fiókok és az alkalmazás kell hozni és telepítve:
   - Egy Azure Machine Learning kísérletezési fiókra 
   - Az Azure Machine Learning Modellkezelés-fiók
   - Egy telepített Azure Machine Learning Workbenchre 

 Ha három vannak létrehozott vagy még nincs telepítve, kövesse a [Azure Machine Learning gyors üzembe helyezés és a Workbench telepítési](../desktop-workbench/quickstart-installation.md) cikk.

1. Előrejelzés az Azure Machine Learning csomag telepítve kell lennie. Ismerje meg, hogyan [telepíti ezt a csomagot Itt](https://aka.ms/aml-packages/forecasting).

## <a name="sample-data-and-jupyter-notebook"></a>Mintaadatok és a Jupyter notebook

### <a name="sample-workflow"></a>Munkafolyamat-minta 
A példában a munkafolyamat követi:
 
1. **Adatok**: az adatkészlet betöltése és átalakíthatja TimeSeriesDataFrame. A dataframe egy idősorozat-adatok az Azure Machine Learning csomag által előírt előrejelzés, itt néven struktúra **AMLPF**.

2. **Funkciók létrehozása**: különböző featurization transzformátorok AMLPF által biztosított szolgáltatások létrehozásához használja.

3. **Train, és válassza ki a legoptimálisabb modellt**: különböző egyváltozós time series modelleket és gépi tanulási modellek teljesítményének összehasonlítására. 

4. **Modell üzembe helyezése**: helyezze üzembe a betanított modell folyamatot webszolgáltatásként, amely az Azure Machine Learning Workbench-n keresztül, így mások is képes használni.

### <a name="get-the-jupyter-notebook"></a>A Jupyter notebook beszerzése

A jegyzetfüzet futtatásához a Kódminták letöltése jelen dokumentumban ismertetett anyagokon saját magának.

> [!div class="nextstepaction"]
> [A Jupyter notebook beszerzése](https://aka.ms/aml-packages/forecasting/notebooks/sales_forecasting)

### <a name="explore-the-sample-data"></a>Ismerje meg a mintaadatokat

A machine learning példák a minták támaszkodjon kövesse kód előrejelzés az [University a Chicagói a Dominick kifinomultabb élelmiszerek adatkészlet](https://research.chicagobooth.edu/kilts/marketing-databases/dominicks) narancssárga lé értékesítés-előrejelzést. A Dominick egy pékséglánc láncot, a Chicagói nagyvárosi körzetében volt.

### <a name="import-any-dependencies-for-this-sample"></a>Ehhez a mintához függőségek importálása

Ezeket a függőségeket kell importálni a következő kódmintákhoz:


```python
import pandas as pd
import numpy as np
import math
import pkg_resources
from datetime import timedelta
import matplotlib
matplotlib.use('agg')
%matplotlib inline
from matplotlib import pyplot as plt

from sklearn.linear_model import Lasso, ElasticNet
from sklearn.ensemble import RandomForestRegressor, GradientBoostingRegressor
from sklearn.neighbors import KNeighborsRegressor

from ftk import TimeSeriesDataFrame, ForecastDataFrame, AzureMLForecastPipeline
from ftk.ts_utils import last_n_periods_split

from ftk.transforms import TimeSeriesImputer, TimeIndexFeaturizer, DropColumns
from ftk.transforms.grain_index_featurizer import GrainIndexFeaturizer
from ftk.models import Arima, SeasonalNaive, Naive, RegressionForecaster, ETS, BestOfForecaster
from ftk.models.forecaster_union import ForecasterUnion
from ftk.model_selection import TSGridSearchCV, RollingOriginValidator

from azuremltkbase.deployment import AMLSettings
from ftk.operationalization.forecast_webservice_factory import ForecastWebserviceFactory
from ftk.operationalization import ScoreContext

from ftk.data import get_a_year_of_daily_weather_data
print('imports done')
```

    imports done
    

## <a name="load-data-and-explore"></a>Adatok betöltése és megismerése

Ez a kódrészlet ebben az esetben egy nyers adatkészlet kezdve tipikus folyamatát mutatja be a [Dominick a kifinomultabb élelmiszerek származó adatok](https://research.chicagobooth.edu/kilts/marketing-databases/dominicks).  A kényelmi funkciót is használhatja [load_dominicks_oj_data](https://docs.microsoft.com/python/api/ftk.data.dominicks_oj.load_dominicks_oj_data).


```python
# Load the data into a pandas DataFrame
csv_path = pkg_resources.resource_filename('ftk', 'data/dominicks_oj/dominicks_oj.csv')
whole_df = pd.read_csv(csv_path, low_memory = False)
whole_df.head()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Store</th>
      <th>márka</th>
      <th>hét</th>
      <th>logmove</th>
      <th>feat</th>
      <th>price</th>
      <th>AGE60</th>
      <th>EDUC</th>
      <th>ETNIKAI</th>
      <th>BEVÉTEL</th>
      <th>HHLARGE</th>
      <th>WORKWOM</th>
      <th>HVAL150</th>
      <th>SSTRDIST</th>
      <th>SSTRVOL</th>
      <th>CPDIST5</th>
      <th>CPWVOL5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>tropicana</td>
      <td>40</td>
      <td>9,02</td>
      <td>0</td>
      <td>3,87</td>
      <td>0,23</td>
      <td>0.25</td>
      <td>0,11</td>
      <td>10.55</td>
      <td>0.10-ás</td>
      <td>0,30</td>
      <td>0.46</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>tropicana</td>
      <td>46</td>
      <td>8.72</td>
      <td>0</td>
      <td>3,87</td>
      <td>0,23</td>
      <td>0.25</td>
      <td>0,11</td>
      <td>10.55</td>
      <td>0.10-ás</td>
      <td>0,30</td>
      <td>0.46</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>tropicana</td>
      <td>47</td>
      <td>8.25</td>
      <td>0</td>
      <td>3,87</td>
      <td>0,23</td>
      <td>0.25</td>
      <td>0,11</td>
      <td>10.55</td>
      <td>0.10-ás</td>
      <td>0,30</td>
      <td>0.46</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>tropicana</td>
      <td>48</td>
      <td>8.99</td>
      <td>0</td>
      <td>3,87</td>
      <td>0,23</td>
      <td>0.25</td>
      <td>0,11</td>
      <td>10.55</td>
      <td>0.10-ás</td>
      <td>0,30</td>
      <td>0.46</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>tropicana</td>
      <td>50</td>
      <td>9.09</td>
      <td>0</td>
      <td>3,87</td>
      <td>0,23</td>
      <td>0.25</td>
      <td>0,11</td>
      <td>10.55</td>
      <td>0.10-ás</td>
      <td>0,30</td>
      <td>0.46</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
    </tr>
  </tbody>
</table>



Az adatok márka és az áruházbeli heti eladások állnak. Eladott mennyiség logaritmusát szerepel a _logmove_ oszlop. Egyes ügyfelek demográfiai funkciók is szerepel. 

A time series modell, a következő elemek kinyerése a dataframe kell: 
+ Egy dátum/idő tengely 
+ Az lehet az előre jelzett értékesítési mennyiség


```python
# The sales are contained in the 'logmove' column. 
# Values are logarithmic, so exponentiate and round them to get quantity sold
def expround(x):
    return math.floor(math.exp(x) + 0.5)
whole_df['Quantity'] = whole_df['logmove'].apply(expround)

# The time axis is in the 'week' column
# This is the week offset from the week of 1989-09-07 through 1989-09-13 inclusive
# Create new datetime columns containing the start and end of each week period
weekZeroStart = pd.to_datetime('1989-09-07 00:00:00')
weekZeroEnd = pd.to_datetime('1989-09-13 23:59:59')
whole_df['WeekFirstDay'] = whole_df['week'].apply(lambda n: weekZeroStart + timedelta(weeks=n))
whole_df['WeekLastDay'] = whole_df['week'].apply(lambda n: weekZeroEnd + timedelta(weeks=n))
whole_df[['store','brand','WeekLastDay','Quantity']].head()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Store</th>
      <th>márka</th>
      <th>WeekLastDay</th>
      <th>Mennyiség</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>tropicana</td>
      <td>1990-06-20 23:59:59</td>
      <td>8256</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>tropicana</td>
      <td>1990-08-01 23:59:59</td>
      <td>6144</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>tropicana</td>
      <td>1990-08-08 23:59:59</td>
      <td>3840</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>tropicana</td>
      <td>1990-08-15 23:59:59</td>
      <td>8000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>tropicana</td>
      <td>1990-08-29 23:59:59</td>
      <td>8896</td>
    </tr>
  </tbody>
</table>




```python
nseries = whole_df.groupby(['store', 'brand']).ngroups
print('{} time series in the data frame.'.format(nseries))
```

    249 time series in the data frame.
    

Az adatok körülbelül 250 különböző kombinációit tároló és a egy adatkeretben márka tartalmazza. Minden kombinációja határozza meg a saját idősorozat, az értékesítés. 

Használhatja a [TimeSeriesDataFrame](https://docs.microsoft.com/python/api/ftk.dataframe_ts.timeseriesdataframe?view=azure-ml-py-latest) osztály egyetlen struktúra használata több sorozat kényelmesen modellezésére a _időfelbontási szint_. A időfelbontási szint szerint van megadva a `store` és `brand` oszlopokat.

A különbség a között _időfelbontási szint_ és _csoport_ , hogy időfelbontási szint mindig fizikailag kifejező, a való világból bármit, amíg a csoport nem kell lennie. A csomag belső függvények csoport használatával az idősorozatban egyetlen modell létrehozása, ha a felhasználó úgy véli, hogy ez a csoportosítás javítja a modell teljesítményét. Alapértelmezés szerint tulajdonoscsoportja időfelbontási szint egyenlőnek kell lennie, és egyetlen modellben minden egyes időfelbontási szint lett tervezve. 


```python
# Create a TimeSeriesDataFrame
# Use end of period as the time index
# Store and brand combinations label the grain 
# i.e. there is one time series for each unique pair of store and grain
whole_tsdf = TimeSeriesDataFrame(whole_df, 
                                 grain_colnames=['store', 'brand'],
                                 time_colname='WeekLastDay', 
                                 ts_value_colname='Quantity',
                                 group_colnames='store')

whole_tsdf[['Quantity']].head()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th></th>
      <th>Mennyiség</th>
    </tr>
    <tr>
      <th>WeekLastDay</th>
      <th>Store</th>
      <th>márka</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1990-06-20 23:59:59</th>
      <th>2</th>
      <th>tropicana</th>
      <td>8256</td>
    </tr>
    <tr>
      <th>1990-08-01 23:59:59</th>
      <th>2</th>
      <th>tropicana</th>
      <td>6144</td>
    </tr>
    <tr>
      <th>1990-08-08 23:59:59</th>
      <th>2</th>
      <th>tropicana</th>
      <td>3840</td>
    </tr>
    <tr>
      <th>1990-08-15 23:59:59</th>
      <th>2</th>
      <th>tropicana</th>
      <td>8000</td>
    </tr>
    <tr>
      <th>1990-08-29 23:59:59</th>
      <th>2</th>
      <th>tropicana</th>
      <td>8896</td>
    </tr>
  </tbody>
</table>



A TimeSeriesDataFrame reprezentációban szereplő a időtengelye időfelbontási szint most már az adatok keret index részét képezik, és lehetővé teszi a pandas dátum és idő, tovább szeletelve funkciókat is egyszerű hozzáférést.


```python
# sort so we can slice
whole_tsdf.sort_index(inplace=True)

# Get sales of dominick's brand orange juice from store 2 during summer 1990
whole_tsdf.loc[pd.IndexSlice['1990-06':'1990-09', 2, 'dominicks'], ['Quantity']]
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th></th>
      <th>Mennyiség</th>
    </tr>
    <tr>
      <th>WeekLastDay</th>
      <th>Store</th>
      <th>márka</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1990-06-20 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>10560</td>
    </tr>
    <tr>
      <th>1990-08-01 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>8000</td>
    </tr>
    <tr>
      <th>1990-08-08 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>6848</td>
    </tr>
    <tr>
      <th>1990-08-15 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>2880</td>
    </tr>
    <tr>
      <th>1990-08-29 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>1600</td>
    </tr>
    <tr>
      <th>1990-09-05-23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>25344</td>
    </tr>
    <tr>
      <th>1990-09-12 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>10752</td>
    </tr>
    <tr>
      <th>1990-09-19-23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>6656</td>
    </tr>
    <tr>
      <th>1990-09-26 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>6592</td>
    </tr>
  </tbody>
</table>



A [TimeSeriesDataFrame.ts_report](https://docs.microsoft.com/python/api/ftk.dataframe_ts.timeseriesdataframe?view=azure-ml-py-latest#ts-report) függvény a time series adatok keret átfogó jelentést hoz létre. A jelentés egy általános leírása és is idősorozat-adatok az adott statisztika tartalmazza. 


```python
whole_tsdf.ts_report()
```

    --------------------------------  Data Overview  ---------------------------------
    <class 'ftk.time_series_data_frame.TimeSeriesDataFrame'>
    MultiIndex: 28947 entries, (1990-06-20 23:59:59, 2, dominicks) to (1992-10-07 23:59:59, 137, tropicana)
    Data columns (total 17 columns):
    week            28947 non-null int64
    logmove         28947 non-null float64
    feat            28947 non-null int64
    price           28947 non-null float64
    AGE60           28947 non-null float64
    EDUC            28947 non-null float64
    ETHNIC          28947 non-null float64
    INCOME          28947 non-null float64
    HHLARGE         28947 non-null float64
    WORKWOM         28947 non-null float64
    HVAL150         28947 non-null float64
    SSTRDIST        28947 non-null float64
    SSTRVOL         28947 non-null float64
    CPDIST5         28947 non-null float64
    CPWVOL5         28947 non-null float64
    Quantity        28947 non-null int64
    WeekFirstDay    28947 non-null datetime64[ns]
    dtypes: datetime64[ns](1), float64(13), int64(3)
    memory usage: 3.8+ MB
    --------------------------  Numerical Variable Summary  --------------------------
              week  logmove     feat    price    AGE60     EDUC   ETHNIC   INCOME  \
    count 28947.00 28947.00 28947.00 28947.00 28947.00 28947.00 28947.00 28947.00   
    mean    100.46     9.17     0.24     2.28     0.17     0.23     0.16    10.62   
    std      34.69     1.02     0.43     0.65     0.06     0.11     0.19     0.28   
    min      40.00     4.16     0.00     0.52     0.06     0.05     0.02     9.87   
    25%      70.00     8.49     0.00     1.79     0.12     0.15     0.04    10.46   
    50%     101.00     9.03     0.00     2.17     0.17     0.23     0.07    10.64   
    75%     130.00     9.76     0.00     2.73     0.21     0.28     0.19    10.80   
    max     160.00    13.48     1.00     3.87     0.31     0.53     1.00    11.24   
    
           HHLARGE  WORKWOM  HVAL150  SSTRDIST  SSTRVOL  CPDIST5  CPWVOL5  \
    count 28947.00 28947.00 28947.00  28947.00 28947.00 28947.00 28947.00   
    mean      0.12     0.36     0.34      5.10     1.21     2.12     0.44   
    std       0.03     0.05     0.24      3.47     0.53     0.73     0.22   
    min       0.01     0.24     0.00      0.13     0.40     0.77     0.09   
    25%       0.10     0.31     0.12      2.77     0.73     1.63     0.27   
    50%       0.11     0.36     0.35      4.65     1.12     1.96     0.38   
    75%       0.14     0.40     0.53      6.65     1.54     2.53     0.56   
    max       0.22     0.47     0.92     17.86     2.57     4.11     1.14   
    
           Quantity  
    count  28947.00  
    mean   17312.21  
    std    27477.66  
    min       64.00  
    25%     4864.00  
    50%     8384.00  
    75%    17408.00  
    max   716416.00  
    ------------------------  Non-Numerical Variable Summary  -----------------------
                   WeekFirstDay
    count                 28947
    unique                  121
    top     1992-03-12 00:00:00
    freq                    249
    first   1990-06-14 00:00:00
    last    1992-10-01 00:00:00
    ------------------------------  Time Series Summary  -----------------------------
    Number of time series                 249
    Minimum time                    1990-06-20 23:59:59
    Maximum time                    1992-10-07 23:59:59
    
    Inferred frequencies
    Number of ['store', 'brand']s with frequency W-WED     249
    Use get_frequency_dict() method to explore ['store', 'brand']s with unusual frequency and clean data
    
    Detected seasonalities
    Number of ['store', 'brand']s with seasonality 1         190
    Number of ['store', 'brand']s with seasonality 15        15
    Number of ['store', 'brand']s with seasonality 14        11
    Number of ['store', 'brand']s with seasonality 7         9
    Number of ['store', 'brand']s with seasonality 6         8
    Number of ['store', 'brand']s with seasonality 8         5
    Number of ['store', 'brand']s with seasonality 2         4
    Number of ['store', 'brand']s with seasonality 23        2
    Number of ['store', 'brand']s with seasonality 3         1
    Number of ['store', 'brand']s with seasonality 11        1
    Number of ['store', 'brand']s with seasonality 12        1
    Number of ['store', 'brand']s with seasonality 13        1
    Number of ['store', 'brand']s with seasonality 47        1
    Use get_seasonality_dict() method to explore ['store', 'brand']s with unusual seasonality and clean data
    -----------------------------  Value Column Summary  -----------------------------
    Value column                        Quantity
    Percentage of missing values        0.00
    Percentage of zero values           0.00
    Mean coefficient of variation       31688.52
    Median coefficient of variation     24000.20
    Minimum coefficient of variation    ['store', 'brand'] (48, 'tropicana'): 4475.53
    Maximum coefficient of variation    ['store', 'brand'] (111, 'dominicks'): 193333.55
    ------------------------------  Correlation Matrix  ------------------------------
        week  logmove  feat  price  AGE60  EDUC  ETHNIC  INCOME  HHLARGE  WORKWOM  \
    0   1.00     0.10  0.04  -0.21  -0.01  0.01    0.00    0.00     0.01    -0.00   
    1   0.10     1.00  0.54  -0.43   0.09  0.00    0.06   -0.04    -0.06    -0.08   
    2   0.04     0.54  1.00  -0.29  -0.00  0.00    0.00   -0.00    -0.00     0.00   
    3  -0.21    -0.43 -0.29   1.00   0.04  0.02    0.04   -0.03    -0.04    -0.02   
    4  -0.01     0.09 -0.00   0.04   1.00 -0.31   -0.09   -0.15    -0.32    -0.63   
    5   0.01     0.00  0.00   0.02  -0.31  1.00   -0.34    0.66    -0.39     0.56   
    6   0.00     0.06  0.00   0.04  -0.09 -0.34    1.00   -0.72     0.25    -0.29   
    7   0.00    -0.04 -0.00  -0.03  -0.15  0.66   -0.72    1.00    -0.08     0.40   
    8   0.01    -0.06 -0.00  -0.04  -0.32 -0.39    0.25   -0.08     1.00    -0.28   
    9  -0.00    -0.08  0.00  -0.02  -0.63  0.56   -0.29    0.40    -0.28     1.00   
    10  0.01     0.02  0.00   0.04  -0.11  0.89   -0.42    0.64    -0.48     0.45   
    11  0.01    -0.00  0.00   0.08   0.07 -0.12    0.54   -0.41     0.06    -0.19   
    12 -0.01    -0.09 -0.00   0.03  -0.05 -0.13    0.23   -0.26     0.04    -0.06   
    13 -0.01     0.02 -0.00  -0.06   0.09 -0.20   -0.22    0.21     0.19    -0.13   
    14 -0.00    -0.12 -0.00  -0.01  -0.09  0.28   -0.38    0.36    -0.20     0.37   
    15  0.03     0.76  0.47  -0.36   0.08 -0.04    0.11   -0.08    -0.00    -0.10   
    
        HVAL150  SSTRDIST  SSTRVOL  CPDIST5  CPWVOL5  Quantity  
    0      0.01      0.01    -0.01    -0.01    -0.00      0.03  
    1      0.02     -0.00    -0.09     0.02    -0.12      0.76  
    2      0.00      0.00    -0.00    -0.00    -0.00      0.47  
    3      0.04      0.08     0.03    -0.06    -0.01     -0.36  
    4     -0.11      0.07    -0.05     0.09    -0.09      0.08  
    5      0.89     -0.12    -0.13    -0.20     0.28     -0.04  
    6     -0.42      0.54     0.23    -0.22    -0.38      0.11  
    7      0.64     -0.41    -0.26     0.21     0.36     -0.08  
    8     -0.48      0.06     0.04     0.19    -0.20     -0.00  
    9      0.45     -0.19    -0.06    -0.13     0.37     -0.10  
    10     1.00     -0.17    -0.24    -0.22     0.27     -0.04  
    11    -0.17      1.00     0.17    -0.11    -0.40      0.06  
    12    -0.24      0.17     1.00    -0.05     0.36     -0.02  
    13    -0.22     -0.11    -0.05     1.00     0.02     -0.00  
    14     0.27     -0.40     0.36     0.02     1.00     -0.11  
    15    -0.04      0.06    -0.02    -0.00    -0.11      1.00  
    You may call TimeSeriesDataFrame.plot_scatter_matrix() to get a correlation matrix plot. However, this
    is not recommended if your data is big.
    



![PNG](./media/how-to-build-deploy-forecast-models/output_15_1.png)



![PNG](./media/how-to-build-deploy-forecast-models/output_15_2.png)



![PNG](./media/how-to-build-deploy-forecast-models/output_15_3.png)



![PNG](./media/how-to-build-deploy-forecast-models/output_15_4.png)



![PNG](./media/how-to-build-deploy-forecast-models/output_15_5.png)



![PNG](./media/how-to-build-deploy-forecast-models/output_15_6.png)


## <a name="integrate-with-external-data"></a>Külső adatok integrálása

Néha hasznos külső adatokat integrálhat, az előrejelzési további szolgáltatásokat. Ez a kódminta a csatlakozik TimeSeriesDataFrame időjárási kapcsolódó külső adatokat.


```python
# Load weather data
weather_1990 = get_a_year_of_daily_weather_data(year=1990)
weather_1991 = get_a_year_of_daily_weather_data(year=1991)
weather_1992 = get_a_year_of_daily_weather_data(year=1992)

# Preprocess weather data
weather_all = pd.concat([weather_1990, weather_1991, weather_1992])
weather_all.reset_index(inplace=True)

# Only use a subset of columns
weather_all = weather_all[['TEMP', 'DEWP', 'WDSP', 'PRCP']]

# Compute the WeekLastDay column, in order to merge with sales data
weather_all['WeekLastDay'] = pd.Series(
    weather_all.time_index - weekZeroStart, 
    index=weather_all.time_index).apply(lambda n: weekZeroEnd + timedelta(weeks=math.floor(n.days/7)))

# Resample daily weather data to weekly data
weather_all = weather_all.groupby('WeekLastDay').mean()

# Set WeekLastDay as new time index
weather_all = TimeSeriesDataFrame(weather_all, time_colname='WeekLastDay')

# Merge weather data with sales data
whole_tsdf = whole_tsdf.merge(weather_all, how='left', on='WeekLastDay')
whole_tsdf.head()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th></th>
      <th>hét</th>
      <th>logmove</th>
      <th>feat</th>
      <th>price</th>
      <th>AGE60</th>
      <th>EDUC</th>
      <th>ETNIKAI</th>
      <th>BEVÉTEL</th>
      <th>HHLARGE</th>
      <th>WORKWOM</th>
      <th>...</th>
      <th>SSTRDIST</th>
      <th>SSTRVOL</th>
      <th>CPDIST5</th>
      <th>CPWVOL5</th>
      <th>Mennyiség</th>
      <th>WeekFirstDay</th>
      <th>TEMP</th>
      <th>DEWP</th>
      <th>WDSP</th>
      <th>PRCP</th>
    </tr>
    <tr>
      <th>WeekLastDay</th>
      <th>Store</th>
      <th>márka</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="5" valign="top">1990-06-20 23:59:59</th>
      <th rowspan="3" valign="top">2</th>
      <th>dominicks</th>
      <td>40</td>
      <td>9.26</td>
      <td>1</td>
      <td>1.59</td>
      <td>0,23</td>
      <td>0.25</td>
      <td>0,11</td>
      <td>10.55</td>
      <td>0.10-ás</td>
      <td>0,30</td>
      <td>...</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
      <td>10560</td>
      <td>1990-06-14</td>
      <td>72,00 lesz</td>
      <td>61.87</td>
      <td>9.74</td>
      <td>0.19</td>
    </tr>
    <tr>
      <th>MINUTE.maid</th>
      <td>40</td>
      <td>8.41</td>
      <td>0</td>
      <td>3.17.</td>
      <td>0,23</td>
      <td>0.25</td>
      <td>0,11</td>
      <td>10.55</td>
      <td>0.10-ás</td>
      <td>0,30</td>
      <td>...</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
      <td>4480</td>
      <td>1990-06-14</td>
      <td>72,00 lesz</td>
      <td>61.87</td>
      <td>9.74</td>
      <td>0.19</td>
    </tr>
    <tr>
      <th>tropicana</th>
      <td>40</td>
      <td>9,02</td>
      <td>0</td>
      <td>3,87</td>
      <td>0,23</td>
      <td>0.25</td>
      <td>0,11</td>
      <td>10.55</td>
      <td>0.10-ás</td>
      <td>0,30</td>
      <td>...</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
      <td>8256</td>
      <td>1990-06-14</td>
      <td>72,00 lesz</td>
      <td>61.87</td>
      <td>9.74</td>
      <td>0.19</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">5</th>
      <th>dominicks</th>
      <td>40</td>
      <td>7.49</td>
      <td>1</td>
      <td>1.59</td>
      <td>0.12</td>
      <td>0.32</td>
      <td>0,05</td>
      <td>10.92</td>
      <td>0.10-ás</td>
      <td>0.41</td>
      <td>...</td>
      <td>3.80</td>
      <td>0.68</td>
      <td>1.60</td>
      <td>0.74</td>
      <td>1792</td>
      <td>1990-06-14</td>
      <td>72,00 lesz</td>
      <td>61.87</td>
      <td>9.74</td>
      <td>0.19</td>
    </tr>
    <tr>
      <th>MINUTE.maid</th>
      <td>40</td>
      <td>8.35</td>
      <td>0</td>
      <td>2.99</td>
      <td>0.12</td>
      <td>0.32</td>
      <td>0,05</td>
      <td>10.92</td>
      <td>0.10-ás</td>
      <td>0.41</td>
      <td>...</td>
      <td>3.80</td>
      <td>0.68</td>
      <td>1.60</td>
      <td>0.74</td>
      <td>4224</td>
      <td>1990-06-14</td>
      <td>72,00 lesz</td>
      <td>61.87</td>
      <td>9.74</td>
      <td>0.19</td>
    </tr>
  </tbody>
</table>


## <a name="preprocess-data-and-impute-missing-values"></a>Az adatok előfeldolgozása és a hiányzó értékeket imputálására

Indítsa el az adatok gyakorlókészlethez és a egy csoportot az tesztelési halmazra a [last_n_periods_split](https://docs.microsoft.com/python/api/ftk.ts_utils?view=azure-ml-py-latest) segédprogram függvény. A létrejövő set tesztelés tartalmazza minden egyes utolsó 40 észrevételeit. 


```python
train_tsdf, test_tsdf = last_n_periods_split(whole_tsdf, 40)
```

Alapszintű time series modellek összefüggő idősorozat van szükség. Ellenőrizze, hogy rendszeres, ami azt jelenti, hogy rendelkeznek-e egy rendszeres időközönként, használja a mintavétel ideje index az adatsorozat-a [check_regularity_by_grain](https://docs.microsoft.com/python/api/ftk.dataframe_ts.timeseriesdataframe?view=azure-ml-py-latest#check-regularity-by-grain) függvény.


```python
ts_regularity = train_tsdf.check_regularity_by_grain()
print(ts_regularity[ts_regularity['regular'] == False])
```

                                              problems  regular
    store brand                                                
    2     dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    5     dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    8     dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    9     dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    12    dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    14    dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    18    dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    21    dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    28    dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    33    dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    ...                                            ...      ...
    119   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    121   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    123   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    126   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    128   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    129   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    130   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    131   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    134   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    137   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    
    [213 rows x 2 columns]
    

Láthatja, hogy a sorozat (213 kívüli 249) többsége szabálytalan. Egy [imputálási átalakító](https://docs.microsoft.com/python/api/ftk.transforms.ts_imputer.timeseriesimputer?view=azure-ml-py-latest) értékesítési mennyiség értékek a hiányzó szükséges. Többféleképpen imputálási, amíg az az alábbi mintakód egy lineáris interpolációs használ.


```python
# Use a TimeSeriesImputer to linearly interpolate missing values
imputer = TimeSeriesImputer(input_column='Quantity', 
                            option='interpolate',
                            method='linear',
                            freq='W-WED')

train_imputed_tsdf = imputer.transform(train_tsdf)
```

A imputálási kód futtatása után az összes adatsorozat rendelkezik egy szokásos gyakoriság:


```python
ts_regularity_imputed = train_imputed_tsdf.check_regularity_by_grain()
print(ts_regularity_imputed[ts_regularity_imputed['regular'] == False])
```

    Empty DataFrame
    Columns: [problems, regular]
    Index: []
    

## <a name="univariate-time-series-models"></a>Egyváltozós time series modellek

Most, hogy eltávolította az adatokat, elkezdheti modellezési.  Először hozzon létre három egyváltozós modell: a "natív" modell, a "szezonális naiv" típusú és egy "ARIMA" modellt.
* Az előre jelzett érték az aktuális időszak a tényleges cél változó értéke a legutóbbi időszak Naiv előrejelzési algoritmust használja.

* Az időszakos Naiv algoritmus a tényleges cél változó értékét az előző szezon azonos idő pont használja, mint az előre jelzett érték az aktuális idő pont. Néhány példa: a tényleges érték az előző év azonos hónapjában segítségével előrejelzési hónappal az aktuális év; tegnap azonos órányi használatával előrejelzést órát még ma. 

* Az exponenciális simítás (ETS) algoritmus előrejelzések szerint súlyozott átlagok múltbeli megfigyelések megoldásokkal csillapított exponenciálisan növekszik, a megfigyelés első régebbi súlyozású: állít elő. 

* A Autoregressziós integrált mozgó átlag (ARIMA) algoritmus a autocorrelation az idősorozat-adatokat rögzíti. ARIMA kapcsolatos további információkért lásd: [ezt a hivatkozást](https://en.wikipedia.org/wiki/Autoregressive_integrated_moving_average)

Indítsa el az egyes modell paraméter az adatfeltárás alapján beállításával. 


```python
oj_series_freq = 'W-WED'
oj_series_seasonality = 52
```

### <a name="initialize-models"></a>Modell inicializálása


```python
# Initialize naive model.
naive_model = Naive(freq=oj_series_freq)

# Initialize seasonal naive model. 
seasonal_naive_model = SeasonalNaive(freq=oj_series_freq, 
                                     seasonality=oj_series_seasonality)

# Initialize ETS model.
ets_model = ETS(freq=oj_series_freq, seasonality=oj_series_seasonality)

# Initialize ARIMA(p,d,q) model.
arima_order = [2, 1, 0]
arima_model = Arima(oj_series_freq, arima_order)
```

### <a name="combine-multiple-models"></a>Több modell egyesítése

A [ForecasterUnion](https://docs.microsoft.com/python/api/ftk.models.forecaster_union?view=azure-ml-py-latest) estimator lehetővé teszi, hogy több estimators egyesítése, és ezeket egyetlen sor kód segítségével a laphoz/előrejelzésével.


```python
forecaster_union = ForecasterUnion(
    forecaster_list=[('naive', naive_model), ('seasonal_naive', seasonal_naive_model), 
                     ('ets', ets_model), ('arima', arima_model)]) 
```

### <a name="fit-and-predict"></a>Szélességhez és előrejelzése

A estimators AMLPF a kövesse az azonos API-t, a scikit-estimators további: regressziós módszert a modellek tanítása és a egy predict metódus előrejelzések létrehozásához. 

**Modellek**  
Mivel ezek a modellek minden egyváltozós modell, egy modellt az adatok minden időfelbontási szint van igazítás. AMLPF használja, minden 249 modell csak egy függvényt hívással kell illeszkednek.


```python
forecaster_union_fitted = forecaster_union.fit(train_imputed_tsdf)
```

**A teszt adatai értékesítés-előrejelzést**  
Hasonlóan a regressziós módszert, hozhat létre minden 249 adatsorozathoz előrejelzéseket a tesztelési adatkészlet egy hívás az a `predict` függvény. 


```python
forecaster_union_prediction = forecaster_union_fitted.predict(test_tsdf, retain_feature_column=True)
```

**Modellek teljesítményének kiértékelése**   

Most, kiszámíthatja a teszt esetén előre jelzett hibákat. Az átlagos abszolút százalékos aránya (MAPE) hiba itt is használhatja. MAPE képest a tényleges értékesítési értékek átlagos abszolút százalékos hiba. A ```calc_error``` függvény néhány beépített funkciókat biztosít a gyakran használt hiba metrikákkal. Az egyéni hiba függvény MedianAPE kiszámítása, és adja át a err_fun argumentum is meghatározhat.


```python
def calc_median_ape(y_true, y_pred):
    y_true = np.array(y_true).astype(float)
    y_pred = np.array(y_pred).astype(float)
    y_true_rm_na = y_true[~(np.isnan(y_true) | np.isnan(y_pred))]
    y_pred_rm_na = y_pred[~(np.isnan(y_true) | np.isnan(y_pred))]
    y_true = y_true_rm_na
    y_pred = y_pred_rm_na
    if len(y_true) == 0:
        # if there is no entries left after removing na data, return np.nan
        return(np.nan)
    y_true_rm_zero = y_true[y_true != 0]
    y_pred_rm_zero = y_pred[y_true != 0]
    if len(y_true_rm_zero) == 0:
        # if all values are zero, np.nan will be returned.
        return(np.nan)
    ape = np.abs((y_true_rm_zero - y_pred_rm_zero) / y_true_rm_zero) * 100
    median_ape = np.median(ape)
    return median_ape
```


```python
forecaster_union_MAPE = forecaster_union_prediction.calc_error(err_name='MAPE',
                                                               by='ModelName')
forecaster_union_MedianAPE = forecaster_union_prediction.calc_error(err_name='MedianAPE', 
                                                                    err_fun=calc_median_ape,
                                                                    by='ModelName')

univariate_model_errors = forecaster_union_MAPE.merge(forecaster_union_MedianAPE, on='ModelName')
univariate_model_errors
```



<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>modelName</th>
      <th>MAPE</th>
      <th>MedianAPE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>arima</td>
      <td>126.57</td>
      <td>66.49</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ETS</td>
      <td>187.89</td>
      <td>75.73</td>
    </tr>
    <tr>
      <th>2</th>
      <td>natív</td>
      <td>103.57</td>
      <td>59.14</td>
    </tr>
    <tr>
      <th>3</th>
      <td>seasonal_naive</td>
      <td>180.54</td>
      <td>65.99</td>
    </tr>
  </tbody>
</table>



## <a name="build-machine-learning-models"></a>Machine learning-modellek létrehozása

Mellett hagyományos egyváltozós modellek Azure Machine Learning-csomag az előrejelzés emellett lehetővé teszi a machine learning-modellek létrehozása.

Ezen modellek szolgáltatások létrehozásával kezdődik.

### <a name="feature-engineering"></a>A szolgáltatás műszaki osztály

**Transzformátorok**   
A csomag számos transzformátorok idősorozat-adatok előfeldolgozása és featurization biztosít. A következő példák az előfeldolgozási és featurization funkcióit mutatják be.


```python
# DropColumns: Drop columns that should not be included for modeling. `logmove` is the log of the number of 
# units sold, so providing this number would be cheating. `WeekFirstDay` would be 
# redundant since we already have a feature for the last day of the week.
columns_to_drop = ['logmove', 'WeekFirstDay', 'week']
column_dropper = DropColumns(columns_to_drop)

# TimeSeriesImputer: Fill missing values in the features
# First, we need to create a dictionary with key as column names and value as values used to fill missing 
# values for that column. We are going to use the mean to fill missing values for each column.
columns_with_missing_values = train_imputed_tsdf.columns[pd.DataFrame(train_imputed_tsdf).isnull().any()].tolist()
columns_with_missing_values = [c for c in columns_with_missing_values if c not in columns_to_drop]
missing_value_imputation_dictionary = {}
for c in columns_with_missing_values:
    missing_value_imputation_dictionary[c] = train_imputed_tsdf[c].mean()
fillna_imputer = TimeSeriesImputer(option='fillna', 
                                   input_column=columns_with_missing_values,
                                   value=missing_value_imputation_dictionary)

# TimeIndexFeaturizer: extract temporal features from timestamps
time_index_featurizer = TimeIndexFeaturizer(correlation_cutoff=0.1, overwrite_columns=True)

# GrainIndexFeaturizer: create indicator variables for stores and brands
grain_featurizer = GrainIndexFeaturizer(overwrite_columns=True, ts_frequency=oj_series_freq)
```

**A folyamatok**   
Folyamat objektumok megkönnyítik a több lépésre mentéséhez, így a különböző objektumok, és újra alkalmazhatók. Ezenkívül folyamat objektumokat is pickled, így könnyen hordozható való üzembe helyezéshez gépeivel. Eddig a folyamat használatával létrehozott összes transzformátorok láncolhatja össze. 


```python
pipeline_ml = AzureMLForecastPipeline([('drop_columns', column_dropper), 
                                       ('fillna_imputer', fillna_imputer),
                                       ('time_index_featurizer', time_index_featurizer),
                                       ('grain_featurizer', grain_featurizer)
                                      ])


train_feature_tsdf = pipeline_ml.fit_transform(train_imputed_tsdf)
test_feature_tsdf = pipeline_ml.transform(test_tsdf)

# Let's get a look at our new feature set
print(train_feature_tsdf.head())
```

    F1 2018-06-14 23:10:03,472 INFO azureml.timeseries - pipeline fit_transform started. 
    F1 2018-06-14 23:10:07,317 INFO azureml.timeseries - pipeline fit_transform finished. Time elapsed 0:00:03.845078
    F1 2018-06-14 23:10:07,317 INFO azureml.timeseries - pipeline transforms started. 
    F1 2018-06-14 23:10:16,499 INFO azureml.timeseries - pipeline transforms finished. Time elapsed 0:00:09.182314
                                           feat  price  AGE60  EDUC  ETHNIC  \
    WeekLastDay         store brand                                           
    1990-06-20 23:59:59 2     dominicks    1.00   1.59   0.23  0.25    0.11   
                              minute.maid  0.00   3.17   0.23  0.25    0.11   
                              tropicana    0.00   3.87   0.23  0.25    0.11   
                        5     dominicks    1.00   1.59   0.12  0.32    0.05   
                              minute.maid  0.00   2.99   0.12  0.32    0.05   
    
                                           INCOME  HHLARGE  WORKWOM  HVAL150  \
    WeekLastDay         store brand                                            
    1990-06-20 23:59:59 2     dominicks     10.55     0.10     0.30     0.46   
                              minute.maid   10.55     0.10     0.30     0.46   
                              tropicana     10.55     0.10     0.30     0.46   
                        5     dominicks     10.92     0.10     0.41     0.54   
                              minute.maid   10.92     0.10     0.41     0.54   
    
                                           SSTRDIST     ...       CPWVOL5  \
    WeekLastDay         store brand                     ...                 
    1990-06-20 23:59:59 2     dominicks        2.11     ...          0.38   
                              minute.maid      2.11     ...          0.38   
                              tropicana        2.11     ...          0.38   
                        5     dominicks        3.80     ...          0.74   
                              minute.maid      3.80     ...          0.74   
    
                                           Quantity  TEMP  DEWP  WDSP  PRCP  year  \
    WeekLastDay         store brand                                                 
    1990-06-20 23:59:59 2     dominicks    10560.00 72.00 61.87  9.74  0.19  1990   
                              minute.maid   4480.00 72.00 61.87  9.74  0.19  1990   
                              tropicana     8256.00 72.00 61.87  9.74  0.19  1990   
                        5     dominicks     1792.00 72.00 61.87  9.74  0.19  1990   
                              minute.maid   4224.00 72.00 61.87  9.74  0.19  1990   
    
                                           day  grain_brand  grain_store  
    WeekLastDay         store brand                                       
    1990-06-20 23:59:59 2     dominicks     20    dominicks            2  
                              minute.maid   20  minute.maid            2  
                              tropicana     20    tropicana            2  
                        5     dominicks     20    dominicks            5  
                              minute.maid   20  minute.maid            5  
    
    [5 rows x 22 columns]
    

 **RegressionForecaster**

A [RegressionForecaster](https://docs.microsoft.com/python/api/ftk.models.regression_forecaster.regressionforecaster?view=azure-ml-py-latest) függvény sklearn regressziós estimators burkolja, így azok a TimeSeriesDataFrame kell képezni. A burkolt forecaster is beteszi mindegyik csoportnál a case tárolóban levő ugyanannak a modellnek. A forecaster tudhat meg egy modellt a sorozat, amely hasonló tekintik, és együtt összevonhatják egy csoportjánál. Adatsorozat-csoport egy modellt gyakran hosszabb adatsorozat adatainak fejlesztésére használja rövid adatsorozathoz előrejelzéseket. Ezek bármely más modellek az erőforrástárban, amelyek támogatják a regressziós modellek helyettesítheti. 


```python
lasso_model = RegressionForecaster(estimator=Lasso(),
                                   make_grain_features=False)
elastic_net_model = RegressionForecaster(estimator=ElasticNet(),
                                         make_grain_features=False)
knn_model = RegressionForecaster(estimator=KNeighborsRegressor(),
                                 make_grain_features=False)
random_forest_model = RegressionForecaster(estimator=RandomForestRegressor(),
                                           make_grain_features=False)
boosted_trees_model = RegressionForecaster(estimator=GradientBoostingRegressor(),
                                           make_grain_features=False)

ml_union = ForecasterUnion(forecaster_list=[
    ('lasso', lasso_model), 
    ('elastic_net', elastic_net_model), 
    ('knn', knn_model), 
    ('random_forest', random_forest_model), 
    ('boosted_trees', boosted_trees_model)
]) 
```


```python
ml_union.fit(train_feature_tsdf, y=train_feature_tsdf.ts_value)
ml_results = ml_union.predict(test_feature_tsdf, retain_feature_column=True)
```


```python
ml_model_MAPE = ml_results.calc_error(err_name='MAPE', by='ModelName')
ml_model_MedianAPE = ml_results.calc_error(err_name='MedianAPE', 
                                           err_fun=calc_median_ape,
                                           by='ModelName')
ml_model_errors = ml_model_MAPE.merge(ml_model_MedianAPE, on='ModelName')
all_errors = pd.concat([univariate_model_errors, ml_model_errors])
all_errors.sort_values('MedianAPE')
```





<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>modelName</th>
      <th>MAPE</th>
      <th>MedianAPE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>random_forest</td>
      <td>78.82</td>
      <td>42.81</td>
    </tr>
    <tr>
      <th>0</th>
      <td>boosted_trees</td>
      <td>78.46</td>
      <td>45.37</td>
    </tr>
    <tr>
      <th>2</th>
      <td>natív</td>
      <td>103.57</td>
      <td>59.14</td>
    </tr>
    <tr>
      <th>2</th>
      <td>knn</td>
      <td>129.85</td>
      <td>65.37</td>
    </tr>
    <tr>
      <th>1</th>
      <td>elastic_net</td>
      <td>125.11</td>
      <td>65.59</td>
    </tr>
    <tr>
      <th>3</th>
      <td>seasonal_naive</td>
      <td>180.54</td>
      <td>65.99</td>
    </tr>
    <tr>
      <th>0</th>
      <td>arima</td>
      <td>126.57</td>
      <td>66.49</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Szabadkézi</td>
      <td>112.87</td>
      <td>67.92</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ETS</td>
      <td>187.89</td>
      <td>75.73</td>
    </tr>
  </tbody>
</table>



Néhány gépi tanulási modelleket képes kihasználni az új funkciók és az a sorozat első jobb előrejelzések pontossága döntésünket is.

### <a name="cross-validation-parameter-and-model-sweeping"></a>Keresztellenőrzési, a paraméter és a modell kezdik    

A csomag néhány hagyományos machine learning-függvény egy előrejelzési alkalmazáshoz alkalmazkodik.  [RollingOriginValidator](https://docs.microsoft.com/python/api/ftk.model_selection.cross_validation.rollingoriginvalidator?view=azure-ml-py-latest) kereszt-ellenőrzési ideiglenesen, does mi lenne, és nem lenne ismert előrejelzési keretrendszer lehetőségekből. 

Az alábbi ábra az egyes szögletes egy idő pontról adatokat jelöli. A kék négyzetes betanítási képviseli, és narancssárga négyzetes képviseli minden egyes modellrészek tesztelése. Tesztelési adatok későbbinek kell lennie az idő pontokról a legnagyobb képzési idő pont. Ellenkező esetben jövőbeli adatkezelési kiszivárgott való betanítási adatok, így a minta értékelés érvénytelenné válik. 
![PNG](./media/how-to-build-deploy-forecast-models/cv_figure.PNG)

**A paraméter kezdik**  
A [TSGridSearchCV](https://docs.microsoft.com/python/api/ftk.model_selection.search.tsgridsearchcv?view=azure-ml-py-latest) osztály teljes körűen keresztül megadott paraméterértékek keres, és használja `RollingOriginValidator` paraméter teljesítmény kiértékelése a legmegfelelőbb paraméterek megtalálása érdekében.


```python
# Set up the `RollingOriginValidator` to do 2 folds of rolling origin cross-validation
rollcv = RollingOriginValidator(n_splits=2)
randomforest_model_for_cv = RegressionForecaster(estimator=RandomForestRegressor(),
                                                 make_grain_features=False)

# Set up our parameter grid and feed it to our grid search algorithm
param_grid_rf = {'estimator__n_estimators': np.array([10, 50, 100])}
grid_cv_rf = TSGridSearchCV(randomforest_model_for_cv, param_grid_rf, cv=rollcv)

# fit and predict
randomforest_cv_fitted= grid_cv_rf.fit(train_feature_tsdf, y=train_feature_tsdf.ts_value)
print('Best paramter: {}'.format(randomforest_cv_fitted.best_params_))
```

    Best paramter: {'estimator__n_estimators': 100}
    

**Modell kezdik**  
A `BestOfForecaster` osztály kiválasztja a legjobb teljesítményt a modell listáját adott modellek. Hasonló `TSGridSearchCV`, a RollingOriginValidator közötti érvényesítése és a teljesítmény kiértékelése is használ.  
Itt adjuk át való használatát mutatja be két modellek listáját `BestOfForecaster`


```python
best_of_forecaster = BestOfForecaster(forecaster_list=[('naive', naive_model), 
                                                       ('random_forest', random_forest_model)])
best_of_forecaster_fitted = best_of_forecaster.fit(train_feature_tsdf,
                                                   validator=RollingOriginValidator(n_step=20, max_horizon=40))
best_of_forecaster_prediction = best_of_forecaster_fitted.predict(test_feature_tsdf)
best_of_forecaster_prediction.head()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th>PointForecast</th>
      <th>DistributionForecast</th>
      <th>Mennyiség</th>
    </tr>
    <tr>
      <th>WeekLastDay</th>
      <th>Store</th>
      <th>márka</th>
      <th>ForecastOriginTime</th>
      <th>modelName</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1992-01-08 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <th>1992-01-01 23:59:59</th>
      <th>random_forest</th>
      <td>9299.20</td>
      <td>&lt;scipy.stats._distn_infrastructure.rv_frozen o...</td>
      <td>11712.00</td>
    </tr>
    <tr>
      <th>1992-01-15 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <th>1992-01-01 23:59:59</th>
      <th>random_forest</th>
      <td>10259.20</td>
      <td>&lt;scipy.stats._distn_infrastructure.rv_frozen o...</td>
      <td>4032.00</td>
    </tr>
    <tr>
      <th>1992-01-22-es 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <th>1992-01-01 23:59:59</th>
      <th>random_forest</th>
      <td>6828.80</td>
      <td>&lt;scipy.stats._distn_infrastructure.rv_frozen o...</td>
      <td>6336.00</td>
    </tr>
    <tr>
      <th>1992-01-29 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <th>1992-01-01 23:59:59</th>
      <th>random_forest</th>
      <td>16633.60</td>
      <td>&lt;scipy.stats._distn_infrastructure.rv_frozen o...</td>
      <td>13632.00</td>
    </tr>
    <tr>
      <th>1992-02-05-23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <th>1992-01-01 23:59:59</th>
      <th>random_forest</th>
      <td>12774.40</td>
      <td>&lt;scipy.stats._distn_infrastructure.rv_frozen o...</td>
      <td>45120.00</td>
    </tr>
  </tbody>
</table>



**Az utolsó folyamat létrehozása**   
Most, hogy azonosította a legoptimálisabb modellt, hozhat létre, és alkalmas az összes transzformátorok és a legjobb modellt a végső folyamatot. 


```python
random_forest_model_final = RegressionForecaster(estimator=RandomForestRegressor(100),make_grain_features=False)
pipeline_ml.add_pipeline_step('random_forest_estimator', random_forest_model_final)
pipeline_ml_fitted = pipeline_ml.fit(train_imputed_tsdf)
final_prediction = pipeline_ml_fitted.predict(test_tsdf)
final_median_ape = final_prediction.calc_error(err_name='MedianAPE', err_fun=calc_median_ape)
print('Median of APE of final pipeline: {0}'.format(final_median_ape))
```

    F1 2018-05-04 11:07:04,108 INFO azureml.timeseries - pipeline fit started. 
    F1 2018-05-04 11:07:43,121 INFO azureml.timeseries - pipeline fit finished. Time elapsed 0:00:39.012880
    F1 2018-05-04 11:07:43,136 INFO azureml.timeseries - pipeline predict started. 
    F1 2018-05-04 11:08:03,564 INFO azureml.timeseries - pipeline predict finished. Time elapsed 0:00:20.428147
    Median of APE of final pipeline: 42.54336821266968
    

## <a name="visualization"></a>Megjelenítés
A `ForecastDataFrame` osztály megjelenítése és elemzése az előrejelzési eredmények ábrázolási funkciókat biztosít. A gyakran használt diagramok használata az adatok. További információt az alábbi minta notebook ábrázolási függvények a rendelkezésre álló összes funkció áttekintésével. 

A `show_error` függvény jeleníti meg egy tetszőleges oszlop szerint összesítve teljesítmény-mérőszámon. Alapértelmezés szerint a `show_error` függvény úgy összesíti a `grain_colnames` , a `ForecastDataFrame`. Gyakran hasznos használatával azonosíthatók a magok és csoportokhoz a legjobb és legrosszabb teljesítmény, különösen akkor, ha a time series nagy számú rendelkezik. A `performance_percent` argumentumának `show_error` lehetővé teszi, hogy adjon meg egy időtartam, és jeleníti meg a hiba egy részének szemek és csoportokhoz.

Jeleníti meg az utolsó 5 %-os teljesítményű a magok, azaz az első 5 %-os MedianAPE


```python
fig, ax = best_of_forecaster_prediction.show_error(err_name='MedianAPE', err_fun=calc_median_ape, performance_percent=(0.95, 1))
```

![PNG](./media/how-to-build-deploy-forecast-models/output_59_0.png)


Jeleníti meg a magok teljesítményének felső 5 %-os, azaz az 5 %-os alsó MedianAPE.


```python
fig, ax = best_of_forecaster_prediction.show_error(err_name='MedianAPE', err_fun=calc_median_ape, performance_percent=(0, 0.05))
```


![PNG](./media/how-to-build-deploy-forecast-models/output_61_0.png)


Miután átfogó teljesítményének ötlet, érdemes egyéni szemek, különösen rosszul végrehajtott megismerése. A `plot_forecast_by_grain` metódus jeleníti meg és előrejelzést a megadott szemek tényleges. Itt azt jeleníti meg a időfelbontási szint a legjobb teljesítményt és a felderített a legrosszabb Teljesítményfigyelő időfelbontási szint a `show_error` diagram.


```python
fig_ax = best_of_forecaster_prediction.plot_forecast_by_grain(grains=[(33, 'tropicana'), (128, 'minute.maid')])
```


![PNG](./media/how-to-build-deploy-forecast-models/output_63_0.png)



![PNG](./media/how-to-build-deploy-forecast-models/output_63_1.png)



## <a name="additional-notebooks"></a>További notebookok
A legfontosabb funkcióit AMLPF részletesebben megismerni tekintse meg a következő notebookok további részletek és példák az egyes szolgáltatásokhoz:  
[Notebook TimeSeriesDataFrame az](https://azuremlftkrelease.blob.core.windows.net/samples/feature_notebooks/Introduction_to_TimeSeriesDataFrames.ipynb)  
[Notebook az adatok konvertálása](https://azuremlftkrelease.blob.core.windows.net/samples/feature_notebooks/Data_Wrangling_Sample.ipynb)  
[Notebook transzformátorok az](https://azuremlftkrelease.blob.core.windows.net/samples/feature_notebooks/Forecast_Package_Transforms.ipynb)  
[A modellek notebook](https://azuremlftkrelease.blob.core.windows.net/samples/feature_notebooks/AMLPF_models_sample_notebook.ipynb)  
[A notebook közötti érvényesítése](https://azuremlftkrelease.blob.core.windows.net/samples/feature_notebooks/Time_Series_Cross_Validation.ipynb)  
[A Lag átalakító és OriginTime notebook](https://azuremlftkrelease.blob.core.windows.net/samples/feature_notebooks/Constructing_Lags_and_Explaining_Origin_Times.ipynb)  
[A Functions küldik az ábrázolást notebook](https://azuremlftkrelease.blob.core.windows.net/samples/feature_notebooks/Plotting_Functions_in_AMLPF.ipynb)

## <a name="operationalization"></a>Üzembe helyezés

Ebben a szakaszban egy folyamat, az Azure Machine Learning webszolgáltatás üzembe helyezéséhez és felhasználását, a tanítási és pontozási folyamatokat.
Jelenleg nincs egyetlen folyamatok nem szerelnek üzembe helyezés esetén támogatottak. Az üzembe helyezett webszolgáltatáshoz pontozási retrains a modell, és előrejelzések az új adatokat generál.

### <a name="set-model-deployment-parameters"></a>Modell üzembe helyezési paraméterek beállítása

Módosítsa a következő paramétereket saját értékeire. Ellenőrizze, hogy a környezet az Azure Machine Learning modellkezelési fiók és erőforráscsoport ugyanabban a régióban találhatók.


```python
azure_subscription = '<subscription name>'

# Two deployment modes are supported: 'local' and 'cluster'. 
# 'local' deployment deploys to a local docker container.
# 'cluster' deployment deploys to a Azure Container Service Kubernetes-based cluster
deployment_type = '<deployment mode>'

# The deployment environment name. 
# This could be an existing environment or a new environment to be created automatically.
aml_env_name = '<deployment env name>'

# The resource group that contains the Azure resources related to the AML environment.
aml_env_resource_group = '<env resource group name>'

# The location where the Azure resources related to the AML environment are located at.
aml_env_location = '<env resource location>'

# The AML model management account name. This could be an existing model management account a new model management 
# account to be created automatically. 
model_management_account_name = '<model management account name>'

# The resource group that contains the Azure resources related to the model management account.
model_management_account_resource_group = '<model management account resource group>'

# The location where the Azure resources related to the model management account are located at.
model_management_account_location = '<model management account location>'

# The name of the deployment/web service.
deployment_name = '<web service name>'

# The directory to store deployment related files, such as pipeline pickle file, score script, 
# and conda dependencies file. 
deployment_working_directory = '<local working directory>'
```

### <a name="define-the-azure-machine-learning-environment-and-deployment"></a>Az Azure Machine Learning-környezet és a központi telepítési definiálása


```python
aml_settings = AMLSettings(azure_subscription=azure_subscription,
                     env_name=aml_env_name, 
                     env_resource_group=aml_env_resource_group,
                     env_location=aml_env_location, 
                     model_management_account_name=model_management_account_name, 
                     model_management_account_resource_group=model_management_account_resource_group,
                     model_management_account_location=model_management_account_location,
                     cluster=deployment_type)

random_forest_model_deploy = RegressionForecaster(estimator=RandomForestRegressor(),make_grain_features=False)
pipeline_deploy = AzureMLForecastPipeline([('drop_columns', column_dropper), 
                                           ('fillna_imputer', fillna_imputer),
                                           ('time_index_featurizer', time_index_featurizer),
                                           ('random_forest_estimator', random_forest_model_deploy)
                                          ])

aml_deployment = ForecastWebserviceFactory(deployment_name=deployment_name,
                                           aml_settings=aml_settings, 
                                           pipeline=pipeline_deploy,
                                           deployment_working_directory=deployment_working_directory,
                                           ftk_wheel_loc='https://azuremlftkrelease.blob.core.windows.net/dailyrelease/azuremlftk-0.1.18165.29a1-py3-none-any.whl')
```

### <a name="create-the-web-service"></a>A webszolgáltatás létrehozása


```python
# This step can take 5 to 20 minutes
aml_deployment.deploy()
```

### <a name="score-the-web-service"></a>A web service pontszám

Pontszám egy kisméretű adatkészlet, használja a [pontszám](https://docs.microsoft.com/python/api/ftk.operationalization.deployment.amlwebservice) elküldeni egy webszolgáltatás metódust hívja fel az összes adatot.


```python
# Need to add empty prediction columns to the validation data frame and create a ForecastDataFrame.
# The scoring API will be updated in later versions to take TimeSeriesDataFrame directly. 
validate_tsdf = test_tsdf.assign(PointForecast=0.0, DistributionForecast=np.nan)
validate_fcast = ForecastDataFrame(validate_tsdf, pred_point='PointForecast', pred_dist='DistributionForecast')

# Define Score Context
score_context = ScoreContext(input_training_data_tsdf=train_imputed_tsdf,
                             input_scoring_data_fcdf=validate_fcast, 
                             pipeline_execution_type='train_predict')

# Get deployed web service
aml_web_service = aml_deployment.get_deployment()

# Score the web service
results = aml_web_service.score(score_context=score_context)
```

Egy nagy méretű adathalmazt pontszám, használja a [párhuzamos pontozási](https://docs.microsoft.com/python/api/ftk.operationalization.deployment.amlwebservice) mód elküldése több webszolgáltatás meghívja,-adatok egy csoporthoz.


```python
results = aml_web_service.score(score_context=score_context, method='parallel')
```

## <a name="next-steps"></a>További lépések

Tudjon meg többet az Azure Machine Learning csomag előrejelzés az alábbi cikkeket:

+ Olvassa el a [csomag áttekintése](https://aka.ms/aml-packages/forecasting).

+ Fedezze fel a [docs hivatkozhat](https://aka.ms/aml-packages/forecasting) a csomag számára.

+ Ismerje meg [egyéb Python-csomagokat az Azure Machine Learning](reference-python-package-overview.md).
