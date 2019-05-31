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
ms.date: 05/21/2019
ms.custom: seoapril2019
ms.openlocfilehash: 929a4ae2e954933bf00550770ba9d41319dc6241
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418047"
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

| Számítási célt | Használat | Leírás |
| ----- | ----- | ----- |
| [Helyi webszolgáltatás](#local) | Tesztelés és hibakeresés | Megfelelő választás a korlátozott tesztelés és hibaelhárítás.
| [Az Azure Kubernetes Service (AKS)](#aks) | Valós idejű következtetésekhez | Megfelelő választás a nagy méretű éles környezetekben üzemelő példányok. Automatikus skálázást és gyors válaszidők biztosít. |
| [Az Azure Container Instances (aci Szolgáltatásban)](#aci) | Tesztelés | Megfelelő választás a lépték esetén a CPU-alapú számítási feladatok. |
| [Az Azure Machine Learning Compute](how-to-run-batch-predictions.md) | (Előzetes verzió) A Batch következtetésekhez | Futtassa a kötegelt pontozási a kiszolgáló nélküli számítási. A normál és alacsony prioritású virtuális gépeket támogatja. |
| [Azure IoT Edge](#iotedge) | (Előzetes verzió) IoT-modul | Üzembe helyezés, és gépi Tanulási modelleket szolgálnak az IoT-eszközökön. |


## <a name="prepare-to-deploy"></a>Az üzembe helyezés előkészítése

Webszolgáltatásként üzembe helyezéséhez létre kell hoznia egy következtetésekhez configuration (`InferenceConfig`) és a egy központi telepítés konfigurálása. Következtetésekhez vagy a modell pontozása, nem a fázis, az üzembe helyezett modell előrejelzési leggyakrabban a termelési adatok szolgál. A következtetésekhez config adja meg a parancsfájlok és a modell kiszolgálására szükséges függőségeket. A deployment config részletesen szolgálnak ki a modell a számítási célnak adja meg.


### <a id="script"></a> 1. A bejegyzés parancsfájl & függőségek definiálása

A bejegyzés parancsfájl egy már üzembe helyezett webszolgáltatás elküldött adatokat fogad, és átadja azokat a modellbe. Ezután a modell által visszaadott válasz vesz igénybe, és adja vissza, amely az ügyfél számára. **A parancsfájl csak a modell**; ismernie kell az adatokat, amelyek a modellt vár, és adja vissza.

A szkript két függvényt, amely betölteni, és futtassa a modell tartalmazza:

* `init()`: Általában ez a függvény a modellben tölt be egy globális objektum. Ez a függvény fut, csak egyszer, amikor a Docker-tárolót, a webszolgáltatás elindult.

* `run(input_data)`: Ez a függvény egy értéket a bemeneti adatok alapján előre jelezni a modellt használ. Bemenetek és kimenetek a futtató szerializálást és deszerializálás általában használni JSON. Nyers bináris adatok is együttműködik. A modellhez való elküldése előtt, vagy az ügyfél való visszatérés előtt alakíthatja át az adatokat.

#### <a name="optional-automatic-swagger-schema-generation"></a>(Nem kötelező) A Swagger-séma automatikus létrehozása

Automatikusan hozzon létre egy sémát a webszolgáltatás számára, a bemeneti minta és/vagy a kimenetet a konstruktort az egyik a meghatározott típusú objektumot kap, és a típus és a minta segítségével automatikusan létre kell hozni a sémát. Az Azure Machine Learning szolgáltatás majd létrehoz egy [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) specifikáció a webszolgáltatás üzembe helyezése során.

Jelenleg a következő típusok támogatottak:

* `pandas`
* `numpy`
* `pyspark`
* Standard Python-objektum

Séma létrehozása használatához közé tartozik a `inference-schema` csomagot a conda-környezet fájlban. Az alábbi példában `[numpy-support]` óta a bejegyzés parancsfájl használ egy numpy paraméter típusa: 

#### <a name="example-dependencies-file"></a>Függőségek példafájl
Az alábbiakban látható egy példa egy Conda-függőségeket fájl következtetésekhez.
```python
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

Ebben a példában a konfiguráció a következő elemeket tartalmazza:

* Következtetésekhez szükséges eszközöket tartalmazó könyvtár
* Hogy ez a modell futtatásához szükséges Python
* A [bejegyzés parancsfájl](#script), amely a telepített szolgáltatásnak küldött webes kérések kezelésére szolgál
* A conda-fájlt, amely leírja a következtetésekhez szükséges Python-csomagok

InferenceConfig funkciójával kapcsolatos további információkért lásd: a [speciális konfiguráció](#advanced-config) szakaszban.

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

### <a id="aks"></a>Az Azure Kubernetes Service (ÉLES)

Használjon egy meglévő AKS-fürtöt, vagy hozzon létre egy újat az Azure Machine Learning SDK-t, a parancssori felület vagy az Azure portal használatával.

<a id="deploy-aks"></a>

Ha már rendelkezik egy AKS-fürt csatolt, telepítheti azt. Ha még nem létrehozott vagy csatolt egy AKS-fürtöt, kövesse a folyamat <a href="#create-attach-aks">hozzon létre egy új AKS-fürt</a>.

+ **Az SDK-val**

  ```python
  aks_target = AksCompute(ws,"myaks")
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

> [!IMPORTANT]
> Létrehozása vagy csatlakoztatása egy AKS-fürtöt a beállítás csak egyszer feldolgozni a munkaterületen. Újból felhasználhatja a fürt több telepítéshez. Ha a fürt vagy az azt tartalmazó erőforráscsoport törléséhez, üzembe kell helyeznie a következő alkalommal létre kell hoznia egy új fürtöt.

További információt a beállítás `autoscale_target_utilization`, `autoscale_max_replicas`, és `autoscale_min_replicas`, tekintse meg a [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) hivatkozást.
A következő példa bemutatja, hogyan hozhat létre egy új Azure Kubernetes Service-fürt:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
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


> [!IMPORTANT]
> A [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), ha az élcsomópontba, győződjön meg arról, szorozva vm_size agent_count nagyobb vagy egyenlő 12 virtuális processzort válasszon agent_count és vm_size, egyéni értékeket. Például ha egy "Standard D3 v2", amelynek 4 virtuális CPU-vm_size majd ki kell választania egy agent_count 3 vagy nagyobb.

**Becsült időtartam**: Körülbelül 20 percet.

#### <a name="attach-an-existing-aks-cluster"></a>Meglévő AKS-fürt csatolása

Ha már rendelkezik az AKS-fürtöt az Azure-előfizetésben, és verzió 1.12. ## és van legalább 12 virtuális processzort, a rendszerképének üzembe helyezéséhez használhatja. A következő kód bemutatja, hogyan csatlakoztathat egy meglévő AKS 1.12 bemutatja. ## fürt a munkaterülethez:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

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


### <a id="azuremlcompute"></a> Batch-felhasználás
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

<a id="advanced-config"></a>

## <a name="advanced-settings"></a>Speciális beállítások 

**<a id="customimage"></a> Egyéni alapszintű rendszerkép használata**

Belsőleg InferenceConfig hoz létre egy Docker-rendszerképet, amely tartalmazza a modell és egyéb eszközök számára a szolgáltatás szükséges. Ha nincs megadva, alapértelmezett alapképet szolgál.

A következtetésekhez konfigurációval használandó kép létrehozásakor, a kép a következő követelményeknek kell megfelelnie:

* Ubuntu 16.04 vagy nagyobb.
* Conda 4.5. # vagy nagyobb.
* Python 3.5-ös verzióját. # vagy 3.6. #.

Egyéni rendszerkép használatához állítsa a `base_image` tulajdonság a következtetésekhez konfigurációját, hogy a cím a kép. Az alábbi példa bemutatja, hogyan mindkét egy nyilvános és privát Azure Container Registry rendszerkép használatához:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

Az alábbi képen URI-k a Microsoft által rendelkezésre bocsátott rendszerképek találhatók, és egy felhasználó vagy a jelszó érték megadása nélkül használható:

* `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03`

Ezek a lemezképek használatához állítsa a `base_image` , az URI-t a listából. Állítsa be `base_image_registry.address` való `mcr.microsoft.com`.

> [!IMPORTANT]
> Microsoft-lemezképek, amelyek CUDA vagy TensorRT csak a Microsoft Azure-szolgáltatásokra kell használni.

A saját rendszerképek feltöltése egy Azure Container registrybe további információkért lásd: [az első rendszerkép leküldése egy privát Docker-tárolójegyzék](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).

Ha a modell tanítása az Azure Machine Learning COMPUTE számítási, a __1.0.22 verzió vagy annál nagyobb__ , az Azure Machine Learning SDK-lemezkép létrehozása betanítás során. A következő példa bemutatja, hogyan használhatja ezt a képet:

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az üzembe helyezett webszolgáltatáshoz törölheti `service.delete()`.
A regisztrált modell törléséhez használja `model.delete()`.

További információkért lásd: a dokumentáció a [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), és [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>További lépések
* [Üzembe helyezés hibáinak elhárítása](how-to-troubleshoot-deployment.md)
* [Biztonságos SSL-lel az Azure Machine Learning-webszolgáltatások](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett gépi Tanulási modell felhasználása](how-to-consume-web-service.md)
* [Az Application insights szolgáltatással az Azure Machine Learning-modellek figyelése](how-to-enable-app-insights.md)
* [A modellek éles adatok gyűjtése](how-to-enable-data-collection.md)

