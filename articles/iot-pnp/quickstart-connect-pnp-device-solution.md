---
title: Az Azure IoT-megoldáshoz csatlakoztatott IoT Plug and Play előzetes verziójú eszköz használata | Microsoft Docs
description: A Node. js használatával csatlakozhat egy IoT Plug and Play előnézeti eszközhöz, amely az Azure IoT-megoldáshoz van csatlakoztatva.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: a082e4b7896b317bf2b28971d3693bada95a3445
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806556"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution"></a>Gyors útmutató: A megoldáshoz csatlakoztatott IoT Plug and Play előnézeti eszköz használata

A IoT Plug and Play Preview leegyszerűsíti a IoT azáltal, hogy lehetővé teszi, hogy a mögöttes eszköz megvalósításának ismerete nélkül kommunikáljon az eszköz képességeivel. Ez a rövid útmutató bemutatja, hogyan használhatja a Node. js-t a megoldáshoz kapcsolódó IoT Plug and Play eszköz csatlakoztatásához és vezérléséhez.

## <a name="prerequisites"></a>Előfeltételek

Töltse le és telepítse a Node. js-t a [NodeJS.org](https://nodejs.org)webhelyről.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>IoT hub előkészítése

A rövid útmutató elvégzéséhez szüksége lesz egy Azure IoT hub-ra is az Azure-előfizetésében. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> A nyilvános előzetes verzióban a IoT Plug and Play funkciói csak az **USA középső**régiójában, Észak- **Európában**és Kelet- **japán** régióban létrehozott IoT-hubokon érhetők el.

Adja hozzá a Microsoft Azure IoT bővítményt az Azure CLI-hez:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

A következő parancs futtatásával hozza létre az eszköz identitását az IoT hub-ban. Cserélje le a **YourIoTHubName** és a **YourDevice** a tényleges nevekre. Ha nem rendelkezik IoT Hubval, az [alábbi útmutatást](../iot-hub/iot-hub-create-using-cli.md) követve hozhat létre egyet:

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Futtassa az alábbi parancsokat az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Futtassa az alábbi parancsokat a hub _IoT hub-kapcsolódási karakterláncának_ lekéréséhez:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="connect-your-device"></a>Az eszköz csatlakoztatása

Ebben a rövid útmutatóban egy, a Node. js-ben írt minta környezeti érzékelőt használ a IoT Plug and Play eszközként. Az alábbi utasítások bemutatják, hogyan telepítheti és futtathatja az eszközt:

1. A GitHub-tárház klónozása:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. A terminálban nyissa meg a klónozott tárház gyökérkönyvtárát, lépjen a **/Azure-IOT-Samples-Node/Digital-Twins/QuickStarts/Device** mappába, majd telepítse az összes függőséget a következő parancs futtatásával:

    ```cmd/sh
    npm install
    ```

1. Az _eszköz-kapcsolatok karakterláncának_konfigurálása:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<your device connection string>
    ```

1. Futtassa a mintát a következő paranccsal:

    ```cmd/sh
    node sample_device.js
    ```

1. Láthatja, hogy az eszköz elküldte a telemetria és annak tulajdonságait. Az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására. Ne zárjuk be ezt a terminált, ezért később meg kell győződnie arról, hogy a szolgáltatási minták is működőképesek.

## <a name="build-the-solution"></a>A megoldás létrehozása

Ebben a rövid útmutatóban egy minta IoT megoldást használ a Node. js-ben, hogy együttműködjön a minta eszközzel.

1. Nyisson meg egy másik terminált. Lépjen a klónozott adattár mappájába, és lépjen a **/Azure-IOT-Samples-Node/Digital-Twins/QuickStarts/Service** mappára. Telepítse az összes függőséget a következő parancs futtatásával:

    ```cmd/sh
    npm install
    ```

1. A _hub-kapcsolatok karakterláncának_konfigurálása:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<your hub connection string>
    ```

### <a name="read-a-property"></a>Tulajdonság beolvasása

1. Amikor az eszközt a terminálban kapcsolja össze, a következő üzenet jelenik meg:

    ```cmd/sh
    reported state property as online
    ```

1. Nyissa meg a **get_digital_twin. js**fájlt. Cserélje le `deviceID` az eszközt az eszköz azonosítójával, és mentse a fájlt.

1. Nyissa meg a Service Sample futtatásához megnyitott terminált, és futtassa a következő parancsot:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. A kimenetben, a _environmentalSensor_ összetevő alatt látható, hogy ugyanazt az állapotot jelentették:

    ```JSON
    reported state property as online
    ```

### <a name="update-a-writable-property"></a>Írható tulajdonság frissítése

1. Nyissa meg a **update_digital_twin_property. js**fájlt.

1. A fájl elején található a nagybetűs helyőrzővel definiált állandók halmaza. Cserélje le a **deviceID** értéket a tényleges eszköz-azonosítójával, frissítse az állandókat a következő értékekkel, és mentse a fájlt:

    ```javascript
    const componentName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 60;
    ```

1. Lépjen a Service minta futtatásához megnyitott terminálra, és futtassa a következő parancsot a minta futtatásához:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. A terminálon az eszközhöz társított digitális Twin-információk láthatók. Keresse meg az _environmentalSensor_összetevőt, az új fényerő 60-as érték jelenik meg.

    ```json
        "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
          "brightness": {
            "reported": {
              "value": 60,
              "desiredState": {
                "code": 200,
                "version": 14,
                "description": "helpful descriptive text"
              }
            },
            "desired": {
              "value": 60
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
    Received an update for brightness: 60
    updated the property
    ```
2. Lépjen vissza a _szolgáltatás_ -terminálhoz, majd futtassa újra az alábbi parancsot a tulajdonság frissítésének megerősítéséhez.
    
    ```cmd/sh
    node get_digital_twin.js
    ```
3. A kimenetben, a environmentalSensor összetevő alatt látható, hogy a frissített fényerő értéke jelentett. Megjegyzés: eltarthat egy ideig, amíg az eszköz befejezi a frissítést. Ezt a lépést csak akkor ismételheti meg, ha az eszköz ténylegesen feldolgozta a tulajdonság frissítését.
    
    ```json
      "brightness": {
        "reported": {
          "value": 60,
          }
       }
    ```

### <a name="invoke-a-command"></a>Parancs meghívása

1. Nyissa meg a **invoke_command. js**fájlt.

1. A fájl elején cserélje le a `deviceID` és a tényleges eszköz azonosítóját. Frissítse az állandókat a következő értékekkel, majd mentse a fájlt:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    ```

1. Nyissa meg azt a terminált, amelyet a Service minta futtatásához nyitott meg. A minta futtatásához használja a következő parancsot:

    ```cmd/sh
    node invoke_command.js
    ```

1. A terminálon a siker a következő kimenethez hasonlít:

    ```cmd/sh
    invoking command blink on component environmentalSensor for device test...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "33e536d3-14f7-4105-88f3-629b9933851c",
      "_response": "helpful response text"
    }
    ```

1. Nyissa meg az _eszköz_ terminálját, és láthatja, hogy a parancs meg lett ismerve:

    ```cmd/sh
    received command: blink for component: environmentalSensor
    acknowledgement succeeded.
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy később további cikkeket is folytat, megtarthatja az ebben a rövid útmutatóban használt erőforrásokat. Ellenkező esetben törölheti az ehhez a rövid útmutatóhoz létrehozott erőforrásokat a további díjak elkerülése érdekében.

A hub és a regisztrált eszköz törléséhez hajtsa végre a következő lépéseket az Azure CLI használatával:

```azurecli-interactive
az group delete --name <Your group name>
```

Ha csak a IoT Hub regisztrált eszközét szeretné törölni, hajtsa végre az alábbi lépéseket az Azure CLI használatával:

```azurecli-interactive
az iot hub device-identity delete --hub-name [YourIoTHubName] --device-id [YourDevice]
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztatható egy IoT Plug and Play-eszköz egy IoT-megoldáshoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Útmutató: Kapcsolódás egy eszközhöz](howto-develop-solution.md)
