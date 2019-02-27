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
ms.openlocfilehash: 19d34e76c73c5ec2472d3eacddc01d6aebb6b9fb
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889103"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Az Azure Machine Learning szolgáltatással modellek üzembe helyezése

Az Azure Machine Learning szolgáltatás több módszert is telepíthet a betanított modell az SDK-t biztosít. Ebből a dokumentumból megtudhatja, hogyan helyezi üzembe a modellt webszolgáltatásként, amely az Azure-felhőben, vagy az IoT Edge-eszközök.

> [!IMPORTANT]
> Eltérő eredetű erőforrások megosztása (CORS) jelenleg nem támogatott a modellt webszolgáltatásként üzembe helyezésekor.

A következő számítási célnak modellek helyezhető üzembe:

| Számítási célt | Üzemelő példány típusa | Leírás |
| ----- | ----- | ----- |
| [Az Azure Kubernetes Service (AKS)](#aks) | Valós idejű következtetésekhez | Megfelelő választás a nagy méretű éles környezetekben üzemelő példányok. Automatikus skálázást és gyors válaszidők biztosít. |
| Az Azure ML Compute | A Batch következtetésekhez | Kiszolgáló nélküli számítási batch előrejelzési futtassa. A normál és alacsony prioritású virtuális gépeket támogatja. |
| [Az Azure Container Instances (aci Szolgáltatásban)](#aci) | Tesztelés | Jó fejlesztési vagy tesztelési célokra. **Nem megfelelő az éles számítási feladatokhoz.** |
| [Azure IoT Edge](#iotedge) | (Előzetes verzió) IoT-modul | Az IoT-eszközökön a modellek üzembe helyezése. Következtetési történik az eszközön. |
| [A mező-programmable gate array (FPGA)](#fpga) | (Előzetes verzió) Webszolgáltatás | Valós idejű következtetési ultraalacsony késése. |

A modell üzembe helyezését a minden számítási célokhoz hasonlít:

1. Betanítása, és regisztrálja a modellt.
1. Állítsa be, és regisztrálja a modellt használó kép.
1. A rendszerkép üzembe helyezése számítási célt.
1. Az üzemelő példány tesztelése

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


Az üzembe helyezést megvalósító munkafolyamat a fogalmakat további információkért lásd: [kezelése, telepítheti és figyelheti a modellek az Azure Machine Learning szolgáltatás](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree) még ma.

- Az Azure Machine Learning szolgáltatás munkaterület és az Azure Machine Learning SDK telepítve van a Pythonhoz készült. Ezekről az előfeltételekről használatával beszerzéséről a [Azure Machine Learning a rövid útmutató – első lépések](quickstart-get-started.md).

- Betanított modell. Ha nem rendelkezik a betanított modell, kövesse a [modelleket taníthat be a](tutorial-train-models-with-aml.md) oktatóanyag betanítására és regisztrálhat egy, az Azure Machine Learning szolgáltatással.

    > [!NOTE]
    > Bár az Azure Machine Learning szolgáltatás bármely általános modellt, amely tölthetők be a Python 3 dolgozhat, az ebben a dokumentumban szereplő példák tesztnek a modell pickle formátumban tárolja.
    > 
    > ONNX-modellekkel használatával kapcsolatos további információkért lásd: a [ONNX és az Azure Machine Learning](how-to-build-deploy-onnx.md) dokumentumot.

## <a id="registermodel"></a> Regisztrálja a betanított modell

A modell beállításjegyzék módja a tárolására és rendszerezheti a betanított modellek az Azure-felhőben. Modellek az Azure Machine Learning szolgáltatás munkaterületen vannak regisztrálva. A modell Azure Machine Learning, vagy egy másik szolgáltatás használatával kell betanítani. A modell fájlból regisztrálásához használja a következő kódot:

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl",
                       model_name = "Mymodel",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**Becsült időtartam**: Körülbelül 10 másodperc.

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
                                                 conda_file = "myenv.yml",
                                                 description = "Image with ridge regression model",
                                                 tags = {"data": "diabetes", "type": "regression"}
                                                 )
```

**Becsült időtartam**: Körülbelül 10 másodperc.

Ebben a példában a következő táblázat ismerteti a fontos paramétereket:

| Paraméter | Leírás |
| ----- | ----- |
| `execution_script` | Itt adhatja meg, amely a szolgáltatásnak küldött kérések fogadására szolgál egy Python-szkriptet. Ebben a példában a parancsfájl tartalmazza a `score.py` fájlt. További információkért lásd: a [parancsfájl végrehajtási](#script) szakaszban. |
| `runtime` | Azt jelzi, hogy a lemezképet használja-e a Python. A másik lehetőség `spark-py`, Apache Spark a Python melyik használ. |
| `conda_file` | Adja meg a conda-környezet fájl segítségével. Ez a fájl a conda-környezet üzembe helyezett modell határozza meg. Ez a fájl létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy környezetben fájlt (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file). |

További információkért lásd: a dokumentáció a [ContainerImage osztályban](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)

### <a id="script"></a> A parancsfájl végrehajtása

A végrehajtási parancsfájl egy üzembe helyezett rendszerképet elküldött adatokat fogad, és továbbítja azt a modellt. Ezután a modell által visszaadott válasz vesz igénybe, és adja vissza, amely az ügyfél számára. A parancsfájl csak a modell; ismernie kell az adatokat, amelyek a modellt vár, és adja vissza. A szkript általában két függvényt, amely betölteni, és futtassa a modell tartalmazza:

* `init()`: Általában ez a függvény a modellben tölt be egy globális objektum. Ezt a függvényt csak egyszer kell futtatni, a Docker-tároló indításakor. 

* `run(input_data)`: Ez a függvény egy értéket a bemeneti adatok alapján előre jelezni a modellt használ. Bemenetek és kimenetek a futtató szerializálást és deszerializálás általában használni JSON. Nyers bináris adatok is együttműködik. A modellhez való elküldése előtt, vagy az ügyfél való visszatérés előtt alakíthatja át az adatokat. 

#### <a name="working-with-json-data"></a>JSON-adatok használata

Az alábbi példa parancsfájl fogad el, és JSON-adatokat adja vissza. A `run` függvény olyan formátumra, hogy a modellt vár, és ezután alakítja át a JSON-válasz visszaküldés előtt alakítja át az adatok JSON-ból:

```python
# import things required by this script
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

## <a id="deploy"></a> A rendszerkép üzembe helyezése

Központi telepítés kap, a folyamat esetén úgy, hogy a számítási célnak függően némileg eltérnek. Az alábbi szakaszokban található információk segítségével megtudhatja, hogyan helyezhet üzembe:

* [Azure Container Instances](#aci)
* [Az Azure Kubernetes-szolgáltatás](#aks)
* [Project Brainwave (mező-programmable gate arrays)](#fpga)
* [Az Azure IoT Edge-eszközök](#iotedge)

> [!NOTE]
> Amikor **webszolgáltatásként üzembe helyezése**, három üzembe helyezési módszert használhatja:
>
> | Módszer | Megjegyzések |
> | ----- | ----- |
> | [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) | Regisztrálja a modellt kell, és ez a módszer használata előtt hozzon létre egy rendszerképet. |
> | [Üzembe helyezése](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) | Ha ezzel a módszerrel nem kell regisztrálja a modellt, vagy a kép létrehozásához. Azonban Ön nem határozhatja meg a modell vagy a kép neve vagy hozzárendelt címkék és leírások. |
> | [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) | Ha ezzel a módszerrel nem kell hozzon létre egy rendszerképet. De nem rendelkezik a létrehozott lemezkép neve felett. |
>
> A példák a jelen dokumentum-használat `deploy_from_image`.

### <a id="aci"></a> Helyezze üzembe az Azure Container Instances (DEVTEST)

A modellek üzembe helyezéséhez a egy webszolgáltatás, ha egy vagy több, a következő feltételek használata Azure Container Instances szolgáltatásban teljesül:

- Gyors üzembe helyezése és a modell érvényesítése kell. ACI üzembe helyezés befejeződött, kevesebb mint 5 perc alatt.
- A tesztelt egy olyan modell, fejlesztés alatt áll. Kvóta és régióban rendelkezésre állás az ACI, olvassa el a [kvóták és régiók rendelkezésre állása az Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) dokumentumot.

Azure Container Instancesben való üzembe helyezéséhez használja az alábbi lépéseket:

1. Adja meg a telepítési konfigurációt. Az alábbi példa meghatározza egy processzormaggal és 1 GB memóriát használó konfiguráció:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. A létrehozott rendszerképének üzembe helyezéséhez a [a lemezkép létrehozásának](#createimage) szakasz ebben a dokumentumban a következő kód használatával:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Becsült időtartam**: Körülbelül 3 perc.

További információkért lásd: a dokumentáció a a [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) és [webszolgáltatás](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) osztályokat.

### <a id="aks"></a> Helyezze üzembe az Azure Kubernetes Service (ÉLES)

Az Azure Kubernetes Service (AKS) használatával a modellt webszolgáltatásként, amely nagy méretű éles üzembe helyezéséhez. Használjon egy meglévő AKS-fürtöt, vagy hozzon létre egy újat az Azure Machine Learning SDK-t, a parancssori felület vagy az Azure portal használatával.

AKS-fürt létrehozása az egy folyamatot, amikor a munkaterülethez. Újból felhasználhatja a fürt több telepítéshez. Ha törli a fürtöt, majd kell létrehoznia egy új fürtöt, üzembe kell helyeznie legközelebb.

Az Azure Kubernetes Service az alábbi képességeket biztosítja:

* Automatikus skálázás
* Naplózás
* A modelladatok gyűjtésének
* A webes szolgáltatások gyors válaszidők

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

Ha már rendelkezik az AKS-fürtöt az Azure-előfizetésben, és 1.11-es verzió. *, használhatja a rendszerképének üzembe helyezéséhez. A következő kód bemutatja, hogyan csatlakoztathat egy meglévő fürthöz a munkaterülethez mutat be:

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

### <a id="fpga"></a> Az Azure ML Compute következtetésekhez

Az Azure Machine Learning számítási célnak létrehozása és felügyelete az Azure Machine Learning szolgáltatás által. A batch-előrejelzés az Azure gépi Tanulási folyamatok használható.

Az Azure ML Compute batch következtetésekhez leírását, olvassa el a [futtatása a Batch-előrejelzések hogyan](how-to-run-batch-predictions.md) dokumentumot.


### <a id="fpga"></a> A mező-programmable gate arrays (FPGA) üzembe helyezése

Project Brainwave ultramagas közel valós idejű következtetési kérelmek elérése érdekében lehetővé teszi. Project Brainwave gyorsítja Neurális hálózatok (DNN) mező-programmable gate arrays az Azure-felhőben üzembe helyezett. Leggyakrabban használt dnn-eket egyaránt elérhetők a tanulással featurizers, vagy saját adatai testre szabható a súlyok betanított.

A Project Brainwave használatával üzembe helyezéséhez, olvassa az [üzembe helyezés az egy FPGA](how-to-deploy-fpga-web-service.md) dokumentumot.

### <a id="iotedge"></a> Az Azure IoT Edge üzembe helyezése

Az Azure IoT Edge-eszköz, Linux vagy Windows-alapú eszköz, amely az Azure IoT Edge-futtatókörnyezet. Machine learning-modellek IoT Edge-modulok ezekre az eszközökre telepíthető. Az IoT Edge-eszköz üzembe helyezéséhez lehetővé teszi, hogy az eszköz a modell használatának közvetlenül, ahelyett, hogy adatokat küldeni a felhőbe feldolgozásra. Gyorsabb válaszidőt és alacsonyabb az adatforgalom kap.

A tárolóregisztrációs adatbázisból az Azure IoT Edge-modulok az eszközre vannak telepítve. Kép a modellből származó létrehozásakor tárolódik a tároló-beállításjegyzékbe a munkaterület.

#### <a name="set-up-your-environment"></a>A környezet kialakítása

* A fejlesztési környezet. További információkért lásd: a [a fejlesztési környezet konfigurálása](how-to-configure-environment.md) dokumentumot.

* Egy [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében. 

* Betanított modell. Találhat egy példát a modell betanítására, a [betanításához egy kép osztályozási modell az Azure Machine Learning](tutorial-train-models-with-aml.md) dokumentumot. Előre betanított modell érhető el a [AI-eszközkészlet az Azure IoT Edge GitHub-adattárat](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

#### <a name="prepare-the-iot-device"></a>Az IoT-eszköz előkészítése
Kell egy IoT hub létrehozása és eszköz regisztrálása, vagy használja újra úgy, hogy az egyik [Ez a szkript](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister).

``` bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister
sudo chmod +x createNregister
sudo ./createNregister <The Azure subscriptionID you want to use> <Resourcegroup to use or create for the IoT hub> <Azure location to use e.g. eastus2> <the Hub ID you want to use or create> <the device ID you want to create>
```

Az eredményül kapott kapcsolati karakterláncot mentése után "cs": "{másolja ezt a karakterláncot}".

Inicializálja az eszköz letöltésével [Ez a szkript](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge) egy UbuntuX64 IoT élcsomóponton, vagy a dsvm-hez, futtassa a következő parancsokat:

```bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge
sudo chmod +x installIoTEdge
sudo ./installIoTEdge
```

Az IoT élcsomóponton elkészült az IoT hub kapcsolati karakterláncát fogadásához. Keresse meg a sor ```device_connection_string:``` , és illessze be a kapcsolati karakterláncot az idézőjelek között a fent.

Akkor is is megtudhatja, hogyan regisztrálja az eszközt, és az IoT-modul telepítéséhez az alábbi a [a rövid útmutató: Az első IoT Edge-modul üzembe helyezése Linux x64 eszköz](../../iot-edge/quickstart-linux.md) dokumentumot.


#### <a name="get-the-container-registry-credentials"></a>A container registry hitelesítő adatainak lekérése
Az eszköz IoT Edge-modul telepítéséhez az Azure IoT a hitelesítő adatokat a tároló-beállításjegyzékbe, amely az Azure Machine Learning szolgáltatás tárolja, a docker-rendszerképek van szüksége.

Később könnyen hozzáférhet a szükséges tárolót a tárolójegyzék hitelesítő adatainak két módon:

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

#### <a name="deploy-the-model-to-the-device"></a>Az eszközön a modell üzembe helyezése

Egyszerűen üzembe helyezhetők a modell futtatásával [Ez a szkript](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel) és biztosítják, hogy a fenti lépéseket a következő információkat: tárolóregisztrációs adatbázis nevét, felhasználónevét, jelszavát, hely URL-címe, kívánt központi telepítés nevét, IoT Hub nevére, és a az Eszközazonosító hozott létre. Ezt megteheti is a virtuális gépen az alábbi lépéseket: 

```bash 
wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel
sudo chmod +x deploymodel
sudo ./deploymodel <ContainerRegistryName> <username> <password> <imageLocationURL> <DeploymentID> <IoTHubname> <DeviceID>
```

Azt is megteheti, követheti a lépések a [üzembe helyezése az Azure IoT Edge-modulok az Azure Portalról](../../iot-edge/how-to-deploy-modules-portal.md) a dokumentumot, a lemezkép telepítése az eszközre. Konfigurálásakor a __beállításjegyzék-beállítások__ az eszközhöz, használja a __bejelentkezési kiszolgáló__, __felhasználónév__, és __jelszó__ a munkaterület tárolóregisztrációs adatbázis.

> [!NOTE]
> Ha még nem ismeri az Azure IoT, a szolgáltatás első lépésekről lásd: a következő dokumentumokban talál:
>
> * [Rövid útmutató: Az első IoT Edge-modul üzembe egy Linux rendszerű eszközön](../../iot-edge/quickstart-linux.md)
> * [Rövid útmutató: Helyezze üzembe az első IoT Edge-modul a Windows-eszköz](../../iot-edge/quickstart.md)


## <a name="testing-web-service-deployments"></a>Webszolgáltatások üzembe helyezéséhez tesztelése

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

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Az üzembe helyezett webszolgáltatáshoz törölheti `service.delete()`.

Kép törléséhez használja `image.delete()`.

A regisztrált modell törléséhez használja `model.delete()`.

További információkért lásd: a dokumentáció a [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--), és [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="troubleshooting"></a>Hibaelhárítás

* __Ha üzembe helyezés során hibák__, használjon `service.get_logs()` a szolgáltatás a naplók megtekintéséhez. A naplózott információk jelezheti, hogy a hiba okát.

* A naplók is tartalmazhat, amely arra utasítja, hogy hiba __naplózási szint megadásához hibakeresési__. A naplózási szint megadásához adja hozzá a pontozó szkript a következő sorokat a lemezkép létrehozásának és a lemezkép használatával hozza létre:

    ```python
    import logging
    logging.basicConfig(level=logging.DEBUG)
    ```

    Ez a változás lehetővé teszi, hogy további naplózást, és előfordulhat, hogy miért jelentkezik a hiba további információt ad vissza.

## <a name="next-steps"></a>További lépések

* [Biztonságos SSL-lel az Azure Machine Learning-webszolgáltatások](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett gépi Tanulási modell felhasználása](how-to-consume-web-service.md)
* [Hogyan futtathat batch-előrejelzés](how-to-run-batch-predictions.md)
* [Az Application insights szolgáltatással az Azure Machine Learning-modellek figyelése](how-to-enable-app-insights.md)
* [A modellek éles adatok gyűjtése](how-to-enable-data-collection.md)
* [Azure Machine Learning service SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Az Azure Machine Learning szolgáltatás használata az Azure Virtual Network szolgáltatással](how-to-enable-virtual-network.md)
* [Gyakorlati tanácsok a javaslattételi rendszerek készítése](https://github.com/Microsoft/Recommenders)
* [Valós idejű ajánlás API létrehozása az Azure-ban](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
