---
title: Modellek üzembe helyezésének módja és helye
titleSuffix: Azure Machine Learning service
description: 'Ismerje meg, hogyan és hol helyezheti üzembe a Azure Machine Learning szolgáltatási modelljeit, beleértve a következőket: Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge és Field-programozható Gate-tömbök.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/06/2019
ms.custom: seoapril2019
ms.openlocfilehash: 5c0c3ade3fd089a4819b8836b07e249fc32c06e0
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543614"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Az Azure Machine Learning szolgáltatással modellek üzembe helyezése

Megtudhatja, hogyan helyezheti üzembe a gépi tanulási modellt webszolgáltatásként az Azure-felhőben, vagy IoT Edge eszközöket.

A munkafolyamat hasonló, függetlenül attól, hogy [Hol helyezi üzembe](#target) a modellt:

1. Regisztrálja a modellt.
1. Felkészülés az üzembe helyezésre (az eszközök, a használat, a számítási cél meghatározása).
1. A modell üzembe helyezése a számítási célra.
1. Tesztelje az üzembe helyezett modellt, más néven webszolgáltatást.

Az üzembe helyezési munkafolyamatban részt vevő fogalmakkal kapcsolatos további információkért lásd: [modellek kezelése, üzembe helyezése és figyelése Azure Machine learning szolgáltatással](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Machine Learning szolgáltatás munkaterületén. További információ: [Azure Machine learning szolgáltatás munkaterületének létrehozása](how-to-manage-workspace.md).

- A modell. Ha nem rendelkezik betanított modellel, az oktatóanyagban szereplő & függőségi fájlok modelljét is használhatja [](https://aka.ms/azml-deploy-cloud).

- Az [Azure CLI-bővítmény Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), [Azure Machine learning Python SDK](https://aka.ms/aml-sdk)-hoz vagy a [Azure Machine learning Visual Studio Code bővítményhez](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Kapcsolódás a munkaterülethez

A következő kód bemutatja, hogyan csatlakozhat egy Azure Machine Learning szolgáltatási munkaterülethez a helyi fejlesztési környezetbe gyorsítótárazott információk használatával:

**Az SDK használata**

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Az SDK munkaterülethez való kapcsolódásával kapcsolatos további információkért tekintse meg a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace)-t.

**A parancssori felület használata**

A parancssori felület használatakor `-w` a vagy `--workspace-name` a paraméterrel adhatja meg a parancs munkaterületét.

**A VS Code használata**

A VS Code használatakor a munkaterület grafikus felületen van kiválasztva. További információ: [modellek üzembe helyezése és kezelése](how-to-vscode-tools.md#deploy-and-manage-models) a vs Code bővítmény dokumentációjában.

## <a id="registermodel"></a>A modell regisztrálása

A regisztrált modell a modellt alkotó egy vagy több fájl logikai tárolója. Ha például több fájlban tárolt modell van, akkor egyetlen modellként regisztrálhatja őket a munkaterületen. A regisztráció után letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.

> [!TIP]
> Modell regisztrálása esetén a Felhőbeli hely (betanítási Futtatás) vagy egy helyi könyvtár elérési útját adja meg. Ez az elérési út csak a feltöltéshez szükséges fájlok megkeresése a regisztrációs folyamat részeként; nem kell megegyeznie a bejegyzési parancsfájlban használt elérési úttal. További információ: [What is get_model_path](#what-is-get_model_path).

A gépi tanulási modellek regisztrálva vannak a Azure Machine Learning munkaterületen. A modell Azure Machine Learning vagy máshonnan is származhat. Az alábbi példák bemutatják, hogyan regisztrálhat egy modellt:

### <a name="register-a-model-from-an-experiment-run"></a>Modell regisztrálása kísérlet futtatásával

Az ebben a szakaszban szereplő kódrészletek bemutatják, hogyan regisztrálhat egy modellt egy képzési futtatásból:

> [!IMPORTANT]
> Ezek a kódrészletek feltételezik, hogy korábban már elvégezte a betanítást `run` , és hozzáfér az objektumhoz (SDK-példa) vagy a futtatási azonosító értékét (CLI-példa). A modellek betanításával kapcsolatos további információkért lásd: [számítási célok létrehozása és használata a modell](how-to-set-up-training-targets.md)betanításához.

+ **Az SDK használata**

  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  A `model_path` a modell Felhőbeli helyét jelöli. Ebben a példában egyetlen fájl elérési útját használjuk. Ha több fájlt szeretne felvenni a modell-regisztrációba, állítsa `model_path` a fájlokat tartalmazó könyvtárba.

+ **A parancssori felület használata**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  A `--asset-path` a modell Felhőbeli helyét jelöli. Ebben a példában egyetlen fájl elérési útját használjuk. Ha több fájlt szeretne felvenni a modell-regisztrációba, állítsa `--asset-path` a fájlokat tartalmazó könyvtárba.

+ **A VS Code használata**

  A [vs Code](how-to-vscode-tools.md#deploy-and-manage-models) kiterjesztésű modell-fájlokkal vagy-mappákkal regisztrálja a modelleket.

### <a name="register-a-model-from-a-local-file"></a>Modell regisztrálása helyi fájlból

A modellhez a modell **helyi elérési útját** biztosítva regisztrálhat. Egy mappát vagy egyetlen fájlt is megadhat. Ezzel a módszerrel regisztrálhatja Azure Machine Learning szolgáltatással betanított modelleket, majd letöltheti vagy Azure Machine Learningn kívül betanított modelleket is.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **ONNX példa a Python SDK-val:**

    ```python
    import os
    import urllib.request
    from azureml.core import Model
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

  Ha több fájlt szeretne felvenni a modell-regisztrációba, állítsa `model_path` a fájlokat tartalmazó könyvtárba.

+ **A parancssori felület használata**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Ha több fájlt szeretne felvenni a modell-regisztrációba, állítsa `-p` a fájlokat tartalmazó könyvtárba.

**Becsült idő**: Körülbelül 10 másodperc.

További információkért lásd: a dokumentáció a a [Model class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

További információ a Azure Machine Learning szolgáltatáson kívül betanított modellek használatáról: [meglévő modell üzembe helyezése](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Számítási cél kiválasztása

A következő számítási célok vagy számítási erőforrások használhatók a webszolgáltatás központi telepítésének üzemeltetéséhez. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Az üzembe helyezés előkészítése

A modell üzembe helyezéséhez több dolog szükséges:

* Egy __bejegyzési parancsfájl__. Ez a szkript fogadja a kéréseket, a modell használatával szerzi a kérést, és visszaadja az eredményeket.

    > [!IMPORTANT]
    > A bejegyzési parancsfájl a modellre jellemző. meg kell ismernie a bejövő kérelmek adatainak formátumát, a modell által várt adatformátumot, valamint az ügyfeleknek visszaadott adatformátumot.
    >
    > Ha a kérelem adatai olyan formátumban vannak, amely nem használható a modellben, a parancsfájl elfogadható formátumba alakíthatja át. A válasz is át lehet alakítani, mielőtt visszatért az ügyfélhez.

    > [!IMPORTANT]
    > A Azure Machine Learning SDK nem biztosítja a webszolgáltatások vagy IoT Edge központi telepítések elérését az adattárhoz vagy az adatkészletekhez való hozzáféréshez. Ha a központi telepítésen kívül tárolt adatokat szeretné elérni az üzembe helyezett modellel, például egy Azure Storage-fiókban, egyéni kódot kell létrehoznia a megfelelő SDK használatával. Például a Pythonhoz készült [Azure Storage SDK](https://github.com/Azure/azure-storage-python)-t.
    >
    > Egy másik alternatíva, amely a forgatókönyv esetében is működhet, a [Batch-előrejelzések](how-to-run-batch-predictions.md), amelyek a pontozáskor hozzáférést biztosítanak az adattárolóhoz.

* **Függőségek**, például segítő parancsfájlok vagy Python/Conda csomagok, amelyek a belépési parancsfájl vagy modell futtatásához szükségesek

* Az üzembe helyezett modellt futtató számítási cél __telepítési konfigurációja__ . Ez a konfiguráció a modell futtatásához szükséges memória-és CPU-követelményeket ismerteti.

Ezek az entitások egy következtetési __konfigurációba__és egy __központi telepítési konfigurációba__vannak ágyazva. A következtetési konfiguráció a bejegyzési parancsfájlra és más függőségekre hivatkozik. Ezek a konfigurációk programozott módon vannak definiálva az SDK használatakor, és JSON-fájlként, amikor a CLI használatával végzik el a telepítést.

### <a id="script"></a> 1. Adja meg a bejegyzési parancsfájlt & függőségeket

A bejegyzési parancsfájl fogadja az üzembe helyezett webszolgáltatásnak küldött és a modellnek átadott adatforrást. Ezután a modell visszaadja a választ, és visszaadja az ügyfélnek. **A szkript a modellre jellemző**. ismernie kell a modell által várt és visszaadott adatok értékét.

A parancsfájl két olyan függvényt tartalmaz, amelyek betöltik és futtatják a modellt:

* `init()`: Ez a függvény általában egy globális objektumba tölti be a modellt. Ezt a függvényt csak egyszer futtatja a webszolgáltatás Docker-tárolójának indításakor.

* `run(input_data)`: Ez a függvény a modellt használva előre jelez egy értéket a bemeneti adatok alapján. A futtatáshoz tartozó bemenetek és kimenetek általában a JSON-t használják a szerializáláshoz és a deszerializáláshoz. A nyers bináris adatmennyiségeket is használhatja. Átalakíthatja az adatokat a modellbe való küldés előtt, vagy az ügyfélnek való visszatérés előtt.

#### <a name="what-is-get_model_path"></a>Mi az a get_model_path?

Modell regisztrálása esetén meg kell adnia a modellnek a beállításjegyzékben való kezeléséhez használt modell nevét. Ezt a nevet használja a [modellhez. szerezze be a _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) , hogy lekérje a modell fájljának elérési útját a helyi fájlrendszeren. Ha egy mappát vagy fájl-gyűjteményt regisztrál, az API a fájlokat tartalmazó könyvtár elérési útját adja vissza.

A modell regisztrálása esetén a rendszer egy olyan nevet ad neki, amely a modell elhelyezésének helyét adja meg helyileg vagy a szolgáltatás telepítése során.

Az alábbi példa egy (a névvel `sklearn_mnist_model.pkl` `sklearn_mnist`regisztrált) nevű fájl elérési útját adja vissza:

```python
model_path = Model.get_model_path('sklearn_mnist')
```

#### <a name="optional-automatic-schema-generation"></a>Választható Séma automatikus létrehozása

Ha automatikusan szeretne létrehozni egy sémát a webszolgáltatás számára, adja meg a bemeneti és/vagy kimeneti adatokat a konstruktorban egy adott típusú objektumhoz, és a típus és a minta használatával automatikusan létrehozza a sémát. A Azure Machine Learning szolgáltatás ezután létrehoz egy [OpenAPI](https://swagger.io/docs/specification/about/) (hencegő) specifikációt a webszolgáltatás számára az üzembe helyezés során.

Jelenleg a következő típusok támogatottak:

* `pandas`
* `numpy`
* `pyspark`
* Standard Python-objektum

A séma-létrehozás használatához foglalja bele `inference-schema` a csomagot a Conda-környezet fájljába.

##### <a name="example-dependencies-file"></a>Példa a függőségek fájlra

A következő YAML egy példa a Conda függőségeinek fájlra:

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Ha automatikus séma-generálást szeretne használni, a belépési parancsfájlnak importálnia **kell** a `inference-schema` csomagokat.

Adja meg a bemeneti és kimeneti minták formátumait `input_sample` a `output_sample` és a változóban, amelyek a webszolgáltatáshoz tartozó kérések és válaszok formátumait jelölik. Ezeket a mintákat a `run()` függvény bemeneti és kimeneti függvényében használhatja. A scikit – az alábbi példa a séma generálását használja.

##### <a name="example-entry-script"></a>Példa a bejegyzés parancsfájlra

Az alábbi példa bemutatja, hogyan fogadhat és adhat vissza JSON-adattartalomot:

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Az alábbi példa bemutatja, hogyan határozhatja meg a bemeneti adatokat `<key: value>` szótárként a Dataframe használatával. Ez a módszer a központilag telepített webszolgáltatás Power BI való felhasználására használható (További információ[a webszolgáltatás Power BIból](https://docs.microsoft.com/power-bi/service-machine-learning-integration)való felhasználásáról):

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
    # replace model_name with your actual model name, if needed
    model_path = Model.get_model_path('model_name')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data
    "input_name_2": "value2",
    # This is a integer type sample. Use the data type that reflects this column in your data
    "input_name_3": 3
}])

# This is a integer type sample. Use the data type that reflects the expected result
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

További példák a parancsfájlokra:

* Pytorch[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Kerasz[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Bináris adatok

Ha a modell fogad bináris adatot, például egy kép, módosítania kell a `score.py` nyers HTTP-kérelmek fogadásához az üzembe helyezéshez használt fájlt. A nyers adat elfogadásához használja `AMLRequest` az osztályt a bejegyzési parancsfájlban `@rawhttp` , és adja hozzá `run()` a bedekorációt a függvényhez.

Az alábbi példa egy olyan példát `score.py` mutat be, amely a bináris adatmennyiséget fogadja el:

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
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Az `AMLRequest` osztály a `azureml.contrib` névtérben található. A névtérben lévő dolgok gyakran változnak, ahogy dolgozunk a szolgáltatás fejlesztésekor. Ennek megfelelően az ebben a névtérben található bármit előzetes verziónak kell tekinteni, és a Microsoft nem támogatja teljes mértékben.
>
> Ha ezt a helyi fejlesztési környezetben kell tesztelni, akkor a következő paranccsal telepítheti az összetevőket:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Több eredetű erőforrás-megosztás (CORS)

Az eltérő eredetű erőforrás-megosztás lehetővé teszi, hogy egy weblapon lévő erőforrásokat egy másik tartománytól lehessen kérni. A CORS az ügyfél kérelmével elküldött HTTP-fejlécek alapján működik, és a szolgáltatás válaszával tért vissza. A CORS és az érvényes fejlécekkel kapcsolatos további információkért lásd: az [idegen eredetű erőforrások megosztása](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) a wikipedia-ben.

A modell telepítésének a CORS támogatására való konfigurálásához használja `AMLResponse` a osztályt a bejegyzési parancsfájlban. Ez az osztály lehetővé teszi a fejlécek beállítását a válasz objektumon.

A következő példa a bejegyzési parancsfájlból származó válasz `Access-Control-Allow-Origin` fejlécét állítja be:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Az `AMLResponse` osztály a `azureml.contrib` névtérben található. A névtérben lévő dolgok gyakran változnak, ahogy dolgozunk a szolgáltatás fejlesztésekor. Ennek megfelelően az ebben a névtérben található bármit előzetes verziónak kell tekinteni, és a Microsoft nem támogatja teljes mértékben.
>
> Ha ezt a helyi fejlesztési környezetben kell tesztelni, akkor a következő paranccsal telepítheti az összetevőket:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. A InferenceConfig meghatározása

A következtetési konfiguráció azt ismerteti, hogyan konfigurálható a modell az előrejelzések készítéséhez. Ez a konfiguráció nem része a belépési parancsfájlnak; Ez a bejegyzési parancsfájlra hivatkozik, és a telepítéshez szükséges összes erőforrás megkeresésére szolgál. Később, a modell tényleges üzembe helyezése során használják.

Az alábbi példa bemutatja, hogyan hozhat létre egy következtetési konfigurációt. Ez a konfiguráció határozza meg a futtatókörnyezetet, a bejegyzés parancsfájlját és (opcionálisan) a Conda környezeti fájlját:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

További információ: a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) osztály referenciája.

További információ a következtetési konfigurációval rendelkező egyéni Docker-rendszerképek használatáról: [modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>CLI-példa InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

A következő parancs bemutatja, hogyan helyezhet üzembe egy modellt a parancssori felület használatával:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Ebben a példában a konfiguráció a következő elemeket tartalmazza:

* Ehhez a modellhez Python szükséges
* A [beléptetési parancsfájl](#script), amely a központilag telepített szolgáltatásnak küldött webes kérelmek kezelésére szolgál.
* A következtetéshez szükséges Python-csomagokat ismertető Conda-fájl

További információ a következtetési konfigurációval rendelkező egyéni Docker-rendszerképek használatáról: [modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. A telepítési konfiguráció megadása

A telepítés előtt meg kell határoznia a telepítési konfigurációt. __A központi telepítési konfiguráció a webszolgáltatást futtató számítási célra vonatkozik__. Ha például helyileg telepíti a szolgáltatást, meg kell adnia azt a portot, ahol a szolgáltatás fogadja a kérelmeket. A telepítési konfiguráció nem része a belépési parancsfájlnak. A modell és a beléptetési parancsfájl tárolására szolgáló számítási cél jellemzőinek meghatározására szolgál.

Előfordulhat, hogy létre kell hoznia a számítási erőforrást is. Ha például még nem rendelkezik a munkaterülethez társított Azure Kubernetes szolgáltatással.

Az alábbi táblázat az egyes számítási célkitűzések központi telepítési konfigurációjának létrehozásához nyújt példát:

| Számítási célt | Üzembe helyezési konfiguráció – példa |
| ----- | ----- |
| Helyi: | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instance | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Ezen osztályok helyi, ACI és AK-alapú webszolgáltatásokhoz való importálása `azureml.core.webservice`a következő helyekről végezhető el:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

> [!TIP]
> A modell szolgáltatásként való üzembe helyezése előtt érdemes lehet profilt használni az optimális CPU-és memória-követelmények meghatározásához. A modellt az SDK-val vagy a parancssori felülettel is felhasználhatja. További információ: [profil ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-) és az [az ml Model Profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile) Reference.
>
> A modell profilkészítési eredményei `Run` objektumként vannak kibocsátva. További információ: a [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py) osztály referenciája.

## <a name="deploy-to-target"></a>Üzembe helyezés célhelyre

Az üzemelő példány a konfiguráció központi telepítési konfigurációjának használatával helyezi üzembe a modelleket. A telepítési folyamat a számítási céltól függetlenül hasonló. Az AK-ra való üzembe helyezés némileg eltér, mivel meg kell adnia az AK-fürtre mutató hivatkozást.

### <a id="local"></a>Helyi telepítés

A helyileg történő üzembe helyezéshez a Docker-t telepíteni kell a helyi gépre.

#### <a name="using-the-sdk"></a>Az SDK használata

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

További információkért tekintse meg a [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), a [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)és a [webszolgáltatás](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)dokumentációját.

#### <a name="using-the-cli"></a>A parancssori felület használata

A CLI használatával történő üzembe helyezéshez használja a következő parancsot. Cserélje `mymodel:1` le a nevet a regisztrált modell nevére és verziójára:

```azurecli-interactive
az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

További információ: az [ml Model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) Reference.

### <a id="notebookvm"></a>NotebookVM webszolgáltatás (DEVTEST)

Lásd: [modell üzembe helyezése notebook virtuális gépeken](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a>Azure Container Instances (DEVTEST)

Lásd: [Azure Container instances üzembe helyezése](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Azure Kubernetes szolgáltatás (DEVTEST & éles környezetben)

Lásd: [üzembe helyezés az Azure Kubernetes szolgáltatásban](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Webszolgáltatások felhasználása

Minden üzembe helyezett webszolgáltatás REST API biztosít, így különböző programozási nyelveken hozhat létre ügyfélalkalmazások-alkalmazásokat. Ha engedélyezte a kulcs hitelesítését a szolgáltatáshoz, meg kell adnia egy szolgáltatási kulcsot a kérelem fejlécében lévő jogkivonatként.
Ha engedélyezte a jogkivonat-hitelesítést a szolgáltatáshoz, meg kell adnia egy Azure Machine Learning JWT tokent tulajdonosi jogkivonatként a kérelem fejlécében.

> [!TIP]
> A séma JSON-dokumentum a szolgáltatás telepítése után kérhető le. Használja a [swagger_uri tulajdonságot](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) az üzembe helyezett webszolgáltatásból, például `service.swagger_uri`:, hogy az URI-t beolvassa a helyi webszolgáltatás hencegő fájljába.

### <a name="request-response-consumption"></a>Kérelem – válasz felhasználás

Íme egy példa arra, hogyan hívhatja meg a szolgáltatást a Pythonban:
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

További információ: [ügyfélalkalmazások létrehozása a](how-to-consume-web-service.md)webszolgáltatások felhasználásához.

### <a name="web-service-schema-openapi-specification"></a>Webszolgáltatás sémája (OpenAPI-specifikáció)

Ha az automatikus séma létrehozását használta az üzemelő példányhoz, a [swagger_uri tulajdonság](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)használatával lekérheti a szolgáltatás OpenAPI-specifikációjának a címeit. Például: `print(service.swagger_uri)`. A specifikáció lekéréséhez használjon GET-kérést (vagy nyissa meg az URI-t egy böngészőben).

A következő JSON-dokumentum egy példa egy központi telepítéshez létrehozott sémára (OpenAPI-specifikáció):

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for the Azure Machine Learning service myservice",
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

A specifikációval kapcsolatos további információkért tekintse [meg az API-specifikáció megnyitása](https://swagger.io/specification/)című témakört.

Egy olyan segédprogram esetében, amely a specifikációból tud ügyféloldali kódtárakat létrehozni, tekintse meg a következőt: [hencegés-CODEGEN](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a>Batch-következtetés
Azure Machine Learning számítási célokat a Azure Machine Learning szolgáltatás hozza létre és kezeli. A kötegelt előrejelzésekhez Azure Machine Learning folyamatokból is felhasználhatók.

A Batch-következtetések Azure Machine Learning számítási feladatokkal való megismeréséhez olvassa el a [Batch-előrejelzések futtatása](how-to-run-batch-predictions.md) című cikket.

### <a id="iotedge"></a>IoT Edge következtetés
A peremhálózati üzembe helyezésének támogatása előzetes verzióban érhető el. További információkért tekintse meg a [Azure Machine learning üzembe helyezése IoT Edge modulként](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) című cikket.


## <a id="update"></a>Webszolgáltatások frissítése

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuous-model-deployment"></a>A modell folyamatos üzembe helyezése 

Az [Azure DevOps](https://azure.microsoft.com/services/devops/)Machine learning-bővítményével folyamatosan üzembe helyezhet modelleket. Az Azure DevOps Machine Learning-bővítményének használatával aktiválhat egy központi telepítési folyamatot, ha új gépi tanulási modellt regisztrálnak Azure Machine Learning szolgáltatás munkaterületen. 

1. Regisztráljon az [Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)-folyamatokra, amelyek lehetővé teszik az alkalmazás folyamatos integrálását és továbbítását bármilyen platformra vagy felhőre. [Az Azure-folyamatok különböznek a ml-folyamatoktól](concept-ml-pipelines.md#compare). 

1. [Hozzon létre egy Azure DevOps-projektet.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Az [Azure-folyamatok Machine learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) -bővítményének telepítése 

1. A __szolgáltatási kapcsolatok__ használatával állítson be egy egyszerű szolgáltatásnevet a Azure Machine learning szolgáltatási munkaterülethez az összes összetevő eléréséhez. Nyissa meg a projekt beállításait, kattintson a szolgáltatás kapcsolatai elemre, majd válassza a Azure Resource Manager lehetőséget.

    ![nézet – szolgáltatás – kapcsolatok](media/how-to-deploy-and-where/view-service-connection.png) 

1. Adja meg a AzureMLWorkspace a __hatókör szintjén__ , és töltse ki a következő paramétereket.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Ezután a gépi tanulási modell Azure-folyamatokkal való folyamatos üzembe helyezéséhez válassza a __kiadás__lehetőséget a folyamatok területen. Adjon hozzá egy új összetevőt, válassza ki a AzureML Model-összetevőt és a korábbi lépésben létrehozott szolgáltatási kapcsolatokat. Válassza ki a modellt és a verziót a központi telepítés elindításához. 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. Engedélyezze a modell-triggert a modell-összetevőn. Az trigger bekapcsolásával a megadott verzió (azaz a modell legújabb verziója a munkaterületen regisztrálva van, az Azure DevOps kiadási folyamata aktiválva lesz. 

    ![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)

További példákért tekintse meg a következő mintát:

* [https://github.com/Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [https://github.com/Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az üzembe helyezett webszolgáltatáshoz törölheti `service.delete()`.
A regisztrált modell törléséhez használja `model.delete()`.

További információkért tekintse meg a [webservice. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)és a [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)dokumentációját.

## <a name="next-steps"></a>További lépések
* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Biztonságos SSL-lel az Azure Machine Learning-webszolgáltatások](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett gépi Tanulási modell felhasználása](how-to-consume-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)

