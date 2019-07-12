---
title: Hogyan és hol érdemes a modellek üzembe helyezése
titleSuffix: Azure Machine Learning service
description: 'Ismerje meg, hogyan és hol, beleértve az Azure Machine Learning szolgáltatás modellek üzembe helyezése: Az Azure Container Instances, az Azure Kubernetes Service, Azure IoT Edge és Field-programmable gate arrays.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.custom: seoapril2019
ms.openlocfilehash: fb23e61142a639420d74c08e5a9a41324acab18b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706285"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Az Azure Machine Learning szolgáltatással modellek üzembe helyezése

Ismerje meg, hogyan helyezhet üzembe a gépi tanulási modellt webszolgáltatásként, amely az Azure-felhőben, vagy az IoT Edge-eszközökön. 

A munkafolyamat hasonlít, függetlenül attól, hogy [, amelyen központi telepítését](#target) a modell:

1. Regisztrálja a modellt.
1. Az üzembe helyezés előkészítése (adja meg, eszközök, használati, számítási célt)
1. A modell rendszerbe állítása a számítási célnak.
1. Az üzembe helyezett modell, más néven a webszolgáltatás teszteléséhez.

Az üzembe helyezést megvalósító munkafolyamat a fogalmakat további információkért lásd: [kezelése, telepítheti és figyelheti a modellek az Azure Machine Learning szolgáltatás](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Előfeltételek

- A modell. Ha nem rendelkezik a betanított modell, a modell használható & függőségi fájlokról megadott [ebben az oktatóanyagban](https://aka.ms/azml-deploy-cloud).

- A [Machine Learning szolgáltatás az Azure CLI-bővítmény](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk), vagy a [Azure Machine Learning Visual Studio Code-bővítmény](how-to-vscode-tools.md).

## <a id="registermodel"></a> Regisztrálja a modellt

Regisztrált modell logikai tárolója, amely a modell alkotó egy vagy több fájlt. Például ha rendelkezik olyan modell, amely több fájlok tárolják, regisztrálhatja azokat egyetlen modellként a munkaterületen. A regisztrációt követően is, majd töltse le vagy a regisztrált modell üzembe helyezése és fogadására regisztrált összes fájlt.

Machine learning-modellek az Azure Machine Learning-munkaterület van regisztrálva. A modell Azure Machine Learning származhatnak, vagy valahol máshol származhatnak. Az alábbi példák bemutatják, hogyan lehet regisztrálni egy modellt a fájlból:

### <a name="register-a-model-from-an-experiment-run"></a>Regisztrálja a modellt egy kísérlet futtatása

+ **Az SDK-val Scikit további példa**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > A modell regisztrálását fel több fájlt, állítsa `model_path` fájlt tartalmazó könyvtárba.

+ **A parancssori felületről**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```

  > [!TIP]
  > A modell regisztrálását fel több fájlt, állítsa `--asset-path` fájlt tartalmazó könyvtárba.

+ **A VS Code használatával**

  Modell-fájlokat vagy mappákat a használatával regisztrálja a [VS Code](how-to-vscode-tools.md#deploy-and-manage-models) bővítmény.

### <a name="register-an-externally-created-model"></a>Regisztrálja a külsőleg létrehozott modellt

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

Regisztrálhat egy külsőleg létrehozott modell azáltal, hogy egy **helyi elérési út** a modellbe. Megadhat egy mappát vagy egyetlen fájlt.

+ **A Python SDK-val ONNX-példa:**
  ```python
  onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
  urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
  !tar xvzf mnist.tar.gz
  
  model = Model.register(workspace = ws,
                         model_path ="mnist/model.onnx",
                         model_name = "onnx_mnist",
                         tags = {"onnx": "demo"},
                         description = "MNIST image classification CNN from ONNX Model Zoo",)
  ```

  > [!TIP]
  > A modell regisztrálását fel több fájlt, állítsa `model_path` fájlt tartalmazó könyvtárba.

+ **A parancssori felületről**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > A modell regisztrálását fel több fájlt, állítsa `-p` fájlt tartalmazó könyvtárba.

**Becsült időtartam**: Körülbelül 10 másodperc.

További információkért lásd: a dokumentáció a a [Model class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

További információk a modellek betanított kívül az Azure Machine Learning szolgáltatásban, lásd: [hogyan helyezhet üzembe egy meglévő modell](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Válasszon egy számítási célnak

A következő számítási céljainak, vagy a számítási erőforrásokat, a webszolgáltatás üzembe üzemeltetéséhez használható. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Az üzembe helyezés előkészítése

Webszolgáltatásként üzembe helyezéséhez létre kell hoznia egy következtetésekhez configuration (`InferenceConfig`) és a egy központi telepítés konfigurálása. Következtetésekhez vagy a modell pontozása, nem a fázis, az üzembe helyezett modell előrejelzési leggyakrabban a termelési adatok szolgál. A következtetésekhez config adja meg a parancsfájlok és a modell kiszolgálására szükséges függőségeket. A deployment config részletesen szolgálnak ki a modell a számítási célnak adja meg.


### <a id="script"></a> 1. A bejegyzés parancsfájl & függőségek definiálása

A bejegyzés parancsfájl egy már üzembe helyezett webszolgáltatás elküldött adatokat fogad, és átadja azokat a modellbe. Ezután a modell által visszaadott válasz vesz igénybe, és adja vissza, amely az ügyfél számára. **A parancsfájl csak a modell**; ismernie kell az adatokat, amelyek a modellt vár, és adja vissza.

A szkript két függvényt, amely betölteni, és futtassa a modell tartalmazza:

* `init()`: Általában ez a függvény a modellben tölt be egy globális objektum. Ez a függvény fut, csak egyszer, amikor a Docker-tárolót, a webszolgáltatás elindult.

* `run(input_data)`: Ez a függvény egy értéket a bemeneti adatok alapján előre jelezni a modellt használ. Bemenetek és kimenetek a futtató szerializálást és deszerializálás általában használni JSON. Nyers bináris adatok is együttműködik. A modellhez való elküldése előtt, vagy az ügyfél való visszatérés előtt alakíthatja át az adatokat.

#### <a name="what-is-getmodelpath"></a>Mit jelent a get_model_path?

Amikor regisztrál egy modellt, adja meg a beállításjegyzékben a modell kezelésére használt modell nevét. Ezt a nevet használja a [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) elérési útját a helyi fájlrendszerben modell fájl(ok) lekéréséhez. Ha regisztrál egy mappa vagy fájl gyűjteménye, az API-t az elérési utat a könyvtárba, amely tartalmazza azokat a fájlokat adja vissza.

Amikor regisztrál egy modellt, akkor adjon meg egy nevet, amely felel meg, ahol a modell kerül, helyileg vagy a szolgáltatás üzembe helyezése során.

Az alábbi példában adja vissza egy elérési utat az egyetlen fájl nevű `sklearn_mnist_model.pkl` (amely regisztrálva lett az a név `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
``` 

#### <a name="optional-automatic-swagger-schema-generation"></a>(Nem kötelező) A Swagger-séma automatikus létrehozása

Automatikusan hozzon létre egy sémát a webszolgáltatás számára, a bemeneti minta és/vagy a kimenetet a konstruktort az egyik a meghatározott típusú objektumot kap, és a típus és a minta segítségével automatikusan létre kell hozni a sémát. Az Azure Machine Learning szolgáltatás majd létrehoz egy [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) specifikáció a webszolgáltatás üzembe helyezése során.

Jelenleg a következő típusok támogatottak:

* `pandas`
* `numpy`
* `pyspark`
* Standard Python-objektum

Séma létrehozása használatához közé tartozik a `inference-schema` csomagot a conda-környezet fájlban. Az alábbi példában `[numpy-support]` óta a bejegyzés parancsfájl használ egy numpy paraméter típusa: 

#### <a name="example-dependencies-file"></a>Függőségek példafájl
A következő yaml-kódot, amelyek az egy Conda-függőségeket fájl következtetésekhez.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Ha szeretné használni az automatikus séma létrehozása, a bejegyzés parancsfájl **kell** importálja a `inference-schema` csomagokat. 

A bemeneti és kimeneti formátumok minta meghatároznia a `input_sample` és `output_sample` változókat, amelyek tartalmazzák a webszolgáltatás a kérések és válaszok formátumok. Ezeket a mintákat használják a bemeneti és kimeneti függvény decorator a `run()` függvény. A scikit-ismerje meg, az alábbi példában séma létrehozása.

> [!TIP]
> A szolgáltatás telepítéséhez használja a `swagger_uri` séma a JSON-dokumentum beolvasására szolgáló tulajdonság.

#### <a name="example-entry-script"></a>Példaszkript bejegyzés

Az alábbi példa bemutatja, hogyan fogadja el, és JSON-adatokat adja vissza:

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

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
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

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>Példa parancsfájl szótár bemenettel (támogatási használat a Power bi-BÓL)

A következő példa bemutatja, hogyan adhat meg a bemeneti adatokat < kulcs: érték > szótár, Dataframe használatával. Ez a módszer a a Power bi-ban üzembe helyezett webszolgáltatás támogatott ([további információ a Power BI-ból a webszolgáltatás használata](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

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
    model_path = Model.get_model_path('model_name')   # replace model_name with your actual model name, if needed
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = pd.DataFrame(data=[{
              "input_name_1": 5.1,         # This is a decimal type sample. Use the data type that reflects this column in your data
              "input_name_2": "value2",    # This is a string type sample. Use the data type that reflects this column in your data
              "input_name_3": 3            # This is a integer type sample. Use the data type that reflects this column in your data
            }])

output_sample = np.array([0])              # This is a integer type sample. Use the data type that reflects the expected result

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
További példa parancsprogramokat tekintse meg az alábbi példák:

* A Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* Tensorflow-hoz: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* Bináris adatok pontozásához: [A webszolgáltatás használata](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. A InferenceConfig definiálása

A következtetésekhez konfigurációs ismerteti, hogyan konfigurálhatja a modellt, hogy előrejelzéseket végezzen. Az alábbi példa bemutatja, hogyan hozhat létre egy következtetésekhez konfigurációt. Ez a konfiguráció a futtatókörnyezet, a bejegyzés parancsfájl és (opcionálisan) a conda-környezet fájl határozza meg:

```python
inference_config = InferenceConfig(runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

További információkért lásd: a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) referencia osztály.

Az egyéni Docker-rendszerkép használata következtetésekhez konfigurációs információkért lásd: [modell üzembe helyezése egy egyéni Docker-rendszerkép használatával hogyan](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig a CLI-példa

A következő JSON-dokumentum következtetésekhez konfiguráció például a machine learning-CLI való használatra:

```JSON
{
   "entryScript": "x/y/score.py",
   "runtime": "python",
   "condaFile": "env/myenv.yml",
   "sourceDirectory":"C:/abc",
}
```

Ebben a fájlban a következő entitásokat érvényesek:

* __entryScript__: A lemezkép futtatásához szükséges kódot tartalmazó helyi fájl elérési útja.
* __futásidejű__: Melyik futásidejű használandó kép. Aktuális támogatott futtatókörnyezet a következők: "a spark-py" és "python".
* __condaFile__ (nem kötelező): A kép használandó conda környezet definícióját tartalmazó helyi fájl elérési útja.
* __extraDockerFileSteps__ (nem kötelező): Kép beállítása során futtatandó további Docker lépéseket tartalmazó helyi fájl elérési útja.
* __sourceDirectory__ (nem kötelező): Elérési út mappákba, amely tartalmazza az összes fájl a lemezkép létrehozásához.
* __enableGpu__ (nem kötelező): E engedélyezése GPU támogatja a képen. A GPU-lemezképet kell használni a Microsoft Azure-szolgáltatásokra például az Azure Container Instances, az Azure Machine Learning COMPUTE számítási, Azure Virtual Machines és Azure Kubernetes Service-ben. Alapértelmezett érték: False.
* __baseImage__ (nem kötelező): Egyéni kép kiindulási lemezképként szolgál. Ha nincs alaplemezkép van megadva, majd az alaprendszerképet használható futásidejű paraméter megadott ki-alapú.
* __baseImageRegistry__ (nem kötelező): Regisztrációs adatbázisba, amely tartalmazza az alap rendszerképet.
* __cudaVersion__ (nem kötelező): CUDA rendszerképeket, amelyeket a GPU-támogatásra van szüksége a telepítendő verzióját. A GPU-lemezképet kell használni a Microsoft Azure-szolgáltatásokra például az Azure Container Instances, az Azure Machine Learning COMPUTE számítási, Azure Virtual Machines és Azure Kubernetes Service-ben. Támogatott verziók a következők: 9.0, 9.1 és 10.0-s verzióját. Ha "enable_gpu" van beállítva, az alapértelmezett "9.1".

Ezek az entitások leképezése paramétereit a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) osztály.

Ezekkel a következő parancs a modell üzembe helyezése a parancssori felület használatával mutatja be:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Ebben a példában a konfiguráció a következő elemeket tartalmazza:

* Következtetésekhez szükséges eszközöket tartalmazó könyvtár
* Hogy ez a modell futtatásához szükséges Python
* A [bejegyzés parancsfájl](#script), amely a telepített szolgáltatásnak küldött webes kérések kezelésére szolgál
* A conda-fájlt, amely leírja a következtetésekhez szükséges Python-csomagok

Az egyéni Docker-rendszerkép használata következtetésekhez konfigurációs információkért lásd: [modell üzembe helyezése egy egyéni Docker-rendszerkép használatával hogyan](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Az üzembe helyezési konfiguráció definiálása

Az üzembe helyezése előtt meg kell határoznia a telepítési konfigurációt. A központi telepítés konfigurálása csak a számítási célnak, amely a web service fogja futtatni. Ha például helyileg telepítésekor meg kell adnia a port, ahol a szolgáltatás kérelmeket fogadó.

Szükség lehet a számítási erőforrás létrehozásához. Például ha még nem teszi az Azure Kubernetes Service társítva van a munkaterülethez.

Az alábbi táblázat mutatja be, az egyes számítási célnak üzembe helyezési konfiguráció létrehozása:

| Számítási célt | Központi telepítés konfigurációs példája |
| ----- | ----- |
| Helyi: | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instance | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

A következő szakaszok bemutatják, hogyan hozhat létre a telepítési konfigurációt, és, amellyel a webszolgáltatás üzembe helyezése.

### <a name="optional-profile-your-model"></a>Nem kötelező: A modell kiértékelése
A modell szolgáltatás a telepítés előtt is készíthet profilt, azt határozza meg optimális CPU és memória-követelmények az SDK-t vagy a parancssori felület használatával.  Modell profilkészítési eredmények többszöröseként vannak egy `Run` objektum. A teljes körű információkat [a modell profil séma megtalálhatók az API-dokumentáció](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)

További információ a [hogyan profil a modell az SDK-val](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)

## <a name="deploy-to-target"></a>Tároló üzembe helyezése

### <a id="local"></a> Helyi üzembe helyezés

Helyi üzembe helyezéséhez rendelkeznie kell **telepített Docker** a helyi gépen.

+ **Az SDK-val**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **A parancssori felületről**

    A parancssori felület használatával történő központi telepítéséhez használja a következő parancsot. Cserélje le `mymodel:1` nevére, illetve a regisztrált modell verziója:

  ```azurecli-interactive
  az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    A bejegyzések a `deploymentconfig.json` paramétereit a dokumentumtérkép [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). A következő táblázat ismerteti az entitások a JSON-dokumentum és a metódus paramétereinek közötti:

    | JSON-entitás | Parametr Metody | Leírás |
    | ----- | ----- | ----- |
    | `computeType` | NA | A számítási cél. Helyi, az értéknek kell lennie `local`. |
    | `port` | `port` | A helyi port, amelyen a HTTP-végpontot a szolgáltatás elérhetővé. |

    A következő JSON-ja üzembe helyezési konfiguráció például a CLI-vel használható:

    ```json
    {
        "computeType": "local",
        "port": 32267
    }
    ```

### <a id="aci"></a> Az Azure Container Instances (DEVTEST)

A modellek üzembe helyezéséhez a egy webszolgáltatás, ha egy vagy több, a következő feltételek használata Azure Container Instances szolgáltatásban teljesül:
- Gyors üzembe helyezése és a modell érvényesítése kell.
- A tesztelt egy olyan modell, fejlesztés alatt áll. 

Kvóta és régióban rendelkezésre állás az ACI, olvassa el a [kvóták és régiók rendelkezésre állása az Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) cikk.

+ **Az SDK-val**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **A parancssori felületről**

    A parancssori felület használatával történő központi telepítéséhez használja a következő parancsot. Cserélje le `mymodel:1` nevére, illetve a regisztrált modell verziója. Cserélje le `myservice` biztosíthat a szolgáltatás nevét:

    ```azurecli-interactive
    az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
    ```

    A bejegyzések a `deploymentconfig.json` paramétereit a dokumentumtérkép [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). A következő táblázat ismerteti az entitások a JSON-dokumentum és a metódus paramétereinek közötti:

    | JSON-entitás | Parametr Metody | Leírás |
    | ----- | ----- | ----- |
    | `computeType` | NA | A számítási cél. Az aci Szolgáltatásban, az értéknek kell lennie `ACI`. |
    | `containerResourceRequirements` | NA | A Processzor és a tároló számára kiosztott memória konfigurációs elemeket tartalmaz. |
    | &emsp;&emsp;`cpu` | `cpu_cores` | A webszolgáltatás lefoglalni a Processzormagok száma. Alapértelmezés szerint, `0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | Memória (GB-ban) a webszolgáltatás lefoglalni. Alapértelmezés szerint `0.5` |
    | `location` | `location` | Az Azure-régió, a webszolgáltatás üzembe helyezéséhez. Ha nincs megadva a munkaterületen, a helyet használja. További információt az elérhető régiók itt található: [ACI-régiók](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
    | `authEnabled` | `auth_enabled` | E hitelesítés engedélyezése erre a webszolgáltatásra. Alapértelmezett érték: False |
    | `sslEnabled` | `ssl_enabled` | Kell-e a webszolgáltatás SSL engedélyezése. Alapértelmezett érték: False. |
    | `appInsightsEnabled` | `enable_app_insights` | E AppInsights engedélyezése erre a webszolgáltatásra. Alapértelmezett érték: False |
    | `sslCertificate` | `ssl_cert_pem_file` | A tanúsítványfájl szükséges, ha SSL engedélyezve van |
    | `sslKey` | `ssl_key_pem_file` | A kulcs fájlját, ha SSL engedélyezve van szükség |
    | `cname` | `ssl_cname` | A CNAME rekordot, ha SSL engedélyezve van |
    | `dnsNameLabel` | `dns_name_label` | A dns-névcímke a pontozási végpontjához. Ha nem ad meg egy egyedi dns-névcímke generál a pontozási végpontjához. |

    A következő JSON-ja üzembe helyezési konfiguráció például a CLI-vel használható:

    ```json
    {
        "computeType": "aci",
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        },
        "authEnabled": true,
        "sslEnabled": false,
        "appInsightsEnabled": false
    }
    ```

+ **A VS Code használatával**

  A [VS Code használatával modellek üzembe helyezése](how-to-vscode-tools.md#deploy-and-manage-models) nem kell létrehozni egy ACI-tároló előre, teszteléséhez, mert ACI tárolók jönnek létre menet közben.

További információkért lásd: a dokumentáció a a [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) és [webszolgáltatás](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) osztályokat.

### <a id="aks"></a>Az Azure Kubernetes Service (DEVTEST & ÉLES)

Használjon egy meglévő AKS-fürtöt, vagy hozzon létre egy újat az Azure Machine Learning SDK-t, a parancssori felület vagy az Azure portal használatával.

<a id="deploy-aks"></a>

Ha már rendelkezik egy AKS-fürt csatolt, telepítheti azt. Ha még nem létrehozott vagy csatolt egy AKS-fürtöt, kövesse a folyamat <a href="#create-attach-aks">hozzon létre egy új AKS-fürt</a>.

+ **Az SDK-val**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **A parancssori felületről**

    A parancssori felület használatával történő központi telepítéséhez használja a következő parancsot. Cserélje le `myaks` az AKS nevű számítási célt. Cserélje le `mymodel:1` nevére, illetve a regisztrált modell verziója. Cserélje le `myservice` biztosíthat a szolgáltatás nevét:

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    A bejegyzések a `deploymentconfig.json` paramétereit a dokumentumtérkép [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). A következő táblázat ismerteti az entitások a JSON-dokumentum és a metódus paramétereinek közötti:

    | JSON-entitás | Parametr Metody | Leírás |
    | ----- | ----- | ----- |
    | `computeType` | NA | A számítási cél. Az aks-hez, az értéknek kell lennie `aks`. |
    | `autoScaler` | NA | Az automatikus skálázás konfigurációs elemeket tartalmazza. A méretező táblázatban találja. |
    | &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Kell-e a webszolgáltatás automatikus skálázás engedélyezéséhez. Ha `numReplicas`  =  `0`, `True`; ellenkező esetben `False`. |
    | &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | A lehető legkevesebb tárolót szeretne használni, amikor az automatikus skálázás a webszolgáltatás. Alapértelmezett, `1`. |
    | &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Szeretne használni, amikor tárolók maximális számának automatikus skálázást a webszolgáltatás. Alapértelmezett, `10`. |
    | &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Milyen gyakran automatikus méretező megkísérli a webszolgáltatás méretezése. Alapértelmezett, `1`. |
    | &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | A cél kihasználtságát (%-os / 100), amelyek az automatikus méretező megpróbáljon-e webszolgáltatás kezelése. Alapértelmezett, `70`. |
    | `dataCollection` | NA | Az adatgyűjtés konfigurációs elemeket tartalmazza. |
    | &emsp;&emsp;`storageEnabled` | `collect_model_data` | Kell-e a webszolgáltatás a modelladatok gyűjtésének engedélyezése. Alapértelmezett, `False`. |
    | `authEnabled` | `auth_enabled` | -E a web service-hitelesítés engedélyezéséhez. Alapértelmezett, `True`. |
    | `containerResourceRequirements` | NA | A Processzor és a tároló számára kiosztott memória konfigurációs elemeket tartalmaz. |
    | &emsp;&emsp;`cpu` | `cpu_cores` | A webszolgáltatás lefoglalni a Processzormagok száma. Alapértelmezés szerint, `0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | Memória (GB-ban) a webszolgáltatás lefoglalni. Alapértelmezés szerint `0.5` |
    | `appInsightsEnabled` | `enable_app_insights` | Kell-e a web service az Application Insights naplózásának engedélyezése. Alapértelmezett, `False`. |
    | `scoringTimeoutMs` | `scoring_timeout_ms` | Szeretné kényszeríteni a kiértékelési hívások a web Service időtúllépés. Alapértelmezett, `60000`. |
    | `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Az egyidejű kérések maximális száma a webszolgáltatás csomópont. Alapértelmezett, `1`. |
    | `maxQueueWaitMs` | `max_request_wait_time` | A maximális időt, egy kérelem marad ezekkel a várólista (ezredmásodpercben) előtt egy 503-as hibát adott vissza. Alapértelmezett, `500`. |
    | `numReplicas` | `num_replicas` | A webszolgáltatás lefoglalni tárolók száma. Nincs alapértelmezett érték. Ha ez a paraméter nincs megadva, a méretező alapértelmezés szerint engedélyezve van. |
    | `keys` | NA | Kulcsok konfigurációs elemeket tartalmazza. |
    | &emsp;&emsp;`primaryKey` | `primary_key` | A webszolgáltatás használt elsődleges hitelesítési kulcs |
    | &emsp;&emsp;`secondaryKey` | `secondary_key` | Erre a webszolgáltatásra használandó másodlagos hitelesítési kulcs |
    | `gpuCores` | `gpu_cores` | A webszolgáltatás lefoglalni GPU-magok számát. Alapértelmezett érték 1. |
    | `livenessProbeRequirements` | NA | Konfigurációs elemek liveness mintavételi követelményeket tartalmazza. |
    | &emsp;&emsp;`periodSeconds` | `period_seconds` | Milyen gyakran (másodpercben) a működőképesség végrehajtásához. Alapértelmezés szerint 10 másodperc. Minimális értéke 1. |
    | &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Miután a tároló elindult, mielőtt liveness mintavételek kezdeményezett másodpercek száma. Alapértelmezett érték: 310 |
    | &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Utána a működőképesség túllépi az időkorlátot másodpercek száma. Az alapértelmezett 2 másodperc. Minimális értéke 1 |
    | &emsp;&emsp;`successThreshold` | `success_threshold` | Minimális egymást követő sikeres, a végrehajtandó működőképességi figyelembe kell venni a sikeres után kellene nem sikerült. Alapértelmezett értéke 1. Minimális értéke 1. |
    | &emsp;&emsp;`failureThreshold` | `failure_threshold` | A Pod indításakor, és a végrehajtandó működőképességi meghiúsul, Kubernetes megpróbálja failureThreshold többször központosítását. Az alapértelmezett érték 3. Minimális értéke 1. |
    | `namespace` | `namespace` | A Kubernetes-névtér, amely a webszolgáltatás üzembe van helyezve. Akár 63 alfanumerikus kisbetűt ("a" – "z", "0"-"9") és kötőjel ("-") karaktert. Az első és utolsó karaktere nem lehet kötőjel. |

    A következő JSON-ja üzembe helyezési konfiguráció például a CLI-vel használható:

    ```json
    {
        "computeType": "aks",
        "autoScaler":
        {
            "autoscaleEnabled": true,
            "minReplicas": 1,
            "maxReplicas": 3,
            "refreshPeriodInSeconds": 1,
            "targetUtilization": 70
        },
        "dataCollection":
        {
            "storageEnabled": true
        },
        "authEnabled": true,
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        }
    }
    ```

+ **A VS Code használatával**

  Is [üzembe az aks-ben a VS Code-bővítményével](how-to-vscode-tools.md#deploy-and-manage-models), de az AKS-fürtök előre konfigurálni kell.

További információ az AKS üzembe helyezési és automatikus méretezés a [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) hivatkozást.

#### Egy új AKS-fürt létrehozása<a id="create-attach-aks"></a>
**Becsült időtartam**: Körülbelül 20 percet.

Létrehozása vagy csatlakoztatása egy AKS-fürtöt a beállítás csak egyszer feldolgozni a munkaterületen. Újból felhasználhatja a fürt több telepítéshez. Ha a fürt vagy az azt tartalmazó erőforráscsoport törléséhez, üzembe kell helyeznie a következő alkalommal létre kell hoznia egy új fürtöt. A munkaterülethez csatlakoztatott több AKS-fürt is rendelkezhet.

Szeretne fejlesztői, ellenőrzés és tesztelés az AKS-fürt létrehozása, ha beállított `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` használatakor [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py). Ezzel a beállítással létrehozott fürt csak egy csomópont van.

> [!IMPORTANT]
> Beállítás `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` hoz létre egy AKS-fürtöt, amely nem megfelelő az éles forgalom kezelésére. Lehet, hogy következtetésekhez alkalommal hosszabb, mint egy éles környezetben létrehozott fürtön. A hibatűrés a fejlesztési-tesztelési fürtökhöz is nem garantált.
>
> Azt javasoljuk, hogy a fejlesztési és tesztelési létrehozott fürtök legalább két virtuális processzort használja.

A következő példa bemutatja, hogyan hozhat létre egy új Azure Kubernetes Service-fürt:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Egy AKS-fürtöt az Azure Machine Learning SDK kívül létrehozásával kapcsolatos további információkért tekintse meg a következő cikkeket:
* [AKS-fürt létrehozása](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Hozzon létre egy AKS-fürt (portál)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

További információ a `cluster_purpose` paramétert, tekintse meg a [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) hivatkozást.

> [!IMPORTANT]
> A [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), ha az élcsomópontba, győződjön meg arról, szorozva vm_size agent_count nagyobb vagy egyenlő 12 virtuális processzort válasszon agent_count és vm_size, egyéni értékeket. Például ha egy "Standard D3 v2", amelynek 4 virtuális CPU-vm_size majd ki kell választania egy agent_count 3 vagy nagyobb.
>
> Az Azure Machine Learning SDK nem biztosít támogatást az AKS-fürt méretezése. A csomópontok méretezése a fürtben, a felhasználói felület használata az AKS-fürt az Azure Portalon. Csak módosíthatja a csomópontok száma, a fürt nem a virtuális gép méretét.

#### <a name="attach-an-existing-aks-cluster"></a>Meglévő AKS-fürt csatolása
**Becsült időtartam:** Körülbelül 5 perc.

Ha már rendelkezik az AKS-fürtöt az Azure-előfizetésben, és verzió 1.12. ##, használhatja a rendszerképének üzembe helyezéséhez.

> [!WARNING]
> AKS-fürt csatlakoztatása egy munkaterületet, hogyan fogja használni a fürt beállításával meghatározhatja a `cluster_purpose` paraméter.
>
> Ha nincs beállítva a `cluster_purpose` paraméter, vagy adja `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, akkor a fürt rendelkeznie kell legalább 12 virtuális processzort érhető el.
>
> Ha `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, akkor a fürt nem kell 12 virtuális processzort. Egy fürtöt, amely konfigurálva van a fejlesztés + tesztelés azonban nem lesz megfelelő az éles-szintű forgalom, és következtetésekhez alkalommal növelhető.

A következő kód bemutatja, hogyan csatlakoztathat egy meglévő AKS 1.12 bemutatja. ## fürt a munkaterülethez:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

További információ a `attack_configuration()`, tekintse meg a [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-) hivatkozást.

További információ a `cluster_purpose` paramétert, tekintse meg a [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) hivatkozást.

## <a name="consume-web-services"></a>Webszolgáltatások felhasználása

Minden üzembe helyezett webszolgáltatás REST API-t, kínálja fel, hogy az ügyfélalkalmazások számos programozási nyelven hozhat létre. Ha engedélyezte a hitelesítést a szolgáltatáshoz, meg kell adnia a szolgáltatáskulcs a kérelem fejlécében tokenként.

### <a name="request-response-consumption"></a>Kérés-válasz fogyasztás

A következő példa bemutatja, hogyan a Python-szolgáltatás indítása:
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

További információkért lásd: [ügyfél létrehozása alkalmazások felhasználhatják őket a problémák megoldásához segítséget](how-to-consume-web-service.md).


### <a id="azuremlcompute"></a> A Batch következtetésekhez
Az Azure Machine Learning Compute tárolók létrehozása és felügyelete az Azure Machine Learning szolgáltatás által. A batch-előrejelzés az Azure Machine Learning-folyamatokat használható.

Az Azure Machine Learning Compute batch következtetésekhez leírását, olvassa el a [futtatása a Batch-előrejelzések hogyan](how-to-run-batch-predictions.md) cikk.

### <a id="iotedge"></a> IoT Edge következtetésekhez
Az Edge-ben való telepítésének támogatásához az előzetes verzióban. További információkért lásd: a [üzembe helyezése az Azure Machine Learning, az IoT Edge-modul](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) cikk.


## <a id="update"></a> Web services frissítése

Amikor új modellt hoz létre, minden szolgáltatás, amely az új modell használni kívánt manuálisan frissítenie kell. A web service frissítéséhez használja a `update` metódust. A következő kód bemutatja, hogyan frissíthető egy új modell használata a web service:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

## <a name="continuous-model-deployment"></a>Folyamatos modell-üzembehelyezés 

Folyamatos üzembe helyezése a Machine Learning-bővítménnyel, a modellek [Azure DevOps](https://azure.microsoft.com/services/devops/). A Machine Learning-bővítmény használatával az Azure DevOps, üzembe helyezési folyamat is indíthat, amikor egy új gépi tanulási modellt regisztrálva van az Azure Machine Learning szolgáltatás munkaterületén. 

1. Regisztráljon [Azure folyamatok](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), amely lehetővé teszi a folyamatos integrációt és teljesítést az alkalmazás bármely platformra/bármely felhőbeli. Az Azure folyamatok [eltér a gépi Tanulási folyamatok](concept-ml-pipelines.md#compare). 

1. [Az Azure DevOps-projekt létrehozása.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Telepítse a [folyamatokat az Azure Machine Learning-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 

1. Használat __kapcsolatok szolgáltatás__ állíthatja be az Azure Machine Learning szolgáltatás munkaterületén egyszerű szolgáltatáskapcsolódási el az összes összetevőt. Nyissa meg a projekt beállításait, kattintson a szolgáltatáskapcsolatokat, és válassza ki az Azure Resource Manager.

    ![view-service-connection](media/how-to-deploy-and-where/view-service-connection.png) 

1. Mint AzureMLWorkspace meghatározása a __szint hatókörét__ , és töltse ki a további paramétereket.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. A gépi tanulási modellt az Azure-folyamatok használatával folyamatosan üzembe helyezéséhez a folyamatok majd __kiadási__. Adjon hozzá egy új összetevőt, és válassza az AzureML modell összetevő és az előző lépésben létrehozott szolgáltatáskapcsolódási. Válassza ki a modell és verzió központi telepítés indításához. 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. A modell összetevő modell eseményindítón engedélyezése. Ha bekapcsolja az eseményindító minden alkalommal a megadott verzió (vagyis) a legújabb verzió), hogy a modell regisztrálása a munkaterületen, az Azure DevOps kibocsátási folyamat akkor indul el. 

    ![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)

Minta projektek és példákért tekintse meg [a MLOps tárház](https://github.com/Microsoft/MLOps)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az üzembe helyezett webszolgáltatáshoz törölheti `service.delete()`.
A regisztrált modell törléséhez használja `model.delete()`.

További információkért lásd: a dokumentáció a [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), és [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>További lépések
* [Egyéni Docker-rendszerkép használata modell üzembe helyezése](how-to-deploy-custom-docker-image.md)
* [Üzembe helyezés hibáinak elhárítása](how-to-troubleshoot-deployment.md)
* [Biztonságos SSL-lel az Azure Machine Learning-webszolgáltatások](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett gépi Tanulási modell felhasználása](how-to-consume-web-service.md)
* [Az Application insights szolgáltatással az Azure Machine Learning-modellek figyelése](how-to-enable-app-insights.md)
* [A modellek éles adatok gyűjtése](how-to-enable-data-collection.md)

