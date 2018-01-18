---
title: "IoT-kiterjesztés használatával az Azure CLI 2.0 IoT peremeszközök modulok telepítése |} Microsoft Docs"
description: "Az IoT-bővítmény használatával az Azure CLI 2.0 IoT peremhálózati eszköz modulok telepítése"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 26067187864f9a2a4c85c953ae8aca888458d245
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Az IoT-bővítmény használatával az Azure CLI 2.0 IoT peremhálózati eszköz modulok telepítése

[Az Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) egy nyílt forráskódú közötti platform parancssori eszköz, például az IoT-Edge Azure-erőforrások kezeléséhez. Az Azure CLI 2.0 a Windows, Linux és MacOS érhető el.

Az Azure CLI 2.0 lehetővé teszi Azure IoT Hub-erőforrások, eszköz üzembe helyezési szolgáltatáspéldány és kapcsolódó hubok nem kezelését. Az új IoT-bővítmény Azure CLI 2.0 szolgáltatások, mint a kezelés, és a teljes IoT peremhálózati képes a következőképpen színesíti.

Az oktatóanyag első lépések elvégzése Azure CLI 2.0 és az IoT-bővítmény beállításához. Majd megtanulhatja a modulok telepítése egy IoT peremhálózati eszköz használatával a rendelkezésre álló parancssori felület parancsait.

## <a name="installation"></a>Telepítés 

### <a name="step-1---install-python"></a>1. lépés – Install Python

[Python 2.7 x vagy Python 3.x](https://www.python.org/downloads/) szükséges.

### <a name="step-2---install-azure-cli-20"></a>– 2. lépés: az Azure CLI 2.0 telepítése

Kövesse a [telepítési útmutató](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) beállítani az Azure CLI 2.0 a környezetben. Az Azure CLI 2.0-s verziója legalább a 2.0.24 kell lennie vagy újabb. Használjon `az –version` érvényesítéséhez. Ebben a verzióban az bővítmény parancsok támogatja, és vezet be Knack parancs keretében. Egy egyszerű telepítése Windows módja töltse le és telepítse a [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>3. lépés – telepítés IoT-bővítmény

[Az IoT-bővítmény fontos](https://github.com/Azure/azure-iot-cli-extension) többféle módon, a bővítmény telepítéséhez ismerteti. A legegyszerűbb módszer `az extension add --name azure-cli-iot-ext`. A telepítés után is használhatja `az extension list` a jelenleg telepített bővítmények érvényesítéséhez vagy `az extension show --name azure-cli-iot-ext` az IoT-bővítmény kapcsolatos részletek megtekintéséhez. A bővítmény eltávolításához használhatja `az extension remove --name azure-cli-iot-ext`.


## <a name="deploy-modules-to-an-iot-edge-device"></a>Az IoT-peremhálózati eszköz modulok telepítése
Ebben az oktatóanyagban, megtudhatja, hogyan egy IoT peremhálózati központi telepítés létrehozásához. A példa bemutatja, hogyan lehet bejelentkezni az Azure-fiókjával, hozzon létre egy Azure-erőforráscsoportot (olyan tároló, amely egy Azure megoldás kapcsolódó erőforrásokat tárol), létrehoz egy IoT-központot, hozzon létre három IoT peremhálózati eszköz identitása, címkék beállítása, majd hozza létre az IoT-Edge központi telepítése, amely az eszközök célozza. Hajtsa végre a megkezdése előtt a fentiekben ismertetett telepítési lépéseit. Ha nem rendelkezik Azure-fiókkal, még akkor is [ingyenes fiók létrehozását](https://azure.microsoft.com/free/?v=17.39a) ma. 


### <a name="1-login-to-the-azure-account"></a>1. Bejelentkezés az Azure-fiókjába
  
    az login

![bejelentkezés][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Hozzon létre egy erőforráscsoportot IoTHubBlogDemo eastus

    az group create -l eastus -n IoTHubBlogDemo

![Erőforráscsoport létrehozása][2]


### <a name="3-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>3. Az IoT-központ blogDemoHub alatt az újonnan létrehozott erőforráscsoport létrehozása

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Az IoT Hub létrehozása][3]


### <a name="4-create-an-iot-edge-device"></a>4. Az IoT-peremhálózati eszköz létrehozása

    az iot hub device-identity create -d edge001 -n blogDemoHub --edge-enabled

![Az IoT-peremhálózati eszköz létrehozása][4]

### <a name="5-apply-configuration-to-the-iot-edge-device"></a>5. Az IoT-peremhálózati eszközön konfigurációjának alkalmazása

Mentse helyileg a központi telepítés JSON-sablon egy txt-fájlba. Az alkalmazás-konfigurációs parancs futtatásakor szüksége lesz a fájl elérési útját.

Alább az egy minta telepítési JSON-sablon több tempSensor modul tartalmazza:

```json
{
  "moduleContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": ""
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-agent:1.0-preview",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-hub:1.0-preview",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "tempSensor": {
      "properties.desired": {}
    }
  }
}
```

    az iot hub apply-configuration --device-id edge001 --hub-name blogDemoHub --content C:\<yourLocation>\edgeconfig.txt

![Konfigurációjának alkalmazása][5]

### <a name="6-list-modules"></a>6. Modulok listájának megtekintése
    
    az iot hub module-identity list --device-id edge001 --hub-name blogDemoHub

![Modulok listájának megtekintése][6]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure-függvény szűrése a nyers adatokat az IoT-peremhálózati eszköz által generált kódot tartalmazó létrehozott. Tartsa felfedezése Azure IoT él, ismerje meg az átjáróként IoT peremhálózati eszköz használatával. 

> [!div class="nextstepaction"]
> [Az IoT-peremhálózati átjáró eszköz létrehozása](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-create-deployment-with-cli-iot-extension/login.jpg
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.jpg
[3]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-hub.jpg
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[5]: ./media/tutorial-create-deployment-with-cli-iot-extension/apply-configuration.PNG
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.PNG

