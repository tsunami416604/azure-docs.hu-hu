---
title: A IoT csatlakoztatása Plug and Play minta C# összetevő-eszköz kódja a IoT Hubhoz | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play minta C#-eszköz kódját, amely több összetevőt használ, és csatlakozik egy IoT hubhoz. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f6f87ed4ba74c3f7750e56d4bb8473cf4b1a4341
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575384"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>Oktatóanyag: IoT csatlakoztatása a Windows rendszeren futó több összetevőt használó Plug and Play IoT Hub (C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre IoT Plug and Play-eszköz-alkalmazást összetevőkkel, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti a központnak küldött adatokat. A minta alkalmazás C# nyelven íródott, és a C#-hoz készült Azure IoT Device SDK tartalmazza. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Az oktatóanyag Windows rendszeren történő elvégzéséhez telepítse a következő szoftvereket a helyi Windows-környezetbe:

* [Visual Studio (Közösség, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Az SDK-tárház klónozása a mintakód használatával

Ha befejezte a gyors üzembe helyezést [: csatlakoztasson egy IoT Plug and Play Windows rendszerű eszközt IoT hub (C#)](quickstart-connect-device-csharp.md), már klónozotta a tárházat.

A mintákat a .NET GitHub-tárház Microsoft Azure IoT SDK-ból klónozott. Nyisson meg egy parancssort egy tetszőleges mappában. Futtassa a következő parancsot a .NET GitHub-tárház [Microsoft Azure IoT-mintáinak](https://github.com/Azure-Samples/azure-iot-samples-csharp) klónozásához:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>A minta eszköz futtatása

Ebben a rövid útmutatóban egy olyan minta hőmérséklet-vezérlő eszközt használ, amelyet C# nyelven írt be a IoT Plug and Play eszközként. A minta eszköz futtatása:

1. Nyissa meg a *Azure-IOT-Samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController\TemperatureController.csproj* projektfájlt a Visual Studio 2019-ben.

1. A Visual Studióban navigáljon a **Project > TemperatureController tulajdonságok > hibakeresés**elemre. Ezután adja hozzá a következő környezeti változókat a projekthez:

    | Név | Érték |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Az érték, amelyet a [környezet beállításakor](set-up-environment.md) jegyzett készített |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | saját PnP-eszköz |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Az érték, amelyet a [környezet beállításakor](set-up-environment.md) jegyzett készített |


1. Most már létrehozhatja a mintát a Visual Studióban, és hibakeresési módban futtathatja.

1. Üzenet jelenik meg, amely közli, hogy az eszköz elküldött néhány információt, és online jelentett. Ezek az üzenetek azt jelzik, hogy az eszköz megkezdte a telemetria-adatok küldését a központba, és készen áll a parancsok és a tulajdonságok frissítéseinek fogadására. Ne zárd be a Visual Studiónak ezt a példányát, hogy erősítse meg, hogy a szolgáltatási minta működik-e.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>A kód áttekintése

Ez a példa egy IoT Plug and Play hőmérséklet-vezérlő eszközt valósít meg. A minta által megvalósított modell [több összetevőt](concepts-components.md)használ. A [hőmérséklet-eszköz digitális Twins Definition Language (DTDL) modellje](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozza meg.

Az eszköz kódja a standard metódus használatával csatlakozik az IoT hubhoz `CreateFromConnectionString` . Az eszköz elküldi a DTDL modell AZONOSÍTÓját a kapcsolatkérelem számára. Egy IoT Plug and Play eszköz a modell AZONOSÍTÓját küldő eszköz:

```csharp
private static DeviceClient InitializeDeviceClient(string hostname, IAuthenticationMethod authenticationMethod)
{
    var options = new ClientOptions
    {
        ModelId = ModelId,
    };

    var deviceClient = DeviceClient.Create(hostname, authenticationMethod, TransportType.Mqtt, options);
    deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
    {
        s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
    });

    return deviceClient;
}
```

A modell AZONOSÍTÓját a kód a következő kódrészletben látható módon tárolja:

```csharp
private const string ModelId = "dtmi:com:example:TemperatureController;1";
```

Miután az eszköz csatlakozik az IoT hubhoz, a kód regisztrálja a parancs-kezelőket. A `reboot` parancs az alapértelmezett összetevőben van definiálva. A `getMaxMinReport` parancs a két termosztát-összetevőben van definiálva:

```csharp
await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2, cancellationToken);

```

A kívánt tulajdonság-frissítésekhez külön kezelők tartoznak a két termosztát-összetevőn:

```csharp
_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

```

A mintakód a telemetria az egyes termosztát-összetevőkből küldi el:

```csharp
await SendTemperatureAsync(Thermostat1, cancellationToken);
await SendTemperatureAsync(Thermostat2, cancellationToken);
```

A `SendTemperatureTelemetryAsync` metódus a `PnpHhelper` osztály használatával hoz létre üzeneteket az egyes összetevőkhöz:

```csharp
using Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

Az `PnpHelper` osztály más mintavételi módszereket is tartalmaz, amelyek több összetevő-modellel is használhatók.

Az Azure IoT Explorer eszköz használatával megtekintheti a két termosztát-összetevő telemetria és tulajdonságait:

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Több összetevős eszköz az Azure IoT Explorerben":::

Az Azure IoT Explorer eszköz használatával a parancsok a két termosztát-összetevő vagy az alapértelmezett összetevő egyikében is hívhatók.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt az összetevőkkel egy IoT hubhoz. Ha többet szeretne megtudni a IoT Plug and Play eszköz modelljeiről, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [IoT Plug and Play modellezési fejlesztői útmutató](concepts-developer-guide-device-csharp.md)
