---
title: Modulok üzembe helyezése az Azure CLI parancssorból – Azure IoT Edge
description: Az Azure CLI az Azure IoT extension segítségével leküldéses egy IoT Edge-modul az IoT Hub az IoT Edge-eszköz, egy központi telepítési jegyzékfájl által konfigurált.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 619ba7cb2d99e0137fd1834096dd5b66ffcd6ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240389"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Azure IoT Edge-modulok üzembe helyezése az Azure CLI-vel

Miután létrehozta az IoT Edge-modulokat az üzleti logikával, szeretné telepíteni őket az eszközökre, hogy a peremhálózaton működjenek. Ha több modullal rendelkezik, amelyek együtt dolgoznak az adatok gyűjtésében és feldolgozásában, egyszerre telepítheti őket, és deklarálhatja az őket összekötő útválasztási szabályokat.

[Az Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forráskódú, platformfüggetlen parancssori eszköz az Azure-erőforrások, például az IoT Edge kezelésére. Lehetővé teszi az Azure IoT Hub-erőforrások, az eszközkiépítési szolgáltatáspéldányok és a csatolt hubok kezelését a dobozból. Az új IoT-bővítmény olyan funkciókkal gazdagítja az Azure CLI-t, mint az eszközkezelés és a teljes IoT Edge-képesség.

Ez a cikk bemutatja, hogyan hozhat létre egy JSON-telepítési jegyzékfájl, majd használja ezt a fájlt, hogy leküldéses a központi telepítés egy IoT Edge-eszköz. Az [IoT Edge-modulok üzembe helyezése és figyelése nagy méretekben című](how-to-deploy-monitor-cli.md) témakörben talál további információt egy olyan központi telepítés létrehozásáról, amely több eszközt céloz meg a megosztott címkék alapján.

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT-központ](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésben.
* IoT [Edge-eszköz](how-to-register-device.md#register-with-the-azure-cli) az IoT Edge futásidejű telepítve.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Az Azure CLI-verziónak legalább 2.0.70-nek kell lennie. A verziószámot az `az --version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer.
* Az [Azure CLI IoT-bővítménye.](https://github.com/Azure/azure-iot-cli-extension)

## <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzékfájl konfigurálása

A központi telepítési jegyzékfájl egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan adatfolyamok a modulok között, és a modul twins kívánt tulajdonságait. A központi telepítési jegyzékek működéséről és létrehozásáról további információt [az IoT Edge-modulok használatba és újrafelhasználtkezelésének ismertetése](module-composition.md)című témakörben talál.

A modulok üzembe helyezéséhez az Azure CLI használatával mentse a központi telepítési jegyzékfájl helyileg .json fájlként. A parancs futtatásakor a következő szakaszban lévő fájlelérési utat fogja használni a konfiguráció eszközre való alkalmazásához.

Íme egy egyszerű központi telepítési jegyzékfájl egy modullal példaként:

```json
{
  "content": {
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
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
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
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

A moduloküzembe helyezése az eszközre a moduladatokkal konfigurált központi telepítési jegyzék alkalmazásával.

Módosítsa a könyvtárakat abba a mappába, ahová a központi telepítési jegyzékfájl kerül mentésre. Ha a VS Code IoT Edge-sablonok egyikét használta, a `deployment.json` megoldáskönyvtár **konfigurációs** `deployment.template.json` mappájában lévő fájlt használja, ne a fájlt.

A következő paranccsal alkalmazhatja a konfigurációt egy IoT Edge-eszközre:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Az eszközazonosító paraméter ben a kis- és nagybetűk et kell figyelembe. A tartalomparaméter a mentett telepítési jegyzékfájlra mutat.

   ![az iot edge set-modules kimenet](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Modulok megtekintése az eszközön

Miután telepítette a modulokat az eszközre, mindegyiket megtekintheti a következő paranccsal:

A modulok megtekintése az IoT Edge-eszközön:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Az eszközazonosító paraméter ben a kis- és nagybetűk et kell figyelembe.

   ![az iot hub modulidentitás-lista kimenete](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [telepítheti és figyelheti az IoT Edge-modulokat nagy méretekben](how-to-deploy-monitor.md)
