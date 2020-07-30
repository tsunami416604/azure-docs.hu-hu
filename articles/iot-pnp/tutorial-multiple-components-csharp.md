---
title: A IoT csatlakoztatása Plug and Play Preview minta C# összetevő-eszköz kódja a IoT Hubhoz | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play előzetes minta C#-eszköz kódját, amely több összetevőt használ, és csatlakozik egy IoT hubhoz. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cf2662bef194bc2b7afdb07f55809ba63f40fd6e
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352799"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>Oktatóanyag: IoT csatlakoztatása a Windows rendszeren futó több összetevőt használó Plug and Play IoT Hub (C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy minta IoT Plug and Play eszköz-alkalmazást az összetevőkkel és a gyökérszintű felülettel, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti az általa az adott hubhoz küldött adatokat. A minta alkalmazás C# nyelven íródott, és a C#-hoz készült Azure IoT Device SDK tartalmazza. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag Windows rendszeren történő elvégzéséhez telepítse a következő szoftvereket a helyi Windows-környezetbe:

* [Visual Studio (Közösség, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).
* [CMAK](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Az oktatóanyag második részében az **Azure IoT Explorer** eszköz használatával kommunikálhat a minta eszközzel. [Töltse le és telepítse az Azure IoT Explorer legújabb kiadását](./howto-use-iot-explorer.md) az operációs rendszeréhez.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot a hub _IoT hub-kapcsolódási karakterláncának_ lekéréséhez. Jegyezze fel ezt a összekapcsolási karakterláncot, amelyet később az oktatóanyagban használ:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Az Azure IoT Explorer eszközzel is megkeresheti az IoT hub kapcsolódási karakterláncát.

A következő parancs futtatásával lekérheti a hubhoz felvett eszközhöz tartozó _eszköz-kapcsolódási karakterláncot_ . Jegyezze fel ezt a összekapcsolási karakterláncot, amelyet később az oktatóanyagban használ:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>A kód letöltése

Ebben az oktatóanyagban olyan fejlesztési környezetet készít elő, amellyel klónozott és felépítheti az Azure IoT Hub Device C# SDK-t.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa a következő parancsot az [Azure IoT C# SDK-k és könyvtárak GitHub-](https://github.com/Azure/azure-iot-sdk-csharp) tárházának klónozásához az alábbi helyre:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-c.git
```

## <a name="build-the-code"></a>A kód létrehozása

Nyissa meg a **azureiot. SLN** Solution fájlt a Visual Studio 2019-ben, és állítsa be a **TemperatureController** projektet indítási projektként. **Megoldáskezelő**a projektfájl a **iothub > Device > Samples**-ben található.

Most már létrehozhatja a mintát a Visual Studióban, és hibakeresési módban futtathatja.

## <a name="run-the-device-sample"></a>Az eszköz mintájának futtatása

Hozzon létre egy **IOTHUB_DEVICE_CONNECTION_STRING** nevű környezeti változót, amely a korábban jegyzett eszköz-kapcsolódási karakterláncot tárolja.

A Visual Studióban a Windowson a kód végrehajtásának nyomon követéséhez adjon hozzá egy töréspontot a `main` függvényhez a program.cs fájlban.

Az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>A kód áttekintése

Ez a példa egy IoT Plug and Play hőmérséklet-vezérlő eszközt valósít meg. A minta által megvalósított modell [több összetevőt](concepts-components.md)használ. A [hőmérséklet-eszköz digitális Twins Definition Language (DTDL) modellje](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozza meg.

Az eszköz kódja a standard metódus használatával csatlakozik az IoT hubhoz `CreateFromConnectionString` . Az eszköz elküldi a DTDL modell AZONOSÍTÓját a kapcsolatkérelem számára. Egy IoT Plug and Play eszköz a modell AZONOSÍTÓját küldő eszköz:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
      ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
      s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

A modell AZONOSÍTÓját a kód a következő kódrészletben látható módon tárolja:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

Miután az eszköz csatlakozik az IoT hubhoz, a kód regisztrálja a parancs-kezelőket. A `reboot` parancs a gyökérszintű felületen van definiálva. A `getMaxMinReport` parancs a két termosztát-összetevőben van definiálva:

```csharp
await s_deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, s_deviceClient);
await s_deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1);
await s_deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2);
```

A kívánt tulajdonság-frissítésekhez külön kezelők tartoznak a két termosztát-összetevőn:

```csharp
s_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
s_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);
```

A mintakód a telemetria az egyes termosztát-összetevőkből küldi el:

```csharp
await SendTemperatureAsync(Thermostat1);
await SendTemperatureAsync(Thermostat2);
```

A `SendTemperature` metódus a `PnpHhelper` osztály használatával hoz létre üzeneteket az egyes összetevőkhöz:

```csharp
Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

Az `PnpHelper` osztály más mintavételi módszereket is tartalmaz, amelyek több összetevő-modellel is használhatók.

Az Azure IoT Explorer eszköz használatával megtekintheti a két termosztát-összetevő telemetria és tulajdonságait:

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Több összetevős eszköz az Azure IoT Explorerben":::

Az Azure IoT Explorer eszköz használatával a parancsok a két termosztát-összetevő vagy a gyökérszintű felületen is meghívhatók.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt az összetevőkkel egy IoT hubhoz. Ha többet szeretne megtudni a IoT Plug and Play eszköz modelljeiről, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [IoT Plug and Play előzetes verzió modellezése – fejlesztői útmutató](concepts-developer-guide.md)
