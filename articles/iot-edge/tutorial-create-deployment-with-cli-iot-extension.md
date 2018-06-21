---
title: Modulok üzembe helyezése IoT Edge-eszközökön az Azure CLI 2.0-hoz készült IoT-bővítménnyel | Microsoft Docs
description: Modulok üzembe helyezése IoT Edge-eszközön az Azure CLI 2.0-hoz készült IoT-bővítménnyel
author: chrissie926
manager: ''
ms.author: menchi
ms.date: 03/02/2018
ms.topic: tutorial
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
md.custom: mvc
ms.openlocfilehash: deee54fe5d11d6d1cf5485357f853b1cb078f96d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631581"
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Modulok üzembe helyezése IoT Edge-eszközön az Azure CLI 2.0-hoz készült IoT-bővítménnyel

Az [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forrású, több platformon is használható parancssori eszköz különböző Azure-erőforrások, például az IoT Edge felügyeletére. Az Azure CLI 2.0 Windows, Linux és Mac OS rendszereken érhető el.

Az Azure CLI 2.0 egy használatra kész megoldás Azure IoT Hub-erőforrások, eszközregisztrációs szolgáltatáspéldányok és csatolt központok felügyeletéhez. Az új IoT-bővítmény az Azure CLI 2.0 funkcióinak körét olyan szolgáltatásokkal egészíti ki, amilyen például az eszközfelügyelet és a teljes körű IoT Edge-képességek.

Ebben a cikkben az Azure CLI 2.0 és az IoT-bővítmény beállítását ismertetjük. Ezt követően a modulok IoT Edge-eszközön, a rendelkezésre álló CLI-parancsokkal történő üzembe helyezésével fog megismerkedni.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-fiók. Ha még nincs fiókja, akár most is [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?v=17.39a). 

* [Python 2.7x vagy Python 3.x](https://www.python.org/downloads/).

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) a saját környezetében. Az Azure CLI 2.0 legalább 2.0.24-es verzióját kell használnia. A verziószámot az `az –-version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. Windows rendszeren a legegyszerűbb megoldás a telepítésre az [MSI](https://aka.ms/InstallAzureCliWindows) letöltése és telepítése.

* [Az Azure CLI 2.0-hoz készült IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension):
   1. Futtassa az `az extension add --name azure-cli-iot-ext` parancsot. 
   2. A telepítés után az `az extension list` paranccsal ellenőrizheti az aktuálisan telepített bővítményeket, az `az extension show --name azure-cli-iot-ext` paranccsal pedig megtekintheti az IoT-bővítmény adatait.
   3. A bővítményt az `az extension remove --name azure-cli-iot-ext` paranccsal távolíthatja el.


## <a name="create-an-iot-edge-device"></a>IoT Edge-eszköz létrehozása
Ez a cikk az IoT Edge üzembe helyezésére vonatkozó utasításokat tartalmazza. A példában bemutatjuk, hogyan jelentkezhet be Azure-fiókjába, hogyan hozhat létre Azure-erőforráscsoportot (egy Azure-megoldás kapcsolt erőforrásait tartalmazó tárolót), egy IoT Hubot és három IoT Edge-eszközidentitást, hogyan adhatja meg a címkéket, majd hogyan hozhat létre egy olyan IoT Edge-beli üzemelő példányt, amely ezeket az eszközöket használja céleszközként. 

Jelentkezzen be az Azure-fiókjába. Az alábbi bejelentkezési parancs megadása után a rendszer felkéri, hogy jelentkezzen be egy webböngésző használatával a következő egyszer használatos kóddal: 

   ```cli
   az login
   ```

Hozzon létre egy **IoTHubCLI** nevű új erőforráscsoportot az USA keleti régiójában: 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![Erőforráscsoport létrehozása][2]

Hozzon létre egy **CLIDemoHub** IoT-központot az újonnan létrehozott erőforráscsoportban:

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >Mindegyik előfizetés egy-egy ingyenes IoT-központtal rendelkezhet. Ha a CLI-paranccsal kíván létrehozni ingyenes központot, akkor a termékváltozat értékének helyére írja be a következőt: `--sku F1`. Ha az előfizetése már tartalmaz egy ingyenes központot, akkor hibaüzenetet fog kapni, ha megpróbál egy másodikat is létrehozni. 

IoT Edge-eszköz létrehozása:

   ```cli
   az iot hub device-identity create --device-id edge001 --hub-name CLIDemoHub --edge-enabled
   ```

   ![IoT Edge-eszköz létrehozása][4]

## <a name="configure-the-iot-edge-device"></a>Az IoT Edge-eszköz konfigurálása

Hozzon létre egy üzembehelyezési JSON-sablont, és mentse azt helyileg TXT-fájlként. Az apply-configuration parancs futtatásához szüksége lesz a fájl elérési útjára.

Az üzembehelyezési JSON-sablonnak minden esetben tartalmaznia kell a két rendszermodult (edgeAgent és edgeHub). E két modul mellett ennek a fájlnak a használatával helyezhet üzembe további modulokat az IoT Edge-eszközön. A következő minta használatával konfigurálhatja az IoT Edge-eszközt egyetlen tempSensor modullal:

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
                 "image": "microsoft/azureiotedge-agent:1.0-preview",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-hub:1.0-preview",
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
                 "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
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

A konfiguráció alkalmazása az IoT Edge-eszközön:

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

A modulok megtekintése az IoT Edge-eszközön:
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![Modulok listázása][6]

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [használhatja az IoT Edge-eszközt átjáróként](how-to-create-transparent-gateway.md).

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

