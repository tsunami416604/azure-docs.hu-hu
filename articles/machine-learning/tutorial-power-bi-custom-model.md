---
title: 'Oktatóanyag: a prediktív modell létrehozása jegyzetfüzettel (2. rész)'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre és helyezhet üzembe gépi tanulási modellt egy Jupyter Notebook kóddal, így a Microsoft Power BIban megjósolhatja az eredményeket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f8209c0d26cf8c572d10666696231b0468cfcbc6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370217"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-notebook-part-1-of-2"></a>Oktatóanyag: Power BI integráció – a prediktív modell létrehozása jegyzetfüzettel (2. rész)

Az oktatóanyag első részében egy prediktív gépi tanulási modellt fog betanítani és üzembe helyezni egy Jupyter Notebook kódot használva. A 2. részben a modellt fogja használni a Microsoft Power BIban várható eredmények előrejelzéséhez.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Jupyter-notebook létrehozása
> * Azure Machine Learning számítási példány létrehozása
> * Regressziós modell betanítása a scikit használatával – Learn
> * A modell üzembe helyezése valós idejű pontozási végponton

Az Power BIban három különböző módon hozhatja létre és helyezheti üzembe a modellt.  Ez a cikk A (z) "A" lehetőséget ismerteti A modellek jegyzetfüzetekkel való betanításához és üzembe helyezéséhez  Ez a beállítás egy, a Azure Machine Learning Studióban üzemeltetett Jupyter notebookok használatával kapcsolatos kód-első szerzői tapasztalatot jeleníti meg. 

Ehelyett használhatja a következőket:

* [B. lehetőség: modellek betanítása és üzembe helyezése a Designer használatával](tutorial-power-bi-designer-model.md)– a tervező (a fogd és vidd felhasználói felület) használatával alacsony kódú szerzői élmény.
* [C. lehetőség: modellek betanítása és üzembe helyezése AUTOMATIZÁLT ml használatával](tutorial-power-bi-automated-model.md) – a kód nélküli létrehozási élmény, amely teljes mértékben automatizálja az adatelőkészítést és a modell betanítását.


## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés ([ingyenes próbaverzió érhető el](https://aka.ms/AMLFree)). 
- Egy Azure Machine Learning-munkaterület. Ha még nem rendelkezik munkaterülettel, kövesse [az Azure Machine learning-munkaterület létrehozását ismertető témakört](./how-to-manage-workspace.md#create-a-workspace).
- A Python nyelv és a gépi tanulási munkafolyamatok bevezető ismerete.

## <a name="create-a-notebook-and-compute"></a>Jegyzetfüzet és számítás létrehozása

A [Azure Machine learning Studio](https://ml.azure.com) kezdőlapon válassza a **Létrehozás új** **jegyzetfüzetet** követve:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="A jegyzetfüzetek létrehozását bemutató képernyőkép":::
 
Megjelenik egy párbeszédpanel, ahol **új fájlt hozhat létre** :

1. A notebookhoz tartozó fájlnév (például `my_model_notebook` )
1. **Fájl típusának** módosítása **jegyzetfüzetre**

Kattintson a **Létrehozás** gombra. Ezután létre kell hoznia néhány számítást, és csatolnia kell a notebookhoz a kód celláinak futtatásához. Ehhez válassza a plusz ikont a jegyzetfüzet tetején:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="A számítási példány létrehozását bemutató képernyőkép":::

Ezután a **számítási példány létrehozása** lapon:

1. Válassza ki a CPU virtuálisgép-méretet – ebben az oktatóanyagban egy **Standard_D11_v2** (két mag, 14 GB RAM) lesz.
1. Kattintson a **Tovább** gombra. 
1. A **beállítások konfigurálása** lapon adjon meg egy érvényes **számítási nevet** (az érvényes karakterek: felső és kisbetűk, számjegyek és a-karakter).
1. Kattintson a **Létrehozás** gombra.

Megjelenhet a jegyzetfüzetben, hogy a **számítási** funkció melletti kör a cián színre vált, ami azt jelzi, hogy a számítási példány létrehozása folyamatban van:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="A létrehozott számítást ábrázoló képernyőfelvétel":::

> [!NOTE]
> A számítási kapacitás kiépítés körülbelül 2-4 percet vesz igénybe.

Miután kiépítte a számítást, a jegyzetfüzet használatával is végrehajthatja a kód celláit. Írja be például a cellába a következőt:

```python
import numpy as np

np.sin(3)
```

Majd a **SHIFT-ENTER** (vagy a **Control-ENTER** ) billentyűt, vagy kattintson a cella melletti lejátszás gombra. A következő kimenetnek kell megjelennie:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="A cella-végrehajtást ábrázoló képernyőfelvétel":::

Most már készen áll egy Machine Learning modell létrehozására!

## <a name="build-a-model-using-scikit-learn"></a>Modell létrehozása a scikit használatával – Learn

Ebben az oktatóanyagban a [diabétesz](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)adatkészletet használja, amely elérhető az [Azure Open adatkészletekben](https://azure.microsoft.com/services/open-datasets/). 


### <a name="import-data"></a>Adatok importálása

Az adatimportáláshoz másolja és illessze be az alábbi kódot egy új, a jegyzetfüzetbe tartozó **cellába** :

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

A `X_df` pandák adatkeret 10 alapszintű bemeneti változót tartalmaz (például kor, nem, testtömeg-index, átlagos vérnyomás és hat Vérszérum-mérés). A `y_df` Panda adatkerete az a cél változó, amely a betegség előrehaladásának mennyiségi mértékét tartalmazza egy évvel az alapkonfiguráció után. Összesen 442 rekord van.

### <a name="train-model"></a>Modell betanítása

Hozzon létre egy új **kódrészletet** a jegyzetfüzetben, és másolja és illessze be az alábbi kódrészletet, amely létrehoz egy Ridge regressziós modellt, és szerializálja a modellt a Python savanyúság-formátumának használatával:

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>A modell regisztrálása

A modell fájlján kívül a regisztrált modell is a modell metaadatait – a modell leírását, a címkéket és a keretrendszer információit – fogja használni, ami hasznos lehet a munkaterületen található modellek kezelésekor és telepítésekor. A címkék használatával például kategorizálhatja a modelleket, és szűrőket alkalmazhat a munkaterület modelljeinek listázásához. Emellett a modellnek a scikit-Learn keretrendszerrel való megjelölése leegyszerűsíti a webszolgáltatásként való üzembe helyezést, ahogy azt a későbbiekben látni fogjuk.

Másolja és illessze be az alábbi kódot egy új, a jegyzetfüzetbe tartozó **cellába** :

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

A modellt Azure Machine Learning Studio is megtekintheti, ha a bal oldali menüben a **végpontok** lehetőségre navigál:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="A modellt bemutató képernyőkép":::

### <a name="define-the-scoring-script"></a>Pontozási parancsfájl definiálása

A Microsoft Power BIba integrált modell telepítésekor meg kell határoznia egy Python *pontozási parancsfájlt* és egy egyéni környezetet. A pontozási parancsfájl két függvényt tartalmaz:

- `init()` – ezt a függvényt a szolgáltatás elindításakor hajtja végre a rendszer. Ez a függvény betölti a modellt (vegye figyelembe, hogy a modellt a rendszer automatikusan letölti a modell beállításjegyzékből) és deszerializálja.
- `run(data)` – Ez a függvény akkor kerül végrehajtásra, ha a szolgáltatás hívása olyan bemeneti adatokkal történik, amelyeknek pontozásra van szükségük. 

>[!NOTE]
> A Python dekoratőr segítségével definiáljuk a bemeneti és kimeneti adatok sémáját, ami fontos a Microsoft Power BI-integráció működéséhez.

Másolja és illessze be az alábbi kódot egy új, a jegyzetfüzetbe tartozó **cellába** . Az alábbi kódrészlethez tartozik egy cella Magic, amely a kódot egy iktatott score.py fogja írni.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Egyéni környezet definiálása

Ezután meg kell határozni a környezetet a modell pontozásához – ebben a környezetben meg kell határozni a fentiekben definiált pontozási szkript (score.py) által igényelt Python-csomagokat, például a pandák, a scikit-Learn stb.

A környezet definiálásához másolja és illessze be az alábbi kódot egy új, a jegyzetfüzetbe tartozó **cellába** :

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>A modell üzembe helyezése

A modell üzembe helyezéséhez másolja és illessze be az alábbi kódot egy új, a jegyzetfüzetbe tartozó **cellába** :

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> A szolgáltatás üzembe helyezése 2-4 percet is igénybe vehet.

A sikeresen üzembe helyezett szolgáltatás következő kimenetét kell látnia:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

A szolgáltatást Azure Machine Learning Studio is megtekintheti, ha a bal oldali menüben a **végpontok** elemre navigál:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="A végpontot ábrázoló képernyőkép":::

Javasoljuk, hogy tesztelje a webszolgáltatást, és győződjön meg arról, hogy az a vártnak megfelelően működik-e. A Azure Machine Learning Studio bal oldali menüjében a **jegyzetfüzetek** lehetőség kiválasztásával térhet vissza a jegyzetfüzethez. A szolgáltatás teszteléséhez másolja és illessze be az alábbi kódot a jegyzetfüzet új **kódjába** :

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

A kimenetnek a következő JSON-struktúrához hasonlóan kell kinéznie: `{'predict': [[205.59], [68.84]]}` .

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan hozhat létre és helyezhet üzembe egy modellt úgy, hogy azok a Microsoft Power BI által felhasználható módon legyenek felhasználhatók. A következő részben megtudhatja, hogyan használja ezt a modellt egy Power BI jelentésből.

> [!div class="nextstepaction"]
> [Oktatóanyag: modell felhasználása Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
