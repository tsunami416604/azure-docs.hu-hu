---
title: Modulok üzembe helyezése a Visual Studio-kód használatával – Azure IoT Edge
description: A Visual Studio-kód IoT-bővítményhasználatával automatikus üzembe helyezéseket hozhat létre az IoT Edge-eszközök csoportjai számára.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 08299a589dc6e8f768cba7ef976e109ef1fb69d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774132"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>IoT Edge-modulok üzembe helyezése a Visual Studio-kód használatával

Az **IoT Edge automatikus központi telepítését** a Visual Studio Code használatával hozhatja létre, amely egyszerre több eszköz folyamatos központi telepítését kezeli. Az IoT Edge automatikus üzembe helyezései az IoT Hub [automatikus eszközfelügyeleti](/azure/iot-hub/iot-hub-automatic-device-management) szolgáltatásának részét képezik. A központi telepítések olyan dinamikus folyamatok, amelyek lehetővé teszik több modul több eszközre történő üzembe helyezését. Nyomon követheti a modulok állapotát és állapotát, és szükség esetén módosíthatja azokat.

További információ: [Az IoT Edge automatikus központi telepítései egyetlen eszközökön vagy nagy méretekben](module-deployment-monitoring.md)című témakörben található.

Ebben a cikkben beállíthatja a Visual Studio-kódot és az IoT-bővítményt. Ezután megtudhatja, hogyan telepítheti a modulokat az IoT Edge-eszközök egy készletére.

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT-központ](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésben.
* IoT [Edge-eszköz](how-to-register-device.md#register-with-visual-studio-code) az IoT Edge futásidejű telepítve.
* [Visual Studio kód](https://code.visualstudio.com/).
* [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) a Visual Studio-kódhoz.

## <a name="sign-in-to-access-your-iot-hub"></a>Bejelentkezés az IoT-központ eléréséhez

Használhatja az Azure IoT-bővítmények a Visual Studio-kód műveleteket a hub. Ahhoz, hogy ezek a műveletek működjenek, be kell jelentkeznie az Azure-fiókjába, és ki kell választania azt az IoT-központot, amelyen dolgozik.

1. A Visual Studio-kódban nyissa meg az **Intéző** nézetet.

1. Az Intéző alján bontsa ki az **Azure IoT Hub** szakaszt.

1. Kattintson a **...** az **Azure IoT Hub** szakasz fejlécében. Ha nem látja a három pontot, vigye az egérmutatót a fejléc fölé.

1. Válassza **az IoT-központ kiválasztása**lehetőséget.

1. Ha nincs bejelentkezve az Azure-fiókjába, kövesse az utasításokat.

1. Válassza ki az Azure-előfizetését.

1. Válassza ki az IoT-központot.

## <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzékfájl konfigurálása

A központi telepítési jegyzékfájl egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni. Azt is leírja, hogyan adatfolyamok a modulok között, és a modul twins kívánt tulajdonságait. További [információ: Ismerje meg, hogyan telepítheti a modulokat, és hogyan hozhat létre útvonalakat az IoT Edge-ben.](module-composition.md)

Ha modulokat szeretne telepíteni a Visual Studio-kód használatával, mentse a központi telepítési jegyzéket helyileg . JSON fájl. A parancs futtatásakor meg kell adnia a helyét, hogy a konfigurációt az eszközre alkalmazhassa.

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

Ha meg kell határoznia, hogy jelenleg mely IoT Edge-eszközöket konfigurálhatja, futtassa az **IoT Edge: Get Device Info** parancsot.

## <a name="identify-devices-with-target-conditions"></a>A célfeltételekkel rendelkező eszközök azonosítása

A központi telepítést fogadó IoT Edge-eszközök azonosításához meg kell adnia egy célfeltételt. A célfeltétel akkor teljesül, ha a megadott feltételeket egy deviceId, címkeérték vagy jelentett tulajdonságérték egyezik.

A címkéket az ikereszközben állíthatja be. Íme egy példa egy ikereszközre, amely címkékkel rendelkezik:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Ez az eszköz akkor kap központi telepítést, ha a központi telepítés célfeltétele `tag.location.building = '20'`olyan kifejezést tartalmaz, amely megfelel a címke egyik értékének, például .

Ha egy adott eszközt a címkéktől vagy más értékektől `deviceId` függetlenül szeretne megcélozni, csak adja meg a célfeltételt.

Íme néhány további példa:

* deviceId ='linuxprod1'
* deviceId = 'linuxprod1' VAGY deviceId = 'linuxprod2' OR deviceId = 'linuxprod3'
* címkék.környezet ='prod'
* tags.environment = 'prod' ÉS tags.location = 'westus2'
* tags.environment = 'prod' OR tags.location = 'westus2'
* tags.operator = 'John' AND tags.environment = 'prod' ÉS NOT deviceId = 'linuxprod1'

A részleteket lásd a [célfeltételben.](module-deployment-monitoring.md#target-condition) Az ikereszközök és -címkék ről az [Eszközök ikreinek megértése és használata az IoT Hubban](../iot-hub/iot-hub-devguide-device-twins.md)című témakörben talál további információt.

### <a name="edit-the-device-twin"></a>Az ikereszköz szerkesztése

A címkék konfigurálásához szerkesztheti az ikereszközt a Visual Studio-kódban. A **Nézet** menüben válassza a **Parancspaletta parancsot,** és futtassa az **IoT Edge: Device Twin szerkesztése** parancsot. Válassza ki az IoT Edge-eszközt, és megjelenik az ikereszköz.

Ebben a példában nincsenek megadva címkék. Cserélje le az `"tags": {}` aktuális üres szakaszt a saját címkedefiníciójára.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

A helyi fájl mentése után futtassa az **IoT Edge: Device Twin frissítése** parancsot.

## <a name="create-deployment-at-scale"></a>Üzembe helyezés létrehozása nagy méretekben

Miután konfigurálta a központi telepítési jegyzékfájl és a konfigurált címkék az ikereszköz, készen áll a telepítésre.

1. A **Nézet** menüben válassza a **Parancspaletta,** és válassza ki az **Azure IoT Edge: Deployment létrehozása méretezéskor** parancsot.

1. Keresse meg a használni kívánt telepítési jegyzékfájl JSON-fájlját, és kattintson **az Edge Deployment Manifest kiválasztása parancsra.**

1. Adja meg az értékeket **a**rendszernek a parancssori parancsával kezdve.

   ![Központi telepítési azonosító megadása](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Adja meg a következő paraméterek értékeit:

  | Paraméter | Leírás |
  | --- | --- |
  | Központi telepítési azonosító | Az IoT hubban létrehozandó központi telepítés neve. Adjon a központi telepítésegyedi nevet, amely legfeljebb 128 kisbetűs. Kerülje a szóközöket `& ^ [ ] { } \ | " < > /`és a következő érvénytelen karaktereket: . |
  | Célfeltétel | Adja meg a célfeltételt annak meghatározásához, hogy mely eszközök lesznek megcélozva ezzel a központi telepítéssel.A feltétel az ikercímkéken vagy az ikereszköz jelentett tulajdonságain alapul, és meg kell egyeznie a kifejezés formátumának.`tags.environment='test' and properties.reported.devicemodel='4000x'`Például. |
  | Prioritás |  Pozitív egész szám. Ha két vagy több központi telepítés ugyanarra az eszközre irányul, a legmagasabb numerikus értékkel rendelkező központi telepítés lesz érvényben. |

  A prioritás megadása után a terminálnak a következő ábrázoláshoz hasonló kimenetet kell megjelenítenie:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Központi telepítések figyelése és módosítása

Az [Azure CLI](how-to-deploy-monitor-cli.md#monitor-a-deployment) vagy az [Azure Portal](how-to-deploy-monitor.md#monitor-a-deployment) használatával figyelheti, módosíthatja és törölheti a központi telepítéseket. Mindkettő metrikákat biztosít a központi telepítések.

## <a name="next-steps"></a>További lépések

További információ a [modulok IoT Edge-eszközökre való üzembe helyezéséről.](module-deployment-monitoring.md)
