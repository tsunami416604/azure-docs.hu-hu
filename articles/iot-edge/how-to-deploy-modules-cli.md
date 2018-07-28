---
title: Az Azure IoT Edge-modulok (CLI) telepítése |} A Microsoft Docs
description: Az IoT-bővítmény, az Azure CLI 2.0 használatával a modulok IoT Edge-eszköz üzembe helyezése
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/27/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 29c11139a2c773db2d26bf44984ad4dc72f2d870
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324605"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli-20"></a>Azure CLI 2.0-val az Azure IoT Edge-modulok telepítése

Miután létrehozott egy IoT Edge modulok az üzleti logikával rendelkező, érdemes őket az eszközökre telepíteni kívánt megfelelően működjenek a peremhálózaton. Ha több modulokat, amelyek együttműködve gyűjtenek és dolgoznak fel adatokat, és egyszerre telepítheti őket, és deklarálja az útválasztási szabályokat, amelyek csatlakoztathatja őket. 

Az [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forrású, több platformon is használható parancssori eszköz különböző Azure-erőforrások, például az IoT Edge felügyeletére. Lehetővé teszi, hogy az Azure IoT Hub-erőforrások, eszközregisztrációs szolgáltatáspéldányok és csatolt központok beépített kezelése. Az új IoT-bővítmény az Azure CLI 2.0 funkcióinak körét olyan szolgáltatásokkal egészíti ki, amilyen például az eszközfelügyelet és a teljes körű IoT Edge-képességek.

Ez a cikk bemutatja, hogyan hozzon létre egy JSON-manifest nasazení, majd küldje le az üzembe helyezés IoT Edge-eszköz fájl használatával. A megosztott címkék alapján több eszköz célzó központi telepítés létrehozásával kapcsolatos információkért lásd: [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy [az IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében. 
* Egy [IoT Edge-eszköz](how-to-register-device-cli.md) az telepítve van az IoT Edge-futtatókörnyezet.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) a saját környezetében. Az Azure CLI 2.0 legalább 2.0.24-es verzióját kell használnia. A verziószámot az `az –-version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. 
* A [IoT-bővítmény az Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>A manifest nasazení konfigurálása

A manifest nasazení egy JSON-dokumentum, amely azt ismerteti, hogy mely modulok üzembe helyezéséhez a modulokat, és az ikermodulokkal tulajdonságaiként közti adatfolyamok. Hogyan alkalmazásjegyzékeket az üzembe helyezési a munkahelyi, és hogyan hozhat létre, azokat kapcsolatos további információkért lásd: [megismerheti, hogyan IoT Edge-modulok használják, konfigurálhatók, és újra felhasználható](module-composition.md).

Azure CLI 2.0 használatával modulok üzembe helyezéséhez egy .JSON kiterjesztésű fájlt mentse helyileg manifest nasazení. A fájl elérési útja fogja használni a következő szakaszban, amikor futtatja a parancsot a alkalmazni a konfigurációt az eszközre. 

Íme egy modult az alapszintű üzemelő példányhoz jegyzék példaként:

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
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
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
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

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

A modul információkkal konfigurált manifest nasazení alkalmazása modulok üzembe az eszközre. 

Módosítsa a könyvtárakat a mappát, ahol a manifest nasazení van. Ha a VS Code IoT Edge-sablonok egyikét, használja a `deployment.json` fájlt a **config** mappát a megoldás címtár. Ne használja a `deployment.template.json` fájlt. 

A következő parancsot használja a alkalmazni a konfigurációt egy IoT Edge-eszközön:

   ```cli
   az iot hub apply-configuration --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Az eszköz azonosító paraméter értéke a kis-és nagybetűket. A tartalom paraméter mutat az üzembe helyezés manifest mentett fájlt. 

   ![Modulok beállítása](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Modulok megjelenítése az eszközön

Miután telepítette a modulokat az eszközön, megtekintheti azokat a következő paranccsal: 

A modulok megtekintése az IoT Edge-eszközön:
    
   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Az eszköz azonosító paraméter értéke a kis-és nagybetűket.

   ![Modulok listázása](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md)