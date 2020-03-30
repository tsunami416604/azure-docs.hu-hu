---
title: Az Azure IoT-megoldáshoz csatlakoztatott IoT Plug and Play előzetes verziójú eszközzel való együttműködés | Microsoft dokumentumok
description: A C# (.NET) használatával csatlakozhat az Azure IoT-megoldásához csatlakoztatott IoT Plug and Play előzetes verzióhoz, és használhatja azt.
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
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Rövid útmutató: A megoldáshoz csatlakoztatott IoT Plug and Play előzetes verziójú eszközzel (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

Az IoT Plug and Play Preview leegyszerűsíti az IoT-t azáltal, hogy lehetővé teszi az eszköz képességeinek kommunikálását az eszköz megvalósításának ismerete nélkül. Ez a rövid útmutató bemutatja, hogyan használhatja a C# (a .NET) segítségével a megoldáshoz csatlakoztatott IoT Plug and Play eszközhöz való csatlakozást és vezérlést.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához telepítenie kell a .NET Core (2.x.x vagy 3.x.x) rendszert a fejlesztői gépre. A .NET Core SDK preferált verzióját több platformra is letöltheti a [.NET Core letöltésből.](https://dotnet.microsoft.com/download/dotnet-core/)

A fejlesztőgépen található .NET verzióját a következő parancs helyi terminálablakban való futtatásával ellenőrizheti: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot az _IoT hub kapcsolati karakterláncának_ lekérnia a hubhoz (megjegyzés későbbi használatra):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>A mintaeszköz futtatása

Ebben a rövid útmutatóban a C# -ban írt minta környezeti érzékelőt használ IoT Plug and Play eszközként. Az alábbi utasítások bemutatják, hogyan telepítheti és futtathatja az eszközt:

1. Nyisson meg egy terminálablakot az Ön által választott könyvtárban. A következő parancs végrehajtása az [Azure IoT-minták c# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-tárházhoz ehhez a helyhez történő klónozásához:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. Ez a terminálablak lesz az _eszköz_ terminálja. Nyissa meg a klónozott tárház mappáját, és keresse meg az **/azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample** mappát.

1. Az _eszköz kapcsolati karakterláncának konfigurálása_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Készítse el a szükséges csomagokat, és futtassa a mintát a következő paranccsal:

    ```cmd\sh
        dotnet run
    ```

1. Megjelenik az üzenetek, amelyek arról szólnak, hogy az eszköz sikeresen regisztrált, és a felhőből érkező frissítésekre vár. Ez azt jelzi, hogy az eszköz most már készen áll a parancsok és a tulajdonságfrissítések fogadására, és megkezdte a telemetriai adatok küldését a hubra. Ne zárja be ezt a terminált, később szüksége lesz rá, hogy ellenőrizze, hogy a szolgáltatásminták is működtek.Don't close this terminal, you'll need it later to confirm the service samples also worked.

## <a name="run-the-sample-solution"></a>A mintamegoldás futtatása

Ebben a rövid útmutatóban egy c# minta IoT-megoldás használatával kommunikálhat a mintaeszközzel.

1. Nyisson meg egy másik _service_ terminálablakot (ez lesz a szervizterminálja). Nyissa meg a klónozott tárház mappáját, és keresse meg az **/azure-iot-samples-csharp/digitaltwin/Samples/service** mappát.

1. Konfigurálja az _IoT hub kapcsolati karakterláncát_ és _az eszközazonosítót_ úgy, hogy a szolgáltatás mindkét félhez csatlakozhasson:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Ingatlan olvasása

1. Amikor csatlakoztatta az _eszközt_ a termináljához, a következő üzenet jelenik meg, amely jelzi az online állapotát:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Menjen a _service_ szervizterminálra, és a következő parancsokkal futtassa a minta olvasásához eszköz információk:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. A _szervizterminál_ kimenetében görgessen az `environmentalSensor` összetevőhöz. Láthatja, `state` hogy a tulajdonság, amely annak jelzésére szolgál, hogy az eszköz online állapotban van-e, _igazként_lett jelentve:

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

1. Lépjen a _service_ szervizterminálra, és állítsa be a következő változókat a frissítandó tulajdonság meghatározásához:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. A következő parancsokkal futtatja a mintát a tulajdonság frissítéséhez:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
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
2. Lépjen vissza _service_ a szervizterminálhoz, és futtassa az alábbi parancsokat az eszközadatok újbóli levezetéséhez, hogy ellenőrizze a tulajdonság frissítésének megerősítését.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. A _szolgáltatásterminál_ kimenetében `environmentalSensor` az összetevő alatt láthatja a frissített fényerő-értéket. Megjegyzés: eltarthat egy ideig, amíg az eszköz befejezi a frissítést. Ezt a lépést addig ismételheti, amíg az eszköz ténylegesen fel nem dolgozta a tulajdonságfrissítést.
    
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

1. Lépjen a _service_ szolgáltatásterminálra, és állítsa be a következő változókat a meghívni a parancs meghatározásához:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. A minta futtatásához használja a következő parancsokat:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. A _szolgáltatásterminál_ kimenetének a következő visszaigazolást kell mutatnia:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Menj az _eszköz_ terminál, látod a parancsot elismerte:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play eszközt egy IoT-megoldáshoz. Ha többet szeretne tudni arról, hogyan hozhat létre olyan megoldást, amely kölcsönhatásba lép az IoT Plug and Play eszközeivel, olvassa el a következő témaköröket:

> [!div class="nextstepaction"]
> [Útmutató: Eszközhöz való csatlakozás és az eszközzel való interakció](howto-develop-solution.md)
