---
title: A modellek webszolgáltatásként üzembe helyezése
titleSuffix: Azure Machine Learning service
description: 'Ismerje meg, hogyan és hol, beleértve az Azure Machine Learning szolgáltatás modellek üzembe helyezése: Az Azure Container Instances, az Azure Kubernetes Service, Azure IoT Edge és Field-programmable gate arrays.'
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 0c171ff768395540c123c4ef2a19168d926b0661
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633827"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Az Azure Machine Learning szolgáltatással modellek üzembe helyezése

Az Azure Machine Learning szolgáltatás több módszert is telepíthet a betanított modell az SDK-t biztosít. Ebből a dokumentumból megtudhatja, hogyan helyezi üzembe a modellt webszolgáltatásként, amely az Azure-felhőben, vagy az IoT edge-eszközök.

A következő számítási célnak modellek helyezhető üzembe:

| Számítási célt | Üzemelő példány típusa | Leírás |
| ----- | ----- | ----- |
| [Az Azure Container Instances (aci Szolgáltatásban)](#aci) | Webszolgáltatás | Gyors üzembe helyezés. Jó fejlesztési vagy tesztelési célokra. |
| [Az Azure Kubernetes Service (AKS)](#aks) | Webszolgáltatás | Megfelelő választás a nagy méretű éles környezetekben üzemelő példányok. Automatikus skálázást és gyors válaszidők biztosít. |
| [Azure IoT Edge](#iotedge) | IoT-modul | Az IoT-eszközökön a modellek üzembe helyezése. Következtetési történik az eszközön. |
| [A mező-programmable gate array (FPGA)](#fpga) | Webszolgáltatás | Valós idejű következtetési ultraalacsony késése. |

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Machine Learning szolgáltatás munkaterület és az Azure Machine Learning SDK telepítve van a Pythonhoz készült. Ezekről az előfeltételekről használatával beszerzéséről a [Azure Machine Learning a rövid útmutató – első lépések](quickstart-get-started.md).

- Mindkét pickle a betanított modell (`.pkl`) vagy az ONNX (`.onnx`) formátumban. Ha nem rendelkezik a betanított modell, kövesse a [modelleket taníthat be a](tutorial-train-models-with-aml.md) oktatóanyag betanítására és regisztrálhat egy, az Azure Machine Learning szolgáltatással.

- A kód szakaszok feltételeztük, hogy `ws` hivatkozik a machine learning-munkaterület. Például: `ws = Workspace.from_config()`.

## <a name="deployment-workflow"></a>Telepítési munkafolyamat

A modell üzembe helyezését a minden számítási célokhoz hasonlít:

1. A modell betanításához.
1. Regisztrálja a modellt.
1. Hozzon létre egy rendszerképet konfigurációt.
1. Hozza létre a rendszerképet.
1. A rendszerkép üzembe helyezése számítási célt.
1. Az üzemelő példány tesztelése
1. (Nem kötelező) A törlés összetevőket.

    * Amikor **webszolgáltatásként üzembe helyezése**, három üzembe helyezési lehetőség áll rendelkezésre:

        * [Üzembe helyezése](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-): Ha ezzel a módszerrel nem kell regisztrálja a modellt, vagy a kép létrehozásához. Azonban Ön nem határozhatja meg a modell vagy a kép neve vagy hozzárendelt címkék és leírások.
        * [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-): Ha ezzel a módszerrel nem kell hozzon létre egy rendszerképet. De nem rendelkezik a létrehozott lemezkép neve felett.
        * [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-): Regisztrálja a modellt, és ez a módszer használata előtt hozzon létre egy rendszerképet.

        A példák a jelen dokumentum-használat `deploy_from_image`.

    * Amikor **, IoT Edge-modul üzembe helyezése**, regisztrálja a modellt és a lemezkép létrehozásának kell.

## <a name="register-a-model"></a>Regisztrálja a modellt

Csak a betanított modellek is üzembe helyezhetők. A modell Azure Machine Learning, vagy egy másik szolgáltatás használatával kell betanítani. A modell fájlból regisztrálásához használja a következő kódot:

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl",
                       model_name = "Mymodel",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

> [!NOTE]
> A példa bemutatja, hogy egy modell pickle-fájlként tárolja, amíg is használt ONNX-modellekkel. ONNX-modellekkel használatával kapcsolatos további információkért lásd: a [ONNX és az Azure Machine Learning](how-to-build-deploy-onnx.md) dokumentumot.

További információkért lásd: a dokumentáció a a [Model class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Egy rendszerkép-konfiguráció létrehozása

Üzembe helyezett modellnél képként vannak csomagolva. A rendszerkép tartalmazza a minta futtatásához szükséges függőségeket.

A **Azure-Tárolópéldányon**, **Azure Kubernetes Service**, és **Azure IoT Edge** telepítések esetén a `azureml.core.image.ContainerImage` osztály egy rendszerkép-konfiguráció létrehozására szolgál. A rendszerkép-konfiguráció szolgál majd hozzon létre egy új Docker-rendszerképet. 

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

Ebben a konfigurációban egy `score.py` át fájlt kéri, hogy a modell. Ez a fájl két függvényt tartalmazza:

* `init()`: Általában ez a függvény a modellben tölt be egy globális objektum. Ezt a függvényt csak egyszer kell futtatni, a Docker-tároló indításakor. 

* `run(input_data)`: Ez a függvény egy értéket a bemeneti adatok alapján előre jelezni a modellt használ. A futtatás bemenetei és kimenetei általában JSON-fájlokat használnak a szerializáláshoz vagy a deszerializáláshoz, de más formátumokat is támogatnak.

Példa `score.py` fájlt, tekintse meg a [kép besorolási oktatóanyag](tutorial-deploy-models-with-aml.md#make-script). Például, hogy az ONNX-modellt használja, tekintse meg a [ONNX és az Azure Machine Learning](how-to-build-deploy-onnx.md) dokumentumot.

A `conda_file` paraméterrel adja meg a conda-környezet fájlt. Ez a fájl a conda-környezet üzembe helyezett modell határozza meg. Ez a fájl létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy környezetben fájlt (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file).

További információkért lásd: a dokumentáció a [ContainerImage osztályban](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)

## <a id="createimage"></a> A lemezkép létrehozása

Miután létrehozta a rendszerkép-konfiguráció, a lemezképek létrehozására használhatja. Ez a rendszerkép a tárolójegyzékbe a munkaterület tárolja. Létrehozása után telepítheti a ugyanazt a lemezképet több szolgáltatásra.

```python
# Create the image from the image configuration
image = ContainerImage.create(name = "myimage", 
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Becsült időtartam**: Körülbelül 3 perc.

Képek egyben a rendszerverzióval ellátott ugyanazzal a névvel több lemezképet is regisztrálhatja. Például az első képen regisztrált `myimage` hozzá van rendelve egy azonosítója `myimage:1`. A következő alkalommal regisztrál egy képen `myimage`, az azonosítója, az új lemezkép `myimage:2`.

További információkért lásd: a dokumentáció a [ContainerImage osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a name="deploy-the-image"></a>A rendszerkép üzembe helyezése

Központi telepítés kap, a folyamat esetén úgy, hogy a számítási célnak függően némileg eltérnek. Az alábbi szakaszokban található információk segítségével megtudhatja, hogyan helyezhet üzembe:

* [Azure Container Instances](#aci)
* [Az Azure Kubernetes-szolgáltatás](#aks)
* [Project Brainwave (mező-programmable gate arrays)](#fpga)
* [Az Azure IoT Edge-eszközök](#iotedge)

### <a id="aci"></a> Az Azure Container Instances szolgáltatásban való üzembe helyezése

A modellek üzembe helyezéséhez a egy webszolgáltatás, ha egy vagy több, a következő feltételek használata Azure Container Instances szolgáltatásban teljesül:

- Gyors üzembe helyezése és a modell érvényesítése kell. ACI üzembe helyezés befejeződött, kevesebb mint 5 perc alatt.
- A tesztelt egy olyan modell, fejlesztés alatt áll. Kvóta és régióban rendelkezésre állás az ACI, olvassa el a [kvóták és régiók rendelkezésre állása az Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) dokumentumot.

Azure Container Instancesben való üzembe helyezéséhez használja az alábbi lépéseket:

1. Adja meg a telepítési konfigurációt. Az alábbi példa meghatározza egy processzormaggal és 1 GB memóriát használó konfiguráció:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. A létrehozott rendszerképének üzembe helyezéséhez a [a lemezkép létrehozásának](#createimage) szakasz ebben a dokumentumban a következő kód használatával:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Becsült időtartam**: Körülbelül 3 perc.

    > [!TIP]
    > Ha üzembe helyezés során hibák, használja `service.get_logs()` az AKS szolgáltatás a naplók megtekintéséhez. A naplózott információk jelezheti, hogy a hiba okát.

További információkért lásd: a dokumentáció a a [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) és [webszolgáltatás](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) osztályokat.

### <a id="aks"></a> Az Azure Kubernetes Service üzembe helyezése

Az Azure Kubernetes Service (AKS) használatával a modellt webszolgáltatásként, amely nagy méretű éles üzembe helyezéséhez. Használjon egy meglévő AKS-fürtöt, vagy hozzon létre egy újat az Azure Machine Learning SDK-t, a parancssori felület vagy az Azure portal használatával.

AKS-fürt létrehozása az egy folyamatot, amikor a munkaterülethez. Újból felhasználhatja a fürt több telepítéshez. Ha törli a fürtöt, majd kell létrehoznia egy új fürtöt, üzembe kell helyeznie legközelebb.

Az Azure Kubernetes Service az alábbi képességeket biztosítja:

* Automatikus skálázás
* Naplózás
* A modelladatok gyűjtésének
* A webes szolgáltatások gyors válaszidők

Azure Kubernetes Service-ben való üzembe helyezéséhez használja az alábbi lépéseket:

1. AKS-fürt létrehozásához használja a következő kódot:

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

    > [!TIP]
    > Ha már rendelkezik az AKS-fürtöt az Azure-előfizetésben, és 1.11-es verzió. *, használhatja a rendszerképének üzembe helyezéséhez. A következő kód bemutatja, hogyan csatlakoztathat egy meglévő fürthöz a munkaterülethez mutat be:
    >
    > ```python
    > from azureml.core.compute import AksCompute, ComputeTarget
    > # Set the resource group that contains the AKS cluster and the cluster name
    > resource_group = 'myresourcegroup'
    > cluster_name = 'mycluster'
    > 
    > # Attatch the cluster to your workgroup
    > attach_config = AksCompute.attach_configuration(resource_group = resource_group,
    >                                          cluster_name = cluster_name)
    > aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
    > 
    > # Wait for the operation to complete
    > aks_target.wait_for_completion(True)
    > ```

2. A létrehozott rendszerképének üzembe helyezéséhez a [a lemezkép létrehozásának](#createimage) szakasz ebben a dokumentumban a következő kód használatával:

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

    > [!TIP]
    > Ha üzembe helyezés során hibák, használja `service.get_logs()` az AKS szolgáltatás a naplók megtekintéséhez. A naplózott információk jelezheti, hogy a hiba okát.

További információkért lásd: a dokumentáció a a [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) és [webszolgáltatás](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py) osztályokat.

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

Akkor is is megtudhatja, hogyan regisztrálja az eszközt, és az IoT-futtatókörnyezet lépésről lépésre követve telepítse a [a rövid útmutató: Az első IoT Edge-modul üzembe helyezése Linux x64 eszköz](../../iot-edge/quickstart-linux.md) dokumentumot.


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
> * [Gyors útmutató: Az első IoT Edge-modul üzembe egy Linux rendszerű eszközön](../../iot-edge/quickstart-linux.md)
> * [Gyors útmutató: Helyezze üzembe az első IoT Edge-modul a Windows-eszköz](../../iot-edge/quickstart.md)


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

## <a name="update-the-web-service"></a>A web service frissítése

A web service frissítéséhez használja a `update` metódust. A következő kód bemutatja, hogyan frissíthető egy új rendszerkép használata a web service:

```python
from azureml.core.webservice import Webservice

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)
# Update the image used by the service
service.update(image = new-image)
print(service.state)
```

> [!NOTE]
> Amikor frissít egy képet, a webszolgáltatás nem frissül automatikusan. Minden egyes szolgáltatás, amely az új lemezképet használni kívánt manuálisan kell frissítenie.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Az üzembe helyezett webszolgáltatáshoz törölheti `service.delete()`.

Kép törléséhez használja `image.delete()`.

A regisztrált modell törléséhez használja `model.delete()`.

## <a name="next-steps"></a>További lépések

* [Biztonságos SSL-lel az Azure Machine Learning-webszolgáltatások](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett gépi Tanulási modell felhasználása](how-to-consume-web-service.md)
* [Hogyan futtathat batch-előrejelzés](how-to-run-batch-predictions.md)
