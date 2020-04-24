---
title: Az Azure IoT-megoldáshoz csatlakoztatott IoT Plug and Play előzetes verziójú eszköz használata | Microsoft Docs
description: A C# (.NET) használatával csatlakozhat egy IoT Plug and Play előnézeti eszközhöz, amely az Azure IoT-megoldáshoz csatlakozik.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0953f68839217c1c75eb86f8399ce023f3863ab4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76963971"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Rövid útmutató: a megoldáshoz csatlakoztatott IoT Plug and Play előnézeti eszköz használata (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

A IoT Plug and Play Preview leegyszerűsíti a IoT azáltal, hogy lehetővé teszi, hogy a mögöttes eszköz megvalósításának ismerete nélkül kommunikáljon az eszköz képességeivel. Ez a rövid útmutató azt ismerteti, hogyan használható a C# (.NET) a megoldáshoz csatlakoztatott IoT Plug and Play-eszköz csatlakoztatásához és vezérléséhez.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez telepítenie kell a .NET Core-t (2. x. x vagy 3. x. x) a fejlesztői gépre. A [.net Core letöltése](https://dotnet.microsoft.com/download/dotnet-core/)több platformra is letöltheti a .net Core SDK kívánt verzióját.

A fejlesztői gépen található .NET-verzió ellenőrzéséhez futtassa a következő parancsot egy helyi terminál ablakban: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot a hub _IoT hub-beli kapcsolódási karakterláncának_ lekéréséhez (jegyezze fel később a használatra):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>A minta eszköz futtatása

Ebben a rövid útmutatóban egy minta környezeti érzékelőt használ, amelyet C# nyelven írt be a IoT Plug and Play eszközként. Az alábbi utasítások bemutatják, hogyan telepítheti és futtathatja az eszközt:

1. Nyisson meg egy terminált az Ön által választott könyvtárban. Futtassa az alábbi parancsot a [C# (.net) GitHub-tárház Azure IoT-mintáinak](https://github.com/Azure-Samples/azure-iot-samples-csharp) klónozásához a következő helyre:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. A rendszer most ezt a terminált fogja használni az _eszköz_ -terminálként. Lépjen a klónozott adattár mappájába, és lépjen a **/Azure-IOT-Samples-csharp/digitaltwin/Samples/Device/EnvironmentalSensorSample** mappára.

1. Az _eszköz-kapcsolatok karakterláncának_konfigurálása:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Hozza létre a szükséges csomagokat, és futtassa a mintát a következő paranccsal:

    ```cmd\sh
        dotnet run
    ```

1. Láthatja, hogy az eszköz regisztrálása sikeresen megtörtént, és a rendszer frissítéseket vár a felhőből. Ez azt jelzi, hogy az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. Ne zárjuk be ezt a terminált, ezért később meg kell győződnie arról, hogy a szolgáltatási minták is működőképesek.

## <a name="run-the-sample-solution"></a>A minta megoldás futtatása

Ebben a rövid útmutatóban egy minta IoT-megoldást használ a C#-ban, hogy együttműködjön a minta eszközzel.

1. Nyisson meg egy másik Terminálablak (ez lesz a _szolgáltatás_ terminálja). Lépjen a klónozott adattár mappájába, és lépjen a **/Azure-IOT-Samples-csharp/digitaltwin/Samples/Service** mappára.

1. Konfigurálja az _IoT hub kapcsolati karakterláncát_ és az _eszköz azonosítóját_ , hogy a szolgáltatás a következőhöz kapcsolódjon:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Tulajdonság beolvasása

1. Amikor csatlakoztatta az _eszközt_ a termináljában, a következő üzenet jelenik meg, amely az online állapotot jelzi:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Nyissa meg a _szolgáltatás_ terminálját, és az alábbi parancsokkal futtassa a mintát az eszköz adatainak olvasásához:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. A _szolgáltatás_ -terminál kimenetében görgessen az `environmentalSensor` összetevőhöz. Láthatja, hogy `state` a tulajdonság, amely azt jelzi, hogy az eszköz online állapotú-e, _igaznak_jelent-e:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Írható tulajdonság frissítése

1. Nyissa meg a _szolgáltatás_ -terminált, és állítsa be a következő változókat a frissíteni kívánt tulajdonság megadásához:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. A következő parancsokkal futtathatja a mintát a tulajdonság frissítéséhez:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
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
            "value": true
          }
        }
      }
    }
    ```

1. Nyissa meg az _eszköz_ terminálját, és láthatja, hogy az eszköz megkapta a frissítést:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Lépjen vissza a _szolgáltatás_ -terminálhoz, és futtassa az alábbi parancsokat az eszköz adatainak újbóli beszerzéséhez, hogy megerősítse a tulajdonság frissítését.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. A _szolgáltatás_ -terminál kimenetében az `environmentalSensor` összetevő alatt látható a frissített fényerő értékének jelentése. Megjegyzés: eltarthat egy ideig, amíg az eszköz befejezi a frissítést. Ezt a lépést csak akkor ismételheti meg, ha az eszköz ténylegesen feldolgozta a tulajdonság frissítését.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>Parancs meghívása

1. Nyissa meg a _szolgáltatás_ -terminált, és állítsa be a következő változókat a meghívni kívánt parancs megadásához:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. A következő parancsokkal futtathatja a mintát a parancs meghívásához:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. A _szolgáltatás_ -terminál kimenetében a következő megerősítésnek kell megjelennie:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Nyissa meg az _eszköz_ terminálját, és láthatja, hogy a parancs meg lett ismerve:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztatható egy IoT Plug and Play-eszköz egy IoT-megoldáshoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Útmutató: Kapcsolódás az eszközhöz](howto-develop-solution.md)
