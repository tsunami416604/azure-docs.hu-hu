---
title: 'Regressziós modell oktatóanyag: Automatizált gépi tanulás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan hozhat létre a machine learning-modellek automatizált machine learning segítségével. Az Azure Machine Learning hajthat végre adatok előfeldolgozása, az algoritmus kiválasztása és a hiperparaméter kiválasztása automatikusan az Ön számára. Ezután a kész modell Azure Machine Learning szolgáltatás van telepítve.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 1e2746ef55f5c50ce9452b7a9d1ab060c69830db
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244270"
---
# <a name="tutorial-use-automated-machine-learning-to-build-your-regression-model"></a>Oktatóanyag: Automatizált gépi tanulás a regressziós modell létrehozása

Ez az oktatóanyag **egy kétrészes oktatóanyag-sorozat második része**. Az előző oktatóanyagban, [előkészítette a NYC-i taxik adatait regressziós modellezéshez](tutorial-data-prep.md).

Most már készen áll a modellben az Azure Machine Learning szolgáltatás kiépítésének megkezdésére. Az oktatóanyag jelen részében az előkészített adatokat használhatja, és automatikusan létrehozhat egy taxi diszkont árak előrejelzésére regressziós modell. Az automatikus gépi tanulási funkciókat a szolgáltatás segítségével meghatározhatja a gépi tanulási célok és korlátozások. Az automatikus gépi tanulási folyamat indíthatja el. Ezután lehetővé teszik az algoritmus kiválasztása és a hiperparaméter finomhangolása történjen meg. Az automatikus gépi tanulási módszert algoritmusok és hiperparaméterek számos kombinációit ismétel, amíg nem talál a legoptimálisabb modellt, a feltétel alapján.

![folyamatábrája](./media/tutorial-auto-train-models/flow2.png)

Ebben az oktatóanyagban elsajátíthatja a következő feladatokat:

> [!div class="checklist"]
> * Egy Python-környezetet, és az SDK-csomagok importálásához.
> * Az Azure Machine Learning szolgáltatás munkaterületén konfigurálhatja.
> * Egy regressziós modell Autotrain.
> * A modell helyileg futtassa egyéni paraméterekkel.
> * Ismerje meg az eredményeket.
> * Regisztrálja a legjobb modellt.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree) még ma.

>[!NOTE]
> Ebben a cikkben kód az Azure Machine Learning SDK 1.0.0-s verziójának lett tesztelve.

## <a name="prerequisites"></a>Előfeltételek

> * [Futtassa az adat-előkészítési oktatóanyagot](tutorial-data-prep.md).
> * Egy automatizált gépi tanulási konfigurálva a környezetben. Példa [Azure notebookok](https://notebooks.azure.com/), helyi Python-környezetben, vagy egy adatelemző virtuális gép. [Állítsa be az automatikus gépi tanulási](samples-notebooks.md).

## <a name="get-the-notebook"></a>A notebook beszerzése

Az Ön kényelme érdekében ez az oktatóanyag [Jupyter-notebookként](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/regression-part2-automated-ml.ipynb) is elérhető. Futtassa a `regression-part2-automated-ml.ipynb` notebook vagy [Azure notebookok](https://notebooks.azure.com/) vagy a saját Jupyter Notebook server.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="import-packages"></a>Csomagok importálása
Ebben az oktatóanyagban szüksége a Python-csomagok importálása:


```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
from azureml.train.automl.run import AutoMLRun
import time
import logging
import os
```

Ha az oktatóanyag a saját Python-környezetben, használja a következő szükséges csomagok telepítéséhez.

```shell
pip install azureml-sdk[automl,notebooks] azureml-dataprep pandas scikit-learn matplotlib
```

## <a name="configure-workspace"></a>Munkaterület konfigurálása

Hozzon létre egy munkaterület-objektumot a meglévő munkaterületről. A `Workspace` egy osztály, amely fogadja a az Azure-előfizetésben és erőforráscsoportban információkat. Is létrehoz egy felhőalapú erőforrás figyeléséhez és nyomon követéséhez a modell futtatások.

A `Workspace.from_config()` beolvassa az **aml_config/config.json** fájlt, és betölti a részleteket a `ws` nevű objektumba.  A `ws` a kód további részében használható ebben az oktatóanyagban.

Miután egy munkaterület-objektumot, nevezze el a kísérletben. Létrehozhat és regisztrálhat egy helyi könyvtárban a munkaterülethez. Minden Futtatás előzményeit rögzíti a megadott kísérlet keretében és a a [az Azure portal](https://portal.azure.com).


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

Az az előző oktatóanyagban létrehozott folyamatot objektum használja. Összefoglalva, ez az oktatóanyag 1. rész így volt használható a machine learning-modell tisztítani a NYC i taxik adatait. Most az adatkészlet különböző funkciók használata, és lehetővé teszi az a funkciók és a egy taxi utazást árát közötti kapcsolatokat hozhat létre automatizált modell. Nyissa meg és futtassa az adatok az adatfolyam, és tekintse át az eredményeket:


```python
import azureml.dataprep as dprep

file_path = os.path.join(os.getcwd(), "dflows.dprep")

package_saved = dprep.Package.open(file_path)
dflow_prepared = package_saved.dataflows[0]
dflow_prepared.get_profile()
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
      <th>Hibaszámláló</th>
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
      <th>Középérték</th>
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

Az adatokat a kísérlethez oszlopok hozzáadásával előkészítése `dflow_x` kell funkciók a modell létrehozásához. Megadhat `dflow_y` az előrejelzési értéknek kell lennie **költség**:

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday','pickup_hour', 'distance','passengers', 'vendor'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-the-data-into-train-and-test-sets"></a>Az adatok felosztása tanítási és csoportok tesztelése

Most az adatok felosztása tanítási és tesztelési használatával beállítja a `train_test_split` működni a `sklearn` könyvtár. Ez a függvény elkülöníti az adatok be az x **funkciók**, modellek tanítása és az y adatkészletet **előrejelzésére értékek**, tesztelési adatkészlet. A `test_size` paraméter határozza meg az adatok tesztelését lefoglalni aránya. A `random_state` paraméter az egyik Kezdőérték a véletlenszerű generátor beállítja, hogy a tanítási és tesztelési elágazást mindig determinisztikus:

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
# flatten y_train to 1d array
y_train.values.flatten()
```

Ebben a lépésben az a célja, hogy adatpont a kész modell teszteléséhez, amely még nem használták a modell betanítását annak érdekében, hogy igaz pontossága mérjük. Egy jól betanított modell más szóval pontos előrejelzéseket adatokból már nem látott képesnek kell lennie. Most már rendelkezik a szükséges csomagokat, és a modell autotraining kész adatokat.

## <a name="automatically-train-a-model"></a>Automatikusan a modell tanítása

Automatikusan betanítja a modellt, hajtsa végre az alábbi lépéseket:
1. A kísérlet futtatásához beállításainak megadása. A betanítási adatok csatolja a konfigurációt, és a betanítási folyamat szabályozó beállítások módosítása.
1. A modell finomhangolása a kísérlet elküldése. A kísérlet elküldése, miután a folyamat végighalad a különböző machine learning-algoritmusok és a hiperparaméter beállításait, a által definiált megkötéseket tartja. A regressziós modell optimalizálása egy pontossága metrika szerint választja ki.

### <a name="define-settings-for-autogeneration-and-tuning"></a>A személyfelismerési és a hangolási beállításainak megadása

Adja meg a kísérlet paramétert, és a modell a személyfelismerési és a hangolási beállításai. Tekintse meg a teljes listáját [beállítások](how-to-configure-auto-train.md). A kísérlet elküldése az alábbi alapértelmezett beállításokkal lesz körülbelül 10 – 15 perc is, de ha azt szeretné, hogy egy rövidebb futási időt, csökkentheti vagy `iterations` vagy `iteration_timeout_minutes`.


|Tulajdonság| Az oktatóanyagban szereplő érték |Leírás|
|----|----|---|
|**iteration_timeout_minutes**|10|Minden egyes ismétléskor percben időkorlát. Ezt az értéket a teljes futásidő csökkentéséhez csökkentse.|
|**iterations**|30|Iterációk száma. Minden egyes ismétléskor egy új gépi tanulási modell tanítása az adatok. Ez az elsődleges, amely befolyásolja a teljes futási idő értéke.|
|**primary_metric**| spearman_correlation | Az optimalizálni kívánt metrika. A regressziós modell Ez a metrika alapján fogja kiválasztani.|
|**preprocess**| True (Igaz) | Használatával **igaz**, a kísérlet is előfeldolgozása a bemeneti adatok (kezelése az adatok hiányoznak, szöveg konvertálása a numerikus, stb.)|
|**Részletességi**| logging.INFO | A naplózási szint szabályozza.|
|**n_cross_validations**|5|Kereszt-ellenőrzési elágazást végrehajtani, ha nincs megadva érvényesítési adatok száma.|



```python
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 30,
    "primary_metric" : 'spearman_correlation',
    "preprocess" : True,
    "verbosity" : logging.INFO,
    "n_cross_validations": 5
}
```

Használja a meghatározott képzési beállításokat paramétere egy `AutoMLConfig` objektum. Ezenkívül megadhatja a betanítási adatok és a modell, amely `regression` ebben az esetben.

```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task = 'regression',
                             debug_log = 'automated_ml_errors.log',
                             path = project_folder,
                             X = x_train.values,
                             y = y_train.values.flatten(),
                             **automl_settings)
```

### <a name="train-the-automatic-regression-model"></a>Az automatikus regressziós modell betanítása

Indítsa el a kísérlet helyi futtatását. Adja át a definiált `automated_ml_config` objektum a kísérletvászonra. A kimenetét állítsa `True` folyamatának megtekintése a kísérlet során:


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

A frissítések megjelenő kimenet a kísérlet futása élő. Minden egyes ismétléskor láthatja, a modell típusa, a Futási időtartam és a képzési pontosságát. A mező `BEST` nyomon követi a legjobban fut képzési pontszám a metrika típusa alapján.

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

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>Option 1: Találatok megjelenítéséhez egy Jupyter widget hozzáadása

Ha egy Jupyter notebookot fog használni, használja a Jupyter notebook widget grafikon és a egy tábla összes eredmény megtekintéséhez:


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![A Futtatás részletei Jupyter widget](./media/tutorial-auto-train-models/automl-dash-output.png)
![Jupyter widget diagram](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>Option 2: Első, és vizsgálja meg az összes futtatási ismétlését a Pythonban

Minden egyes kísérlet előzményeinek lekérése is, és minden egyes ismétléskor futtatásához egyedi metrikák vizsgálata. Minden egyes futtatásához egyedi modell Gyökátlagos (root_mean_squared_error) megvizsgálásával láthatja, hogy a legtöbb ismétlések vannak előrejelzésére ($3-4) ésszerű tűréshatáron belül taxi valós költségeit.

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

import pandas as pd
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

Válassza ki a legjobb folyamat az ismétlések. A `get_output` metódust `automl_classifier` a legjobb Futtatás és illesztett modell az utolsó hívás szélességhez tartozó adja vissza. A túlterhelések a használatával `get_output`, kérheti, hogy a legjobb futtatás, és bármely illesztett modell naplózott metrika vagy egy adott iterációhoz:

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-the-best-model-accuracy"></a>A legpontosabb modell tesztelése

Használja a legjobb modellt a tesztelési adatkészletnél taxi vitel előrejelzésére előrejelzéseket futtatásához. A függvény `predict` a legjobb modellt használja, és megbecsüli y, értékének **költség kivételre**, az a `x_test` adatkészlet. Nyomtatás az első 10-es előre jelzett költség értékeit `y_predict`:

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Hozzon létre egy pontdiagram jelenítheti meg az előre jelzett költség értékeket, a tényleges költségek értékek képest. A következő kódban a `distance` funkció az x tengely és utazás `cost` , az y tengelyen. Minden egyes út távolság értéke a előre jelzett költség varianciáját összehasonlítani, az első 100, előre meghatározott és a tényleges költségek értékek külön adatsorozatként jönnek létre. Vizsgálata folyamatban van a diagram mutatja, hogy a távolság/költség kapcsolat szinte lineáris, és az előre jelzett költség értékek a következők a legtöbb esetben a nagyon közeli azonos trip távolság tényleges költségek értékeit.

```python
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(14, 10))
ax1 = fig.add_subplot(111)

distance_vals = [x[4] for x in x_test.values]
y_actual = y_test.values.flatten().tolist()

ax1.scatter(distance_vals[:100], y_predict[:100], s=18, c='b', marker="s", label='Predicted')
ax1.scatter(distance_vals[:100], y_actual[:100], s=18, c='r', marker="o", label='Actual')

ax1.set_xlabel('distance (mi)')
ax1.set_title('Predicted and Actual Cost/Distance')
ax1.set_ylabel('Cost ($)')

plt.legend(loc='upper left', prop={'size': 12})
plt.rcParams.update({'font.size': 14})
plt.show()
```

![Előrejelzési pontdiagram](./media/tutorial-auto-train-models/automl-scatter-plot.png)

Kiszámítja a `root mean squared error` az eredmények. Használja a `y_test` dataframe. Konvertálja az előre jelzett értékek összehasonlítására listáját. A függvény `mean_squared_error` két Pole hodnot tart, és kiszámítja az átlagos squared hiba közöttük. Hiba történt az eredmény négyzetgyökét véve adja meg ugyanazt a mértékegységet az y változóként **költség**. Azt jelzi, illesztésnek nagyjából a taxi diszkont előrejelzéseket, a tényleges vitel:

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

    3.2204936862688798

Futtassa a következő kódot a teljes használatával számítják ki az átlagos abszolút százalékos hiba (MAPE) `y_actual` és `y_predict` adatkészletek. Ez a metrika egy minden egyes előrejelzett és tényleges értékkel és összegeket összes különbségek közötti különbségek abszolút eltérésének számítja ki. Ezután fejezi ki, hogy az összeg az összes tényleges értékek százalékban:

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

A végső előrejelzés pontosságát mérőszámokat azt látja, hogy a modell viszonylag jó taxi vitel a az adatkészlet-szolgáltatások, általában belül + - $3.00 előrejelzésére. A hagyományos gépi tanulási modell fejlesztési folyamatok magas erőforrás-igényes, és jelentős tartomány ismeretek és idő befektetési futtatásához, és több tucatnyi modellek eredményeinek összehasonlítására, igényel. Automatizált machine learning használatával kiválóan alkalmas a rövid idő alatt az a forgatókönyvben számos különböző modell tesztelhető.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Az automatikus Machine learning oktatóanyagban tette a következő feladatokat:

> [!div class="checklist"]
> * Konfigurált egy munkaterületet, és a egy kísérletet az előkészített adatok.
> * Tanítása egy automatizált regressziós modell helyileg az egyéni paraméterekkel.
> * Felderített és ellenőrzött képzési eredményeket.
> * A legjobb modellt regisztrálva.

[A modell üzembe helyezése](tutorial-deploy-models-with-aml.md) az Azure Machine Learning.
