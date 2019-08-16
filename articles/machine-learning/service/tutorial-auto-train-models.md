---
title: 'A regressziós modell oktatóanyaga: Automatizált ML'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan hozhatja ki gépi tanulási modellt az automatizált gépi tanulás használatával. A Azure Machine Learning az adatok előfeldolgozását, az algoritmus kiválasztását és a hiperparaméter kiválasztását automatizált módon végezheti el. Ezután az utolsó modell üzembe helyezése Azure Machine Learning szolgáltatással történik.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: trbye
ms.date: 08/11/2019
ms.custom: seodec18
ms.openlocfilehash: 49f46c09cfcfef2ab1e74ae7c08d9a54289293ac
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534829"
---
# <a name="tutorial-use-automated-machine-learning-to-build-your-regression-model"></a>Oktatóanyag: Az automatizált gépi tanulás használata a regressziós modell létrehozásához

Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat második része**. Az előző oktatóanyagban, [előkészítette a NYC-i taxik adatait regressziós modellezéshez](tutorial-data-prep.md).

Most már készen áll arra, hogy megkezdje a modell kiépítése Azure Machine Learning szolgáltatással. Az oktatóanyag ezen részében az előkészített és automatikusan létrehoz egy regressziós modellt, amellyel előre jelezheti a taxi viteldíjak árát. A szolgáltatás automatizált gépi tanulási képességeinek használatával megadhatja a gépi tanulási célokat és korlátozásokat. Elindítja az automatizált gépi tanulás folyamatát. Ezután engedélyezze az algoritmus kiválasztását és a hiperparaméter finomhangolását. Az automatizált gépi tanulási módszer az algoritmusok és a hiperparaméterek beállítása számos kombinációjára támaszkodik, amíg meg nem találja a legjobb modellt a feltétele alapján.

![Folyamatábra](./media/tutorial-auto-train-models/flow2.png)

Ez az oktatóanyag a következő feladatokat ismerteti:

> [!div class="checklist"]
> * Hozzon létre egy Python-környezetet, és importálja az SDK-csomagokat.
> * Azure Machine Learning szolgáltatás munkaterületének konfigurálása.
> * Egy regressziós modell újratanítása.
> * A modell helyi futtatása egyéni paraméterekkel.
> * Ismerkedjen meg az eredményekkel.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

>[!NOTE]
> A cikkben ismertetett kód Azure Machine Learning SDK 1.0.39-verzióval lett tesztelve.

## <a name="prerequisites"></a>Előfeltételek

* Fejezze be az első részt [](tutorial-data-prep.md), az adatelőkészítési oktatóanyagot.

* Az első rész befejezése után nyissa meg az **oktatóanyagok/Regression-part2-Automated-ml. ipynb** jegyzetfüzetet ugyanazzal a notebook-kiszolgálóval.

Ez az oktatóanyag a githubon [](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) is elérhető, ha saját [helyi környezetében](how-to-configure-environment.md#local)szeretné használni.  Győződjön meg arról, hogy `matplotlib` telepítette `automl` és `notebooks` az extrákat a Azure Machine learning SDK-ból.

## <a name="start"></a>A fejlesztési környezet beállítása

Egy Python-notebookban a fejlesztési munka összes beállítása megadható. A telepítő a következő műveleteket tartalmazza:

* Az SDK telepítése
* Python-csomagok importálása
* Munkaterület konfigurálása

### <a name="install-and-import-packages"></a>Csomagok telepítése és importálása

Ha saját Python-környezetében követi az oktatóanyagot, a következő paranccsal telepítheti a szükséges csomagokat.

```shell
pip install azureml-sdk[automl,notebooks] matplotlib
```

Importálja az oktatóanyagban szükséges Python-csomagokat:

```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
import logging
import os
```

### <a name="configure-workspace"></a>Munkaterület konfigurálása

Hozzon létre egy munkaterület-objektumot a meglévő munkaterületről. A [munkaterület](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) egy olyan osztály, amely elfogadja az Azure-előfizetést és az erőforrás-információkat. Létrehoz egy felhőalapú erőforrást is a modell futtatásának figyelésére és nyomon követésére.

A `Workspace.from_config()` beolvassa a **config.json** fájlt, és betölti a részleteket a `ws` nevű objektumba.  A `ws` a kód további részében használható ebben az oktatóanyagban.

A munkaterület-objektum megadását követően adja meg a kísérlet nevét. Hozzon létre és regisztráljon helyi könyvtárat a munkaterülettel. Az összes Futtatás előzményeit a megadott kísérlet és a [Azure Portal](https://portal.azure.com)rögzíti.


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>Adatok megismerése

Használja az előző oktatóanyagban létrehozott adatfolyam-objektumot. Az oktatóanyag 1. része megtisztította a New York-i taxi adatait, hogy a gépi tanulási modellben is felhasználható legyen. Mostantól különböző funkciókat használhat az adatkészletből, és lehetővé teszi, hogy az automatizált modell kapcsolatokat építsen ki a szolgáltatások és a taxik ára között. Nyissa meg és futtassa az adatfolyamot, és tekintse át az eredményeket:


```python
import azureml.dataprep as dprep

file_path = os.path.join(os.getcwd(), "dflows.dprep")

dflow_prepared = dprep.Dataflow.open(file_path)
dflow_prepared.get_profile()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>Max</th>
      <th>Count</th>
      <th>Hiányzó darabszám</th>
      <th>Hiányzó darabszám</th>
      <th>Hiányzó százalék</th>
      <th>Hibaszámláló</th>
      <th>Üres száma</th>
      <th>0,1% quantile</th>
      <th>1% quantile</th>
      <th>5% quantile</th>
      <th>25% quantile</th>
      <th>50% quantile</th>
      <th>75% quantile</th>
      <th>95% quantile</th>
      <th>99% quantile</th>
      <th>99,9% quantile</th>
      <th>középérték</th>
      <th>Standard szórás</th>
      <th>Variancia</th>
      <th>Döntés</th>
      <th>Értékek</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Szállító</th>
      <td>FieldType.STRING</td>
      <td>1</td>
      <td>VTS</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_weekday</th>
      <td>FieldType.STRING</td>
      <td>Péntek</td>
      <td>Szerda</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2.90047</td>
      <td>2.69355</td>
      <td>9.72889</td>
      <td>16</td>
      <td>19.3713</td>
      <td>22.6974</td>
      <td>23</td>
      <td>23</td>
      <td>14.2731</td>
      <td>6.59242</td>
      <td>43.46</td>
      <td>-0.693723</td>
      <td>-0.570403</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>4.99701</td>
      <td>4.95833</td>
      <td>14.1528</td>
      <td>29.3832</td>
      <td>44.6825</td>
      <td>56.4444</td>
      <td>58.9909</td>
      <td>59</td>
      <td>29.427</td>
      <td>17.4333</td>
      <td>303.921</td>
      <td>0.0120999</td>
      <td>-1.20981</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.28131</td>
      <td>5</td>
      <td>14.7832</td>
      <td>29.9293</td>
      <td>44.725</td>
      <td>56.7573</td>
      <td>59</td>
      <td>59</td>
      <td>29.7443</td>
      <td>17.3595</td>
      <td>301.351</td>
      <td>-0.0252399</td>
      <td>-1.19616</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>Péntek</td>
      <td>Szerda</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>dropoff_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2.57153</td>
      <td>2</td>
      <td>9.58795</td>
      <td>15.9994</td>
      <td>19.6184</td>
      <td>22.8317</td>
      <td>23</td>
      <td>23</td>
      <td>14.2105</td>
      <td>6.71093</td>
      <td>45.0365</td>
      <td>-0.687292</td>
      <td>-0.61951</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.44383</td>
      <td>4.84694</td>
      <td>14.1036</td>
      <td>28.8365</td>
      <td>44.3102</td>
      <td>56.6892</td>
      <td>59</td>
      <td>59</td>
      <td>29.2907</td>
      <td>17.4108</td>
      <td>303.136</td>
      <td>0.0222514</td>
      <td>-1.2181</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.07801</td>
      <td>5</td>
      <td>14.5751</td>
      <td>29.5972</td>
      <td>45.4649</td>
      <td>56.2729</td>
      <td>59</td>
      <td>59</td>
      <td>29.772</td>
      <td>17.5337</td>
      <td>307.429</td>
      <td>-0.0212575</td>
      <td>-1.226</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>I</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0781</td>
      <td>-73.7459</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0578</td>
      <td>-73.9639</td>
      <td>-73.9656</td>
      <td>-73.9508</td>
      <td>-73.9255</td>
      <td>-73.8529</td>
      <td>-73.8302</td>
      <td>-73.8238</td>
      <td>-73.7697</td>
      <td>-73.9123</td>
      <td>0.0503757</td>
      <td>0.00253771</td>
      <td>0.352172</td>
      <td>-0.923743</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5755</td>
      <td>40.8799</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632</td>
      <td>40.7117</td>
      <td>40.7115</td>
      <td>40.7213</td>
      <td>40.7565</td>
      <td>40.8058</td>
      <td>40.8478</td>
      <td>40.8676</td>
      <td>40.8778</td>
      <td>40.7649</td>
      <td>0.0494674</td>
      <td>0.00244702</td>
      <td>0.205972</td>
      <td>-0.777945</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0857</td>
      <td>-73.7209</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0775</td>
      <td>-73.9875</td>
      <td>-73.9882</td>
      <td>-73.9638</td>
      <td>-73.935</td>
      <td>-73.8755</td>
      <td>-73.8125</td>
      <td>-73.7759</td>
      <td>-73.7327</td>
      <td>-73.9202</td>
      <td>0.0584627</td>
      <td>0.00341789</td>
      <td>0.623622</td>
      <td>-0.262603</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5835</td>
      <td>40.8797</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.5973</td>
      <td>40.6928</td>
      <td>40.6911</td>
      <td>40.7226</td>
      <td>40.7567</td>
      <td>40.7918</td>
      <td>40.8495</td>
      <td>40.868</td>
      <td>40.8787</td>
      <td>40.7583</td>
      <td>0.0517399</td>
      <td>0.00267701</td>
      <td>0.0390404</td>
      <td>-0.203525</td>
    </tr>
    <tr>
      <th>az utasok</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>5</td>
      <td>6</td>
      <td>6</td>
      <td>2.39249</td>
      <td>1.83197</td>
      <td>3.3561</td>
      <td>0.763144</td>
      <td>-1.23467</td>
    </tr>
    <tr>
      <th>távolságskála</th>
      <td>FieldType.DECIMAL</td>
      <td>0.01</td>
      <td>32.34</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0108744</td>
      <td>0.743898</td>
      <td>0.738194</td>
      <td>1.243</td>
      <td>2.40168</td>
      <td>4.74478</td>
      <td>10.5136</td>
      <td>14.9011</td>
      <td>21.8035</td>
      <td>3.5447</td>
      <td>3.2943</td>
      <td>10.8524</td>
      <td>1.91556</td>
      <td>4.99898</td>
    </tr>
    <tr>
      <th>költség</th>
      <td>FieldType.DECIMAL</td>
      <td>0,1</td>
      <td>88</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2.33837</td>
      <td>5.00491</td>
      <td>5</td>
      <td>6.93129</td>
      <td>10.524</td>
      <td>17.4811</td>
      <td>33.2343</td>
      <td>50.0093</td>
      <td>63.1753</td>
      <td>13.6843</td>
      <td>9.66571</td>
      <td>93.426</td>
      <td>1.78518</td>
      <td>4.13972</td>
    </tr>
  </tbody>
</table>

Az adatokat a kísérlethez oszlopok hozzáadásával előkészítése `dflow_x` kell funkciók a modell létrehozásához. Ön határozza `dflow_y` meg, hogy az előrejelzés értéke, a **Cost**:

```python
dflow_X = dflow_prepared.keep_columns(
    ['pickup_weekday', 'pickup_hour', 'distance', 'passengers', 'vendor'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-the-data-into-train-and-test-sets"></a>Az adat felosztása a vonatra és a tesztelési csoportokra

Most az adat kiosztását betanítási és tesztelési csoportokra bontja `train_test_split` a `sklearn` könyvtárban található függvény használatával. Ez a függvény elkülöníti az adatokat az x, a **funkciók**, az adatkészlet és a modell betanítása, valamint az y, az **előre jelzett értékek**, a tesztelési adatkészlet számára. A `test_size` paraméter határozza meg az adatok tesztelését lefoglalni aránya. A `random_state` paraméter beállítja a magot a véletlenszerű generátorra, így a vonat-teszt felosztás mindig determinisztikus:

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(
    x_df, y_df, test_size=0.2, random_state=223)
# flatten y_train to 1d array
y_train.values.flatten()
```

Ennek a lépésnek a célja, hogy az adatpontokkal tesztelje a modell betanítására nem használt befejezett modellt a valódi pontosság mérése érdekében. Más szóval a jól betanított modellnek képesnek kell lennie arra, hogy a már nem látott adatok alapján pontosan elvégezze az előrejelzések készítését. Most már rendelkezik a modell kiképzéséhez szükséges csomagokkal és adatkészletekkel.

## <a name="automatically-train-a-model"></a>Automatikusan a modell tanítása

A modellek automatikus betanításához hajtsa végre a következő lépéseket:
1. Adja meg a kísérlet futtatásának beállításait. Csatolja a betanítási adatait a konfigurációhoz, és módosítsa a betanítási folyamatot szabályozó beállításokat.
1. A kísérlet beküldése a modell finomhangolására. A kísérlet elküldése után a folyamat különböző gépi tanulási algoritmusokon és hiperparaméter-beállításokon keresztül megismétli a megadott megkötéseket. A pontosság mérőszámának optimalizálásával kiválasztja a legjobban illeszkedő modellt.

### <a name="define-settings-for-autogeneration-and-tuning"></a>A személyfelismerési és a hangolási beállításainak megadása

Adja meg a kísérlet paraméterét és a modell beállításait az automatikus létrehozáshoz és a finomhangoláshoz. Tekintse meg a teljes listáját [beállítások](how-to-configure-auto-train.md). Ha a kísérletet ezekkel az alapértelmezett beállításokkal küldi el, a rendszer körülbelül 10-15 percig tart, de ha rövidebb futási `iteration_timeout_minutes`időt szeretne, csökkentse `iterations` a vagy a értéket.


|Tulajdonság| Az oktatóanyagban szereplő érték |Leírás|
|----|----|---|
|**iteration_timeout_minutes**|10|Az egyes iterációk időkorlátja percben kifejezve. Csökkentse ezt az értéket a teljes futtatókörnyezet csökkentése érdekében.|
|**iterations**|30|Iterációk száma. Minden egyes iterációban egy új gépi tanulási modell van betanítva az adataival. Ez az elsődleges érték, amely a teljes futási időt befolyásolja.|
|**primary_metric**| spearman_correlation | Az optimalizálni kívánt metrika. A legjobban illeszkedő modell a metrika alapján lesz kiválasztva.|
|**preprocess**| True | Az **igaz**értékkel a kísérlet feldolgozhatja a bemeneti adatokat (a hiányzó adatok kezelésével, a szöveg a numerikus szöveggé való konvertálásával stb.).|
|**Részletességi**| logging.INFO | A naplózási szint szabályozza.|
|**n_cross_validations**|5|Az ellenőrzési adatok megadásakor végrehajtandó, több ellenőrzési felosztások száma.|



```python
automl_settings = {
    "iteration_timeout_minutes": 10,
    "iterations": 30,
    "primary_metric": 'spearman_correlation',
    "preprocess": True,
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

A megadott tanítási beállításokat paraméterként használhatja egy `AutoMLConfig` objektumhoz. Továbbá adja meg a betanítási adatait és a modell típusát, amely `regression` ebben az esetben.

```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task='regression',
                                   debug_log='automated_ml_errors.log',
                                   path=project_folder,
                                   X=x_train.values,
                                   y=y_train.values.flatten(),
                                   **automl_settings)
```

> [!NOTE]
> Az automatizált gépi tanulás előfeldolgozásának lépései (a funkciók normalizálása, a hiányzó adatkezelés, a szöveg konvertálása a numerikus formátumba stb.) az alapul szolgáló modell részévé válnak. A modell előrejelzésekhez való használatakor a betanítás során alkalmazott azonos előfeldolgozási lépéseket a rendszer automatikusan alkalmazza a bemeneti adatokra.

### <a name="train-the-automatic-regression-model"></a>Az automatikus regressziós modell betanítása

Indítsa el a kísérlet helyi futtatását. Adja át a `automated_ml_config` definiált objektumot a kísérletnek. Állítsa be a kimenetet `True` , hogy megtekintse az előrehaladást a kísérlet során:


```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

A rendszer a kísérlet futtatásakor élőben jeleníti meg a frissítéseket. Minden egyes iterációnál megjelenik a modell típusa, a Futtatás időtartama és a képzés pontossága. A mező `BEST` a metrikák típusától függően a legjobb futó tanítási pontszámot követi nyomon.

    Parent Run ID: AutoML_02778de3-3696-46e9-a71b-521c8fca0651
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:08       0.9447    0.9447
             1   StandardScalerWrapper GradientBoosting         0:00:09       0.9536    0.9536
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.8580    0.9536
             3   StandardScalerWrapper RandomForest             0:00:08       0.9147    0.9536
             4   StandardScalerWrapper ExtremeRandomTrees       0:00:45       0.9398    0.9536
             5   MaxAbsScaler LightGBM                          0:00:08       0.9562    0.9562
             6   StandardScalerWrapper ExtremeRandomTrees       0:00:27       0.8282    0.9562
             7   StandardScalerWrapper LightGBM                 0:00:07       0.9421    0.9562
             8   MaxAbsScaler DecisionTree                      0:00:08       0.9526    0.9562
             9   MaxAbsScaler RandomForest                      0:00:09       0.9355    0.9562
            10   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            11   MaxAbsScaler LightGBM                          0:00:09       0.9553    0.9602
            12   MaxAbsScaler DecisionTree                      0:00:07       0.9484    0.9602
            13   MaxAbsScaler LightGBM                          0:00:08       0.9540    0.9602
            14   MaxAbsScaler RandomForest                      0:00:10       0.9365    0.9602
            15   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:49       0.9171    0.9602
            17   SparseNormalizer LightGBM                      0:00:08       0.9191    0.9602
            18   MaxAbsScaler DecisionTree                      0:00:08       0.9402    0.9602
            19   StandardScalerWrapper ElasticNet               0:00:08       0.9603    0.9603
            20   MaxAbsScaler DecisionTree                      0:00:08       0.9513    0.9603
            21   MaxAbsScaler SGD                               0:00:08       0.9603    0.9603
            22   MaxAbsScaler SGD                               0:00:10       0.9602    0.9603
            23   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            24   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            25   MaxAbsScaler SGD                               0:00:09       0.9603    0.9603
            26   TruncatedSVDWrapper ElasticNet                 0:00:09       0.9602    0.9603
            27   MaxAbsScaler SGD                               0:00:12       0.9413    0.9603
            28   StandardScalerWrapper ElasticNet               0:00:07       0.9603    0.9603
            29    Ensemble                                      0:00:38       0.9622    0.9622

## <a name="explore-the-results"></a>Az eredmények vizsgálata

Fedezze fel az automatikus képzési egy Jupyter widgettel, vagy a kísérlet előzmények megvizsgálásával eredményeit.

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>1\. lehetőség: Jupyter widget hozzáadása az eredmények megtekintéséhez

Ha Jupyter-jegyzetfüzetet használ, ezzel a [Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) -widgettel megtekintheti az összes eredmény gráfját és táblázatát:


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter widget-Futtatás](./media/tutorial-auto-train-models/automl-dash-output.png)
részletei![Jupyter widget Plot](./media/tutorial-auto-train-models/automl-chart-output.png)

Ugyanazokat az eredményeket a rendszer a munkaterületen tárolja.  A Futtatás eredményét a következő hivatkozással érheti el:

```
local_run.get_portal_url()
```


### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>2\. lehetőség: Minden futtatási iteráció beolvasása és vizsgálata a Pythonban

Lekérheti az egyes kísérletek előzményeit is, és megtekintheti az egyes iterációk egyes futtatásainak egyes mérőszámait. Ha megvizsgálja az egyes modellek GYÖKÁTLAGOS (root_mean_squared_error), láthatja, hogy a legtöbb iteráció ésszerűen ($ 3-4) belül előre megjósolja a taxi méltányos árát.

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items()
               if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>20</th>
      <th>21</th>
      <th>22</th>
      <th>23</th>
      <th>24</th>
      <th>25</th>
      <th>26</th>
      <th>27</th>
      <th>28</th>
      <th>29</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0.811037</td>
      <td>0.880553</td>
      <td>0.398582</td>
      <td>0.776040</td>
      <td>0.663869</td>
      <td>0.875911</td>
      <td>0.115632</td>
      <td>0.586905</td>
      <td>0.851911</td>
      <td>0.793964</td>
      <td>...</td>
      <td>0.850023</td>
      <td>0.883603</td>
      <td>0.883704</td>
      <td>0.880797</td>
      <td>0.881564</td>
      <td>0.883708</td>
      <td>0.881826</td>
      <td>0.585377</td>
      <td>0.883123</td>
      <td>0.886817</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>2.189444</td>
      <td>1.500412</td>
      <td>5.480531</td>
      <td>2.626316</td>
      <td>2.973026</td>
      <td>1.550199</td>
      <td>6.383868</td>
      <td>4.414241</td>
      <td>1.743328</td>
      <td>2.294601</td>
      <td>...</td>
      <td>1.797402</td>
      <td>1.415815</td>
      <td>1.418167</td>
      <td>1.578617</td>
      <td>1.559427</td>
      <td>1.413042</td>
      <td>1.551698</td>
      <td>4.069196</td>
      <td>1.505795</td>
      <td>1.430957</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>1.438417</td>
      <td>0.850899</td>
      <td>4.579662</td>
      <td>1.765210</td>
      <td>1.594600</td>
      <td>0.869883</td>
      <td>4.266450</td>
      <td>3.627355</td>
      <td>0.954992</td>
      <td>1.361014</td>
      <td>...</td>
      <td>0.973634</td>
      <td>0.774814</td>
      <td>0.797269</td>
      <td>1.147234</td>
      <td>1.116424</td>
      <td>0.783958</td>
      <td>1.098464</td>
      <td>2.709027</td>
      <td>1.003728</td>
      <td>0.851724</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error</th>
      <td>0.024908</td>
      <td>0.017070</td>
      <td>0.062350</td>
      <td>0.029878</td>
      <td>0.033823</td>
      <td>0.017636</td>
      <td>0.072626</td>
      <td>0.050219</td>
      <td>0.019833</td>
      <td>0.026105</td>
      <td>...</td>
      <td>0.020448</td>
      <td>0.016107</td>
      <td>0.016134</td>
      <td>0.017959</td>
      <td>0.017741</td>
      <td>0.016076</td>
      <td>0.017653</td>
      <td>0.046293</td>
      <td>0.017131</td>
      <td>0.016279</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0.016364</td>
      <td>0.009680</td>
      <td>0.052101</td>
      <td>0.020082</td>
      <td>0.018141</td>
      <td>0.009896</td>
      <td>0.048538</td>
      <td>0.041267</td>
      <td>0.010865</td>
      <td>0.015484</td>
      <td>...</td>
      <td>0.011077</td>
      <td>0.008815</td>
      <td>0.009070</td>
      <td>0.013052</td>
      <td>0.012701</td>
      <td>0.008919</td>
      <td>0.012497</td>
      <td>0.030819</td>
      <td>0.011419</td>
      <td>0.009690</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error</th>
      <td>0.047968</td>
      <td>0.037882</td>
      <td>0.085572</td>
      <td>0.052282</td>
      <td>0.065809</td>
      <td>0.038664</td>
      <td>0.109401</td>
      <td>0.071104</td>
      <td>0.042294</td>
      <td>0.049967</td>
      <td>...</td>
      <td>0.042565</td>
      <td>0.037685</td>
      <td>0.037557</td>
      <td>0.037643</td>
      <td>0.037513</td>
      <td>0.037560</td>
      <td>0.037465</td>
      <td>0.072077</td>
      <td>0.037249</td>
      <td>0.036716</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error</th>
      <td>0.055353</td>
      <td>0.045000</td>
      <td>0.110219</td>
      <td>0.065633</td>
      <td>0.063589</td>
      <td>0.044412</td>
      <td>0.123433</td>
      <td>0.092312</td>
      <td>0.046130</td>
      <td>0.055243</td>
      <td>...</td>
      <td>0.046540</td>
      <td>0.041804</td>
      <td>0.041771</td>
      <td>0.045175</td>
      <td>0.044628</td>
      <td>0.041617</td>
      <td>0.044405</td>
      <td>0.079651</td>
      <td>0.042799</td>
      <td>0.041530</td>
    </tr>
    <tr>
      <th>r2_score</th>
      <td>0.810900</td>
      <td>0.880328</td>
      <td>0.398076</td>
      <td>0.775957</td>
      <td>0.642812</td>
      <td>0.875719</td>
      <td>0.021603</td>
      <td>0.586514</td>
      <td>0.851767</td>
      <td>0.793671</td>
      <td>...</td>
      <td>0.849809</td>
      <td>0.880142</td>
      <td>0.880952</td>
      <td>0.880586</td>
      <td>0.881347</td>
      <td>0.880887</td>
      <td>0.881613</td>
      <td>0.548121</td>
      <td>0.882883</td>
      <td>0.886321</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>4.216362</td>
      <td>3.329810</td>
      <td>7.521765</td>
      <td>4.595604</td>
      <td>5.784601</td>
      <td>3.398540</td>
      <td>9.616354</td>
      <td>6.250011</td>
      <td>3.717661</td>
      <td>4.392072</td>
      <td>...</td>
      <td>3.741447</td>
      <td>3.312533</td>
      <td>3.301242</td>
      <td>3.308795</td>
      <td>3.297389</td>
      <td>3.301485</td>
      <td>3.293182</td>
      <td>6.335581</td>
      <td>3.274209</td>
      <td>3.227365</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0.243184</td>
      <td>0.197702</td>
      <td>0.484227</td>
      <td>0.288349</td>
      <td>0.279367</td>
      <td>0.195116</td>
      <td>0.542281</td>
      <td>0.405559</td>
      <td>0.202666</td>
      <td>0.242702</td>
      <td>...</td>
      <td>0.204464</td>
      <td>0.183658</td>
      <td>0.183514</td>
      <td>0.198468</td>
      <td>0.196067</td>
      <td>0.182836</td>
      <td>0.195087</td>
      <td>0.349935</td>
      <td>0.188031</td>
      <td>0.182455</td>
    </tr>
    <tr>
      <th>spearman_correlation</th>
      <td>0.944743</td>
      <td>0.953618</td>
      <td>0.857965</td>
      <td>0.914703</td>
      <td>0.939846</td>
      <td>0.956159</td>
      <td>0.828187</td>
      <td>0.942069</td>
      <td>0.952581</td>
      <td>0.935477</td>
      <td>...</td>
      <td>0.951287</td>
      <td>0.960335</td>
      <td>0.960195</td>
      <td>0.960279</td>
      <td>0.960288</td>
      <td>0.960323</td>
      <td>0.960161</td>
      <td>0.941254</td>
      <td>0.960293</td>
      <td>0.962158</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0.944743</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>...</td>
      <td>0.960303</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.962158</td>
    </tr>
  </tbody>
</table>
<p>12 * 30 oszlopok sorok</p>
</div>

## <a name="retrieve-the-best-model"></a>A legjobb modellt beolvasása

Válassza ki a legjobb folyamat az ismétlések. A `get_output` metódust `automl_classifier` a legjobb Futtatás és illesztett modell az utolsó hívás szélességhez tartozó adja vissza. A túlterhelések bekapcsolásával `get_output`lekérheti az összes naplózott metrika vagy egy adott iteráció esetében a legjobb futtatási és a beépített modellt:

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-the-best-model-accuracy"></a>A legpontosabb modell tesztelése

A legjobb modell használatával előrejelzéseket futtathat a tesztelési adatkészleten a taxi viteldíjak előrejelzéséhez. A függvény `predict` a legjobb modellt használja, és az `x_test` adatkészletből megjósolja az y, az **utazás költségeit**. Nyomtassa ki az első 10 előre jelzett költségadatok `y_predict`értékét a következőből:

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Hozzon létre egy scatter-mintaterületet, amely megjeleníti a becsült költségadatok értékét a tényleges költségadatok alapján. A következő kód az x `distance` tengely és az út `cost` y tengelyként való használatát használja. Ha a becsült költségeket az egyes utazási távolságok értéke alapján össze szeretné hasonlítani, az első 100 előre jelzett és tényleges költségadatok külön adatsorozatként jönnek létre. A mintaterület vizsgálata azt mutatja, hogy a távolság/ár kapcsolat közel lineáris, és az előre jelzett költségadatok a legtöbb esetben nagyon közel vannak az azonos utazási távolság tényleges értékéhez.

```python
%matplotlib inline

import matplotlib.pyplot as plt

fig = plt.figure(figsize=(14, 10))
ax1 = fig.add_subplot(111)

distance_vals = [x[4] for x in x_test.values]
y_actual = y_test.values.flatten().tolist()

ax1.scatter(distance_vals[:100], y_predict[:100],
            s=18, c='b', marker="s", label='Predicted')
ax1.scatter(distance_vals[:100], y_actual[:100],
            s=18, c='r', marker="o", label='Actual')

ax1.set_xlabel('distance (mi)')
ax1.set_title('Predicted and Actual Cost/Distance')
ax1.set_ylabel('Cost ($)')

plt.legend(loc='upper left', prop={'size': 12})
plt.rcParams.update({'font.size': 14})
plt.show()
```

![Előrejelzési pontdiagram](./media/tutorial-auto-train-models/automl-scatter-plot.png)

`root mean squared error` Az eredmények kiszámítása. Használja a `y_test` dataframe. Alakítsa át egy listára, hogy összehasonlítsa az előre jelzett értékeket. A függvény `mean_squared_error` két tömb értéket vesz igénybe, és kiszámítja a közöttük lévő átlagos négyzetes hibát. Az eredmény négyzet gyökerének megadásával az y változóval megegyező egységekben talál hibát. Nagyjából azt jelzi, hogy a taxi viteldíjai milyen mértékben vannak a tényleges díjaktól:

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

    3.2204936862688798

Futtassa a következő kódot a teljes `y_actual` és `y_predict` adatkészletek használatával a középérték abszolút százalékának (mape) kiszámításához. Ez a mérőszám az összes előre jelzett és tényleges érték közötti abszolút különbséget számítja ki, és összegzi az összes különbséget. Ezt követően az összeg a tényleges értékek összegének százalékában kifejezve:

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
    0.10545153869569586

    Model Accuracy:
    0.8945484613043041

A végső előrejelzés pontossági mérőszámai alapján láthatja, hogy a modell elég jó, ha az adatkészlet szolgáltatásaiból, jellemzően a +-$3,00-n belül megjósolja a taxi viteldíjait. A hagyományos gépi tanulási modell fejlesztési folyamatok magas erőforrás-igényes, és jelentős tartomány ismeretek és idő befektetési futtatásához, és több tucatnyi modellek eredményeinek összehasonlítására, igényel. Az automatizált gépi tanulás nagyszerű módja annak, hogy gyorsan tesztelje a forgatókönyv számos különböző modelljét.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben az automatizált gépi tanulási oktatóanyagban a következő feladatokat hajtotta végre:

> [!div class="checklist"]
> * Egy kísérlethez konfigurált egy munkaterületet és egy előkészített adatkészletet.
> * Automatikus regressziós modell használatával, helyi egyéni paraméterekkel.
> * A betanítási eredmények megismerése és felülvizsgálata.

[A modell üzembe helyezése](tutorial-deploy-models-with-aml.md) az Azure Machine Learning.
