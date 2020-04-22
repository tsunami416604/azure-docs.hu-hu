---
title: IoT Plug and Play preview mintaeszköz-kód csatlakoztatása az IoT Hubhoz (Windows) | Microsoft dokumentumok
description: IoT Plug and Play preview mintaeszköz-kódot hozhat létre és futtatható windowsos, IoT-elosztóhoz csatlakozó mintaeszköz-kódot. Az Azure IoT-kezelő eszközzel megtekintheti az eszköz által a hubra küldött információkat.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 080820024db1302ff5a841761428442396b90040
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769851"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Rövid útmutató: A Windows rendszeren futó IoT Plug and Play előzetes verziójú eszközalkalmazás és az IoT Hub (C Windows) alkalmazásának csatlakoztatása

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy minta IoT Plug and Play eszközalkalmazást, hogyan csatlakoztathatja az IoT-központhoz, és az Azure IoT Explorer eszközzel megtekintheti a hubnak küldött információkat. A mintaalkalmazás C-ben íródott, és az Azure IoT Hub Device C SDK tartalmazza. A megoldásfejlesztő az Azure IoT-felfedező eszközzel anélkül, hogy bármilyen eszközkódot kellene megtekintenie, az IoT Plug and Play eszközök képességeit.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához a következő szoftvert kell telepítenie a helyi számítógépre:

* [Visual Studio (közösségi, professzionális vagy enterprise)](https://visualstudio.microsoft.com/downloads/) – a Visual Studio telepítésekor mindenképpen vegye fel a **NuGet csomagkezelő** és a **C++ munkaterheléssel ellátott Asztalfejlesztés** összetevőt.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Az Azure IoT-felfedező telepítése

Töltse le és telepítse az **Azure IoT Explorer** legújabb kiadását az eszköz [tárházlapjáról,](https://github.com/Azure/azure-iot-explorer/releases) válassza ki az .msi fájlt az "Eszközök" alatt a legújabb frissítéshez.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot az _IoT hub kapcsolati karakterláncának_ lekérnia a hubhoz (megjegyzés későbbi használatra):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban előkészíti a fejlesztői környezetet, amely az Azure IoT Hub-eszköz C SDK klónozásához és létrehozásához használható.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Az alábbi parancs végrehajtása az [Azure IoT C SDK-k és a GitHub-tárház](https://github.com/Azure/azure-iot-sdk-c) ezen a helyen történő klónozásához:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Ez a művelet várhatóan több percig is eltarthat.

## <a name="build-the-code"></a>A kód létrehozása

Az sdk eszköz segítségével hozhatja létre a mellékelt mintakódot. Az alkalmazást hoz létre szimulálja egy eszköz, amely csatlakozik egy IoT hub. Az alkalmazás telemetriai adatokat és tulajdonságokat küld, és parancsokat fogad.

1. Hozzon `cmake` létre egy alkönyvtárat az eszköz SDK-gyökérmappájában, és keresse meg azt a mappát:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat az eszköz SDK és a létrehozott kódcsonk létrehozásához:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Ha a cmake nem találja a C++ fordítót, az előző parancs futtatásakor buildhibákat kap. Ebben az esetben próbálja meg futtatni ezt a parancsot a [Visual Studio parancssori parancssorból.](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)

## <a name="run-the-device-sample"></a>Az eszközminta futtatása

Futtasson egy mintaalkalmazást az SDK-ban egy IoT Plug and Play-eszköz szimulálására, amely telemetriát küld az IoT hubnak. A mintaalkalmazás futtatásához használja ezeket a parancsokat, és adja át az _eszköz kapcsolati karakterláncát_ paraméterként.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Az eszköz most már készen áll a parancsok és a tulajdonságfrissítések fogadására, és megkezdte a telemetriai adatok küldését a hubra. A következő lépések végrehajtásával tartsa a mintát.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítéséhez használja az Azure IoT-felfedezőt

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Annak érdekében, hogy az eszköz le tudja olvasni a felületmodell-definíciókat az eszközről, válassza a **Beállítások lehetőséget.** A Beállítások menüben **előfordulhat,** hogy a csatlakoztatott eszközön már megjelennek a Plug and Play konfigurációkban; ha nem, válassza a **+ Add module definition source** and then On the connected **device** to add it.

1. Az **Eszközök** áttekintése lapon keresse meg a korábban létrehozott eszközidentitást. Ha az eszközalkalmazás továbbra is fut a parancssorban, ellenőrizze, hogy az eszköz **kapcsolati állapota** az Azure IoT Explorer _ben csatlakoztatottként jelent-e_ (ha nem, nyomja meg a **Frissítés** gombot, amíg nincs). A további részletek megtekintéséhez válassza ki az eszközt.

1. Bontsa ki a felületet az ID **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** paranccsal, hogy felfedje a felületet és az IoT Plug and Play primitíveket – tulajdonságokat, parancsokat és telemetriai adatokat.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat ioT Plug and Play-eszközt egy IoT-központhoz. Ha többet szeretne tudni arról, hogyan hozhat létre olyan megoldást, amely kölcsönhatásba lép az IoT Plug and Play eszközeivel, olvassa el a következő témaköröket:

> [!div class="nextstepaction"]
> [Útmutató: Csatlakozás IoT Plug and Play előzetes verziójához és használata](howto-develop-solution.md)
