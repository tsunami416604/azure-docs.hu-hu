---
title: Azure IoT biztonsági modulok (CLI) telepítése |} Microsoft Docs
description: Modulok IoT peremhálózati eszköz telepítéséhez használható Azure CLI 2.0 IoT kiterjesztése
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/08/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 02a7533cf1c06142f564ed9fd6008a25be9fc603
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036072"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli-20"></a>Az Azure CLI 2.0 Azure IoT peremhálózati modulok telepítése

Miután létrehozott egy IoT peremhálózati modulok az üzleti logikát, érdemes őket az eszközökre telepíteni kívánt működéséhez a peremhálózaton. Ha több modulokat, amelyek együttműködése gyűjti és feldolgozza, egyszerre telepítheti őket, és csatlakoztassa őket az útválasztási szabályokat deklarálható. 

Az [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forrású, több platformon is használható parancssori eszköz különböző Azure-erőforrások, például az IoT Edge felügyeletére. Ez lehetővé teszi, hogy Azure IoT Hub erőforrások, eszköz üzembe helyezési szolgáltatáspéldány és kapcsolódó hubok a kezdő verzióról kezelésére. Az új IoT-bővítmény az Azure CLI 2.0 funkcióinak körét olyan szolgáltatásokkal egészíti ki, amilyen például az eszközfelügyelet és a teljes körű IoT Edge-képességek.

Ez a cikk bemutatja, hogyan hozzon létre egy JSON-üzembe helyezési jegyzék, majd küldje le a központi telepítés IoT peremhálózati eszköz, hogy a fájl segítségével. A központi telepítés, amelynek célpontja a megosztott címkék alapján több eszközre létrehozásával kapcsolatos további információkért lásd: [telepítés és az IoT peremhálózati modulok léptékű figyelése](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT-központ](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésben. 
* Egy [IoT peremhálózati eszköz](how-to-register-device-cli.md) telepített IoT peremhálózati futtatási idő mellett.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) a saját környezetében. Az Azure CLI 2.0 legalább 2.0.24-es verzióját kell használnia. A verziószámot az `az –-version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. 
* A [IoT-bővítményt az Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Egy üzembe helyezési jegyzék konfigurálása

Egy üzembe helyezési jegyzék egy JSON-dokumentumában, hogy melyik modulokat telepíteni, hogyan közötti adatáramlás a modulok, és a modul twins kívánt tulajdonságait ismerteti. Hogyan telepítési módjától munkahelyi és hogyan hozza létre a címzetteket kapcsolatos további információkért lásd: [megérteni, hogyan IoT peremhálózati modulok használják, konfigurálhatók, és használja fel újra](module-composition.md).

Modulok használata Azure CLI 2.0 telepítéséhez mentése .txt fájlként az üzembe helyezési jegyzék helyileg. A fájl elérési útját fogja használni a következő szakaszban, a konfiguráció alkalmazása az eszköz a parancs futtatásakor. 

Íme egy egyszerű üzembe helyezési jegyzék egy modul példa:

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
               "loggingOptions": "",
               "registryCredentials": {
                 "registryName": {
                   "username": "",
                   "password": "",
                   "address": ""
                 }
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
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
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

## <a name="deploy-to-your-device"></a>Az eszköz telepítése

Modulok az eszközre, az üzembe helyezési jegyzék, a modul adatokkal konfigurált alkalmazásával telepítheti. 

Az alábbi parancs segítségével a beállítások alkalmazásának IoT peremhálózati eszköz:

   ```cli
   az iot hub apply-configuration --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Az eszköz azonosítója paraméter kis-és nagybetűket. A tartalom paraméter mutat a központi telepítés manifest mentett fájlt. 

## <a name="view-modules-on-your-device"></a>Modulok megjelenítése az eszközön

Modulok telepítése után a rendszer, ha ezek a következő paranccsal tekintheti meg: 

A modulok megtekintése az IoT Edge-eszközön:
    
   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Az eszköz azonosítója paraméter kis-és nagybetűket.

   ![Modulok listázása](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [telepítés és az IoT peremhálózati modulok léptékű figyelése](how-to-deploy-monitor.md)