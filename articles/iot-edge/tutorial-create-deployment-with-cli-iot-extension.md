---
title: "IoT-kiterjesztés használatával az Azure CLI 2.0 IoT peremeszközök modulok telepítése |} Microsoft Docs"
description: "Az IoT-bővítmény használatával az Azure CLI 2.0 IoT peremhálózati eszköz modulok telepítése"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 02/12/2018
ms.topic: article
ms.service: iot-edge
ms.custom: mvc
ms.reviewer: kgremban
ms.openlocfilehash: ce3e979428233af578d71dee5ed10103e105f4f4
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Az IoT-bővítmény használatával az Azure CLI 2.0 IoT peremhálózati eszköz modulok telepítése

[Az Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) egy nyílt forráskódú közötti platform parancssori eszköz, például az IoT-Edge Azure-erőforrások kezeléséhez. Az Azure CLI 2.0 a Windows, Linux és MacOS érhető el.

Az Azure CLI 2.0 lehetővé teszi Azure IoT Hub-erőforrások, eszköz üzembe helyezési szolgáltatáspéldány és kapcsolódó hubok nem kezelését. Az új IoT-bővítmény Azure CLI 2.0 szolgáltatások, mint a kezelés, és a teljes IoT peremhálózati képes a következőképpen színesíti.

Az oktatóanyag első lépések elvégzése Azure CLI 2.0 és az IoT-bővítmény beállításához. Majd megtanulhatja a modulok telepítése egy IoT peremhálózati eszköz használatával a rendelkezésre álló parancssori felület parancsait.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-fiók. Ha egy még nem rendelkezik, akkor [ingyenes fiók létrehozását](https://azure.microsoft.com/free/?v=17.39a) ma. 

* [Python 2.7 x vagy Python 3.x](https://www.python.org/downloads/).

* [Az Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Az Azure CLI 2.0-s verziója legalább a 2.0.24 kell lennie vagy újabb. Használjon `az –-version` érvényesítéséhez. Ebben a verzióban az bővítmény parancsok támogatja, és vezet be Knack parancs keretében. Egy egyszerű telepítése Windows módja töltse le és telepítse a [MSI](https://aka.ms/InstallAzureCliWindows).

* [Az IoT-bővítmény az Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension):
   1. Futtassa az `az extension add --name azure-cli-iot-ext` parancsot. 
   2. Telepítés után `az extension list` a jelenleg telepített bővítmények érvényesítéséhez vagy `az extension show --name azure-cli-iot-ext` az IoT-bővítmény kapcsolatos részletek megtekintéséhez.
   3. A bővítmény eltávolításához használja `az extension remove --name azure-cli-iot-ext`.


## <a name="create-an-iot-edge-device"></a>Az IoT-peremhálózati eszköz létrehozása
Ez a cikk ismerteti az IoT-Edge központi telepítés létrehozásához. A példa bemutatja, hogyan jelentkezzen be az Azure-fiókjával, hozzon létre egy Azure-erőforráscsoportot (olyan tároló, amely egy Azure megoldás kapcsolódó erőforrásokat tárol), létrehoz egy IoT-központot, hozzon létre három IoT peremhálózati eszköz identitása, címkék beállítása, majd hozza létre az IoT-Edge központi telepítése, amely az eszközök célozza. 

Jelentkezzen be az Azure-fiókjával. Miután megadta a következő bejelentkezési parancsot, egy webböngésző segítségével bejelentkezhetnek a egyszeri kódok felkéri: 

   ```cli
   az login
   ```

Hozzon létre egy új erőforráscsoportot nevű **IoTHubCLI** az USA keleti régiójában: 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![Erőforráscsoport létrehozása][2]

Létrehoz egy IoT-központot nevű **CLIDemoHub** az újonnan létrehozott erőforráscsoport:

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >Minden előfizetés egy ingyenes IoT-központ számára engedélyezett. Szabad hub létrehozása a parancssori felület parancs, cserélje le a Termékváltozat értékét `--sku F1`. Ha már van egy ingyenes hub az előfizetéshez, lesz egy hibaüzenet, amikor megpróbál létrehozni egy másikat. 

Hozzon létre egy IoT peremhálózati eszköz:

   ```cli
   az iot hub device-identity create --device-id edge001 -hub-name CLIDemoHub --edge-enabled
   ```

   ![Az IoT-peremhálózati eszköz létrehozása][4]

## <a name="configure-the-iot-edge-device"></a>Az IoT-peremhálózati eszköz konfigurálása

Központi telepítés JSON-sablon létrehozása, és helyileg mentése txt-fájlként. Az alkalmazás-konfigurációs parancs futtatásakor szüksége lesz a fájl elérési útját.

A központi telepítési JSON sablonok mindig tartalmaznia kell a két rendszer modulok, edgeAgent és edgeHub. Mellett azokat a két ezt a fájlt segítségével telepítheti további modulok az IoT peremhálózati eszközön. Az alábbi minta segítségével konfigurálása, IoT peremhálózati eszköz egy tempSensor modult:

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

A konfiguráció alkalmazásához az IoT-peremhálózati eszközön:

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

A modulok megtekintése az IoT-peremhálózati eszközön:
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![Modulok listájának megtekintése][6]

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [átjáróként IoT peremhálózati eszköz használata](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

