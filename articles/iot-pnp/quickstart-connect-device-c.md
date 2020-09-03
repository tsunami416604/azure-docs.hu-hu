---
title: A IoT csatlakoztatása Plug and Play a C-eszköz kódjának előnézete IoT Hub | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play előzetes minta-eszköz kódját Linuxon vagy Windowson, amely egy IoT hubhoz csatlakozik. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: afe7396ebdada97b9311d0afe903f40757084586
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89426112"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-or-windows-to-iot-hub-c"></a>Rövid útmutató: a Linux vagy Windows rendszeren futó minta IoT Plug and Play előzetes verziójának csatlakoztatása IoT Hub (C)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Plug and Play-alkalmazást, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti az általa küldött telemetria. A minta alkalmazás C nyelven íródott, amely a C Azure IoT Device SDK-ban szerepel. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

Ezt a rövid útmutatót Linux vagy Windows rendszeren is futtathatja. Az ebben a rövid útmutatóban található rendszerhéj-parancsok követik a (z) "" elérésiút-elválasztók Linux-konvencióját `/` , ha a Windows rendszerű számítógépeken a következőt használja: " `\` ".

Az előfeltételek eltérnek az operációs rendszertől:

### <a name="linux"></a>Linux

Ez a rövid útmutató feltételezi, hogy a Ubuntu Linux használja. A rövid útmutató lépései az Ubuntu 18,04 használatával lettek tesztelve.

A gyors útmutató Linuxon való elvégzéséhez telepítse a következő szoftvereket a helyi linuxos környezetbe:

Telepítse a **GCC**, a **git**, a **CMAK**és az összes szükséges függőséget a `apt-get` parancs használatával:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Ellenőrizze, hogy a verziója `cmake` meghaladja-e a **2.8.12** , és hogy a **GCC** verziója meghaladja-e a **4.4.7**-t.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

A rövid útmutató Windows rendszeren történő elvégzéséhez telepítse a következő szoftvereket a helyi Windows-környezetbe:

* [Visual Studio (közösségi, szakmai vagy vállalati)](https://visualstudio.microsoft.com/downloads/) – ügyeljen arra, hogy a Visual Studio [telepítésekor](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) a **C++** számítási feladatait is tartalmazza.
* [Git](https://git-scm.com/download/).
* [CMAK](https://cmake.org/download/).

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

Ebben a rövid útmutatóban olyan fejlesztési környezetet készít elő, amellyel klónozott és felépítheti az Azure IoT Hub Device C SDK-t.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa az alábbi parancsot az [Azure IoT C SDK-k és könyvtárak GitHub-](https://github.com/Azure/azure-iot-sdk-c) tárházának klónozásához a következő helyre:

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

A művelet elvégzése több percet is igénybe vehet.

## <a name="build-the-code"></a>A kód létrehozása

Az eszköz SDK használatával hozza létre a mellékelt mintakód:

1. Hozzon létre egy _CMAK_ -alkönyvtárat az eszköz SDK gyökérkönyvtárában, és navigáljon a következő mappába:

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Az SDK és a minták létrehozásához futtassa a következő parancsokat:

    ```cmd\bash
    cmake ..
    cmake --build .
    ```

> [!TIP]
> Windows rendszeren megnyithatja a parancs által generált megoldást a `cmake` Visual Studio 2019-ben. Nyissa meg a *azure_iot_sdks. SLN* projektfájlt a _CMAK_ könyvtárban, és állítsa be a **pnp_simple_thermostat** projektet indítási projektként a megoldásban. Most már létrehozhatja a mintát a Visual Studióban, és hibakeresési módban futtathatja.

## <a name="run-the-device-sample"></a>Az eszköz mintájának futtatása

A minta alkalmazásnak az SDK-ban való futtatásához, amely szimulál egy IoT Plug and Play eszköz telemetria küldését az IoT hubhoz:

Hozzon létre két környezeti változót a minta konfigurálásához, hogy kapcsolati sztringet használjon az IoT hubhoz való kapcsolódáshoz:

- **IOTHUB_DEVICE_SECURITY_TYPE** az értékkel `"connectionString"`
- **IOTHUB_DEVICE_CONNECTION_STRING** a korábban létrehozott eszköz-kapcsolódási karakterláncot.

A _CMAK_ mappában navigáljon a végrehajtható fájlt tartalmazó mappához, és futtassa a következőt:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_simple_thermostat\Debug
.\pnp_simple_thermostat.exe
```

> [!TIP]
> A Visual Studióban a Windowson a kód végrehajtásának nyomon követéséhez adjon hozzá egy töréspontot a `main` függvényhez a _pnp_simple_thermostat. c_ fájlban.

Az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>A kód áttekintése

Ez a példa egy egyszerű IoT Plug and Play termosztátos eszközt implementál. A minta által megvalósított modell nem használ IoT Plug and Play [összetevőket](concepts-components.md). A [termosztát eszköz DTDL-modell fájlja](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozza meg.

Az eszköz kódja a standard függvény használatával csatlakozik az IoT hubhoz:

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

Az eszköz elküldi a DTDL modell AZONOSÍTÓját a kapcsolatkérelem számára. Egy IoT Plug and Play eszköz a modell AZONOSÍTÓját küldő eszköz:

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

A tulajdonságokat frissítő, a parancsokat kezelő és a telemetria küldő kód megegyezik egy olyan eszköz kódjával, amely nem használja a IoT Plug and Play konvencióit.

A kód a plébános-függvénytárat használja az IoT hub-ból eljuttatott hasznos adatokban található JSON-objektumok elemzéséhez:

```c
// JSON parser
#include "parson.h"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hubhoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Útmutató: Kapcsolódás az eszközhöz](howto-develop-solution.md)
