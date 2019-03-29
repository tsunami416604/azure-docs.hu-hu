---
title: A modellek webszolgáltatásként üzembe helyezése
titleSuffix: Azure Machine Learning service
description: 'Ismerje meg, hogyan és hol, beleértve az Azure Machine Learning szolgáltatás modellek üzembe helyezése: Az Azure Container Instances, az Azure Kubernetes Service, Azure IoT Edge és Field-programmable gate arrays.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: ea2986ea2b2f561288773a7d187101f90f3e9fa9
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622127"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Az Azure Machine Learning szolgáltatással modellek üzembe helyezése

Az Azure Machine Learning SDK-t telepítheti a betanított modell számos lehetőséget biztosít. Ebből a dokumentumból megtudhatja, hogyan helyezi üzembe a modellt webszolgáltatásként, amely az Azure-felhőben, vagy az IoT Edge-eszközök.

A következő számítási célnak modellek helyezhető üzembe:

| Számítási célt | Üzemelő példány típusa | Leírás |
| ----- | ----- | ----- |
| [Azure Kubernetes Service (AKS)](#aks) | Valós idejű következtetésekhez | Megfelelő választás a nagy méretű éles környezetekben üzemelő példányok. Automatikus skálázást és gyors válaszidők biztosít. |
| [Az Azure Machine Learning Compute (amlcompute)](#azuremlcompute) | A Batch következtetésekhez | Kiszolgáló nélküli számítási batch előrejelzési futtassa. A normál és alacsony prioritású virtuális gépeket támogatja. |
| [Az Azure Container Instances (aci Szolgáltatásban)](#aci) | Tesztelés | Jó fejlesztési vagy tesztelési célokra. **Nem megfelelő az éles számítási feladatokhoz.** |
| [Azure IoT Edge](#iotedge) | (Előzetes verzió) IoT-modul | Az IoT-eszközökön a modellek üzembe helyezése. Következtetési történik az eszközön. |
| [A mező-programmable gate array (FPGA)](#fpga) | (Előzetes verzió) Webszolgáltatás | Valós idejű következtetési ultraalacsony késése. |

A modell üzembe helyezését a minden számítási célokhoz hasonlít:

1. Betanítása, és regisztrálja a modellt.
1. Állítsa be, és regisztrálja a modellt használó kép.
1. A rendszerkép üzembe helyezése számítási célt.
1. Az üzemelő példány tesztelése

A következő videó bemutatja az Azure Container Instancesben való üzembe helyezéséhez:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


Az üzembe helyezést megvalósító munkafolyamat a fogalmakat további információkért lásd: [kezelése, telepítheti és figyelheti a modellek az Azure Machine Learning szolgáltatás](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-prereq](../../../includes/aml-prereq.md)]

- Betanított modell. Ha nem rendelkezik a betanított modell, kövesse a [modelleket taníthat be a](tutorial-train-models-with-aml.md) oktatóanyag betanítására és regisztrálhat egy, az Azure Machine Learning szolgáltatással.

    > [!NOTE]
    > Bár az Azure Machine Learning szolgáltatás bármely általános modellt, amely tölthetők be a Python 3 dolgozhat, az ebben a dokumentumban szereplő példák bemutatják, Python pickle formátumban tárolja a modell használatával.
    >
    > ONNX-modellekkel használatával kapcsolatos további információkért lásd: a [ONNX és az Azure Machine Learning](how-to-build-deploy-onnx.md) dokumentumot.

## <a id="registermodel"></a> Regisztrálja a betanított modell

A modell beállításjegyzék módja a tárolására és rendszerezheti a betanított modellek az Azure-felhőben. Modellek az Azure Machine Learning szolgáltatás munkaterületen vannak regisztrálva. A modell Azure Machine Learning, vagy egy másik szolgáltatás használatával kell betanítani. A következő kód bemutatja, hogyan regisztrálja a modellt a fájlból, állítsa be egy nevet, a címkék és a egy leírás:

```python
from azureml.core.model import Model

model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**Becsült időtartam**: Körülbelül 10 másodperc.

A modellek regisztrálása egy példa: [betanításához egy kép osztályozó](tutorial-train-models-with-aml.md).

További információkért lásd: a dokumentáció a a [Model class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Hozzon létre és regisztrálja a kép

Üzembe helyezett modellnél képként vannak csomagolva. A rendszerkép tartalmazza a minta futtatásához szükséges függőségeket.

A **Azure-Tárolópéldányon**, **Azure Kubernetes Service**, és **Azure IoT Edge** telepítések esetén a [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) osztály egy rendszerkép-konfiguráció létrehozására szolgál. A rendszerkép-konfiguráció szolgál majd hozzon létre egy új Docker-rendszerképet.

A következő kód bemutatja, hogyan hozhat létre egy új rendszerkép-konfiguráció:

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml"}
                                                 )
```

**Becsült időtartam**: Körülbelül 10 másodperc.

Ebben a példában a következő táblázat ismerteti a fontos paramétereket:

| Paraméter | Leírás |
| ----- | ----- |
| `execution_script` | Itt adhatja meg, amely a szolgáltatásnak küldött kérések fogadására szolgál egy Python-szkriptet. Ebben a példában a parancsfájl tartalmazza a `score.py` fájlt. További információkért lásd: a [parancsfájl végrehajtási](#script) szakaszban. |
| `runtime` | Azt jelzi, hogy a lemezképet használja-e a Python. A másik lehetőség `spark-py`, Apache Spark a Python melyik használ. |
| `conda_file` | Adja meg a conda-környezet fájl segítségével. Ez a fájl a conda-környezet üzembe helyezett modell határozza meg. Ez a fájl létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy környezetben fájlt (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file). |

Egy példa egy rendszerkép-konfiguráció létrehozása: [üzembe helyezése egy kép osztályozó](tutorial-deploy-models-with-aml.md).

További információkért lásd: a dokumentáció a [ContainerImage osztályban](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)

### <a id="script"></a> A parancsfájl végrehajtása

A végrehajtási parancsfájl egy üzembe helyezett rendszerképet elküldött adatokat fogad, és továbbítja azt a modellt. Ezután a modell által visszaadott válasz vesz igénybe, és adja vissza, amely az ügyfél számára. **A parancsfájl csak a modell**; ismernie kell az adatokat, amelyek a modellt vár, és adja vissza. Példa parancsfájl, amely egy kép osztályozási modell együttműködni, lásd: [üzembe helyezése egy kép osztályozó](tutorial-deploy-models-with-aml.md).

A szkript két függvényt, amely betölteni, és futtassa a modell tartalmazza:

* `init()`: Általában ez a függvény a modellben tölt be egy globális objektum. Ezt a függvényt csak egyszer kell futtatni, a Docker-tároló indításakor.

* `run(input_data)`: Ez a függvény egy értéket a bemeneti adatok alapján előre jelezni a modellt használ. Bemenetek és kimenetek a futtató szerializálást és deszerializálás általában használni JSON. Nyers bináris adatok is együttműködik. A modellhez való elküldése előtt, vagy az ügyfél való visszatérés előtt alakíthatja át az adatokat.

#### <a name="working-with-json-data"></a>JSON-adatok használata

Az alábbi példa parancsfájl fogad el, és JSON-adatokat adja vissza. A `run` függvény olyan formátumra, hogy a modellt vár, és ezután alakítja át a JSON-válasz visszaküldés előtt alakítja át az adatok JSON-ból:

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>Bináris adatok használata

Ha a modell fogad __bináris adatok__, használjon `AMLRequest`, `AMLResponse`, és `rawhttp`. Az alábbi példa parancsfájl bináris adatokat fogad, és a POST kérésekhez a fordított bájtok adja vissza. A GET-kérésekhez akkor a válasz törzsében a teljes URL-címet adja vissza:

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> A `azureml.contrib` névtér gyakran változik, dolgozunk a szolgáltatás fejlesztéséhez. Ezért semmit a névtérben lévő kell előzetes minősül, és nincs teljes egészében a Microsoft támogatja.
>
> Tesztelje a helyi fejlesztési környezetbe kell, ha az összetevőket telepíthet a `contrib` névtér a következő paranccsal:
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> A lemezkép regisztrálása

Miután létrehozta a rendszerkép-konfiguráció, a lemezkép regisztrálása használhatja. Ez a rendszerkép a tárolójegyzékbe a munkaterület tárolja. Létrehozása után telepítheti a ugyanazt a lemezképet több szolgáltatásra.

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage",
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Becsült időtartam**: Körülbelül 3 perc.

Képek egyben a rendszerverzióval ellátott ugyanazzal a névvel több lemezképet is regisztrálhatja. Például az első képen regisztrált `myimage` hozzá van rendelve egy azonosítója `myimage:1`. A következő alkalommal regisztrál egy képen `myimage`, az azonosítója, az új lemezkép `myimage:2`.

További információkért lásd: a dokumentáció a [ContainerImage osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a id="deploy"></a> Webszolgáltatásként üzembe helyezése

Központi telepítés kap, a folyamat esetén úgy, hogy a számítási célnak függően némileg eltérnek. Az alábbi szakaszokban található információk segítségével megtudhatja, hogyan helyezhet üzembe:

| Számítási célt | Üzemelő példány típusa | Leírás |
| ----- | ----- | ----- |
| [Azure Kubernetes Service (AKS)](#aks) | Webszolgáltatás (valós idejű következtetésekhez)| Megfelelő választás a nagy méretű éles környezetekben üzemelő példányok. Automatikus skálázást és gyors válaszidők biztosít. |
| [Az Azure ML Compute](#azuremlcompute) | Webszolgáltatás (kötegelt következtetésekhez)| Kiszolgáló nélküli számítási batch előrejelzési futtassa. A normál és alacsony prioritású virtuális gépeket támogatja. |
| [Az Azure Container Instances (aci Szolgáltatásban)](#aci) | Webszolgáltatás (Dev/test)| Jó fejlesztési vagy tesztelési célokra. **Nem megfelelő az éles számítási feladatokhoz.** |
| [Azure IoT Edge](#iotedge) | (Előzetes verzió) IoT-modul | Az IoT-eszközökön a modellek üzembe helyezése. Következtetési történik az eszközön. |
| [A mező-programmable gate array (FPGA)](#fpga) | (Előzetes verzió) Webszolgáltatás | Valós idejű következtetési ultraalacsony késése. |

> [!IMPORTANT]
> Eltérő eredetű erőforrások megosztása (CORS) jelenleg nem támogatott a modellt webszolgáltatásként üzembe helyezésekor.

Az a szakaszban található példák használata [deploy_from_image](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), ami megköveteli, hogy a modell és a lemezkép regisztrálása egy központi telepítés végrehajtása előtt. Más központi telepítési módszerekkel kapcsolatos további információkért lásd: [üzembe helyezése](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) és [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-).

### <a id="aci"></a> Helyezze üzembe az Azure Container Instances (DEVTEST)

A modellek üzembe helyezéséhez a egy webszolgáltatás, ha egy vagy több, a következő feltételek használata Azure Container Instances szolgáltatásban teljesül:

- Gyors üzembe helyezése és a modell érvényesítése kell. ACI üzembe helyezés befejeződött, kevesebb mint 5 perc alatt.
- A tesztelt egy olyan modell, fejlesztés alatt áll. Kvóta és régióban rendelkezésre állás az ACI, olvassa el a [kvóták és régiók rendelkezésre állása az Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) dokumentumot.

Azure Container Instancesben való üzembe helyezéséhez használja az alábbi lépéseket:

1. Adja meg a telepítési konfigurációt. Ez a konfiguráció a modell követelményeitől függ. Az alábbi példa meghatározza egy processzormaggal és 1 GB memóriát használó konfiguráció:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. A létrehozott rendszerképének üzembe helyezéséhez a [a lemezkép létrehozásának](#createimage) szakasz ebben a dokumentumban a következő kód használatával:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Becsült időtartam**: Körülbelül 5 perc.

További információkért lásd: a dokumentáció a a [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) és [webszolgáltatás](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) osztályokat.

### <a id="aks"></a> Helyezze üzembe az Azure Kubernetes Service (ÉLES)

Az Azure Kubernetes Service (AKS) használatával a modellt webszolgáltatásként, amely nagy méretű éles üzembe helyezéséhez. Használjon egy meglévő AKS-fürtöt, vagy hozzon létre egy újat az Azure Machine Learning SDK-t, a parancssori felület vagy az Azure portal használatával.

AKS-fürt létrehozása az egy folyamatot, amikor a munkaterülethez. Újból felhasználhatja a fürt több telepítéshez.

> [!IMPORTANT]
> Ha törli a fürtöt, majd kell létrehoznia egy új fürtöt, üzembe kell helyeznie legközelebb.

Az Azure Kubernetes Service az alábbi képességeket biztosítja:

* Automatikus skálázás
* Naplózás
* A modelladatok gyűjtésének
* A webes szolgáltatások gyors válaszidők
* A TLS megszüntetése
* Authentication

#### <a name="autoscaling"></a>Automatikus skálázás

Az automatikus skálázási beállítás vezérelhető `autoscale_target_utilization`, `autoscale_min_replicas`, és `autoscale_max_replicas` az AKS webszolgáltatás. A következő példa bemutatja, hogyan engedélyezheti az automatikus skálázást:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Felfelé és lefelé méretezését, döntések az aktuális tárolóban replikák kihasználását engedményt alapul. A replikákat, foglalt (az egy kérelem feldolgozása) száma osztva az összes aktuális replikák száma az aktuális kihasználtságát méri. Ha ez a szám meghaladja a cél-kihasználtság, akkor több replika jönnek létre. Ha alacsonyabb, majd replikák csökken. Alapértelmezés szerint a célkihasználtság a 70 %.

Replikák hozzáadásával, döntések történik, és az gyorsan megvalósítva (körülbelül 1 másodperc). Döntéseket hozhat, távolítsa el a replikákat hosszabb időt vesz igénybe (körülbelül 1 perc). Ezt a viselkedést abban az esetben, ha új kérések érkeznek, hogy azok kezelni tudja tartja egy percig inaktív replikák körül.

Kiszámíthatja a szükséges replikákat az alábbi kód használatával:

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

További információt a beállítás `autoscale_target_utilization`, `autoscale_max_replicas`, és `autoscale_min_replicas`, tekintse meg a [AksWebservice.deploy_configuration](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) hivatkozást.

#### <a name="create-a-new-cluster"></a>Új fürt létrehozása

Az Azure Kubernetes Service-fürtöt létrehozni, használja a következő kódot:

> [!IMPORTANT]
> Az AKS-fürtöt hoz létre a rendszer egy folyamatot, amikor a munkaterülethez. Létrehozása után újból felhasználhatja a fürt több telepítéshez. Ha törli a fürtöt vagy az azt tartalmazó erőforráscsoportot, majd kell létrehoznia egy új fürtöt, üzembe kell helyeznie legközelebb.
> A [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), ha az élcsomópontba, győződjön meg arról, szorozva vm_size agent_count nagyobb vagy egyenlő 12 virtuális processzort válasszon agent_count és vm_size, egyéni értékeket. Például ha egy "Standard D3 v2", amelynek 4 virtuális CPU-vm_size majd ki kell választania egy agent_count 3 vagy nagyobb.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

**Becsült időtartam**: Körülbelül 20 percet.

#### <a name="use-an-existing-cluster"></a>Használjon egy meglévő fürthöz

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

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**Becsült időtartam**: Körülbelül 3 perc.

Egy AKS-fürtöt az Azure Machine Learning SDK kívül létrehozásával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [AKS-fürt létrehozása](https://docs.microsoft.com/cli/azure/aks?toc=%2Fen-us%2Fazure%2Faks%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Hozzon létre egy AKS-fürt (portál)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

#### <a name="deploy-the-image"></a>A rendszerkép üzembe helyezése

A létrehozott rendszerképének üzembe helyezéséhez a [a lemezkép létrehozásának](#createimage) szakasz ebben a dokumentumban az Azure Kubernetes-Server-fürt a következő kód használatával:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
service = Webservice.deploy_from_image(workspace = ws,
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
service.wait_for_deployment(show_output = True)
print(service.state)
```

**Becsült időtartam**: Körülbelül 3 perc.

További információkért lásd: a dokumentáció a a [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) és [webszolgáltatás](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) osztályokat.

### <a id="azuremlcompute"></a> Az Azure ML Compute következtetésekhez

Az Azure Machine Learning számítási célnak létrehozása és felügyelete az Azure Machine Learning szolgáltatás által. A batch-előrejelzés az Azure gépi Tanulási folyamatok használható.

Az Azure ML Compute batch következtetésekhez leírását, olvassa el a [futtatása a Batch-előrejelzések hogyan](how-to-run-batch-predictions.md) dokumentumot.


### <a id="fpga"></a> A mező-programmable gate arrays (FPGA) üzembe helyezése

Project Brainwave ultramagas közel valós idejű következtetési kérelmek elérése érdekében lehetővé teszi. Project Brainwave gyorsítja Neurális hálózatok (DNN) mező-programmable gate arrays az Azure-felhőben üzembe helyezett. Leggyakrabban használt dnn-eket egyaránt elérhetők a tanulással featurizers, vagy saját adatai testre szabható a súlyok betanított.

A Project Brainwave használatával üzembe helyezéséhez, olvassa az [üzembe helyezés az egy FPGA](how-to-deploy-fpga-web-service.md) dokumentumot.

## <a name="define-schema"></a>Séma megadása

Egyéni decorator használható [OpenAPI](https://swagger.io/docs/specification/about/) specifikáció. generációs és a bemeneti írja be a fájlkezelési a webszolgáltatás üzembe helyezésekor. Az a `score.py` fájlt, a bemeneti és/vagy a konstruktorban kimeneti mintát biztosít egy meghatározott típusú objektum, és amelyet a típusa és a minta automatikus létrehozása a sémát. Jelenleg a következő típusok támogatottak:

* `pandas`
* `numpy`
* `pyspark`
* standard Python

Először ellenőrizze a szükséges függőségek, a `inference-schema` csomag szerepelnek a `env.yml` conda-környezet fájlt. Ez a példa a `numpy` paraméter írja be a séma, így a felesleges pip `[numpy-support]` is telepítve van.

```python
%%writefile myenv.yml
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

Ezután módosítsa a `score.py` importálandó fájl a `inference-schema` csomagokat. A bemeneti és kimeneti formátumok minta meghatároznia a `input_sample` és `output_sample` változókat, amelyek tartalmazzák a webszolgáltatás a kérések és válaszok formátumok. Ezeket a mintákat használják a bemeneti és kimeneti függvény decorator a `run()` függvény.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)


input_sample = np.array([[1.8]])
output_sample = np.array([43638.88])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

A normál regisztrációs és a webes szolgáltatás üzembe helyezési folyamat és a frissített lépéseinek `score.py` fájlt, a Swagger uri beolvasása a szolgáltatástól. Ez az uri kérő adja vissza a `swagger.json` fájlt.

```python
service.wait_for_deployment(show_output=True)
print(service.swagger_uri)
```



Amikor létrehoz egy új rendszerképet, manuálisan frissítenie kell minden egyes szolgáltatás, amelyet szeretne az új rendszerképet használja. A web service frissítéséhez használja a `update` metódust. A következő kód bemutatja, hogyan frissíthető egy új rendszerkép használata a web service:

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

További információkért lásd: a dokumentáció a a [webszolgáltatás](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) osztály.

## <a name="test-web-service-deployments"></a>Webszolgáltatások üzembe helyezéséhez tesztelése

A webszolgáltatás üzembe teszteléséhez használhatja a `run` webszolgáltatás metódusa. A következő példában egy JSON-dokumentumok értéke egy webszolgáltatás, és az eredmény jelenik meg. Az elküldött adatok egyeznie kell a modellt vár. Ebben a példában az adatok formátuma megfelel-e a bemeneti a küzdő modell által várt.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

A webszolgáltatás, egy REST API-t, így a ügyfélalkalmazások számos programozási nyelven hozhat létre. További információkért lásd: [ügyfél létrehozása alkalmazások felhasználhatják őket a problémák megoldásához segítséget](how-to-consume-web-service.md).

## <a id="update"></a> A web service frissítése

Amikor létrehoz egy új rendszerképet, manuálisan frissítenie kell minden egyes szolgáltatás, amelyet szeretne az új rendszerképet használja. A web service frissítéséhez használja a `update` metódust. A következő kód bemutatja, hogyan frissíthető egy új rendszerkép használata a web service:

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

További információkért lásd: a dokumentáció a a [webszolgáltatás](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) osztály.

## <a id="iotedge"></a> Az Azure IoT Edge üzembe helyezése

Az Azure IoT Edge-eszköz, Linux vagy Windows-alapú eszköz, amely az Azure IoT Edge-futtatókörnyezet. Az Azure IoT Hub használatával, telepíthet gépi tanulási modellek ezekre az eszközökre, IoT Edge-modulok. Az IoT Edge-eszköz üzembe helyezéséhez lehetővé teszi, hogy az eszköz a modell használatának közvetlenül, ahelyett, hogy adatokat küldeni a felhőbe feldolgozásra. Gyorsabb válaszidőt és alacsonyabb az adatforgalom kap.

A tárolóregisztrációs adatbázisból az Azure IoT Edge-modulok az eszközre vannak telepítve. Kép a modellből származó létrehozásakor tárolódik a tároló-beállításjegyzékbe a munkaterület.

> [!IMPORTANT]
> Ebben a szakaszban található információk azt feltételezi, hogy már ismeri az Azure IoT Hub és az Azure IoT Edge-modulok. Bár ebben a szakaszban az adatok egy részét az Azure Machine Learning szolgáltatásra, a legtöbb, a folyamat az edge-eszköz üzembe helyezése az Azure IoT service történik.
>
> Ha ismeri az Azure IoT-vel, tekintse meg [Azure IoT – alapok](https://docs.microsoft.com/azure/iot-fundamentals/) és [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) alapvető tudnivalókat. Ezután ebben a szakaszban az egyéb hivatkozások segítségével további tudnivalók a meghatározott műveleteket.

### <a name="set-up-your-environment"></a>A környezet kialakítása

* A fejlesztési környezet. További információkért lásd: a [a fejlesztési környezet konfigurálása](how-to-configure-environment.md) dokumentumot.

* Egy [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében.

* Betanított modell. Találhat egy példát a modell betanítására, a [betanításához egy kép osztályozási modell az Azure Machine Learning](tutorial-train-models-with-aml.md) dokumentumot. Előre betanított modell érhető el a [AI-eszközkészlet az Azure IoT Edge GitHub-adattárat](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

### <a id="getcontainer"></a> A container registry hitelesítő adatainak lekérése

Az eszköz IoT Edge-modul telepítéséhez az Azure IoT a hitelesítő adatokat a tároló-beállításjegyzékbe, amely az Azure Machine Learning szolgáltatás tárolja, a docker-rendszerképek van szüksége.

A hitelesítő adatokat kétféleképpen kaphat:

+ **Az Azure Portalon**:

  1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/signin/index).

  1. Nyissa meg az Azure Machine Learning szolgáltatás munkaterületet, és válassza ki __áttekintése__. Nyissa meg a tároló beállításjegyzék-beállításokat, jelölje be a __beállításjegyzék__ hivatkozásra.

     ![A tároló beállításjegyzék-bejegyzés képe](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. Egyszer a container registry esetében válassza **Tárelérési kulcsok** , majd engedélyezze a rendszergazdai felhasználót.

     ![A hozzáférési kulcsok képernyő képe](./media/how-to-deploy-and-where/findaccesskey.png)

  1. Mentse az értékeket a **bejelentkezési kiszolgáló**, **felhasználónév**, és **jelszó**.

+ **Egy Python-szkriptet a**:

  1. Használja a következő Python-szkriptet a fenti tároló létrehozásához futtatott kód után:

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. Mentse az értékeket a ContainerURL, servername, felhasználónév és jelszó.

     Adja meg az IoT Edge-ben a privát tárolójegyzékben található rendszerképek elérése ezen hitelesítő adatok szükségesek.

### <a name="prepare-the-iot-device"></a>Az IoT-eszköz előkészítése

Az eszköz regisztrálása az Azure IoT Hub szolgáltatással, és telepítse az IoT Edge-futtatókörnyezet az eszközön. Ha nem ismeri ezt a folyamatot, tekintse meg a [a rövid útmutató: Az első IoT Edge-modul üzembe helyezése Linux x64 eszköz](../../iot-edge/quickstart-linux.md).

Vannak más módszerek az eszköz regisztrálása:

* [Azure Portal](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)
* [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-cli)
* [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-vscode)

### <a name="deploy-the-model-to-the-device"></a>Az eszközön a modell üzembe helyezése

A modell üzembe helyezése az eszközhöz, használja a beállításjegyzék-információk gyűjtött a [container registry hitelesítő adatainak lekérése](#getcontainer) az üzembe helyezett házirendmodul szakaszban ismertetett lépéseket, az IoT Edge-modulok. Például, hogy amikor [üzembe helyezése az Azure IoT Edge-modulok az Azure Portalról](../../iot-edge/how-to-deploy-modules-portal.md), konfigurálnia kell a __beállításjegyzék-beállítások__ az eszközhöz. Használja a __bejelentkezési kiszolgáló__, __felhasználónév__, és __jelszó__ a munkaterület tároló-beállításjegyzékhez.

Ezenkívül telepítheti használatával [Azure CLI-vel](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli) és [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-vscode).

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Az üzembe helyezett webszolgáltatáshoz törölheti `service.delete()`.

Kép törléséhez használja `image.delete()`.

A regisztrált modell törléséhez használja `model.delete()`.

További információkért lásd: a dokumentáció a [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--), és [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>További lépések

* [Üzembe helyezés hibáinak elhárítása](how-to-troubleshoot-deployment.md)
* [Biztonságos SSL-lel az Azure Machine Learning-webszolgáltatások](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett gépi Tanulási modell felhasználása](how-to-consume-web-service.md)
* [Hogyan futtathat batch-előrejelzés](how-to-run-batch-predictions.md)
* [Az Application insights szolgáltatással az Azure Machine Learning-modellek figyelése](how-to-enable-app-insights.md)
* [A modellek éles adatok gyűjtése](how-to-enable-data-collection.md)
* [Azure Machine Learning service SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Az Azure Machine Learning szolgáltatás használata az Azure Virtual Network szolgáltatással](how-to-enable-virtual-network.md)
* [Gyakorlati tanácsok a javaslattételi rendszerek készítése](https://github.com/Microsoft/Recommenders)
* [Valós idejű ajánlás API létrehozása az Azure-ban](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
