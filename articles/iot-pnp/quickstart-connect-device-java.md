---
title: A IoT csatlakoztatása Plug and Play a Java-eszköz kódjának betekintő mintája a IoT Hubhoz | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play előzetes minta-eszköz kódját, amely egy IoT hubhoz csatlakozik. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b89c92e675ab505878f350e9716af95050ce28b2
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352915"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-java"></a>Rövid útmutató: a Windows rendszeren futó minta IoT Plug and Play előzetes verziójú eszközének csatlakoztatása IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Plug and Play-alkalmazást, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti az általa küldött telemetria. A minta alkalmazás Java nyelven íródott, és megtalálható a Javához készült Azure IoT Device SDK-ban. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató Windows rendszeren történő elvégzéséhez telepítse a következő szoftvereket a helyi Windows-környezetbe:

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

## <a name="download-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban egy fejlesztési környezetet készít elő, amellyel klónozott és felépítheti az Azure IoT Hub-eszköz Java SDK-t.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa az alábbi parancsot az [Azure IoT Java SDK-k és könyvtárak GitHub-](https://github.com/Azure/azure-iot-sdk-java) tárházának klónozásához a következő helyre:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="build-the-code"></a>A kód létrehozása

Windows rendszeren navigáljon a klónozott Java SDK-tárház gyökérkönyvtárához. Ezután keresse meg a *\device\iot-Device-samples\pnp-Device-sample\thermostat-Device-Sample* mappát.

Futtassa a következő parancsot a minta alkalmazás létrehozásához:

```cmd
mvn clean package
```

## <a name="run-the-device-sample"></a>Az eszköz mintájának futtatása

Hozzon létre egy **IOTHUB_DEVICE_CONNECTION_STRING** nevű környezeti változót, amely a korábban jegyzett eszköz-kapcsolódási karakterláncot tárolja.

A minta alkalmazás futtatásához futtassa a következő parancsot:

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

Az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>A kód áttekintése

Ez a példa egy egyszerű IoT Plug and Play termosztátos eszközt implementál. A minta által megvalósított modell nem használ IoT Plug and Play [összetevőket](concepts-components.md). A [termosztát eszköz DTDL-modell fájlja](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozza meg.

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

A tulajdonságokat frissítő, a parancsokat kezelő és a telemetria küldő kód megegyezik egy olyan eszköz kódjával, amely nem használja a IoT Plug and Play konvencióit.

A minta egy JSON-függvénytárat használ a JSON-objektumok elemzéséhez az IoT hub által eljuttatott hasznos adatokban:

```java
import com.google.gson.Gson;

...

Date since = new Gson().fromJson(jsonRequest, Date.class);
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hubhoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Útmutató: Kapcsolódás az eszközhöz](howto-develop-solution.md)
