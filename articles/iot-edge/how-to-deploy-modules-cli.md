---
title: Modulok parancssorból – Azure IoT Edge üzembe helyezése |} A Microsoft Docs
description: Az Azure CLI-hez az IoT-bővítmény használatával üzembe helyezhet modulok IoT Edge-eszköz
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 766b51f208e7e8f4a49109e32864f2726b8ccd63
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156442"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Az Azure CLI-vel az Azure IoT Edge-modulok telepítése

Miután létrehozott egy IoT Edge modulok az üzleti logikával rendelkező, érdemes őket az eszközökre telepíteni kívánt megfelelően működjenek a peremhálózaton. Ha több modulokat, amelyek együttműködve gyűjtenek és dolgoznak fel adatokat, és egyszerre telepítheti őket, és deklarálja az útválasztási szabályokat, amelyek csatlakoztathatja őket.

[Az Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forráskódú közötti platform parancssori eszköz, például az IoT Edge Azure-erőforrások kezeléséhez. Lehetővé teszi, hogy az Azure IoT Hub-erőforrások, eszközregisztrációs szolgáltatáspéldányok és csatolt központok beépített kezelése. Az új IoT-bővítmény Azure CLI-vel bővíti, például az Eszközfelügyelet és teljes körű IoT Edge-képességek.

Ez a cikk bemutatja, hogyan hozzon létre egy JSON-manifest nasazení, majd küldje le az üzembe helyezés IoT Edge-eszköz fájl használatával. A megosztott címkék alapján több eszköz célzó központi telepítés létrehozásával kapcsolatos információkért lásd: [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy [az IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében.
* Egy [IoT Edge-eszköz](how-to-register-device-cli.md) az telepítve van az IoT Edge-futtatókörnyezet.
* [Az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Legalább az Azure CLI 2.0.24-es verzióját kell vagy újabb. A verziószámot az `az –-version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer.
* A [IoT-bővítmény az Azure CLI-vel](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>A manifest nasazení konfigurálása

A manifest nasazení egy JSON-dokumentum, amely azt ismerteti, hogy mely modulok üzembe helyezéséhez a modulokat, és az ikermodulokkal tulajdonságaiként közti adatfolyamok. Hogyan alkalmazásjegyzékeket az üzembe helyezési a munkahelyi, és hogyan hozhat létre, azokat kapcsolatos további információkért lásd: [megismerheti, hogyan IoT Edge-modulok használják, konfigurálhatók, és újra felhasználható](module-composition.md).

Az Azure CLI-vel modulok üzembe helyezéséhez egy .JSON kiterjesztésű fájlt mentse helyileg manifest nasazení. A fájl elérési útja fogja használni a következő szakaszban, amikor futtatja a parancsot a alkalmazni a konfigurációt az eszközre.

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

Módosítsa a könyvtárakat a mappát, ahol a manifest nasazení van. Ha a VS Code IoT Edge-sablonok egyikét, használja a `deployment.json` fájlt a **config** mappát a megoldás címtár, és nem a `deployment.template.json` fájl.

A következő parancsot használja a alkalmazni a konfigurációt egy IoT Edge-eszközön:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Az eszköz azonosító paraméter értéke a kis-és nagybetűket. A tartalom paraméter mutat az üzembe helyezés manifest mentett fájlt.

   ![az iot edge set-modul kimenete](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Modulok megjelenítése az eszközön

Miután telepítette a modulokat az eszközön, megtekintheti azokat a következő paranccsal:

A modulok megtekintése az IoT Edge-eszközön:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Az eszköz azonosító paraméter értéke a kis-és nagybetűket.

   ![az iot hub modul-identity list kimeneti](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok](how-to-deploy-monitor.md)
