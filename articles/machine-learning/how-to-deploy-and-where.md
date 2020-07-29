---
title: Modellek üzembe helyezésének módja és helye
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan és hol helyezheti üzembe a Azure Machine Learning modelleket, beleértve a Azure Container Instances, az Azure Kubernetes Service, a Azure IoT Edge és a Field-programozható Gate-tömböket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: f592e265cafc3e56dc0616e6eeb748c851084c32
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317875"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Modellek üzembe helyezése az Azure Machine Learninggel
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Megtudhatja, hogyan helyezheti üzembe a gépi tanulási modellt webszolgáltatásként az Azure-felhőben, vagy Azure IoT Edge eszközöket.

A munkafolyamat a modell [üzembe helyezésének helyétől](#target) függetlenül is hasonló:

1. A modell regisztrálása.
1. Felkészülés az üzembe helyezésre. (Az eszközök, a használat, és a számítási cél meghatározása.)
1. A modell üzembe helyezése a számítási célon.
1. Tesztelje az üzembe helyezett modellt, más néven webszolgáltatást.

Az üzembe helyezési munkafolyamatban részt vevő fogalmakkal kapcsolatos további információkért lásd: [modellek kezelése, üzembe helyezése és figyelése Azure Machine Learningokkal](concept-model-management-and-deployment.md).

> [!IMPORTANT]
> Javasoljuk, hogy a webszolgáltatásba való üzembe helyezés előtt helyileg végezzen hibakeresést, további információ: [helyi hibakeresés](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally)
>
> Azure Machine Learning- [központi telepítés helyi jegyzetfüzetre című témakört](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local) is használhatja.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

- Egy modell. Ha nem rendelkezik betanított modellel, az [oktatóanyagban](https://aka.ms/azml-deploy-cloud)szereplő modell-és függőségi fájlokat is használhatja.

- Az [Azure CLI-bővítmény a Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), a [pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-hoz vagy a [Visual Studio Code](tutorial-setup-vscode-extension.md)-hoz készült Azure Machine learning.

## <a name="connect-to-your-workspace"></a>Csatlakozás a munkaterülethez

A következő kód bemutatja, hogyan csatlakozhat egy Azure Machine Learning munkaterülethez a helyi fejlesztési környezetbe gyorsítótárazott információk használatával:

+ **Az SDK használata**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Az SDK munkaterülethez való kapcsolódásával kapcsolatos további információkért tekintse meg a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) dokumentációját.

+ **A parancssori felület használata**

   A parancssori felület használatakor a `-w` vagy a `--workspace-name` paraméterrel adhatja meg a parancs munkaterületét.

+ **A Visual Studio Code használata**

   A Visual Studio Code használatakor a munkaterületet grafikus felületen kell kiválasztania. További információ: [modellek üzembe helyezése és kezelése](how-to-manage-resources-vscode.md#endpoints) a Visual Studio Code bővítmény dokumentációjában.

## <a name="register-your-model"></a><a id="registermodel"></a>A modell regisztrálása

A regisztrált modell a modellt alkotó egy vagy több fájl logikai tárolója. Ha például több fájlban tárolt modell van, akkor egyetlen modellként regisztrálhatja őket a munkaterületen. A fájlok regisztrálása után letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.

> [!TIP]
> Modell regisztrálása esetén megadja a Felhőbeli hely (betanítási Futtatás) vagy egy helyi könyvtár elérési útját. Ez az elérési út csak a feltöltéshez szükséges fájlok megkeresése a regisztrációs folyamat részeként. Nem kell megegyeznie a bejegyzési parancsfájlban használt elérési úttal. További információ: [a modell fájljainak megkeresése a belépési parancsfájlban](#load-model-files-in-your-entry-script).

A gépi tanulási modellek regisztrálva vannak a Azure Machine Learning munkaterületen. A modell Azure Machine Learning vagy máshonnan is származhat. Modell regisztrálása esetén a modellre vonatkozó metaadatokat is megadhat. A `tags` modell-regisztrációra alkalmazott és a szótárak használatával szűrheti a modelleket `properties` .

Az alábbi példák bemutatják, hogyan regisztrálhat egy modellt.

### <a name="register-a-model-from-an-experiment-run"></a>Modell regisztrálása kísérlet futtatásával

Az ebben a szakaszban szereplő kódrészletek bemutatják, hogyan regisztrálhat egy modellt egy képzési futtatásból:

> [!IMPORTANT]
> A kódrészletek használatához előzőleg be kell állítania egy tanítási futtatást, és hozzá kell férnie az `Run` objektumhoz (SDK-példa) vagy a futtatási azonosító értékéhez (CLI-példa). A modellek betanításával kapcsolatos további információkért lásd: [számítási célok beállítása a modell betanításához](how-to-set-up-training-targets.md).

+ **Az SDK használata**

  Ha az SDK-t használja a modell betanításához, akkor a modell kiképzésének módjától függően [futtathat egy futtatási](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) objektumot vagy egy [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) -objektumot. Minden objektum használható a kísérlet futtatásával létrehozott modellek regisztrálására.

  + Modell regisztrálása egy `azureml.core.Run` objektumból:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    A `model_path` paraméter a modell Felhőbeli helyét jelöli. Ebben a példában egyetlen fájl elérési útját használjuk. Ha több fájlt szeretne szerepeltetni a modell regisztrációjában, állítsa `model_path` a fájlokat tartalmazó mappa elérési útjára. További információ: [Run. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) dokumentáció.

  + Modell regisztrálása egy `azureml.train.automl.run.AutoMLRun` objektumból:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    Ebben a példában a `metric` és a `iteration` paraméterek nincsenek megadva, ezért a rendszer a legjobb elsődleges metrikával rendelkező iterációt regisztrálja. A rendszer a `model_id` futtatásból visszaadott értéket használja a modell neve helyett.

    További információ: [AutoMLRun. register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) dokumentáció.

+ **A parancssori felület használata**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  A `--asset-path` paraméter a modell Felhőbeli helyét jelöli. Ebben a példában egyetlen fájl elérési útját használjuk. Ha több fájlt szeretne szerepeltetni a modell regisztrációjában, állítsa `--asset-path` a fájlokat tartalmazó mappa elérési útjára.

+ **A Visual Studio Code használata**

  A [Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) bővítmény használatával bármilyen modell-fájl vagy mappa használatával regisztrálhat modelleket.

### <a name="register-a-model-from-a-local-file"></a>Modell regisztrálása helyi fájlból

A modell helyi elérési útjának megadásával regisztrálhat egy modellt. Megadhatja egy mappa vagy egy fájl elérési útját. Ezzel a módszerrel regisztrálhatja Azure Machine Learning, majd letöltheti a betanított modelleket. Ezt a módszert használhatja a Azure Machine Learningon kívül betanított modellek regisztrálására is.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

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

  Ha több fájlt szeretne szerepeltetni a modell regisztrációjában, állítsa `model_path` a fájlokat tartalmazó mappa elérési útjára.

+ **A parancssori felület használata**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Ha több fájlt szeretne szerepeltetni a modell regisztrációjában, állítsa `-p` a fájlokat tartalmazó mappa elérési útjára.

**Becsült idő**: körülbelül 10 másodperc.

További információkért tekintse meg a [modell osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)dokumentációját.

A Azure Machine Learningon kívül betanított modellek használatáról a következő témakörben talál további információt: [meglévő modell üzembe helyezése](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>Egy-és többmodelles végpontok
Az Azure ML egyetlen végpont mögött egyetlen vagy több modell üzembe helyezését támogatja.

A többmodelles végpontok egy megosztott tárolót használnak több modell üzemeltetéséhez. Ez segít csökkenteni a költségeket, javítja a kihasználtságot, és lehetővé teszi, hogy a modulok összevonásával együtt egyesítse a modulokat. Az üzembe helyezési parancsfájlban megadott modellek csatlakoztatása és elérhetővé tétele a kiszolgáló tároló lemezén történik – az igény szerinti memóriába betöltheti őket, a pontszám pedig a pontozási időpontban kért adott modell alapján.

Egy E2E példa, amely bemutatja, hogyan használható több modell egyetlen tárolós végpont mögött, lásd a következő [példát](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model) :

## <a name="prepare-to-deploy"></a>Az üzembe helyezés előkészítése

A modell szolgáltatásként való üzembe helyezéséhez a következő összetevőkre van szükség:

* **Következtetési környezet definiálása**. Ez a környezet beágyazza a modellnek a következtetéshez való futtatásához szükséges függőségeket.
* **Pontozási kód definiálása**. Ez a szkript fogadja a kéréseket, a modell használatával szerzi be a kérelmeket, és visszaadja az eredményeket.
* **Adja meg a következtetési konfigurációt**. A következtetési konfiguráció meghatározza a környezet konfigurációját, a belépési parancsfájlt és a modell szolgáltatásként való futtatásához szükséges egyéb összetevőket.

Ha rendelkezik a szükséges összetevőkkel, megtekintheti azt a szolgáltatást, amely a modell üzembe helyezésének eredményeképpen jön létre a CPU-és memória-követelmények megismerése érdekében.

### <a name="1-define-inference-environment"></a>1. a következtetési környezet meghatározása

A következtetések konfigurálásával megtudhatja, hogyan állíthatja be a modellt tartalmazó webes szolgáltatást. Ezt később, a modell telepítésekor használják.

A következtetések konfigurálásával Azure Machine Learning környezetek határozzák meg az üzemelő példányhoz szükséges szoftver-függőségeket. A környezetek lehetővé teszik a képzéshez és a telepítéshez szükséges szoftver-függőségek létrehozását, kezelését és újrafelhasználását. Létrehozhat egy környezetet egyéni függőségi fájlokból, vagy használhatja a kurátori Azure Machine Learning környezetek egyikét. Az alábbi YAML egy Conda-függőségi fájlra mutat példát. Vegye figyelembe, hogy a azureml alapértelmezett értékeit kell megadnia a (z) >= 1.0.45 lépésszám-függőségként, mert tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat. Ha automatikus séma-generálást szeretne használni, a belépési parancsfájlnak importálnia kell a `inference-schema` csomagokat is.

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
> Ha a függőség a Conda és a PIP (a PyPi) szolgáltatáson keresztül érhető el, a Microsoft a Conda-verzió használatát javasolja, mivel a Conda-csomagok jellemzően olyan előre elkészített bináris fájlokkal rendelkeznek, amelyek megbízhatóbb telepítést tesznek elérhetővé.
>
> További információ: a [Conda és a pip ismertetése](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Ha szeretné megnézni, hogy a függőség elérhető-e a Conda-on keresztül, használja a `conda search <package-name>` parancsot, vagy használja a csomag indexeit a és a értéknél [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

A függőségek fájl használatával létrehozhat egy környezeti objektumot, és mentheti a munkaterületre későbbi használatra:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. pontozási kód definiálása

A bejegyzés parancsfájlja fogadja az üzembe helyezett webszolgáltatásnak küldött adatokat, majd továbbítja azokat a modellnek. Ezután a modell által visszaadott választ visszaküldi az ügyfélnek. *A szkript a modellre jellemző*. Ismernie kell a modell által várt és visszaadott adatok értékét.

A szkript két olyan függvényt tartalmaz, amelyek betöltik és futtatják a modellt:

* `init()`: Ez a függvény általában egy globális objektumba tölti be a modellt. Ez a függvény csak egyszer fut, amikor a webszolgáltatás Docker-tárolója elindult.

* `run(input_data)`: Ez a függvény a modellt használva előre jelez egy értéket a bemeneti adatok alapján. A futtatás be- és kimenetei általában JSON-fájlt használnak a szerializáláshoz és deszerializáláshoz. Nyers bináris adatokkal is dolgozhat. Az adatokat átalakíthatja, mielőtt elküldené őket a modellnek vagy visszaküldené az ügyfélnek.

#### <a name="load-model-files-in-your-entry-script"></a>A modell fájljainak betöltése a bejegyzési parancsfájlba

A következő két módon kereshet modelleket a nevezési parancsfájlban:
* `AZUREML_MODEL_DIR`: Egy környezeti változó, amely a modell helyének elérési útját tartalmazza.
* `Model.get_model_path`: Egy API, amely visszaadja a modell elérési útját a regisztrált modell nevével.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR a szolgáltatás telepítése során létrehozott környezeti változó. Ezt a környezeti változót használhatja a telepített modell (ek) helyének megkereséséhez.

A következő táblázat a telepített modellek számától függően AZUREML_MODEL_DIR értékét ismerteti:

| Üzembe helyezés | Környezeti változó értéke |
| ----- | ----- |
| Egyetlen modell | A modellt tartalmazó mappa elérési útja. |
| Több modell | Az összes modellt tartalmazó mappa elérési útja. A modellek a mappa neve és verziója szerint találhatók ( `$MODEL_NAME/$VERSION` ) |

A modell regisztrálása és üzembe helyezése során a rendszer a modelleket a AZUREML_MODEL_DIR útvonalon helyezi el, és az eredeti fájlnevek megmaradnak.

Ha szeretné beolvasni a parancsfájl elérési útját a bejegyzési parancsfájlban, akkor a környezeti változót a keresett fájl elérési útjával kell egyesíteni.

**Példa egyetlen modellre**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Több modell – példa**

Ebben a forgatókönyvben két modell van regisztrálva a munkaterületen:

* `my_first_model`: Egy fájlt ( `my_first_model.pkl` ) tartalmaz, és csak egy verzió ( `1` ) található.
* `my_second_model`: Egy fájlt ( `my_second_model.pkl` ) tartalmaz, és két verzió; `1` és `2` .

A szolgáltatás üzembe helyezése után mindkét modell a telepítés műveletben van megadva:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

A szolgáltatást futtató Docker-rendszerképben a `AZUREML_MODEL_DIR` környezeti változó tartalmazza azt a könyvtárat, ahol a modellek találhatók.
Ebben a könyvtárban mindegyik modell egy könyvtár elérési útjában található `MODEL_NAME/VERSION` . Ahol a a `MODEL_NAME` regisztrált modell neve, és `VERSION` a modell verziója. A regisztrált modellt alkotó fájlokat a rendszer ezekben a címtárakban tárolja.

Ebben a példában az elérési utak a következőek: `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` és `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` .


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Modell regisztrálása esetén meg kell adnia a modellnek a beállításjegyzékben való kezeléséhez használt modell nevét. Ezt a nevet használja a [Model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) metódussal, hogy lekérje a modell fájljának vagy fájljainak elérési útját a helyi fájlrendszerben. Ha egy mappát vagy fájl-gyűjteményt regisztrál, az API a fájlokat tartalmazó könyvtár elérési útját adja vissza.

Modell regisztrálása esetén a nevet adja meg. A név a modell elhelyezésének helyét adja meg helyileg vagy a szolgáltatás telepítése során.

#### <a name="optional-define-model-web-service-schema"></a>Választható Modell-webszolgáltatás sémájának definiálása

Ha automatikusan szeretne létrehozni egy sémát a webszolgáltatás számára, adja meg a bemeneti és/vagy kimeneti adatokat a konstruktorban egy adott típusú objektumhoz. A rendszer a típust és a mintát használja a séma automatikus létrehozásához. A Azure Machine Learning Ezután létrehoz egy [OpenAPI](https://swagger.io/docs/specification/about/) (hencegő) specifikációt a webszolgáltatás számára az üzembe helyezés során.

Ezek a típusok jelenleg támogatottak:

* `pandas`
* `numpy`
* `pyspark`
* Standard Python-objektum

A séma generálásához a függőségi fájlban adja meg a nyílt forráskódú `inference-schema` csomagot. A csomaggal kapcsolatos további információkért lásd: [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . Adja meg a bemeneti és kimeneti minták formátumait a `input_sample` és a `output_sample` változóban, amelyek a webszolgáltatáshoz tartozó kérések és válaszok formátumait jelölik. Ezeket a mintákat a függvény bemeneti és kimeneti függvényében használhatja `run()` . A következő scikit példa a séma generálását használja.

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

##### <a name="power-bi-compatible-endpoint"></a>Power BI kompatibilis végpont 

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

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample, enforce_shape=False))
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
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Bináris adatértékek](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. a következtetési konfiguráció megadása
    
Az alábbi példa bemutatja egy környezet betöltését a munkaterületről, majd azt a következtetési konfigurációval használva:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

További információ a környezetekről: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md).

További információ a konfigurációval kapcsolatban: [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) Class dokumentáció.

További információ a következtetési konfigurációval rendelkező egyéni Docker-rendszerképek használatáról: [modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md).

#### <a name="cli-example-of-inferenceconfig"></a>CLI-példa InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

A következő parancs bemutatja, hogyan helyezhet üzembe egy modellt a parancssori felület használatával:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Ebben a példában a konfiguráció a következő beállításokat adja meg:

* A modellhez Python szükséges
* A [beléptetési parancsfájl](#script), amely a központilag telepített szolgáltatásnak küldött webes kérelmek kezelésére szolgál.
* A következtetéshez szükséges Python-csomagokat ismertető Conda-fájl

További információ a következtetési konfigurációval rendelkező egyéni Docker-rendszerképek használatáról: [modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md).

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (nem kötelező) profil a modellben az erőforrások kihasználtságának meghatározásához

Miután regisztrálta a modelljét, és előkészítette az üzembe helyezéséhez szükséges egyéb összetevőket, megadhatja, hogy a telepített szolgáltatásnak milyen PROCESSZORral és memóriával kell rendelkeznie. A profilkészítés a modellt futtató szolgáltatást teszteli, és olyan információkat ad vissza, mint például a CPU-használat, a memóriahasználat és a válasz késése. Emellett javaslatot tesz a processzor és a memória számára az erőforrás-használat alapján.

A modell profiljának elvégzéséhez a következőkre lesz szüksége:
* Egy regisztrált modell.
* A beléptetési parancsfájl és a környezeti környezet definíciója alapján megjelenő következtetési konfiguráció.
* Egyoszlopos táblázatos adatkészlet, amelyben minden sor tartalmaz egy minta típusú kérelmeket jelölő karakterláncot.

> [!IMPORTANT]
> Ezen a ponton csak olyan szolgáltatások profilkészítését támogatjuk, amelyek a kérési adatok karakterláncnak számítanak, például: karakterlánc szerializált JSON, szöveg, karakterlánc szerializált rendszerkép stb. Az adatkészlet (string) egyes sorainak tartalmát a rendszer a HTTP-kérelem törzsébe helyezi el, és elküldi a modellnek a pontozást tartalmazó szolgáltatásnak.

Az alábbi példa bemutatja, hogyan hozhat létre egy bemeneti adatkészletet olyan szolgáltatás profiljához, amely a beérkező kérések adatait szerializált JSON-ként kívánja tartalmazni. Ebben az esetben létrehoztunk egy adatkészleten alapuló 100-példányt az azonos kérelem adattartalmából. A valós forgatókönyvekben azt javasoljuk, hogy használjon nagyobb adatkészleteket, amelyek különböző bemeneteket tartalmaznak, különösen akkor, ha a modell Erőforrás-kihasználtsága/viselkedése a bemenettől függ.

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

Ha már rendelkezik a mintavételi adatokat tartalmazó adatkészlettel, hozzon létre egy következtetési konfigurációt. A következtetési konfiguráció a score.py és a környezeti definíción alapul. Az alábbi példa bemutatja, hogyan hozhatja létre a következtetési konfigurációt, és hogyan futtathatja a profilkészítést:

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

A következő parancs bemutatja, hogyan lehet profilt felvenni a parancssori felület használatával:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> A profilkészítés által visszaadott adatok megőrzéséhez használja a modell címkéit vagy tulajdonságait. A címkék vagy tulajdonságok használatával a modell beállításjegyzékében tárolja az adatait. Az alábbi példák azt mutatják be, hogyan adhat hozzá olyan új címkét, amely tartalmazza a `requestedCpu` és a `requestedMemoryInGb` információkat:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Üzembe helyezés célhelyre

Az üzemelő példány a konfiguráció központi telepítési konfigurációjának használatával helyezi üzembe a modelleket. A telepítési folyamat a számítási céltól függetlenül hasonló. Az Azure Kubernetes szolgáltatás (ak) üzembe helyezése némileg eltérő, mert meg kell adnia egy hivatkozást az AK-fürthöz.

### <a name="choose-a-compute-target"></a>Számítási cél kiválasztása

A webszolgáltatás központi telepítésének üzemeltetéséhez a következő számítási célokat vagy számítási erőforrásokat használhatja:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

> [!NOTE]
> * Az ACI csak kisméretű modellek esetében alkalmas, <1 GB méretű. 
> * Javasoljuk, hogy a nagyobb modellek fejlesztéséhez használjon egycsomópontos AK-t.

### <a name="define-your-deployment-configuration"></a>A telepítési konfiguráció megadása

A modell üzembe helyezése előtt meg kell határoznia a telepítési konfigurációt. *A központi telepítési konfiguráció a webszolgáltatást futtató számítási célra vonatkozik.* Ha például helyileg helyez üzembe egy modellt, meg kell adnia azt a portot, ahol a szolgáltatás fogadja a kérelmeket. A telepítési konfiguráció nem része a belépési parancsfájlnak. A rendszer a modell és a beléptetési parancsfájlt futtató számítási cél jellemzőinek meghatározására szolgál.

Előfordulhat, hogy a számítási erőforrást is létre kell hoznia, ha például még nem rendelkezik a munkaterülethez társított Azure Kubernetes Service-(ak-) példánnyal.

Az alábbi táblázat az egyes számítási célkitűzések központi telepítési konfigurációjának létrehozásához nyújt példát:

| Számítási cél | Üzembe helyezési konfiguráció – példa |
| ----- | ----- |
| Helyi | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

A helyi, Azure Container Instances és AK webszolgáltatásokhoz tartozó osztályok a következő helyekről importálhatók `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-tls"></a>Központi telepítések biztonságossá tétele a TLS-vel

További információ a webszolgáltatások telepítésének biztonságossá tételéről: a [TLS engedélyezése és üzembe helyezése](how-to-secure-web-service.md#enable).

### <a name="local-deployment"></a><a id="local"></a>Helyi telepítés

Modell helyi üzembe helyezéséhez a Docker-t telepíteni kell a helyi gépre.

#### <a name="using-the-sdk"></a>Az SDK használata

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

További információkért tekintse meg a [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), a [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)és a [webszolgáltatások](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)dokumentációját.

#### <a name="using-the-cli"></a>A parancssori felület használata

Ha a parancssori felület használatával szeretne üzembe helyezni egy modellt, használja a következő parancsot. Cserélje le a `mymodel:1` nevet a regisztrált modell nevére és verziójára:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

További információ: az [ml Model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) dokumentáció.

### <a name="understanding-service-state"></a>A szolgáltatás állapotának ismertetése

A modell telepítése során előfordulhat, hogy a szolgáltatás állapota megváltozik a teljes üzembe helyezése során.

Az alábbi táblázat a különböző szolgáltatás-állapotokat ismerteti:

| Webszolgáltatás állapota | Leírás | Végső állapot?
| ----- | ----- | ----- |
| Transitioning | A szolgáltatás üzembe helyezési folyamatban van. | Nem |
| Nem kifogástalan | A szolgáltatás telepítve van, de jelenleg nem érhető el.  | Nem |
| Unschedulable | A szolgáltatás jelenleg nem telepíthető az erőforrások hiánya miatt. | Nem |
| Sikertelen | Hiba vagy összeomlás miatt nem sikerült telepíteni a szolgáltatást. | Igen |
| Kifogástalan | A szolgáltatás kifogástalan állapotban van, és a végpont elérhető. | Igen |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Számítási példány webszolgáltatás (dev/test)

Lásd: [modell üzembe helyezése Azure Machine learning számítási példányra](how-to-deploy-local-container-notebook-vm.md).

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Azure Container Instances (dev/test)

Lásd: [Azure Container instances üzembe helyezése](how-to-deploy-azure-container-instance.md).

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Azure Kubernetes szolgáltatás (fejlesztési/tesztelési és éles üzem)

Lásd: [üzembe helyezés az Azure Kubernetes szolgáltatásban](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>A/B tesztelés (vezérelt bevezetés)
További információ: ML- [modellek szabályozott bevezetése](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview) további információkhoz.

## <a name="consume-web-services"></a>Webszolgáltatások felhasználása

Minden üzembe helyezett webszolgáltatás REST-végpontot biztosít, így bármilyen programozási nyelven létrehozhat ügyfélalkalmazások.
Ha engedélyezte a szolgáltatás kulcs-alapú hitelesítését, meg kell adnia egy szolgáltatási kulcsot a kérelem fejlécében lévő jogkivonatként.
Ha engedélyezte a jogkivonat-alapú hitelesítést a szolgáltatáshoz, meg kell adnia egy Azure Machine Learning JSON Web Token (JWT) tulajdonosi jogkivonatként a kérelem fejlécében. 

Az elsődleges különbség az, hogy a **kulcsok statikusak, és manuálisan is újra létrehozhatók**, és a **jogkivonatokat a lejárat után frissíteni**kell. A kulcs alapú hitelesítés az Azure Container instance és az Azure Kubernetes Service szolgáltatásban telepített webszolgáltatások esetében támogatott, és a jogkivonat-alapú hitelesítés **csak** az Azure Kubernetes Service-példányok esetében érhető el. További információért és a kód-példákért tekintse meg az [útmutató](how-to-setup-authentication.md#web-service-authentication) a hitelesítéshez című témakört.

> [!TIP]
> A séma JSON-dokumentum a szolgáltatás telepítése után kérhető le. Használja a központilag telepített webszolgáltatás [swagger_uri tulajdonságát](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) (például) a `service.swagger_uri` helyi webszolgáltatás hencegő fájljához tartozó URI-azonosító lekéréséhez.

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

Ha a központi telepítéshez automatikus sémát használ, a [swagger_uri tulajdonság](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)használatával lekérheti a szolgáltatás OpenAPI-specifikációjának a címeit. (Például: `print(service.swagger_uri)` .) Használja a GET kérelmet, vagy nyissa meg az URI-t egy böngészőben a specifikáció lekéréséhez.

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

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Batch-következtetés
A Azure Machine Learning számítási célokat Azure Machine Learning hozza létre és kezeli. A kötegelt előrejelzésekhez Azure Machine Learning folyamatokból is felhasználhatók.

A Batch-következtetések Azure Machine Learning számítási feladatokkal való áttekintését lásd: [a Batch-előrejelzések futtatása](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>IoT Edge következtetés
A peremhálózati üzembe helyezésének támogatása előzetes verzióban érhető el. További információ: [Azure Machine learning telepítése IoT Edge modulként](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="update-web-services"></a><a id="update"></a>Webszolgáltatások frissítése

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Modellek folyamatos üzembe helyezése

A modellek folyamatos üzembe helyezéséhez használja az [Azure DevOps](https://azure.microsoft.com/services/devops/)Machine learning-bővítményét. Az Azure DevOps Machine Learning bővítményével elindíthat egy központi telepítési folyamatot, amikor egy új gépi tanulási modell van regisztrálva egy Azure Machine Learning munkaterületen.

1. Regisztráljon az [Azure-folyamatokra](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), ami lehetővé teszi az alkalmazás folyamatos integrálását és megvalósítását bármilyen platformon vagy felhőben. (Vegye figyelembe, hogy az Azure-folyamatok nem egyeznek meg [Machine learning folyamatokkal](concept-ml-pipelines.md#compare).)

1. [Hozzon létre egy Azure DevOps-projektet.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Telepítse az [Azure-folyamatok Machine learning-bővítményét](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. A szolgáltatási kapcsolatok szolgáltatással hozzon létre egy egyszerű szolgáltatásnevet a Azure Machine Learning munkaterülethez, hogy hozzáférjen az összetevőkhöz. Nyissa meg a projekt beállításait, válassza a **szolgáltatás kapcsolatai**lehetőséget, majd válassza a **Azure Resource Manager**:

    [![Azure Resource Manager kiválasztása](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. A **hatókör szintje** listán válassza a **AzureMLWorkspace**lehetőséget, majd adja meg a többi értéket:

    ![AzureMLWorkspace kiválasztása](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. A gépi tanulási modell Azure-folyamatokkal való folyamatos üzembe helyezéséhez a folyamatok területen válassza a **kiadás**lehetőséget. Adjon hozzá egy új összetevőt, majd válassza ki a **AzureML-modell** összetevőt és a korábban létrehozott szolgáltatási kapcsolatokat. A telepítés elindításához válassza ki a modellt és a verziót:

    [![AzureML modell kiválasztása](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Engedélyezze a modell-triggert a modell-összetevőn. Ha bekapcsolja a triggert, minden alkalommal, amikor a modell megadott verziója (azaz a legújabb verziója) regisztrálva van a munkaterületen, az Azure DevOps kiadási folyamat aktiválódik.

    [![Modell-trigger engedélyezése](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

További példákért tekintse meg ezeket a mintákat a GitHubon:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Modell letöltése
Ha le szeretné tölteni a modellt a saját végrehajtási környezetében való használatra, ezt a következő SDK/CLI-parancsokkal teheti meg:

SDK
```python
model_path = Model(ws,'mymodel').download()
```

CLI
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>Előnézet Nem kód modell telepítése

A nem kód modellek üzembe helyezése jelenleg előzetes verzióban érhető el, és a következő Machine learning-keretrendszereket támogatja:

### <a name="tensorflow-savedmodel-format"></a>Tensorflow SavedModel formátuma
A Tensorflow-modelleket **SavedModel formátumban** kell regisztrálni, hogy a kód nélküli üzembe helyezéssel működjön.

A SavedModel létrehozásával kapcsolatos információkért tekintse meg [ezt a hivatkozást](https://www.tensorflow.org/guide/saved_model) .

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

A modellek kiértékeléséhez tekintse meg a [webszolgáltatásként üzembe helyezett Azure Machine learning modell felhasználása](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service)című témakört. Számos ONNX-projekt protopuf-fájlokat használ a betanítási és érvényesítési adatmennyiségek tömörítéséhez, ami megnehezíti a szolgáltatás által várt adatformátum megismeretét. Modell fejlesztőként dokumentálja a fejlesztőket:

* Bemeneti formátum (JSON vagy bináris)
* Bemeneti adatok alakja és típusa (például az alakban lévő lebegőpontos tömb [100100, 3])
* Tartományi információk (például egy képnek, a színtérnek, az összetevők sorrendjének, valamint az értékek normalizálása)

Ha a Pytorch-t használja, a [Pytorch-ről a ONNX-re való exportáláskor a rendszer](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) az átalakítással és korlátozásokkal kapcsolatos részleteket tartalmaz. 

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

Megjegyzés: a predict_proba támogató modellek alapértelmezés szerint ezt a metódust fogják használni. Ennek felülbírálásához a következőképpen módosíthatja a POST törzset:
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

Megjegyzés: ezeket a függőségeket az előre elkészített scikit-Learn következtetési tároló tartalmazza:

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
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

Miután létrehozta a csomagot, a használatával lekérheti `package.pull()` a rendszerképet a helyi Docker-környezetbe. A parancs kimenete megjeleníti a rendszerkép nevét. Például: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

A modell letöltése után a `docker images` parancs használatával listázhatja a helyi rendszerképeket:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Ha egy helyi tárolót szeretne elindítani a rendszerkép alapján, a következő parancs használatával indítson el egy nevesített tárolót a rendszerhéjból vagy a parancssorból. Cserélje le az `<imageid>` értéket a parancs által visszaadott rendszerkép-azonosítóra `docker images` .

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Ez a parancs elindítja a nevű rendszerkép legújabb verzióját `myimage` . Leképezi a 6789-es helyi portot a webszolgáltatást figyelő tárolóban lévő portra (5001). Emellett a nevet `mycontainer` a tárolóhoz rendeli, amely megkönnyíti a tároló leállítását. A tároló elindítása után elküldheti a kérelmeket a következőnek: `http://localhost:6789/score` .

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

Ez a kód letölti a rendszerképnek a könyvtárba való felépítéséhez szükséges fájlokat `imagefiles` . A mentett fájlokban található Docker egy Azure Container registryben tárolt alaprendszerképre hivatkozik. Amikor létrehoz egy rendszerképet a helyi Docker-telepítéshez, a-beállításjegyzékben való hitelesítéshez a címnek, a felhasználónevet és a jelszót kell használnia. A következő lépésekkel hozhatja létre a rendszerképet egy helyi Docker-telepítés használatával:

1. Egy rendszerhéjból vagy parancssori munkamenetből a következő paranccsal hitelesítheti a Docker-t az Azure Container Registry használatával. Cserélje `<address>` le `<username>` a, a és a `<password>` értéket a által lekért értékekre `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. A rendszerkép létrehozásához használja a következő parancsot. A helyére írja be annak a `<imagefiles>` könyvtárnak az elérési útját, amelyben `package.save()` a fájlokat mentette.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Ezzel a paranccsal állítható be a rendszerkép neve `myimage` .

A rendszerkép felépítésének ellenőrzéséhez használja az `docker images` parancsot. A képet a következő `myimage` listában kell látni:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Ha új tárolót szeretne elindítani a rendszerkép alapján, használja a következő parancsot:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Ez a parancs elindítja a nevű rendszerkép legújabb verzióját `myimage` . Leképezi a 6789-es helyi portot a webszolgáltatást figyelő tárolóban lévő portra (5001). Emellett a nevet `mycontainer` a tárolóhoz rendeli, amely megkönnyíti a tároló leállítását. A tároló elindítása után elküldheti a kérelmeket a következőnek: `http://localhost:6789/score` .

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

Központilag telepített webszolgáltatás törléséhez használja a következőt: `service.delete()` .
Regisztrált modell törléséhez használja a következőt: `model.delete()` .

További információ: a [webszolgáltatások dokumentációja. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) és [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Speciális bejegyzési parancsfájl készítése

<a id="binary"></a>

### <a name="binary-data"></a>Bináris adatok

Ha a modellben bináris adatok (például rendszerkép) is szerepelnek, akkor módosítania kell a `score.py` központi telepítéshez használt fájlt, hogy fogadja a nyers http-kérelmeket. A nyers adat elfogadásához használja az `AMLRequest` osztályt a bejegyzési parancsfájlban, és adja hozzá a `@rawhttp` bedekorációt a `run()` függvényhez.

Az alábbi példa egy olyan példát mutat be, `score.py` amely a bináris adatmennyiséget fogadja el:

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
> Az `AMLRequest` osztály a `azureml.contrib` névtérben található. A névtérben lévő entitások gyakran változnak, ahogy dolgozunk a szolgáltatás fejlesztésekor. Az ebben a névtérben található bármit olyan előzetes verziónak kell tekinteni, amelyet a Microsoft nem támogat.
>
> Ha ezt a helyi fejlesztési környezetben kell tesztelni, akkor a következő paranccsal telepítheti az összetevőket:
>
> ```shell
> pip install azureml-contrib-services
> ```

Az `AMLRequest` osztály csak a score.py található nyers közzétett adat elérését teszi lehetővé, nincs ügyféloldali összetevő. Egy ügyfélről az adatok a szokásos módon kerülnek közzétételre. Például a következő Python-kód beolvas egy képfájlt, és az adatokat:

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>Több eredetű erőforrás-megosztás (CORS)

Az eltérő eredetű erőforrás-megosztás lehetővé teszi, hogy egy weblapon lévő erőforrásokat egy másik tartománytól lehessen kérni. A CORS az ügyfél kérelmével elküldött HTTP-fejléceken keresztül működik, és a szolgáltatás válaszával tért vissza. További információ a CORS és az érvényes fejlécekről: a wikipedia [több eredetű erőforrás-megosztása](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) .

A modell telepítésének a CORS támogatására való konfigurálásához használja a `AMLResponse` osztályt a bejegyzési parancsfájlban. Ez az osztály lehetővé teszi a fejlécek beállítását a válasz objektumon.

A következő példa a `Access-Control-Allow-Origin` bejegyzési parancsfájlból származó válasz fejlécét állítja be:

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

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Az `AMLResponse` osztály a `azureml.contrib` névtérben található. A névtérben lévő entitások gyakran változnak, ahogy dolgozunk a szolgáltatás fejlesztésekor. Az ebben a névtérben található bármit olyan előzetes verziónak kell tekinteni, amelyet a Microsoft nem támogat.
>
> Ha ezt a helyi fejlesztési környezetben kell tesztelni, akkor a következő paranccsal telepítheti az összetevőket:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning csak a POST és a GET kérelmeket irányítja a pontozási szolgáltatást futtató tárolók számára. Ez hibákhoz vezethet, mert a böngészők a CORS-kérelmekre vonatkozó beállításokat használnak.
> 

## <a name="next-steps"></a>További lépések

* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Webszolgáltatás biztonságossá tétele a TLS használatával Azure Machine Learning](how-to-secure-web-service.md)
* [Azure Machine Learning-modell felhasználása webszolgáltatásként](how-to-consume-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
* [Esemény-riasztások és eseményindítók létrehozása a modell üzembe helyezéséhez](how-to-use-event-grid.md)

