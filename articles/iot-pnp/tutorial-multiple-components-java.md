---
title: A IoT csatlakoztatása Plug and Play minta Java-összetevő-eszköz kódja a IoT Hubhoz | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play tekintse meg a Java-eszköz kódját, amely több összetevőt használ, és csatlakozik egy IoT hubhoz. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1d16d8c54939c4f659b6a1530e2d360b957a09ad
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352795"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-java"></a>Oktatóanyag: minta IoT csatlakoztatása Plug and Play előzetes verzió több összetevő-eszköz alkalmazása IoT Hub (Java) szolgáltatáshoz

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre több összetevőből álló IoT Plug and Play eszköz-alkalmazást, hogyan csatlakoztathatja az IoT hubhoz, és az Azure CLI használatával megtekintheti az általa küldött telemetria. A minta alkalmazás Java nyelven íródott, és megtalálható a Javához készült Azure IoT Device SDK-ban. A megoldás-szerkesztő az Azure CLI használatával tudja értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy minta IoT Plug and Play eszköz-alkalmazást az összetevőkkel és a gyökérszintű felülettel, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti az általa az adott hubhoz küldött adatokat. A minta alkalmazás Java nyelven íródott, és megtalálható a Javához készült Azure IoT Device SDK-ban. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag Windows rendszeren történő elvégzéséhez telepítse a következő szoftvereket a helyi Windows-környezetbe:

* Java SE Development Kit 8. A [Java hosszú távú Azure-és Azure stack-támogatásában](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)a **hosszú távú támogatás**alatt válassza a **Java 8**lehetőséget.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Ha a rövid útmutató második részében a minta eszközzel szeretne kommunikálni, használja az **Azure IoT Explorer** eszközt. [Töltse le és telepítse az Azure IoT Explorer legújabb kiadását](./howto-use-iot-explorer.md) az operációs rendszeréhez.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot a hub _IoT hub-kapcsolódási karakterláncának_ lekéréséhez. Jegyezze fel ezt a összekapcsolási karakterláncot, amelyet később a rövid útmutatóban fog használni:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Az Azure IoT Explorer eszközzel is megkeresheti az IoT hub kapcsolódási karakterláncát.

A következő parancs futtatásával lekérheti a hubhoz felvett eszközhöz tartozó _eszköz-kapcsolódási karakterláncot_ . Jegyezze fel ezt a összekapcsolási karakterláncot, amelyet később a rövid útmutatóban fog használni:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>A kód letöltése

Ebben az oktatóanyagban olyan fejlesztési környezetet készít elő, amellyel klónozott és felépítheti az Azure IoT Hub Java SDK-t.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa az alábbi parancsot az [Azure IoT Java SDK-k és könyvtárak GitHub-](https://github.com/Azure/azure-iot-sdk-java) tárházának klónozásához a következő helyre:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

A művelet elvégzése több percet is igénybe vehet.

## <a name="build-the-code"></a>A kód létrehozása

Windows rendszeren navigáljon a klónozott Java SDK-tárház gyökérkönyvtárához. Ezután keresse meg a *\device\iot-Device-samples\pnp-Device-sample\temerature-Controller-Device-Sample* mappát.

Futtassa a következő parancsot a minta alkalmazás létrehozásához:

```java
mvn clean package
```

## <a name="run-the-device-sample"></a>Az eszköz mintájának futtatása

Hozzon létre egy **IOTHUB_DEVICE_CONNECTION_STRING** nevű környezeti változót, amely a korábban jegyzett eszköz-kapcsolódási karakterláncot tárolja.

A minta alkalmazás futtatásához futtassa a következő parancsot:

```java
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

Az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>A kód áttekintése

Ez a példa egy IoT Plug and Play hőmérséklet-vezérlő eszközt valósít meg. A minta által megvalósított modell [több összetevőt](concepts-components.md)használ. A [hőmérséklet-eszköz digitális Twins Definition Language (DTDL) modellje](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozza meg.

Az eszköz kódja a standard `DeviceClient` osztály használatával csatlakozik az IoT hubhoz. Az eszköz elküldi a DTDL modell AZONOSÍTÓját a kapcsolatkérelem számára. Egy IoT Plug and Play eszköz a modell AZONOSÍTÓját küldő eszköz:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

A modell AZONOSÍTÓját a kód a következő kódrészletben látható módon tárolja:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

Miután az eszköz csatlakozik az IoT hubhoz, a kód regisztrálja a parancs-kezelőket.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

A kívánt tulajdonság-frissítésekhez külön kezelők tartoznak a két termosztát-összetevőn:

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

A mintakód a telemetria az egyes termosztát-összetevőkből küldi el:

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

A `sendTemperatureReading` metódus a `PnpHhelper` osztály használatával hoz létre üzeneteket az egyes összetevőkhöz:

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

Az `PnpHelper` osztály más mintavételi módszereket is tartalmaz, amelyek több összetevő-modellel is használhatók.

Az Azure IoT Explorer eszköz használatával megtekintheti a két termosztát-összetevő telemetria és tulajdonságait:

:::image type="content" source="media/tutorial-multiple-components-java/multiple-component.png" alt-text="Több összetevős eszköz az Azure IoT Explorerben":::

Az Azure IoT Explorer eszköz használatával a parancsok a két termosztát-összetevő vagy a gyökérszintű felületen is meghívhatók.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt az összetevőkkel egy IoT hubhoz. Ha többet szeretne megtudni a IoT Plug and Play eszköz modelljeiről, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [IoT Plug and Play előzetes verzió modellezése – fejlesztői útmutató](concepts-developer-guide.md)
