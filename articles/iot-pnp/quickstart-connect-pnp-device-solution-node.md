---
title: Az Azure IoT-megoldáshoz csatlakoztatott IoT Plug and Play előzetes verziójú eszközzel való együttműködés | Microsoft dokumentumok
description: A Node.js használatával csatlakozhat az Azure IoT-megoldásához csatlakoztatott IoT Plug and Play előzetes verziójához, és használhatja azt.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9962763e647faddc5a2179f304aeb3fa8ca256e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75550740"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Rövid útmutató: A megoldáshoz csatlakoztatott IoT Plug and Play előzetes verziójú eszközzel (Node.js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

Az IoT Plug and Play Preview leegyszerűsíti az IoT-t azáltal, hogy lehetővé teszi az eszköz képességeinek kommunikálását az eszköz megvalósításának ismerete nélkül. Ez a rövid útmutató bemutatja, hogyan csatlakozhat a Node.js-hez a megoldáshoz csatlakoztatott IoT Plug and Play eszközhöz, és hogyan vezérelheti azt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához szüksége van a node.js a fejlesztői gépen. Letöltheti a legújabb ajánlott verziót több platformra [a nodejs.org.](https://nodejs.org)

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot az _IoT hub kapcsolati karakterláncának_ lekérnia a hubhoz (megjegyzés későbbi használatra):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>A mintaeszköz futtatása

Ebben a rövid útmutatóban a Node.js-ben írt minta környezeti érzékelőt használhatja IoT Plug and Play eszközként. Az alábbi utasítások bemutatják, hogyan telepítheti és futtathatja az eszközt:

1. Nyisson meg egy terminálablakot az Ön által választott könyvtárban. A következő parancs végrehajtása az [Azure IoT-minták klónozásához a Node.js](https://github.com/azure-samples/azure-iot-samples-node) GitHub-tárházhoz erre a helyre:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Ez a terminálablak lesz az _eszköz_ terminálja. Nyissa meg a klónozott tárház mappáját, és keresse meg az **/azure-iot-samples-node/digital-twins/Quickstarts/Device** mappát. Telepítse az összes függőséget a következő parancs futtatásával:

    ```cmd/sh
    npm install
    ```

1. Az _eszköz kapcsolati karakterláncának konfigurálása_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Futtassa a mintát a következő paranccsal:

    ```cmd/sh
    node sample_device.js
    ```

1. Olyan üzenetek jelennek meg, amelyek szerint az eszköz bizonyos információkat küldött, és az interneten jelentette magát. Ez azt jelzi, hogy az eszköz megkezdte a telemetriai adatok küldését a hubra, és most már készen áll a parancsok és a tulajdonságfrissítések fogadására. Ne zárja be ezt a terminált, később szüksége lesz rá, hogy ellenőrizze, hogy a szolgáltatásminták is működtek.Don't close this terminal, you'll need it later to confirm the service samples also worked.

## <a name="run-the-sample-solution"></a>A mintamegoldás futtatása

Ebben a rövid útmutatóban egy minta IoT-megoldás node.js a mintaeszköz rel.

1. Nyisson meg egy másik _service_ terminálablakot (ez lesz a szervizterminálja). Nyissa meg a klónozott tárház mappáját, és keresse meg az **/azure-iot-samples-node/digital-twins/Quickstarts/Service** mappát. Telepítse az összes függőséget a következő parancs futtatásával:

    ```cmd/sh
    npm install
    ```

1. Konfigurálja az _IoT hub kapcsolati karakterláncát_ úgy, hogy a szolgáltatás csatlakozhasson hozzá:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Ingatlan olvasása

1. Amikor csatlakoztatta az _eszközt_ a termináljához, a következő üzenet jelenik meg, amely jelzi az online állapotát:

    ```cmd/sh
    reported state property as online
    ```

1. Az **/azure-iot-samples-node/digital-twins/Quickstarts/Service** mappában nyissa meg a **get_digital_twin.js**fájlt. Cserélje `<DEVICE_ID_GOES_HERE>` le a helyőrzőt az eszközazonosítóra, és mentse a fájlt.

1. Menjen a _service_ szervizterminálra, és a következő paranccsal futtassa a mintát az eszközadatok olvasásához:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. A _szervizterminál_ kimenetében görgessen az `environmentalSensor` összetevőhöz. Láthatja, `state` hogy az ingatlan _online_szolgáltatásként lett jelentve:

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

1. Nyissa meg a fájlt **update_digital_twin_property.js**.

1. A fájl elején a nagybetűs helyőrzőkkel definiált állandók halmaza van. Cserélje `<DEVICE_ID_GOES_HERE>` le a helyőrzőt a tényleges eszközazonosítóra, frissítse a fennmaradó állandókat a következő értékekkel, és mentse a fájlt:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Lépjen a _service_ szervizterminálra, és a következő paranccsal futtassa a mintát a tulajdonság frissítéséhez:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. A _szolgáltatásterminál_ kimenete a frissített eszközadatokat jeleníti meg. Görgessen `environmentalSensor` az összetevőhöz az új 42-es fényerőérték megtekintéséhez.

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
2. Lépjen vissza _service_ a szervizterminálhoz, és futtassa az alábbi parancsot az eszközadatok újbóli levezetéséhez, hogy ellenőrizze a tulajdonság frissítésének megerősítését.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. A _szolgáltatásterminál_ kimenetében `environmentalSensor` az összetevő alatt láthatja a frissített fényerő-értéket. Megjegyzés: eltarthat egy ideig, amíg az eszköz befejezi a frissítést. Ezt a lépést addig ismételheti, amíg az eszköz ténylegesen fel nem dolgozta a tulajdonságfrissítést.
    
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

1. Nyissa meg a **fájlt invoke_command.js**.

1. A fájl elején cserélje le `<DEVICE_ID_GOES_HERE>` a helyőrzőt a tényleges eszközazonosítóra. Frissítse a fennmaradó állandókat a következő értékekkel, majd mentse a fájlt:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Menjen a _service_ szervizterminálhoz. A parancs meghívásához a következő paranccsal futtatja a mintát:

    ```cmd/sh
    node invoke_command.js
    ```

1. A _szolgáltatásterminál_ kimenetének a következő visszaigazolást kell mutatnia:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Menj az _eszköz_ terminál, látod a parancsot elismerte:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play eszközt egy IoT-megoldáshoz. Ha többet szeretne tudni arról, hogyan hozhat létre olyan megoldást, amely kölcsönhatásba lép az IoT Plug and Play eszközeivel, olvassa el a következő témaköröket:

> [!div class="nextstepaction"]
> [Útmutató: Eszközhöz való csatlakozás és az eszközzel való interakció](howto-develop-solution.md)
