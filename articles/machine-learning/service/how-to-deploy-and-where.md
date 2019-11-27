---
title: Modellek üzembe helyezésének módja és helye
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan és hol helyezheti üzembe a Azure Machine Learning modelleket, beleértve a Azure Container Instances, az Azure Kubernetes Service, a Azure IoT Edge és a Field-programozható Gate-tömböket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 63d2aa5c9e4ec751d9b95ba0d884e6dc17e207bb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276795"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Modellek üzembe helyezése Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Megtudhatja, hogyan helyezheti üzembe a gépi tanulási modellt webszolgáltatásként az Azure-felhőben, vagy Azure IoT Edge eszközöket.

A munkafolyamat a modell [üzembe helyezésének helyétől](#target) függetlenül is hasonló:

1. Regisztrálja a modellt.
1. Felkészülés a telepítésre. (Az eszközök, a használat, a számítási cél meghatározása.)
1. A modell üzembe helyezése a számítási célra.
1. Tesztelje az üzembe helyezett modellt, más néven webszolgáltatást.

Az üzembe helyezési munkafolyamatban részt vevő fogalmakkal kapcsolatos további információkért lásd: [modellek kezelése, üzembe helyezése és figyelése Azure Machine Learningokkal](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

- A modell. Ha nem rendelkezik betanított modellel, az [oktatóanyagban](https://aka.ms/azml-deploy-cloud)szereplő modell-és függőségi fájlokat is használhatja.

- Az [Azure CLI-bővítmény a Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), a [pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-hoz vagy a [Visual Studio Code](how-to-vscode-tools.md)-hoz készült Azure Machine learning.

## <a name="connect-to-your-workspace"></a>Kapcsolódás a munkaterülethez

A következő kód bemutatja, hogyan csatlakozhat egy Azure Machine Learning munkaterülethez a helyi fejlesztési környezetbe gyorsítótárazott információk használatával:

+ **Az SDK használata**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Az SDK munkaterülethez való kapcsolódásával kapcsolatos további információkért tekintse meg a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) dokumentációját.

+ **A parancssori felület használata**

   A CLI használatakor a `-w` vagy a `--workspace-name` paraméterrel adhatja meg a parancs munkaterületét.

+ **A VS Code használata**

   A VS Code használatakor a munkaterületet grafikus felületen választhatja ki. További információ: [modellek üzembe helyezése és kezelése](how-to-vscode-tools.md#deploy-and-manage-models) a vs Code bővítmény dokumentációjában.

## <a id="registermodel"></a>A modell regisztrálása

A regisztrált modell a modellt alkotó egy vagy több fájl logikai tárolója. Ha például több fájlban tárolt modell van, akkor egyetlen modellként regisztrálhatja őket a munkaterületen. A fájlok regisztrálása után letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.

> [!TIP]
> Modell regisztrálása esetén megadja a Felhőbeli hely (betanítási Futtatás) vagy egy helyi könyvtár elérési útját. Ez az elérési út csak a feltöltéshez szükséges fájlok megkeresése a regisztrációs folyamat részeként. Nem kell megegyeznie a bejegyzési parancsfájlban használt elérési úttal. További információ: [a modell fájljainak megkeresése a belépési parancsfájlban](#locate-model-files-in-your-entry-script).

A gépi tanulási modellek regisztrálva vannak a Azure Machine Learning munkaterületen. A modell Azure Machine Learning vagy máshonnan is származhat. Az alábbi példák bemutatják, hogyan regisztrálhat egy modellt.

### <a name="register-a-model-from-an-experiment-run"></a>Modell regisztrálása kísérlet futtatásával

Az ebben a szakaszban szereplő kódrészletek bemutatják, hogyan regisztrálhat egy modellt egy képzési futtatásból:

> [!IMPORTANT]
> A kódrészletek használatához előzőleg be kell állítania egy tanítási futtatást, és hozzá kell férnie a `Run` objektumhoz (SDK-példa) vagy a futtatási azonosító értékéhez (CLI-példa). A modellek betanításával kapcsolatos további információkért lásd: [számítási célok beállítása a modell betanításához](how-to-set-up-training-targets.md).

+ **Az SDK használata**

  Ha az SDK-t használja a modell betanításához, akkor a modell kiképzésének módjától függően [futtathat egy futtatási](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master) objektumot vagy egy [AutoMLRun](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master) -objektumot. Minden objektum használható a kísérlet futtatásával létrehozott modellek regisztrálására.

  + Modell regisztrálása egy `azureml.core.Run` objektumból:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    A `model_path` paraméter a modell Felhőbeli helyét jelöli. Ebben a példában egyetlen fájl elérési útját használjuk. Ha több fájlt szeretne felvenni a modell-regisztrációba, állítsa `model_path` a fájlokat tartalmazó mappa elérési útjára. További információ: [Run. register_model](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none----kwargs-) dokumentáció.

  + Modell regisztrálása egy `azureml.train.automl.run.AutoMLRun` objektumból:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    Ebben a példában a `metric` és `iteration` paraméterek nincsenek megadva, ezért a rendszer a legjobb elsődleges metrikával rendelkező iterációt regisztrálja. A rendszer a futtatásból visszaadott `model_id` értéket használja a modell neve helyett.

    További információ: [AutoMLRun. register_model](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master#register-model-description-none--tags-none--iteration-none--metric-none-) dokumentáció.

+ **A parancssori felület használata**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  A `--asset-path` paraméter a modell Felhőbeli helyét jelöli. Ebben a példában egyetlen fájl elérési útját használjuk. Ha több fájlt szeretne felvenni a modell-regisztrációba, állítsa `--asset-path` a fájlokat tartalmazó mappa elérési útjára.

+ **A VS Code használata**

  A [vs Code](how-to-vscode-tools.md#deploy-and-manage-models) bővítmény használatával bármilyen modell-fájl vagy-mappa használatával regisztrálhat modelleket.

### <a name="register-a-model-from-a-local-file"></a>Modell regisztrálása helyi fájlból

A modell helyi elérési útjának megadásával regisztrálhat egy modellt. Megadhatja egy mappa vagy egy fájl elérési útját. Ezzel a módszerrel regisztrálhatja Azure Machine Learning, majd letöltheti a betanított modelleket. Ezt a módszert használhatja a Azure Machine Learningon kívül betanított modellek regisztrálására is.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **Az SDK és a ONNX használata**

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

  Ha több fájlt szeretne felvenni a modell-regisztrációba, állítsa `model_path` a fájlokat tartalmazó mappa elérési útjára.

+ **A parancssori felület használata**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Ha több fájlt szeretne felvenni a modell-regisztrációba, állítsa `-p` a fájlokat tartalmazó mappa elérési útjára.

**Becsült idő**: körülbelül 10 másodperc.

További információkért tekintse meg a [modell osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)dokumentációját.

A Azure Machine Learningon kívül betanított modellek használatáról a következő témakörben talál további információt: [meglévő modell üzembe helyezése](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Számítási cél kiválasztása

A webszolgáltatás központi telepítésének üzemeltetéséhez a következő számítási célokat vagy számítási erőforrásokat használhatja:

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Az üzembe helyezés előkészítése

A modell üzembe helyezéséhez a következő elemek szükségesek:

* **Egy bejegyzési parancsfájl**. Ez a szkript fogadja a kéréseket, a modell használatával szerzi be a kérelmeket, és visszaadja az eredményeket.

    > [!IMPORTANT]
    > * A bejegyzési parancsfájl a modellre jellemző. Meg kell ismernie a bejövő kérelmek adatainak formátumát, a modell által várt adatformátumot, valamint az ügyfeleknek visszaadott adatformátumot.
    >
    >   Ha a kérelem adatai formátuma nem használható a modellben, akkor a szkript elfogadható formátumba alakíthatja át. Átalakíthatja a választ is, mielőtt visszaadná azt az ügyfélnek.
    >
    > * Az Azure Machine Learning SDK nem biztosít módot a webszolgáltatások vagy IoT Edge üzembe helyezések számára az adattár vagy adatkészletek eléréséhez. Ha a telepített modellnek hozzá kell férnie az üzemelő példányon kívül tárolt adatokat, például egy Azure Storage-fiókban található adatokat, egyéni kódot kell létrehoznia a megfelelő SDK használatával. Például a [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python)-t.
    >
    >   A forgatókönyvnek megfelelő alternatív megoldás a Batch- [Előrejelzés](how-to-run-batch-predictions.md), amely hozzáférést biztosít az adattárakhoz a pontozás során.

* **Függőségek**, például segítő parancsfájlok vagy Python/Conda csomagok, amelyek a belépési parancsfájl vagy modell futtatásához szükségesek.

* Az üzembe helyezett modellt futtató számítási cél **telepítési konfigurációja** . Ez a konfiguráció a modell futtatásához szükséges memória-és CPU-követelményeket ismerteti.

Ezek az elemek egy *következtetési konfigurációba* és egy *központi telepítési konfigurációba*vannak ágyazva. A következtetési konfiguráció a bejegyzési parancsfájlra és más függőségekre hivatkozik. Ezeket a konfigurációkat programozott módon definiálhatja, ha az SDK használatával végzi el az üzembe helyezést. A parancssori felület használatakor a JSON-fájlokban definiálhatja őket.

### <a id="script"></a>1. adja meg a bejegyzési parancsfájlt és a függőségeket

A bejegyzési parancsfájl fogadja az üzembe helyezett webszolgáltatásnak küldött és a modellnek átadott adatforrást. Ezután a modell visszaadja a választ, és visszaadja az ügyfélnek. *A szkript a modellre jellemző*. Ismernie kell a modell által várt és visszaadott adatok értékét.

A parancsfájl két olyan függvényt tartalmaz, amelyek betöltik és futtatják a modellt:

* `init()`: Ez a függvény általában egy globális objektumba tölti be a modellt. Ez a függvény csak egyszer fut, amikor a webszolgáltatás Docker-tárolója elindult.

* `run(input_data)`: Ez a függvény a modellt használva előre jelez egy értéket a bemeneti adatok alapján. A Futtatás bemenetei és kimenetei általában a JSON-t használják a szerializáláshoz és a deszerializáláshoz. A nyers bináris adatmennyiségeket is használhatja. Az adatokat átalakíthatja a modellbe való küldés előtt, vagy az ügyfélnek való visszatérés előtt.

#### <a name="locate-model-files-in-your-entry-script"></a>Adja meg a modell fájljait a belépési parancsfájlban

A következő két módon kereshet modelleket a nevezési parancsfájlban:
* `AZUREML_MODEL_DIR`: egy környezeti változó, amely a modell helyének elérési útját tartalmazza.
* `Model.get_model_path`: egy API, amely visszaadja a modell elérési útját a regisztrált modell neve alapján.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR a szolgáltatás telepítése során létrehozott környezeti változó. Ezt a környezeti változót használhatja a telepített modell (ek) helyének megkereséséhez.

A következő táblázat a telepített modellek számától függően AZUREML_MODEL_DIR értékét ismerteti:

| Környezet | Környezeti változó értéke |
| ----- | ----- |
| Egyetlen modell | A modellt tartalmazó mappa elérési útja. |
| Több modell | Az összes modellt tartalmazó mappa elérési útja. A modellek a mappa neve és verziója szerint találhatók (`$MODEL_NAME/$VERSION`) |

A modellben lévő fájl elérési útjának lekéréséhez a környezeti változót a keresett fájlnévvel kell kombinálni.
A rendszer a regisztráció és az üzembe helyezés során megőrzi a modell fájljainak fájlneveit. 

**Példa egyetlen modellre**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
```

**Több modell – példa**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model/1/sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Modell regisztrálása esetén meg kell adnia a modellnek a beállításjegyzékben való kezeléséhez használt modell nevét. Ezt a nevet használja a [Model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) metódussal, hogy lekérje a modell fájljának vagy fájljainak elérési útját a helyi fájlrendszerben. Ha egy mappát vagy fájl-gyűjteményt regisztrál, az API a fájlokat tartalmazó könyvtár elérési útját adja vissza.

Modell regisztrálása esetén a nevet adja meg. A név a modell elhelyezésének helyét adja meg helyileg vagy a szolgáltatás telepítése során.

> [!IMPORTANT]
> Ha az automatizált gépi tanulást használta a modell betanításához, a modell neveként `model_id` értéket kell használni. Az automatizált gépi tanulással betanított modellek regisztrálásához és üzembe helyezéséhez lásd: az [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features) a githubon.

A következő példa egy `sklearn_mnist_model.pkl` nevű fájl elérési útját adja vissza (amely a (z) `sklearn_mnist`névvel lett regisztrálva):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Választható Séma automatikus létrehozása

Ha automatikusan szeretne létrehozni egy sémát a webszolgáltatás számára, adja meg a bemeneti és/vagy kimeneti adatokat a konstruktorban egy adott típusú objektumhoz. A rendszer a típust és a mintát használja a séma automatikus létrehozásához. A Azure Machine Learning Ezután létrehoz egy [OpenAPI](https://swagger.io/docs/specification/about/) (hencegő) specifikációt a webszolgáltatás számára az üzembe helyezés során.

Ezek a típusok jelenleg támogatottak:

* `pandas`
* `numpy`
* `pyspark`
* Standard Python-objektum

A séma generálásához vegye fel a `inference-schema` csomagot a Conda-környezet fájljába. A csomaggal kapcsolatos további információkért lásd: [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema).

##### <a name="example-dependencies-file"></a>Példa a függőségek fájlra

A következő YAML egy példa a Conda függőségeinek fájlra:

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
    - azureml-defaults
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Ha a függőség a Conda és a PIP (a PyPi) szolgáltatáson keresztül érhető el, a Microsoft a Conda-verzió használatát javasolja, mivel a Conda-csomagok jellemzően olyan előre elkészített bináris fájlokkal rendelkeznek, amelyek megbízhatóbb telepítést tesznek elérhetővé.
>
> További információ: a [Conda és a pip ismertetése](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Ha szeretné megnézni, hogy a függőség elérhető-e a Conda-on keresztül, használja a `conda search <package-name>` parancsot, vagy használja a csomag indexeit [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) és [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo).

Ha automatikus séma-generálást szeretne használni, a bejegyzési parancsfájlnak importálnia kell a `inference-schema` csomagokat.

Adja meg a bemeneti és kimeneti minta formátumait a `input_sample` és `output_sample` változóknál, amelyek a webszolgáltatáshoz tartozó kérések és válaszok formátumait jelölik. Ezeket a mintákat a bemeneti és kimeneti függvényekben a `run()` függvényben használhatja. A következő scikit példa a séma generálását használja.

##### <a name="example-entry-script"></a>Példa a bejegyzés parancsfájlra

Az alábbi példa bemutatja, hogyan fogadhat és adhat vissza JSON-adattartalomot:

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

Az alábbi példa bemutatja, hogyan határozhatja meg a bemeneti adatokat `<key: value>` szótárként egy DataFrame használatával. Ez a módszer a központilag telepített webszolgáltatás Power BI való felhasználására használható. ([További információ a webszolgáltatás Power BIból való](https://docs.microsoft.com/power-bi/service-machine-learning-integration)használatáról.)

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

További példákért tekintse meg a következő parancsfájlokat:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Kerasz](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Bináris adatok

Ha a modellben bináris adatok (például rendszerkép) is szerepelnek, akkor módosítania kell a központi telepítéshez használt `score.py` fájlt, hogy fogadja a nyers HTTP-kérelmeket. A nyers adat elfogadásához használja a `AMLRequest` osztályt a belépési parancsfájlban, és adja hozzá a `@rawhttp` dekoratőr elemet a `run()` függvényhez.

Az alábbi példa egy olyan `score.py` mutat be, amely a bináris adatmennyiséget fogadja el:

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
> A `AMLRequest` osztály a `azureml.contrib` névtérben található. A névtérben lévő entitások gyakran változnak, ahogy dolgozunk a szolgáltatás fejlesztésekor. Az ebben a névtérben található bármit olyan előzetes verziónak kell tekinteni, amelyet a Microsoft nem támogat.
>
> Ha ezt a helyi fejlesztési környezetben kell tesztelni, akkor a következő paranccsal telepítheti az összetevőket:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Több eredetű erőforrás-megosztás (CORS)

Az eltérő eredetű erőforrás-megosztás lehetővé teszi, hogy egy weblapon lévő erőforrásokat egy másik tartománytól lehessen kérni. A CORS az ügyfél kérelmével elküldött HTTP-fejléceken keresztül működik, és a szolgáltatás válaszával tért vissza. További információ a CORS és az érvényes fejlécekről: a wikipedia [több eredetű erőforrás-megosztása](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) .

A modell telepítésének a CORS támogatására való konfigurálásához használja a `AMLResponse` osztályt a belépési parancsfájlban. Ez az osztály lehetővé teszi a fejlécek beállítását a válasz objektumon.

A következő példa a bejegyzési parancsfájl válaszának `Access-Control-Allow-Origin` fejlécét állítja be:

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
> A `AMLResponse` osztály a `azureml.contrib` névtérben található. A névtérben lévő entitások gyakran változnak, ahogy dolgozunk a szolgáltatás fejlesztésekor. Az ebben a névtérben található bármit olyan előzetes verziónak kell tekinteni, amelyet a Microsoft nem támogat.
>
> Ha ezt a helyi fejlesztési környezetben kell tesztelni, akkor a következő paranccsal telepítheti az összetevőket:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. adja meg a InferenceConfig

A következtetési konfiguráció azt ismerteti, hogyan konfigurálható a modell az előrejelzések készítéséhez. Ez a konfiguráció nem része a belépési parancsfájlnak. Ez a bejegyzési parancsfájlra hivatkozik, és a telepítéshez szükséges összes erőforrás megkeresésére szolgál. Ezt később, a modell telepítésekor használják.

A következtetések konfigurálásával Azure Machine Learning környezetek határozzák meg az üzemelő példányhoz szükséges szoftver-függőségeket. A környezetek lehetővé teszik a képzéshez és a telepítéshez szükséges szoftver-függőségek létrehozását, kezelését és újrafelhasználását. Az alábbi példa bemutatja egy környezet betöltését a munkaterületről, majd azt a következtetési konfigurációval használva:

```python
from azureml.core import Environment
from azureml.core.model import InferenceConfig

deploy_env = Environment.get(workspace=ws,name="myenv",version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=deploy_env)
```

További információ a környezetekről: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md).

A függőségeket közvetlenül is meghatározhatja környezet használata nélkül is. Az alábbi példa bemutatja, hogyan hozhat létre olyan következtetési konfigurációt, amely egy Conda-fájlból tölt be szoftver-függőségeket:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

További információt a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) osztály dokumentációjában talál.

További információ a következtetési konfigurációval rendelkező egyéni Docker-rendszerképek használatáról: [modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>CLI-példa InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

A következő parancs bemutatja, hogyan helyezhet üzembe egy modellt a parancssori felület használatával:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Ebben a példában a konfiguráció a következő beállításokat adja meg:

* A modellhez Python szükséges.
* A [beléptetési parancsfájl](#script), amely a központilag telepített szolgáltatásnak küldött webes kérelmek kezelésére szolgál.
* A következtetéshez szükséges Python-csomagokat ismertető Conda-fájl.

További információ a következtetési konfigurációval rendelkező egyéni Docker-rendszerképek használatáról: [modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. a telepítési konfiguráció megadása

A modell üzembe helyezése előtt meg kell határoznia a telepítési konfigurációt. *A központi telepítési konfiguráció a webszolgáltatást futtató számítási célra vonatkozik.* Ha például helyileg helyez üzembe egy modellt, meg kell adnia azt a portot, ahol a szolgáltatás fogadja a kérelmeket. A telepítési konfiguráció nem része a belépési parancsfájlnak. A rendszer a modell és a beléptetési parancsfájlt futtató számítási cél jellemzőinek meghatározására szolgál.

Előfordulhat, hogy a számítási erőforrást is létre kell hoznia, ha például még nem rendelkezik a munkaterülethez társított Azure Kubernetes Service-(ak-) példánnyal.

Az alábbi táblázat az egyes számítási célkitűzések központi telepítési konfigurációjának létrehozásához nyújt példát:

| Számítási célt | Üzembe helyezési konfiguráció – példa |
| ----- | ----- |
| Helyi: | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

A helyi, Azure Container Instances és az AK-alapú webszolgáltatások osztályai a `azureml.core.webservice`ból importálhatók:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

#### <a name="profiling"></a>Profilkészítés

A modell szolgáltatásként való üzembe helyezése előtt érdemes lehet profilt használni az optimális CPU-és memória-követelmények meghatározásához. Használhatja az SDK-t vagy a CLI-t is a modell profiljának megkezdéséhez. Az alábbi példák azt mutatják be, hogyan lehet profilt felvenni az SDK használatával.

> [!IMPORTANT]
> Profilkészítés használatakor a megadott következtetési konfiguráció nem hivatkozhat Azure Machine Learning környezetre. Ehelyett adja meg a szoftver függőségeit a `InferenceConfig` objektum `conda_file` paraméterének használatával.

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(True)
profiling_results = profile.get_results()
print(profiling_results)
```

Ez a kód a következő kimenethez hasonló eredményt jelenít meg:

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

A modell profilkészítési eredményei `Run` objektumként vannak kibocsátva.

További információ a parancssori felületről történő profilkészítésről: [az ml Model Profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

További információ a következő dokumentumokban található:

* [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [profil ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)
* [A konfigurációs fájl sémájának következtetése](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>Üzembe helyezés célhelyre

Az üzemelő példány a konfiguráció központi telepítési konfigurációjának használatával helyezi üzembe a modelleket. A telepítési folyamat a számítási céltól függetlenül hasonló. Az AK-ra való üzembe helyezés némileg eltérő, mert meg kell adnia egy hivatkozást az AK-fürthöz.

### <a name="securing-deployments-with-ssl"></a>Központi telepítések biztonságossá tétele SSL használatával

A webszolgáltatások telepítésének biztonságossá tételéről további információt az [SSL használata webszolgáltatások biztonságossá](how-to-secure-web-service.md#enable)tételéhez című témakörben talál.

### <a id="local"></a>Helyi telepítés

Modell helyi üzembe helyezéséhez a Docker-t telepíteni kell a helyi gépre.

#### <a name="using-the-sdk"></a>Az SDK használata
zzs
```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

További információkért tekintse meg a [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), a [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)és a [webszolgáltatások](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)dokumentációját.

#### <a name="using-the-cli"></a>A parancssori felület használata

Ha a parancssori felület használatával szeretne üzembe helyezni egy modellt, használja a következő parancsot. Cserélje le a `mymodel:1`t a regisztrált modell nevére és verziójára:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

További információ: az [ml Model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) dokumentáció.

### <a id="notebookvm"></a>Notebook VM webszolgáltatás (dev/test)

Lásd: [modell üzembe helyezése Azure Machine learning notebook virtuális gépre](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a>Azure Container Instances (dev/test)

Lásd: [Azure Container instances üzembe helyezése](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Azure Kubernetes szolgáltatás (fejlesztési/tesztelési és éles üzem)

Lásd: [üzembe helyezés az Azure Kubernetes szolgáltatásban](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Webszolgáltatások felhasználása

Minden üzembe helyezett webszolgáltatás REST API biztosít, így különböző programozási nyelveken hozhat létre ügyfélalkalmazások-alkalmazásokat.
Ha engedélyezte a kulcs hitelesítését a szolgáltatáshoz, meg kell adnia egy szolgáltatási kulcsot a kérelem fejlécében lévő jogkivonatként.
Ha engedélyezte a jogkivonat-hitelesítést a szolgáltatáshoz, meg kell adnia egy Azure Machine Learning JWT tokent tulajdonosi jogkivonatként a kérelem fejlécében.

> [!TIP]
> A séma JSON-dokumentum a szolgáltatás telepítése után kérhető le. Használja a központilag telepített webszolgáltatás [swagger_uri tulajdonságát](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) (például `service.swagger_uri`) a helyi webszolgáltatás hencegő fájljához tartozó URI beszerzéséhez.

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

További információ: [ügyfélalkalmazások létrehozása webszolgáltatások felhasználásához](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Webszolgáltatás sémája (OpenAPI-specifikáció)

Ha a központi telepítéshez automatikus sémát használ, a [swagger_uri tulajdonság](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)használatával lekérheti a szolgáltatás OpenAPI-specifikációjának a címeit. (Például `print(service.swagger_uri)`.) Használja a GET kérelmet, vagy nyissa meg az URI-t egy böngészőben a specifikáció lekéréséhez.

A következő JSON-dokumentum egy példa egy központi telepítéshez létrehozott sémára (OpenAPI-specifikáció):

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

További információ: OpenAPI- [specifikáció](https://swagger.io/specification/).

Egy olyan segédprogram esetében, amely a specifikációból tud ügyféloldali kódtárakat létrehozni, tekintse meg a következőt: [hencegés-CODEGEN](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a>Batch-következtetés
A Azure Machine Learning számítási célokat Azure Machine Learning hozza létre és kezeli. A kötegelt előrejelzésekhez Azure Machine Learning folyamatokból is felhasználhatók.

A Batch-következtetések Azure Machine Learning számítási feladatokkal való áttekintését lásd: [a Batch-előrejelzések futtatása](tutorial-pipeline-batch-scoring-classification.md).

### <a id="iotedge"></a>IoT Edge következtetés
A peremhálózati üzembe helyezésének támogatása előzetes verzióban érhető el. További információ: [Azure Machine learning telepítése IoT Edge modulként](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a id="update"></a>Webszolgáltatások frissítése

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Modellek folyamatos üzembe helyezése

A modellek folyamatos üzembe helyezéséhez használja az [Azure DevOps](https://azure.microsoft.com/services/devops/)Machine learning-bővítményét. Az Azure DevOps Machine Learning bővítményével elindíthat egy központi telepítési folyamatot, amikor egy új gépi tanulási modell van regisztrálva egy Azure Machine Learning munkaterületen.

1. Regisztráljon az [Azure-folyamatokra](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), ami lehetővé teszi az alkalmazás folyamatos integrálását és megvalósítását bármilyen platformon vagy felhőben. (Vegye figyelembe, hogy az Azure-folyamatok nem egyeznek meg [Machine learning folyamatokkal](concept-ml-pipelines.md#compare).)

1. [Hozzon létre egy Azure DevOps-projektet.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Telepítse az [Azure-folyamatok Machine learning-bővítményét](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. A szolgáltatási kapcsolatok szolgáltatással hozzon létre egy egyszerű szolgáltatásnevet a Azure Machine Learning munkaterülethez, hogy hozzáférjen az összetevőkhöz. Nyissa meg a projekt beállításait, válassza a **szolgáltatás kapcsolatai**lehetőséget, majd válassza a **Azure Resource Manager**:

    [![válassza ki Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. A **hatókör szintje** listán válassza a **AzureMLWorkspace**lehetőséget, majd adja meg a többi értéket:

    ![AzureMLWorkspace kiválasztása](media/how-to-deploy-and-where/resource-manager-connection.png)

1. A gépi tanulási modell Azure-folyamatokkal való folyamatos üzembe helyezéséhez a folyamatok területen válassza a **kiadás**lehetőséget. Adjon hozzá egy új összetevőt, majd válassza ki a **AzureML-modell** összetevőt és a korábban létrehozott szolgáltatási kapcsolatokat. A telepítés elindításához válassza ki a modellt és a verziót:

    [![AzureML modell kiválasztása](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Engedélyezze a modell-triggert a modell-összetevőn. Ha bekapcsolja a triggert, minden alkalommal, amikor a modell megadott verziója (azaz a legújabb verziója) regisztrálva van a munkaterületen, az Azure DevOps kiadási folyamat aktiválódik.

    [a modell-trigger ![engedélyezése](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

További példákért tekintse meg ezeket a mintákat a GitHubon:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Modell letöltése
Ha le szeretné tölteni a modellt a saját végrehajtási környezetében való használatra, ezt a következő SDK/CLI-parancsokkal teheti meg:

SDK
```python
model_path = Model(ws,'mymodel').download()
```

CLI:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>Előnézet Nem kód modell telepítése

A nem kód modellek üzembe helyezése jelenleg előzetes verzióban érhető el, és a következő Machine learning-keretrendszereket támogatja:

### <a name="tensorflow-savedmodel-format"></a>Tensorflow SavedModel formátuma

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

A ONNX-modell regisztrálása és üzembe helyezése bármely ONNX-következtetési gráf esetében támogatott. Az előfeldolgozási és a utófeldolgozás lépések jelenleg nem támogatottak.

Íme egy példa arra, hogyan regisztrálhatók és telepíthetők egy MNIST ONNX-modell:

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

### <a name="scikit-learn-models"></a>Scikit – modellek megismerése

Az összes beépített scikit-típushoz nem támogatott a programkód-modell üzembe helyezése.

Íme egy példa arra, hogyan regisztrálhat és helyezhet üzembe egy sklearn-modellt további kód nélkül:

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

Megjegyzés: ezek a függőségek az előre elkészített sklearn következtetési tárolóban szerepelnek:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Csomagok modelljei

Bizonyos esetekben előfordulhat, hogy létre kell hoznia egy Docker-rendszerképet a modell üzembe helyezése nélkül (Ha például telepíteni szeretné a [Azure app Service](how-to-deploy-app-service.md)). Vagy előfordulhat, hogy le szeretné tölteni a rendszerképet, és egy helyi Docker-telepítésre kell futtatnia. Előfordulhat, hogy még le szeretné tölteni a rendszerkép létrehozásához használt fájlokat, megvizsgálhatja őket, módosíthatja azokat, és manuálisan is felépítheti a rendszerképet.

A modell csomagolása lehetővé teszi ezeket a dolgokat. Minden olyan eszközt becsomagol, amely a modell webszolgáltatásként való üzemeltetéséhez szükséges, és lehetővé teszi egy teljesen felépített Docker-rendszerkép vagy a létrehozásához szükséges fájlok letöltését. A modell csomagolásának két módja van:

**Csomagolt modell letöltése:** Töltsön le egy Docker-rendszerképet, amely tartalmazza a modellt és más, a webszolgáltatásként való üzemeltetéséhez szükséges fájlokat.

**Docker létrehozása:** A Docker-rendszerkép létrehozásához szükséges Docker, modell, bejegyzési parancsfájl és egyéb eszközök letöltése. Ezután megvizsgálhatja a fájlokat, vagy módosíthatja a módosításokat a rendszerkép helyi létrehozása előtt.

Mindkét csomag használható helyi Docker-rendszerkép lekérésére.

> [!TIP]
> A csomagok létrehozása hasonló a modellek üzembe helyezéséhez. Egy regisztrált modellt és egy következtetési konfigurációt használ.

> [!IMPORTANT]
> Egy teljesen felépített rendszerkép letöltéséhez vagy a rendszerkép helyi létrehozásához telepítenie kell a [Docker](https://www.docker.com) -t a fejlesztői környezetbe.

### <a name="download-a-packaged-model"></a>Csomagolt modell letöltése

Az alábbi példa létrehoz egy rendszerképet, amely regisztrálva van az Azure Container registryben a munkaterülethez:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

A csomag létrehozása után a `package.pull()` segítségével lekérheti a rendszerképet a helyi Docker-környezetbe. A parancs kimenete megjeleníti a rendszerkép nevét. Például: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

A modell letöltése után a `docker images` parancs használatával listázhatja a helyi rendszerképeket:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Ha egy helyi tárolót szeretne elindítani a rendszerkép alapján, a következő parancs használatával indítson el egy nevesített tárolót a rendszerhéjból vagy a parancssorból. Cserélje le a `<imageid>` értéket a `docker images` parancs által visszaadott rendszerkép-AZONOSÍTÓra.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Ez a parancs elindítja a `myimage`nevű rendszerkép legújabb verzióját. Leképezi a 6789-es helyi portot a webszolgáltatást figyelő tárolóban lévő portra (5001). Emellett a tárolóhoz `mycontainer` nevet rendeli hozzá, amely megkönnyíti a tároló leállítását. A tároló elindítása után elküldheti a kérelmeket `http://localhost:6789/score`.

### <a name="generate-a-dockerfile-and-dependencies"></a>Docker és függőségek létrehozása

Az alábbi példa bemutatja, hogyan töltheti le a rendszerképek helyi létrehozásához szükséges Docker, modellt és egyéb eszközöket. A `generate_dockerfile=True` paraméter azt jelzi, hogy a fájlokat nem teljesen felépített képként szeretné megadni.

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

Ez a kód letölti a rendszerképek `imagefiles` könyvtárba való felépítéséhez szükséges fájlokat. A mentett fájlokban található Docker egy Azure Container registryben tárolt alaprendszerképre hivatkozik. Amikor létrehoz egy rendszerképet a helyi Docker-telepítéshez, a-beállításjegyzékben való hitelesítéshez a címnek, a felhasználónevet és a jelszót kell használnia. A következő lépésekkel hozhatja létre a rendszerképet egy helyi Docker-telepítés használatával:

1. Egy rendszerhéjból vagy parancssori munkamenetből a következő paranccsal hitelesítheti a Docker-t az Azure Container Registry használatával. Cserélje le a `<address>`, `<username>`és `<password>` értéket a `package.get_container_registry()`által beolvasott értékekre.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. A rendszerkép létrehozásához használja a következő parancsot. A `<imagefiles>` helyére írja be annak a könyvtárnak az elérési útját, ahol a `package.save()` mentette a fájlokat.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Ez a parancs a rendszerkép nevét állítja be `myimage`.

A rendszerkép felépítésének ellenőrzéséhez használja a `docker images` parancsot. A listában a `myimage` rendszerkép jelenik meg:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Ha új tárolót szeretne elindítani a rendszerkép alapján, használja a következő parancsot:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Ez a parancs elindítja a `myimage`nevű rendszerkép legújabb verzióját. Leképezi a 6789-es helyi portot a webszolgáltatást figyelő tárolóban lévő portra (5001). Emellett a tárolóhoz `mycontainer` nevet rendeli hozzá, amely megkönnyíti a tároló leállítását. A tároló elindítása után elküldheti a kérelmeket `http://localhost:6789/score`.

### <a name="example-client-to-test-the-local-container"></a>Példa az ügyfélre a helyi tároló teszteléséhez

A következő kód egy olyan Python-ügyfélre mutat példát, amely a tárolóval használható:

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

Más programozási nyelveken található ügyfelek esetében lásd: [webszolgáltatásként üzembe helyezett modellek felhasználása](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>A Docker-tároló leállítása

A tároló leállításához használja a következő parancsot egy másik rendszerhéjból vagy parancssorból:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Központilag telepített webszolgáltatás törléséhez használja a `service.delete()`.
A regisztrált modellek törléséhez használja a `model.delete()`.

További információ: a [webszolgáltatások dokumentációja. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) és [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>További lépések

* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Biztonságos Azure Machine Learning webszolgáltatások SSL használatával](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett Azure Machine Learning-modell felhasználása](how-to-consume-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)

