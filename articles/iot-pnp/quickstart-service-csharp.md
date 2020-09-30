---
title: Az Azure IoT-megoldáshoz (C#) csatlakoztatott IoT-Plug and Play eszköz használata | Microsoft Docs
description: A C# használatával csatlakozhat egy IoT Plug and Play eszközhöz, amely az Azure IoT-megoldáshoz csatlakozik.
author: ericmitt
ms.author: ericmitt
ms.date: 09/21/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 688541147a9a765ed94d8e6c18ce4ee63615f627
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583563"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-c"></a>Gyors útmutató: a megoldáshoz csatlakoztatott IoT Plug and Play eszköz használata (C#)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

A IoT Plug and Play leegyszerűsíti a IoT azáltal, hogy az eszköz képességeinek ismerete nélkül teszi lehetővé az eszközök képességeit. Ez a rövid útmutató azt ismerteti, hogyan használható a C# a megoldáshoz csatlakoztatott IoT Plug and Play-eszköz csatlakoztatásához és vezérléséhez.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

A rövid útmutató Windows rendszeren való elvégzéséhez a következő szoftverekre van szükség a fejlesztői gépen:

* [Visual Studio (Közösség, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Az SDK-tárház klónozása a mintakód használatával

Ha befejezte a gyors üzembe helyezést [: csatlakoztasson egy IoT Plug and Play Windows rendszerű eszközt IoT hub (C#)](quickstart-connect-device-csharp.md), már klónozotta a tárházat.

A mintákat a .NET GitHub-tárház Microsoft Azure IoT SDK-ból klónozott. Nyisson meg egy parancssort egy tetszőleges mappában. Futtassa a következő parancsot a .NET GitHub-tárház [Microsoft Azure IoT-mintáinak](https://github.com/Azure-Samples/azure-iot-samples-csharp) klónozásához:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>A minta eszköz futtatása

Ebben a rövid útmutatóban egy C# nyelven írt minta termosztátot használ a IoT Plug and Play eszközként. A minta eszköz futtatása:

1. Nyissa meg a *Azure-IOT-Samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* projektfájlt a Visual Studio 2019-ben.

1. A Visual Studióban navigáljon a **Project > termosztát tulajdonságai > hibakeresés**elemre. Ezután adja hozzá a következő környezeti változókat a projekthez:

    | Név | Érték |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Az érték, amelyet a [környezet beállításakor](set-up-environment.md) jegyzett készített |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | saját PnP-eszköz |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Az érték, amelyet a [környezet beállításakor](set-up-environment.md) jegyzett készített |


1. Most már létrehozhatja a mintát a Visual Studióban, és hibakeresési módban futtathatja.

1. Üzenet jelenik meg, amely közli, hogy az eszköz elküldött néhány információt, és online jelentett. Ezek az üzenetek azt jelzik, hogy az eszköz megkezdte a telemetria-adatok küldését a központba, és készen áll a parancsok és a tulajdonságok frissítéseinek fogadására. Ne zárd be a Visual Studiónak ezt a példányát, hogy erősítse meg, hogy a szolgáltatási minta működik-e.

## <a name="run-the-sample-solution"></a>A minta megoldás futtatása

A [környezet beállítása a IoT Plug and Play rövid útmutatók és oktatóanyagok](set-up-environment.md) létrehozott két környezeti változót a minta konfigurálásához a IoT hub és az eszközhöz való kapcsolódáshoz:

* **IOTHUB_CONNECTION_STRING**: a IoT hub-kapcsolatok karakterlánca korábban már jegyzett készített.
* **DEVICE_ID**: `"my-pnp-device"` .

Ebben a rövid útmutatóban egy minta IoT megoldást használ a C#-ban, hogy együttműködjön az imént beállított eszközzel.

1. A Visual Studio egy másik példányában nyissa meg a *Azure-IOT-Samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj* projektet.

1. A Visual Studióban navigáljon a **Project > termosztát tulajdonságai > hibakeresés**elemre. Ezután adja hozzá a következő környezeti változókat a projekthez:

    | Név | Érték |
    | ---- | ----- |
    | DEVICE_ID | saját PnP-eszköz |
    | IOTHUB_CONNECTION_STRING | Az érték, amelyet a [környezet beállításakor](set-up-environment.md) jegyzett készített |

1. Most már létrehozhatja a mintát a Visual Studióban, és hibakeresési módban futtathatja.

### <a name="get-digital-twin"></a>Digitális dupla Letöltés

A következő kódrészlet azt mutatja be, hogy a szolgáltatásalkalmazás hogyan kérdezi le a digitális IKeret:

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> Ez a példa a **Microsoft. Azure. Devices. Client;** névteret használja a **IoT hub szolgáltatás ügyfelétől**. A modell AZONOSÍTÓjának beolvasásával kapcsolatos további tudnivalókért tekintse meg a [fejlesztői útmutató](concepts-developer-guide.md)című témakört.

Ez a kód a következő kimenetet hozza létre:

```cmd
[09/21/2020 11:26:04]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
```

A következő kódrészlet azt mutatja be, hogyan használható egy *javítás* a tulajdonságok frissítésére a digitális Twin használatával:

```C#
// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired[PropertyName] = PropertyValue;
await s_registryManager.UpdateTwinAsync(s_deviceId, twinPatch, twin.ETag);
```

Ez a kód a következő kimenetet hozza létre az eszközről, amikor a szolgáltatás frissíti a `targetTemperature` tulajdonságot:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Received - { "targetTemperature": 60°C }.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is InProgress.

...

[09/21/2020 11:26:17]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is Completed.
```

### <a name="invoke-a-command"></a>Parancs meghívása

A következő kódrészlet bemutatja, hogyan hívhat meg egy parancsot:

```csharp
private static async Task InvokeCommandAsync()
{
    var commandInvocation = new CloudToDeviceMethod(CommandName) { ResponseTimeout = TimeSpan.FromSeconds(30) };

    // Set command payload
    string componentCommandPayload = JsonConvert.SerializeObject(s_dateTime);
    commandInvocation.SetPayloadJson(componentCommandPayload);

    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, commandInvocation);

    if (result == null)
    {
        throw new Exception($"Command {CommandName} invocation returned null");
    }

    s_logger.LogDebug($"Command {CommandName} invocation result status is: {result.Status}");
}
```

Ez a kód a következő kimenetet hozza létre az eszközről, amikor a szolgáltatás meghívja a `getMaxMinReport` parancsot:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 21/09/2020 11:25:58.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 21/09/2020 11:25:58: maxTemp=32, minTemp=32, avgTemp=32, startTime=21/09/2020 11:25:59, endTime=21/09/2020 11:26:04
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztatható egy IoT Plug and Play-eszköz egy IoT-megoldáshoz. Ha többet szeretne megtudni a IoT Plug and Play eszköz modelljeiről, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [IoT Plug and Play modellezési fejlesztői útmutató](concepts-developer-guide-device-csharp.md)
