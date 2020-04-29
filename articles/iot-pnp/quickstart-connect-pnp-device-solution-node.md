---
title: Az Azure IoT-megoldáshoz csatlakoztatott IoT Plug and Play előzetes verziójú eszköz használata | Microsoft Docs
description: A Node. js használatával csatlakozhat egy IoT Plug and Play előnézeti eszközhöz, amely az Azure IoT-megoldáshoz van csatlakoztatva.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9962763e647faddc5a2179f304aeb3fa8ca256e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75550740"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Rövid útmutató: a megoldáshoz csatlakoztatott IoT Plug and Play előnézeti eszköz használata (node. js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

A IoT Plug and Play Preview leegyszerűsíti a IoT azáltal, hogy lehetővé teszi, hogy a mögöttes eszköz megvalósításának ismerete nélkül kommunikáljon az eszköz képességeivel. Ez a rövid útmutató bemutatja, hogyan használhatja a Node. js-t a megoldáshoz kapcsolódó IoT Plug and Play eszköz csatlakoztatásához és vezérléséhez.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a fejlesztői gépen a Node. js fájlra van szükség. A [NodeJS.org](https://nodejs.org)több platformon is letöltheti a legújabb ajánlott verziót.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot a hub _IoT hub-beli kapcsolódási karakterláncának_ lekéréséhez (jegyezze fel később a használatra):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>A minta eszköz futtatása

Ebben a rövid útmutatóban egy, a Node. js-ben írt minta környezeti érzékelőt használ a IoT Plug and Play eszközként. Az alábbi utasítások bemutatják, hogyan telepítheti és futtathatja az eszközt:

1. Nyisson meg egy terminált az Ön által választott könyvtárban. Futtassa a következő parancsot a Node. js GitHub-adattárhoz [tartozó Azure IoT-minták](https://github.com/azure-samples/azure-iot-samples-node) ezen a helyen történő klónozásához:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. A rendszer most ezt a terminált fogja használni az _eszköz_ -terminálként. Lépjen a klónozott adattár mappájába, és lépjen a **/Azure-IOT-Samples-Node/Digital-Twins/QuickStarts/Device** mappára. Telepítse az összes függőséget a következő parancs futtatásával:

    ```cmd/sh
    npm install
    ```

1. Az _eszköz-kapcsolatok karakterláncának_konfigurálása:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Futtassa a mintát a következő paranccsal:

    ```cmd/sh
    node sample_device.js
    ```

1. Üzenet jelenik meg, amely közli, hogy az eszköz elküldött néhány információt, és online jelentett. Ez azt jelzi, hogy az eszköz megkezdte a telemetria adatok küldését a központba, és most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására. Ne zárjuk be ezt a terminált, ezért később meg kell győződnie arról, hogy a szolgáltatási minták is működőképesek.

## <a name="run-the-sample-solution"></a>A minta megoldás futtatása

Ebben a rövid útmutatóban egy minta IoT megoldást használ a Node. js-ben, hogy együttműködjön a minta eszközzel.

1. Nyisson meg egy másik Terminálablak (ez lesz a _szolgáltatás_ terminálja). Lépjen a klónozott adattár mappájába, és lépjen a **/Azure-IOT-Samples-Node/Digital-Twins/QuickStarts/Service** mappára. Telepítse az összes függőséget a következő parancs futtatásával:

    ```cmd/sh
    npm install
    ```

1. Konfigurálja az _IoT hub kapcsolati karakterláncát_ , hogy a szolgáltatás csatlakozhasson hozzá:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Tulajdonság beolvasása

1. Amikor csatlakoztatta az _eszközt_ a termináljában, a következő üzenet jelenik meg, amely az online állapotot jelzi:

    ```cmd/sh
    reported state property as online
    ```

1. A **/Azure-IOT-Samples-Node/Digital-Twins/QuickStarts/Service** mappában Nyissa meg a **get_digital_twin. js**fájlt. Cserélje le `<DEVICE_ID_GOES_HERE>` a helyőrzőt az eszköz azonosítójával, és mentse a fájlt.

1. Nyissa meg a _szolgáltatás_ terminálját, és a következő paranccsal futtassa az eszköz adatainak olvasásához használt mintát:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. A _szolgáltatás_ -terminál kimenetében görgessen az `environmentalSensor` összetevőhöz. Láthatja, hogy `state` a tulajdonságot _online_jelentették:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Írható tulajdonság frissítése

1. Nyissa meg a **update_digital_twin_property. js**fájlt.

1. A fájl elején található a nagybetűs helyőrzővel definiált állandók halmaza. Cserélje le `<DEVICE_ID_GOES_HERE>` a helyőrzőt a tényleges eszköz azonosítójával, frissítse a fennmaradó állandókat a következő értékekkel, és mentse a fájlt:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Nyissa meg a _szolgáltatás_ terminálját, és a következő paranccsal futtassa a (z) tulajdonság frissítéséhez használandó mintát:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. A _szolgáltatás_ -terminál kimenete a frissített eszköz információit jeleníti meg. Görgessen az `environmentalSensor` összetevőhöz az új fényerő 42-es értékének megtekintéséhez.

    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

1. Nyissa meg az _eszköz_ terminálját, és láthatja, hogy az eszköz megkapta a frissítést:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Lépjen vissza a _szolgáltatás_ -terminálhoz, és futtassa az alábbi parancsot az eszköz adatainak újbóli beszerzéséhez, hogy megbizonyosodjon róla, hogy a tulajdonság frissítve lett.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. A _szolgáltatás_ -terminál kimenetében az `environmentalSensor` összetevő alatt látható a frissített fényerő értékének jelentése. Megjegyzés: eltarthat egy ideig, amíg az eszköz befejezi a frissítést. Ezt a lépést csak akkor ismételheti meg, ha az eszköz ténylegesen feldolgozta a tulajdonság frissítését.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>Parancs meghívása

1. Nyissa meg a **invoke_command. js**fájlt.

1. A fájl elején cserélje le a `<DEVICE_ID_GOES_HERE>` helyőrzőt a tényleges eszköz azonosítójával. Frissítse a fennmaradó állandókat a következő értékekkel, majd mentse a fájlt:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Nyissa meg a _szolgáltatás_ terminálját. A következő parancs használatával futtassa a mintát a parancs meghívásához:

    ```cmd/sh
    node invoke_command.js
    ```

1. A _szolgáltatás_ -terminál kimenetében a következő megerősítésnek kell megjelennie:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Nyissa meg az _eszköz_ terminálját, és láthatja, hogy a parancs meg lett ismerve:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztatható egy IoT Plug and Play-eszköz egy IoT-megoldáshoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Útmutató: Kapcsolódás az eszközhöz](howto-develop-solution.md)
