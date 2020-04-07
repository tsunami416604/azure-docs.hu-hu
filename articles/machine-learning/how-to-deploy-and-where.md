---
title: Modellek üzembe helyezése és helye
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan és hol telepítheti az Azure Machine Learning-modelleket, beleértve az Azure Container Instances, az Azure Kubernetes-szolgáltatást, az Azure IoT Edge-et és a mezőprogramozható kaputömböket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 02/27/2020
ms.custom: seoapril2019
ms.openlocfilehash: 3fe13dcb35e6985d160f52b7ee3f9da4accd7806
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756664"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Modellek üzembe helyezése az Azure Machine Learninggel
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ismerje meg, hogyan telepítheti gépi tanulási modelljét webszolgáltatásként az Azure-felhőben vagy az Azure IoT Edge-eszközökön.

A munkafolyamat hasonló, függetlenül [attól, hogy hol telepíti](#target) a modellt:

1. Regisztrálja a modellt.
1. Készítse elő az üzembe helyezést. (Adja meg az eszközöket, a felhasználást, a számítási célt.)
1. Telepítse a modellt a számítási célra.
1. Tesztelje az üzembe helyezett modellt, más néven egy webszolgáltatást.

A telepítési munkafolyamatban szereplő fogalmakról további információt a [Modellek kezelése, üzembe helyezése és figyelése](concept-model-management-and-deployment.md)az Azure Machine Learning szolgáltatással című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Create an Azure Machine Learning workspace](how-to-manage-workspace.md).

- Egy modell. Ha nem rendelkezik betanított modellel, használhatja az [oktatóanyagban](https://aka.ms/azml-deploy-cloud)biztosított modell- és függőségi fájlokat.

- A [Machine Learning szolgáltatás Azure CLI-bővítménye](reference-azure-machine-learning-cli.md), az Azure Machine Learning [SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)vagy az Azure Machine Learning Visual Studio Code [bővítmény.](tutorial-setup-vscode-extension.md)

## <a name="connect-to-your-workspace"></a>Csatlakozás a munkaterülethez

Az alábbi kód bemutatja, hogyan csatlakozhat egy Azure Machine Learning-munkaterülethez a helyi fejlesztési környezetben gyorsítótárazott információk használatával:

+ **Az SDK használata**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  További információ az SDK használatával egy munkaterülethez való csatlakozáshoz, tekintse meg az [Azure Machine Learning SDK python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) dokumentáció.

+ **A CLI használata**

   A CLI használatakor `-w` a `--workspace-name` vagy a paraméterrel adja meg a parancs munkaterületét.

+ **A Visual Studio Code használata**

   A Visual Studio-kód használatakor a munkaterületet grafikus felületsegítségével kell kiválasztani. További információt a [Modellek telepítése és kezelése](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) a Visual Studio-kódbővítmény dokumentációjában című témakörben talál.

## <a name="register-your-model"></a><a id="registermodel"></a>A modell regisztrálása

A regisztrált modell a modellt egy vagy több fájlt tartalmazó logikai tároló. Ha például egy olyan modellel rendelkezik, amely több fájlban van tárolva, regisztrálhatja őket egyetlen modellként a munkaterületen. A fájlok regisztrálása után letöltheti vagy telepítheti a regisztrált modellt, és megkaphatja az összes regisztrált fájlt.

> [!TIP]
> Amikor regisztrál egy modellt, megadja egy felhőhely (egy betanítási futtatásból) vagy egy helyi címtár elérési útját. Ez az elérési út csak azért, hogy keresse meg a fájlokat feltölteni részeként a regisztrációs folyamat. Nem kell, hogy megfeleljen a bejegyzés parancsfájlban használt elérési utat. További információt a [Modellfájlok megkeresése a bejegyzési parancsfájlban](#load-model-files-in-your-entry-script)című témakörben talál.

A gépi tanulási modellek regisztrálva vannak az Azure Machine Learning-munkaterületen. A modell származhat az Azure Machine Learningből vagy máshonnan. A modell regisztrálásakor tetszés szerint metaadatokat adhat meg a modellről. A `tags` `properties` modellregisztrációra alkalmazott szótárak és szótárak ezután modellek szűrésére használhatók.

Az alábbi példák bemutatják, hogyan regisztrálhat egy modellt.

### <a name="register-a-model-from-an-experiment-run"></a>Modell regisztrálása kísérletfuttatásból

Ebben a szakaszban a kódrészletek bemutatják, hogyan regisztrálhat egy modellt egy betanítási sorozatból:

> [!IMPORTANT]
> Ezeka kódrészletek használatához korábban egy betanítási futtatást kellett végrehajtania, és hozzáféréssel kell rendelkeznie az `Run` objektumhoz (például SDK) vagy a futtatási azonosító értékéhez (CLI példa). A betanítási modellekről a Számítási [célok beállítása a modellbetanításhoz című](how-to-set-up-training-targets.md)témakörben talál további információt.

+ **Az SDK használata**

  Amikor az SDK-t használja egy modell betanításához, a modell betanításának módjától függően fogadhat [egy Futtatás](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) objektumot vagy egy [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) objektumot. Minden objektum használható egy kísérlet futtatása által létrehozott modell regisztrálására.

  + Modell regisztrálása `azureml.core.Run` objektumból:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    A `model_path` paraméter a modell felhőbeli helyére hivatkozik. Ebben a példában egyetlen fájl elérési útját használja a rendszer. Ha több fájlt szeretne felvenni `model_path` a modell regisztrációjába, állítsa a fájlokat tartalmazó mappa elérési útját. További információt a [Run.register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) dokumentációjában talál.

  + Modell regisztrálása `azureml.train.automl.run.AutoMLRun` objektumból:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    Ebben a példában `iteration` a és a `metric` paraméterek nincsenek megadva, így a legjobb elsődleges metrikával rendelkező iteráció regisztrálva lesz. A `model_id` futtatásból visszaadott értéket a modellnév helyett használja a rendszer.

    További információt az [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) dokumentációjában talál.

+ **A CLI használata**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  A `--asset-path` paraméter a modell felhőbeli helyére hivatkozik. Ebben a példában egyetlen fájl elérési útját használja a rendszer. Ha több fájlt szeretne felvenni `--asset-path` a modell regisztrációjába, állítsa a fájlokat tartalmazó mappa elérési útját.

+ **A Visual Studio Code használata**

  Modellek et regisztrálhat bármilyen modellfájl vagy mappa használatával a [Visual Studio Kód](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) bővítmény használatával.

### <a name="register-a-model-from-a-local-file"></a>Modell regisztrálása helyi fájlból

A modell regisztrálásával a modell helyi elérési útját. Megadhatja egy mappa vagy egyetlen fájl elérési útját. Ezzel a módszerrel regisztrálhatja az Azure Machine Learning szolgáltatással betanított és majd letöltött modelleket. Ezzel a módszerrel az Azure Machine Learningen kívül betanított modellek et is regisztrálhat.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Az SDK és az ONNX használata**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Ha több fájlt szeretne felvenni `model_path` a modell regisztrációjába, állítsa a fájlokat tartalmazó mappa elérési útját.

+ **A CLI használata**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Ha több fájlt szeretne felvenni `-p` a modell regisztrációjába, állítsa a fájlokat tartalmazó mappa elérési útját.

**Becsült idő**: Körülbelül 10 másodperc.

További információt a [Modellosztály dokumentációjában](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)talál.

Az Azure Machine Learningen kívül betanított modellek ről további információt a [Meglévő modell üzembe helyezése](how-to-deploy-existing-model.md)című témakörben talál.

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>Egy- és többmodelles végpontok
Az Azure ML egyetlen vagy több modell üzembe helyezését támogatja egyetlen végpont mögött.

A többmodelles végpontok egy megosztott tárolót használnak több modell üzemeltetéséhez. Ez segít csökkenteni a rezsiköltségeket, javítja a kihasználtságot, és lehetővé teszi a modulok együttesekbe való összeláncolását. A központi telepítési parancsfájlban megadott modellek csatlakoztatva vannak, és elérhetővé teszik a kiszolgáló tároló lemezén – igény szerint betöltheti őket a memóriába, és a pontozáskor kért modell alapján pontozáskor is megszerezheti őket.

Egy E2E-példában, amely bemutatja, hogyan kell használni több modell mögött egy konténeres végpont, [lásd ezt a példát](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model)

## <a name="prepare-to-deploy"></a>Az üzembe helyezés előkészítése

A modell szolgáltatásként való üzembe helyezéséhez a következő összetevőkre van szükség:

* **Következtetési környezet definiálása**. Ez a környezet magában foglalja a modell futtatásához szükséges függőségeket a következtetés.
* **Adja meg a pontozási kódot.** Ez a parancsfájl elfogadja a kérelmeket, a modell használatával pontozza a kérelmeket, és visszaadja az eredményeket.
* **Következtetési konfiguráció definiálása**. A következtetés konfigurációja határozza meg a környezet konfigurációját, a belépési parancsfájlt és a modell szolgáltatásként való futtatásához szükséges egyéb összetevőket.

Miután rendelkezik a szükséges összetevőkkel, profilozhatja a szolgáltatást, amely a modell üzembe helyezése ként jön létre a processzor- és memóriakövetelmények megértéséhez.

### <a name="1-define-inference-environment"></a>1. Következtetési környezet meghatározása

A következtetéskonfiguráció leírja, hogyan állíthatja be a modellt tartalmazó webszolgáltatást. A modell telepítésekor később használja.

A következtetéskonfiguráció az Azure Machine Learning-környezetek használatával határozza meg a központi telepítéshez szükséges szoftverfüggőségeket. A környezetek lehetővé teszik a betanításhoz és üzembe helyezéshez szükséges szoftverfüggőségek létrehozását, kezelését és újrafelhasználását. Létrehozhat egy környezetet egyéni függőségi fájlokból, vagy használhatja a válogatott Azure Machine Learning-környezetek egyikét. A következő YAML egy példa egy Conda függőségi fájl a következtetés. Vegye figyelembe, hogy meg kell adnia azureml-defaults a verion >= 1.0.45 pip-függőség, mert tartalmazza a szükséges funkciókat a modell webszolgáltatásként. Ha automatikus sémagenerálást szeretne használni, a belépési parancsfájlnak is importálnia kell a `inference-schema` csomagokat.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
      # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Ha a függőség a Conda és a pip (a PyPi-től) keresztül is elérhető, a Microsoft a Conda-verzió használatát javasolja, mivel a Conda-csomagok általában előre elkészített bináris fájlokat hoznak létre, amelyek megbízhatóbbá teszik a telepítést.
>
> További [információ: Conda és Pip .](https://www.anaconda.com/understanding-conda-and-pip/)
>
> Annak ellenőrzéséhez, hogy a függőség elérhető-e `conda search <package-name>` a Conda-n [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) keresztül, használja a parancsot, vagy használja a csomagindexeket a és [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo)a alkalmazásban.

A függőségi fájl segítségével létrehozhat egy környezeti objektumot, és mentheti azt a munkaterületre későbbi használatra:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. Pontozási kód meghatározása

A belépési parancsfájl fogadja az üzembe helyezett webszolgáltatásnak küldött adatokat, és továbbítja azokat a modellnek. Ezután veszi a modell által visszaadott választ, és azt adja vissza az ügyfélnek. *A parancsfájl a modellre jellemző.* Meg kell értenie a modell által várt és visszaadott adatokat.

A parancsfájl két függvényt tartalmaz, amelyek betöltik és futtatják a modellt:

* `init()`: Ez a függvény általában egy globális objektumba tölti be a modellt. Ez a függvény csak egyszer fut, amikor a Docker-tároló a webszolgáltatás elindul.

* `run(input_data)`: Ez a függvény a modell segítségével jósolja meg a bemeneti adatokon alapuló értéket. A futtatás bemenetei és kimenetei általában JSON-t használnak a szerializáláshoz és a deszerializáláshoz. Nyers bináris adatokkal is dolgozhat. Az adatokat átalakíthatja, mielőtt elküldené a modellnek, vagy mielőtt visszaküldené az ügyfélnek.

#### <a name="load-model-files-in-your-entry-script"></a>Modellfájlok betöltése a bejegyzési parancsfájlba

A bejegyzési parancsfájlban kétféleképpen keresheti meg a modelleket:
* `AZUREML_MODEL_DIR`: A modell helyére vezető elérési utat tartalmazó környezeti változó.
* `Model.get_model_path`: Egy API, amely a regisztrált modellnév használatával adja vissza a modellfájl elérési útját.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR egy környezeti változó a szolgáltatás telepítése során létrehozott. Ezzel a környezeti változóval megkeresheti az üzembe helyezett modell(ek) helyét.

Az alábbi táblázat a telepített modellek számától függően ismerteti AZUREML_MODEL_DIR értékét:

| Környezet | Környezeti változó értéke |
| ----- | ----- |
| Egyetlen modell | A modellt tartalmazó mappa elérési útja. |
| Több modell | Az összes modellt tartalmazó mappa elérési útja. Modellek találhatók név és verzió ebben`$MODEL_NAME/$VERSION`a mappában ( ) |

A modell regisztrációja és üzembe helyezése során a modellek a AZUREML_MODEL_DIR elérési útba kerülnek, és az eredeti fájlnevek megmaradnak.

Ha meg szeretné szerezni egy modellfájl elérési útját a beviteli parancsfájlban, kombinálja a környezeti változót a keresett fájlelérési úttal.

**Példa egyetlen modellre**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Több modell példa**
```python
# Example when the model is a file, and the deployment contains multiple models
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model', '1', 'sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Amikor regisztrál egy modellt, megad egy modellnevet, amely a modell kezeléséhez használatos a beállításjegyzékben. Ezt a nevet a [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) metódussal használhatja a modellfájl vagy a helyi fájlrendszeren lévő fájlok elérési útjának beolvasásához. Ha regisztrál egy mappát vagy fájlgyűjteményt, ez az API a fájlokat tartalmazó könyvtár elérési útját adja vissza.

Amikor regisztrál egy modellt, nevet ad neki. A név megfelel a modell helyének, akár helyileg, akár a szolgáltatás üzembe helyezése során.

#### <a name="optional-define-model-web-service-schema"></a>(Nem kötelező) Modell webszolgáltatás-séma definiálása

Ha automatikusan létre szeretne hozni egy sémát a webszolgáltatáshoz, adjon meg egy mintát a konstruktor bemeneti és/vagy kimeneti adataiból a definiált típusobjektumok egyikéhez. A típus és a minta a séma automatikus létrehozásához használatos. Az Azure Machine Learning ezután létrehoz egy [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) specifikációt a webszolgáltatás hoz létre a központi telepítés során.

Ezek a típusok jelenleg támogatottak:

* `pandas`
* `numpy`
* `pyspark`
* Szabványos Python-objektum

A sémagenerálás használatához foglalja `inference-schema` bele a nyílt forráskódú csomagot a függőségfájlba. A csomaggal kapcsolatos további [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema)információkért lásd: . Adja meg a bemeneti és `input_sample` `output_sample` kimeneti mintaformátumokat a és a változókban, amelyek a webszolgáltatás kérés- és válaszformátumait jelölik. Használja ezeket a mintákat a bemeneti `run()` és kimeneti függvény dekoratátorok a funkciót. A következő scikit-learn példa sémagenerálást használ.

##### <a name="example-entry-script"></a>Példa bejegyzésparancsfájl

A következő példa bemutatja, hogyan lehet elfogadni és visszaadni a JSON-adatokat:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

A következő példa bemutatja, hogyan definiálhatja a bemeneti adatokat szótárként `<key: value>` egy DataFrame használatával. Ez a módszer a Power BI-ból telepített webszolgáltatás fogyasztásához támogatott. ([További információ a webszolgáltatás Power BI-ból történő felhasználásáról.)](https://docs.microsoft.com/power-bi/service-machine-learning-integration)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

További példákat az alábbi parancsfájlokban talál:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [Auto ML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Bináris adatok](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. Következtetési konfiguráció meghatározása
    
A következő példa bemutatja egy környezet betöltését a munkaterületről, majd a következtetéskonfigurációval való használatát:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

A környezetekkel kapcsolatos további információkért olvassa el a [Környezetek létrehozása és kezelése a betanításhoz és telepítéshez című témakört.](how-to-use-environments.md)

A következtetések konfigurációjáról további információt a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) osztály dokumentációjában talál.

Az egyéni Docker-rendszerkép következtetési konfigurációval történő használatáról a [Modell üzembe helyezése egyéni Docker-lemezkép használatával című témakörben](how-to-deploy-custom-docker-image.md)talál további információt.

#### <a name="cli-example-of-inferenceconfig"></a>CLI példa inferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

A következő parancs bemutatja, hogyan telepíthet egy modellt a CLI használatával:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Ebben a példában a konfiguráció a következő beállításokat adja meg:

* Hogy a modell python-t igényel.
* A [bejegyzésparancsfájl](#script), amely a telepített szolgáltatásnak küldött webes kérelmek kezelésére szolgál.
* A Conda-fájl, amely leírja a következtetéshez szükséges Python-csomagokat.

Az egyéni Docker-rendszerkép következtetési konfigurációval történő használatáról a [Modell üzembe helyezése egyéni Docker-lemezkép használatával című témakörben](how-to-deploy-custom-docker-image.md)talál további információt.

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (Nem kötelező) Profil a modell erőforrás-kihasználtság meghatározásához

Miután regisztrálta a modellt, és előkészítette a központi telepítéshez szükséges egyéb összetevőket, meghatározhatja a processzort és a memóriát, amire az üzembe helyezett szolgáltatásnak szüksége lesz. A profilkészítés teszteli a modellt futtató szolgáltatást, és olyan információkat ad vissza, mint a PROCESSZOR-használat, a memóriahasználat és a válaszkés. Azt is előírja, hogy a cpu és a memória erőforrás-használat alapján.

Annak érdekében, hogy profila modell, szüksége lesz:
* Egy regisztrált modell.
* A bejegyzési parancsfájl és a következtetés környezetdefiníció n alapuló következtetési konfiguráció.
* Egyetlen oszlopos táblázatos adatkészlet, ahol minden sor egy karakterláncot tartalmaz, amely mintakérelem-adatokat jelöl.

> [!IMPORTANT]
> Ezen a ponton csak azon szolgáltatások profilalkotását támogatjuk, amelyek azt várják, hogy a kérelem adatai karakterlánclegyenek, például: string szerializált json, szöveg, string szerializált lemezkép stb. Az adatkészlet (karakterlánc) minden egyes sorának tartalma a HTTP-kérelem törzsébe kerül, és elküldi a szolgáltatásnak, amely a modellt pontozásra tartalmazza.

Az alábbi példa bemutatja, hogyan hozhat létre egy bemeneti adatkészletet egy olyan szolgáltatás profilzatához, amely elvárja, hogy a bejövő kérelem adatok szerializált jsont tartalmazzanak. Ebben az esetben létrehoztunk egy adatkészlet alapú száz példányban ugyanannak a kérelem adattartalomnak. Valós helyzetekben azt javasoljuk, hogy használjon nagyobb adatkészletek, amelyek különböző bemenetek, különösen akkor, ha a modell erőforrás-használat/viselkedés bemeneti függő.

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Miután a mintakérelem adatait tartalmazó adatkészlet készen áll, hozzon létre egy következtetési konfigurációt. A következtetés konfigurációja a score.py és a környezetdefiníción alapul. A következő példa bemutatja, hogyan hozhat létre a következtetéskonfigurációt és futtathat profilkészítést:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

A következő parancs bemutatja, hogyan lehet egy modellt profilozni a CLI használatával:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> A profilkészítés által visszaadott információk megőrzéséhez használja a modell címkéit vagy tulajdonságait. Címkék vagy tulajdonságok használatával tárolja az adatokat a modell a modell rendszerleíró adatbázisban. Az alábbi példák bemutatják egy `requestedCpu` `requestedMemoryInGb` új címke hozzáadását, amely a következő információkat tartalmazza:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Telepítés a célhoz

A központi telepítés a következtetés konfigurációs központi telepítési konfigurációját használja a modellek központi telepítéséhez. A telepítési folyamat hasonló, függetlenül a számítási cél. Az AKS-re való üzembe helyezés némileg eltérő, mivel meg kell adnia egy hivatkozást az AKS-fürtre.

### <a name="choose-a-compute-target"></a>Számítási cél kiválasztása

A következő számítási célokat vagy számítási erőforrásokat használhatja a webszolgáltatás telepítésének üzemeltetéséhez:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

### <a name="define-your-deployment-configuration"></a>A központi telepítési konfiguráció meghatározása

A modell üzembe helyezése előtt meg kell határoznia a központi telepítési konfigurációt. *A központi telepítési konfiguráció a számítási cél, amely a webszolgáltatás üzemeltetésére vonatkozik.* Ha például helyileg telepít egy modellt, meg kell adnia azt a portot, ahol a szolgáltatás elfogadja a kéréseket. A központi telepítési konfiguráció nem része a belépési parancsfájlnak. A számítási cél, amely a modell és a bejegyzési parancsfájl üzemeltetéséhez használt.

Előfordulhat, hogy a számítási erőforrást is létre kell hoznia, ha például még nem rendelkezik a munkaterülethez társított Azure Kubernetes-szolgáltatás (AKS) példányával.

Az alábbi táblázat egy példát mutat be az egyes számítási célokkal kapcsolatos központi telepítési konfiguráció létrehozására:

| Számítási cél | Példa a központi telepítés konfigurációjára |
| ----- | ----- |
| Helyi | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

A helyi, az Azure Container-példányok és az AKS-webszolgáltatások osztályai `azureml.core.webservice`importálhatók:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-tls"></a>Központi telepítések biztonságossá tétele a TLS-sel

A webszolgáltatások telepítésének biztonságossá helyezéséről a [TLS engedélyezése és telepítése című témakörben talál](how-to-secure-web-service.md#enable)további információt.

### <a name="local-deployment"></a><a id="local"></a>Helyi telepítés

A modell helyi üzembe helyezéséhez a Docker telepítve kell lennie a helyi számítógépen.

#### <a name="using-the-sdk"></a>Az SDK használata

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

További információt a [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)és webservice dokumentációjában [talál.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)

#### <a name="using-the-cli"></a>A CLI használata

Ha egy modellt a CLI használatával szeretne telepíteni, használja a következő parancsot. Cserélje `mymodel:1` le a regisztrált modell nevére és verziójára:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

További információt az [az ml modell üzembe helyezésének dokumentációjában talál.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="understanding-service-state"></a>A szolgáltatás állapotának ismertetése

A modell üzembe helyezése során előfordulhat, hogy a szolgáltatás állapota megváltozik, miközben teljes mértékben telepíti.

Az alábbi táblázat a különböző szolgáltatási állapotokat ismerteti:

| Webszolgáltatás állapota | Leírás | Végső állapot?
| ----- | ----- | ----- |
| Átállás | A szolgáltatás üzembe helyezése folyamatban van. | Nem |
| Nem kifogástalan | A szolgáltatás telepítve van, de jelenleg nem érhető el.  | Nem |
| Nem schedulable | A szolgáltatás jelenleg nem telepíthető az erőforrások hiánya miatt. | Nem |
| Sikertelen | A szolgáltatás telepítése hiba vagy összeomlás miatt nem sikerült. | Igen |
| Kifogástalan | A szolgáltatás kifogástalan állapotú, és a végpont elérhető. | Igen |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Számítási példány webszolgáltatása (fejlesztési/tesztelési)

Lásd: [Modell üzembe helyezése az Azure Machine Learning számítási példányához.](how-to-deploy-local-container-notebook-vm.md)

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Azure container-példányok (fejlesztői/tesztelési)

Lásd: [Üzembe helyezés az Azure Container-példányokba.](how-to-deploy-azure-container-instance.md)

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Azure Kubernetes szolgáltatás (fejlesztés/tesztelés és éles környezet)

Lásd: [Üzembe helyezés az Azure Kubernetes szolgáltatásba.](how-to-deploy-azure-kubernetes-service.md)

### <a name="ab-testing-controlled-rollout"></a>A/B vizsgálat (ellenőrzött bevezetés)
További [információ: Az ml-modellek ellenőrzött bevezetése.](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview)

## <a name="consume-web-services"></a>Webszolgáltatások felhasználása

Minden telepített webszolgáltatás rest-végpontot biztosít, így bármilyen programozási nyelven létrehozhat ügyfélalkalmazásokat.
Ha engedélyezte a kulcsalapú hitelesítést a szolgáltatáshoz, meg kell adnia egy szolgáltatáskulcsot jogkivonatként a kérelem fejlécében.
Ha engedélyezte a jogkivonat-alapú hitelesítést a szolgáltatáshoz, meg kell adnia egy Azure Machine Learning JSON webtoken (JWT) tulajdonosi jogkivonatként a kérelem fejlécében. 

Az elsődleges különbség az, hogy a **kulcsok statikusak és manuálisan újragenerálhatók**, és a **jogkivonatokat a lejáratkor frissíteni kell**. A kulcsalapú hitelesítés támogatott az Azure Container Instance és az Azure Kubernetes Service üzembe helyezett webszolgáltatások, és a token-alapú hitelesítés **csak** az Azure Kubernetes-szolgáltatás központi telepítések. További információkért és konkrét kódmintákért tekintse meg [az](how-to-setup-authentication.md#web-service-authentication) útmutató a hitelesítést.

> [!TIP]
> A szolgáltatás üzembe helyezése után a JSON-dokumentum beolvasható. Használja a [swagger_uri tulajdonságot](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) az üzembe helyezett `service.swagger_uri`webszolgáltatás (például) az URI a helyi webszolgáltatás Swagger fájl.

### <a name="request-response-consumption"></a>Kérés-válasz felhasználás

Íme egy példa a szolgáltatás pythonban való meghívására:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

További információ: [Ügyfélalkalmazások létrehozása a webszolgáltatások felhasználásához.](how-to-consume-web-service.md)

### <a name="web-service-schema-openapi-specification"></a>Webszolgáltatás sémája (OpenAPI specifikáció)

Ha a központi telepítéshez automatikus sémagenerálást használt, a szolgáltatás OpenAPI-specifikációjának címét a [swagger_uri tulajdonság](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)használatával szerezheti be. (Például.) `print(service.swagger_uri)` Használja a GET kérelmet, vagy nyissa meg az URI-t a böngészőben a specifikáció lekéréséhez.

A következő JSON-dokumentum egy példa egy központi telepítéshez létrehozott sémára (OpenAPI specifikáció):

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

További információ: [OpenAPI specification](https://swagger.io/specification/).

A specifikációból ügyfélkódtárakat létrehozó segédprogramról a [swagger-codegen](https://github.com/swagger-api/swagger-codegen)című témakörben lehet.

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Kötegkövetkeztetés
Az Azure Machine Learning számítási céljait az Azure Machine Learning hozta létre és kezeli. Ezek az Azure Machine Learning-folyamatok kötegelt előrejelzéséhez használhatók.

Az Azure Machine Learning Compute-val való kötegelt következtetés forgatókönyvét a [Kötegelt előrejelzések futtatása.](tutorial-pipeline-batch-scoring-classification.md)

### <a name="iot-edge-inference"></a><a id="iotedge"></a>IoT Edge-következtetések
A peremhálózati üzembe helyezés támogatása előzetes verzióban érhető el. További információ: [Az Azure Machine Learning üzembe helyezése IoT Edge-modulként.](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)


## <a name="update-web-services"></a><a id="update"></a>Webszolgáltatások frissítése

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Modellek folyamatos üzembe helyezése

A modelleket folyamatosan telepítheti az [Azure DevOps](https://azure.microsoft.com/services/devops/)Machine Learning bővítményhasználatával. Az Azure DevOps Machine Learning-bővítmény használatával üzembe helyezheti a folyamatot, ha egy új gépi tanulási modell regisztrálva van egy Azure Machine Learning-munkaterületen.

1. Regisztráljon az [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)szolgáltatásra, amely lehetővé teszi az alkalmazás folyamatos integrációját és kézbesítését bármely platformra vagy felhőbe. (Vegye figyelembe, hogy az Azure-folyamatok nem ugyanaz, mint a [Machine Learning-folyamatok.)](concept-ml-pipelines.md#compare)

1. [Hozzon létre egy Azure DevOps-projektet.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Telepítse a [Machine Learning bővítményt az Azure-folyamatokhoz.](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)

1. A szolgáltatáskapcsolatok használatával hozzon létre egy egyszerű szolgáltatás kapcsolatot az Azure Machine Learning-munkaterülettel, így hozzáférhet a műtermékek. Nyissa meg a projektbeállításokat, válassza **a Szolgáltatáskapcsolatok**lehetőséget, majd válassza az **Azure Resource Manager**lehetőséget:

    [![Az Azure Resource Manager kiválasztása](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. A **Hatókör szint** listában válassza az **AzureMLWorkspace**elemet, majd adja meg a többi értéket:

    ![Az AzureMLWorkspace kiválasztása](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Ha az Azure Pipelines használatával folyamatosan üzembe szeretné helyezni a gépi tanulási modellt, a folyamatok alatt válassza a **release**lehetőséget. Adjon hozzá egy új összetevőt, majd válassza ki az **AzureML-modell** összetevőt és a korábban létrehozott szolgáltatáskapcsolatot. Válassza ki a telepítés indításához a modellt és a verziót:

    [![AzureML-modell kiválasztása](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Engedélyezze a modell eseményindító a modell műtermék. Amikor bekapcsolja az eseményindítót, minden alkalommal, amikor a modell megadott verziója (azaz a legújabb verzió) regisztrálva van a munkaterületen, egy Azure DevOps kiadási folyamat aktiválódik.

    [![A modell eseményindítójának engedélyezése](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

További mintaprojektekés példák: a GitHub alábbi minta-reposjai:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Modell letöltése
Ha azt szeretné, hogy töltse le a modellt használni a saját végrehajtási környezetben, akkor ezt a következő SDK / CLI parancsok:

Sdk:
```python
model_path = Model(ws,'mymodel').download()
```

Cli:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>(Előzetes verzió) Kód nélküli modell központi telepítése

No-code modell központi telepítése jelenleg előzetes verzióban, és támogatja a következő gépi tanulási keretrendszerek:

### <a name="tensorflow-savedmodel-format"></a>Tensorflow SavedModel formátum
A Tensorflow-modelleket **SavedModel formátumban** kell regisztrálni a kód nélküli modell telepítéséhez.

A SavedModel létrehozásáról ezen a [linken](https://www.tensorflow.org/guide/saved_model) talál információt.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>ONNX-modellek

Az ONNX modell regisztrációja és üzembe helyezése minden ONNX-következtetési diagramesetében támogatott. Az előfeldolgozási és a folyamat utáni lépések jelenleg nem támogatottak.

Íme egy példa az MNIST ONNX modell regisztrálására és telepítésére:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Ha Pytorch-ot használsz, [a PyTorch-ról az ONNX-re történő exportálás](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) a konverzióval és a korlátozásokkal kapcsolatos részleteket tartalmazza. 

### <a name="scikit-learn-models"></a>Scikit-learn modellek

Egyetlen kódmodell-telepítés sem támogatott az összes beépített scikit-learn modelltípushoz.

Íme egy példa arra, hogyan regisztrálhat és helyezhet üzembe egy sklearn modellt extra kód nélkül:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

MEGJEGYZÉS: A predict_proba támogató modellek alapértelmezés szerint ezt a módszert fogják használni. Ha felül szeretné bírni ezt az előrejelzést, a POST törzset az alábbiak szerint módosíthatja:
```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

MEGJEGYZÉS: Ezek a függőségek szerepelnek az előre összeállított sklearn következtetéstárolóban:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Csomagmodellek

Bizonyos esetekben előfordulhat, hogy a modell üzembe helyezése nélkül szeretne létrehozni egy Docker-lemezképet (ha például [az Azure App Service-ben](how-to-deploy-app-service.md)kíván üzembe helyezni). Vagy érdemes letölteni a lemezképet, és futtatni egy helyi Docker-telepítésen. Előfordulhat, hogy még a kép létrehozásához használt fájlokat is le szeretné tölteni, meg szeretné vizsgálni, módosítani, és manuálisan is fel építeni a képet.

A modell csomagolása lehetővé teszi, hogy ezeket a dolgokat megtegye. A modell webszolgáltatásként való üzemeltetéséhez szükséges összes eszközt becsomagolja, és lehetővé teszi egy teljesen beépített Docker-lemezkép vagy a létrehozáshoz szükséges fájlok letöltését. A modellcsomagolás kétféleképpen használható:

**Csomagban lévő modell letöltése:** Töltse le a Docker-lemezképet, amely tartalmazza a modellt és a webszolgáltatásként való üzemeltetéséhez szükséges egyéb fájlokat.

**Docker-fájl létrehozása:** Töltse le a Docker-fájlt, a modellt, a belépési parancsfájlt és a Docker-lemezkép létrehozásához szükséges egyéb eszközöket. Ezután megvizsgálhatja a fájlokat, vagy módosíthatja a képet, mielőtt a képet helyileg hozná létre.

Mindkét csomag használható egy helyi Docker-rendszerkép lefelvételéhez.

> [!TIP]
> A csomag létrehozása hasonló a modell üzembe helyezéséhez. Regisztrált modellt és következtetési konfigurációt használ.

> [!IMPORTANT]
> Egy teljesen beépített lemezkép letöltéséhez vagy egy lemezkép helyi létrehozásához telepítenie kell a [Dockert](https://www.docker.com) a fejlesztői környezetben.

### <a name="download-a-packaged-model"></a>Csomagolt modell letöltése

A következő példa egy lemezképet hoz létre, amely regisztrálva van a munkaterület Azure-tároló beállításjegyzékében:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Miután létrehozott egy csomagot, `package.pull()` a rendszerkép lekérése a helyi Docker-környezetbe. A parancs kimenete megjeleníti a kép nevét. Példa: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

A modell letöltése után `docker images` a paranccsal sorolja fel a helyi képeket:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Ha a lemezkép alapján helyi tárolót szeretne indítani, a következő paranccsal elindíthat egy elnevezett tárolót a rendszerhéjból vagy a parancssorból. Cserélje `<imageid>` le az értéket a `docker images` parancs által visszaadott képazonosítóra.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Ez a parancs elindítja a `myimage`kép legújabb verzióját. Leképezi a 6789-es helyi portot a tárolóporthoz, amelyen a webszolgáltatás figyel (5001). A nevet `mycontainer` is hozzárendeli a tárolóhoz, ami megkönnyíti a tároló leállítását. A tároló indítása után kéréseket `http://localhost:6789/score`küldhet a rendszernek.

### <a name="generate-a-dockerfile-and-dependencies"></a>Docker-fájl és függőségek létrehozása

A következő példa bemutatja, hogyan töltheti le a Dockerfile, modell és egyéb szükséges eszközök egy lemezkép helyi létrehozásához szükséges. A `generate_dockerfile=True` paraméter azt jelzi, hogy a fájlokat szeretné, nem pedig egy teljesen felépített képet.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Ez a kód letölti a lemezkép `imagefiles` könyvtárba való felépítéséhez szükséges fájlokat. A mentett fájlokban szereplő Docker-fájl egy Azure-tároló beállításjegyzékében tárolt alaprendszerképre hivatkozik. Amikor a lemezképet a helyi Docker-telepítésre építi fel, a címet, a felhasználónevet és a jelszót kell használnia a beállításjegyzék hitelesítéséhez. A következő lépésekkel hozd létre a lemezképet egy helyi Docker-telepítés használatával:

1. Egy rendszerhéj vagy parancssori munkamenet, a következő paranccsal hitelesíti a Docker az Azure-tároló beállításjegyzék. Cserélje `<address>` `<username>`le `<password>` a , és `package.get_container_registry()`a program által beolvasott értékekre.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. A lemezkép létrehozásához használja a következő parancsot. Cserélje `<imagefiles>` le annak a `package.save()` könyvtárnak az elérési útját, ahová a fájlokat mentette.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Ez a parancs a `myimage`kép nevét .

A rendszerkép felépítésének ellenőrzéséhez `docker images` használja a parancsot. Meg kell `myimage` jelennie a képen a listában:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Ha új tárolót szeretne indítani a lemezkép alapján, használja a következő parancsot:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Ez a parancs elindítja a `myimage`kép legújabb verzióját. Leképezi a 6789-es helyi portot a tárolóporthoz, amelyen a webszolgáltatás figyel (5001). A nevet `mycontainer` is hozzárendeli a tárolóhoz, ami megkönnyíti a tároló leállítását. A tároló indítása után kéréseket `http://localhost:6789/score`küldhet a rendszernek.

### <a name="example-client-to-test-the-local-container"></a>Példa ügyfél a helyi tároló tesztelésére

A következő kód egy példa egy Python-ügyfél, amely használható a tároló:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Például más programozási nyelveken lévő ügyfelek, [lásd: Felhasználása webszolgáltatásként telepített modellek](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>A Docker-tároló leállítása

A tároló leállításához használja a következő parancsot egy másik parancsértelmezőből vagy parancssorból:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Telepített webszolgáltatás törléséhez használja `service.delete()`a használatát.
Regisztrált modell törléséhez `model.delete()`használja a használatát.

További információt a [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) és a [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)dokumentációjában talál.

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Speciális beviteli parancsfájl-készítés

<a id="binary"></a>

### <a name="binary-data"></a>Bináris adatok

Ha a modell bináris adatokat fogad el, `score.py` például egy lemezképet, módosítania kell a központi telepítéshez használt fájlt a nyers HTTP-kérelmek fogadásához. A nyers adatok elfogadásához használja az osztályt a `AMLRequest` beviteli parancsfájlban, és adja hozzá a `@rawhttp` lakberendezőt a `run()` függvényhez.

Íme egy példa a `score.py` bináris adatokat fogadó:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Az `AMLRequest` osztály a `azureml.contrib` névtérben van. A névtérben lévő entitások gyakran változnak, miközben a szolgáltatás fejlesztésén dolgozunk. A névtérben lévő ketkeegyünk olyan előzetes verziónak, amelyet a Microsoft nem támogat teljes mértékben.
>
> Ha ezt a helyi fejlesztői környezetben kell tesztelnie, az összetevőket a következő paranccsal telepítheti:
>
> ```shell
> pip install azureml-contrib-services
> ```

Az `AMLRequest` osztály csak lehetővé teszi a nyers feladott adatok elérését a score.py, nincs ügyféloldali összetevő. Egy ügyféltől a szokásos módon kell adatokat közzétenni. Például a következő Python-kód beolvas egy képfájlt, és közzéteszi az adatokat:

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>Több forrásból származó erőforrás-megosztás (CORS)

A több forrásból származó erőforrások megosztása lehetővé teszi, hogy a weblapon lévő erőforrásokat egy másik tartományból lehessen kérni. A CORS http-fejléceken keresztül működik, amelyeket az ügyfélkéréssel küld, és a szolgáltatásválaszával küld vissza. A CORS-ról és az érvényes fejlécekről további információt a [Wikipédia több forrásból származó erőforrás-megosztásában](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) talál.

Ha a modell központi telepítését a `AMLResponse` CORS támogatására szeretné konfigurálni, használja a bejegyzési parancsfájlban lévő osztályt. Ez az osztály lehetővé teszi a fejlécek beállítását a válaszobjektumon.

A következő példa `Access-Control-Allow-Origin` beállítja a bejegyzésparancsfájl válaszának fejlécét:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Az `AMLResponse` osztály a `azureml.contrib` névtérben van. A névtérben lévő entitások gyakran változnak, miközben a szolgáltatás fejlesztésén dolgozunk. A névtérben lévő ketkeegyünk olyan előzetes verziónak, amelyet a Microsoft nem támogat teljes mértékben.
>
> Ha ezt a helyi fejlesztői környezetben kell tesztelnie, az összetevőket a következő paranccsal telepítheti:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Az Azure Machine Learning csak postai és get-kérelmeket továbbít a pontozási szolgáltatást futtató tárolókba. Ez hibákat okozhat a webböngészők miatt, amelyek a REPÜLÉS előtti CORS-kéréseket használó BEÁLLÍTÁSOK kéréseket használják.
> 

## <a name="next-steps"></a>További lépések

* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Központi telepítés – hibaelhárítás](how-to-troubleshoot-deployment.md)
* [Webszolgáltatás biztonságossá tétele az Azure Machine Learning en keresztül a TLS használatával](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett Azure Machine Learning-modell felhasználása](how-to-consume-web-service.md)
* [Az Azure Machine Learning-modellek figyelése az Application Insights segítségével](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelésben lévő modellekről](how-to-enable-data-collection.md)
* [Eseményriasztások és eseményindítók létrehozása modelltelepítésekhez](how-to-use-event-grid.md)

