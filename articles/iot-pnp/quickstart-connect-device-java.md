---
title: A IoT csatlakoztatása Plug and Play minta Java-eszköz kódja a IoT Hubhoz | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play minta-eszköz kódját, amely egy IoT hubhoz csatlakozik. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b63e1c0bba4d6ac250119c2ac0d9a1cd0e4ee362
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91577016"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-running-on-windows-to-iot-hub-java"></a>Rövid útmutató: a Windows rendszeren futó IoT Plug and Play-eszköz csatlakoztatása IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Plug and Play-alkalmazást, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti az általa küldött telemetria. A minta alkalmazás Java nyelven íródott, és megtalálható a Javához készült Azure IoT Device SDK-ban. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

A rövid útmutató Windows rendszeren történő elvégzéséhez telepítse a következő szoftvereket a helyi Windows-környezetbe:

* Java SE Development Kit 8. A [Java hosszú távú Azure-és Azure stack-támogatásában](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)a **hosszú távú támogatás**alatt válassza a **Java 8**lehetőséget.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban egy fejlesztési környezetet készít elő, amellyel klónozott és felépítheti az Azure IoT Hub-eszköz Java SDK-t.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa az alábbi parancsot az [Azure IoT Java SDK-k és könyvtárak GitHub-](https://github.com/Azure/azure-iot-sdk-java) tárházának klónozásához a következő helyre:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-the-code"></a>A kód létrehozása

Windows rendszeren navigáljon a klónozott Java SDK-tárház gyökérkönyvtárához.

Futtassa a következő parancsot a minta alkalmazás létrehozásához:

```cmd
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Az eszköz mintájának futtatása

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

A minta-konfigurációval kapcsolatos további tudnivalókért tekintse meg a [minta](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/pnp-device-sample/readme.md)információit.

Navigáljon a *\device\iot-Device-samples\pnp-Device-sample\thermostat-Device-Sample* mappára.

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

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hubhoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Útmutató: Kapcsolódás az eszközhöz](howto-develop-solution.md)
