---
title: Linux vagy Windows rendszeren futó IoT Plug and Play híd-minta összekapcsolása egy IoT hubhoz | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play hidat Linuxon vagy Windowson, amely egy IoT hubhoz csatlakozik. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4c84fbb131809c92d597b46d18c53e46b636f8ef
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613475"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Linux vagy Windows rendszeren futó IoT Plug and Play híd-minta összekapcsolása IoT Hub

Ez az útmutató bemutatja, hogyan hozhatja létre a IoT Plug and Play híd minta környezeti adapterét, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti az általa küldött telemetria. A IoT Plug and Play-híd C nyelven íródott, és tartalmazza a C Azure IoT Device SDK-t. Az oktatóanyag végére képesnek kell lennie a IoT Plug and Play-híd futtatására és a jelentés telemetria megtekintésére az Azure IoT Explorerben: :::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="egy képernyőfelvétel, amely az Azure IoT Explorert mutatja be a jelentett telemetria (páratartalom, hőmérséklet) a IoT Plug and Play Bridge-ből.":::

## <a name="prerequisites"></a>Előfeltételek

Ezt a rövid útmutatót Linux vagy Windows rendszeren is futtathatja. A útmutatóban szereplő rendszerhéj-parancsok követik az elérésiút-elválasztók Windows-konvencióját () `\` , ha a Linuxon a mellett a következőt használja: " `/` ".

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

* [Visual Studio (közösségi, szakmai vagy vállalati)](https://visualstudio.microsoft.com/downloads/) – ügyeljen arra, hogy a Visual Studio [telepítésekor](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019&preserve-view=true) a **C++** számítási feladatait is tartalmazza.
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

## <a name="view-the-model"></a>A modell megtekintése

A későbbi lépésekben az Azure IoT Explorer használatával tekintheti meg az eszközt, amikor az IoT hubhoz csatlakozik. Az Azure IoT Explorernek szüksége lesz az eszköz által küldött **modell-azonosítóval** megegyező nevű modell helyi példányára. A Model fájl lehetővé teszi, hogy a IoT Explorer megjelenjen az eszköz által megvalósított telemetria, tulajdonságok és parancsok.

Amikor letölti a kódot az alábbi lépésben, a a mappában található minta-modell fájljait tartalmazza `pnpbridge/docs/schema` . Az Azure IoT Explorer előkészítője:

1. Hozzon létre egy *modell* nevű mappát a helyi gépen.
1. [EnvironmentalSensor.js](https://aka.ms/iot-pnp-bridge-env-model) megtekintése és a JSON-fájl mentése a *models* mappába
1. Tekintse [ megRootBridgeSampleDevice.js](https://aka.ms/iot-pnp-bridge-root-model) , és mentse a JSON-fájlt a *modellek* mappába.

## <a name="download-the-code"></a>A kód letöltése

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa az alábbi parancsot a [IoT Plug and Play Bridge](https://aka.ms/iotplugandplaybridge) GitHub-tárházának klónozásához a következő helyre:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Miután klónozott a IoT Plug and Play Bridge-tárházat a gépére, nyisson meg egy rendszergazdai parancssort, és navigáljon a klónozott adattár könyvtárához:

```cmd
cd pnpbridge
git submodule update --init --recursive
```

A művelet elvégzése több percet is igénybe vehet.

>[!NOTE]
> Ha a git Clone sub modul frissítésével kapcsolatos problémákba ütközik, ez a Windows fájlelérési utakkal és a git-vel kapcsolatban ismert probléma: https://github.com/msysgit/git/pull/110 . A probléma megoldásához próbálja meg a következő parancsot: `git config --system core.longpaths true`

## <a name="setting-up-the-configuration-json"></a>A konfigurációs JSON beállítása

Miután klónozott a IoT Plug and Play Bridge-tárházat a gépre, navigáljon a `pnpbridge/docs/schema` klónozott adattár könyvtárába, ahol megtalálja a [konfiguráció JSON](https://aka.ms/iot-pnp-bridge-env-config) -t vagy a `config.json` híd környezeti érzékelő mintáját. A konfigurációs fájlokról a [IoT Plug and Play Bridge-fogalmakat ismertető dokumentumban](concepts-iot-pnp-bridge.md)olvashat bővebben.

A `root-_interface_model_id` mezőhöz másolni kell a IoT Plug and Play modell azonosítóját, amely az eszköz modelljét azonosítja. Ebben a példában ez `dtmi:com:example:SampleDevice;1`. Módosítsa a következő paramétereket a fájl **pnp_bridge_parameters** csomópontjában a `config.json` ":

  A kapcsolatok karakterláncának használata (Megjegyzés: a symmetric_keynak meg kell egyeznie az SAS-kulccsal a következő kapcsolatban:

  ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "[To fill in]",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
  ```

 Ha kitöltötte a fájlt, a következőhöz `config.json` hasonlónak kell lennie:

   ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "dtmi:com:example:SampleDevice;1",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
```

 Miután létrehozta a hidat, ezt a `config.json` könyvtárat kell megadnia, mint a hidat, vagy meg kell adnia annak elérési útját.

## <a name="build-the-iot-plug-and-play-bridge"></a>A IoT Plug and Play-híd létrehozása

Navigáljon a *pnpbridge* mappára az adattár könyvtárában.

A Windows a következőt futtatja a [Visual studióhoz készült fejlesztői parancssorban](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs):

```cmd
cd scripts\windows
build.cmd
```

A Linuxhoz hasonlóan a következőt is futtathatja:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>Windows rendszeren megnyithatja a CMAK parancs által létrehozott megoldást a Visual Studio 2019-ben. Nyissa meg a *azure_iot_pnp_bridge. SLN* projektfájlt a CMAK könyvtárban, és állítsa be a *pnpbridge_bin* projektet indítási projektként a megoldásban. Most már létrehozhatja a mintát a Visual Studióban, és hibakeresési módban futtathatja.

## <a name="start-the-iot-plug-and-play-bridge"></a>A IoT Plug and Play-híd elindítása

 Indítsa el a IoT Plug and Play Bridge-mintát környezeti érzékelőkhöz a *pnpbridge* mappában való navigálással, és futtassa a következő parancsot egy parancssorban:

```bash
 cd cmake/pnpbridge_x86/src/adaptors/samples/environmental_sensor/
./pnpbridge_environmentalsensor

```

```cmd
REM Windows
cd cmake\pnpbridge_x86\src\adaptors\samples\environmental_sensor
Debug\pnpbridge_environmentalsensor.exe
```

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hubhoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

* [Mi a IoT Plug and Play híd?](./concepts-iot-pnp-bridge.md)
* [Lásd a GitHub fejlesztői referenciáját a IoT Plug and Play Bridge-hez](https://aka.ms/iot-pnp-bridge-dev-doc)
* [IoT Plug and Play híd a GitHubon](https://aka.ms/iotplugandplaybridge)
