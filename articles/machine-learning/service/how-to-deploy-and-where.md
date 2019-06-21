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
ms.date: 05/31/2019
ms.custom: seoapril2019
ms.openlocfilehash: c4ab5fe4625bce1ed66258a5b9aab597dae17a1a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303995"
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

Regisztrálja a gépi tanulási modellek az Azure Machine Learning-munkaterületen. A modell Azure Machine Learning származhatnak, vagy valahol máshol származhatnak. Az alábbi példák bemutatják, hogyan lehet regisztrálni egy modellt a fájlból:

### <a name="register-a-model-from-an-experiment-run"></a>Regisztrálja a modellt egy kísérlet futtatása

+ **Az SDK-val Scikit további példa**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```
+ **A parancssori felületről**
  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```


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

+ **A parancssori felületről**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

**Becsült időtartam**: Körülbelül 10 másodperc.

További információkért lásd: a dokumentáció a a [Model class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

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
Amikor regisztrál egy modellt, adja meg a beállításjegyzékben a modell kezelésére használt modell nevét. Az API, amely visszaadja az elérési útját a helyi fájlrendszerben modell fájl(ok) get_model_path ezt a nevet fogja használni. Ha regisztrál egy mappa vagy fájl gyűjteménye, az API-t az elérési utat a könyvtárba, amely tartalmazza azokat a fájlokat adja vissza.

Ha regisztrálja a modellt, akkor adjon meg egy nevet, amely megfelel, ahol a modell kerül, helyileg vagy a szolgáltatás üzembe helyezése során.

Az alábbi példában vissza fog térni egy elérési utat egy egyetlen fájl neve "sklearn_mnist_model.pkl" (amely regisztrálva lett az a név "sklearn_mnist")
```
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

A következtetésekhez konfigurációs ismerteti, hogyan konfigurálhatja a modellt, hogy előrejelzéseket végezzen. A következő példa bemutatja, hogyan hozhat létre egy következtetésekhez konfigurációt:

```python
inference_config = InferenceConfig(source_directory="C:/abc",
                                   runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig a CLI-példa
```JSON
{
   "entryScript": "x/y/score.py",
   "runtime": "python",
   "condaFile": "env/myenv.yml",
   "sourceDirectory":"C:/abc",
}
```
```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Ebben a példában a konfiguráció a következő elemeket tartalmazza:

* Következtetésekhez szükséges eszközöket tartalmazó könyvtár
* Hogy ez a modell futtatásához szükséges Python
* A [bejegyzés parancsfájl](#script), amely a telepített szolgáltatásnak küldött webes kérések kezelésére szolgál
* A conda-fájlt, amely leírja a következtetésekhez szükséges Python-csomagok

InferenceConfig funkciójával kapcsolatos további információkért lásd: a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) referencia osztály.

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
A modell szolgáltatás a telepítés előtt érdemes profilt, hogy optimális CPU és memória-követelmények meghatározása.
Ez az SDK-t vagy a CLI-n keresztül teheti meg.

További információ az SDK-dokumentáció itt megtekinthet: https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-

A modellekre profilkészítés eredményei egy Futtatás objektum vannak rendelkezésre.
A modell profil séma a részletekről itt található: https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py

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

  ```azurecli-interactive
  az ml model deploy -m sklearn_mnist:1 -ic inferenceconfig.json -dc deploymentconfig.json
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

  ```azurecli-interactive
  az ml model deploy -m sklearn_mnist:1 -n aciservice -ic inferenceconfig.json -dc deploymentconfig.json
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

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n aksservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

+ **A VS Code használatával**

  Is [üzembe az aks-ben a VS Code-bővítményével](how-to-vscode-tools.md#deploy-and-manage-models), de az AKS-fürtök előre konfigurálni kell.

További információ az AKS üzembe helyezési és automatikus méretezés a [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) hivatkozást.

#### Egy új AKS-fürt létrehozása<a id="create-attach-aks"></a>
**Becsült időtartam:** Körülbelül 5 perc.

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

**Becsült időtartam**: Körülbelül 20 percet.

#### <a name="attach-an-existing-aks-cluster"></a>Meglévő AKS-fürt csatolása

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

