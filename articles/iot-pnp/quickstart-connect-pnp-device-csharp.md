---
title: IoT Plug and Play előzetes verziójú eszközkód csatlakoztatása az IoT Hubhoz | Microsoft dokumentumok
description: C# (.NET) használatával hozzon létre és futtasson IoT Plug and Play preview mintaeszköz-kódot, amely egy IoT-központhoz csatlakozik. Az Azure IoT-kezelő eszközzel megtekintheti az eszköz által a hubra küldött információkat.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 90d39635ac6302f816f39ca19cc00a39cfbbf850
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121012"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Rövid útmutató: Csatlakoztasson egy minta IoT Plug and Play preview eszközalkalmazást az IoT Hubhoz (C#)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy minta IoT Plug and Play eszközalkalmazást, hogyan csatlakoztathatja az IoT-központhoz, és az Azure IoT Explorer eszközzel megtekintheti a hubnak küldött információkat. A mintaalkalmazás C# (a .NET), és az Azure IoT-minták C# (.NET) gyűjtemény részeként biztosított. A megoldásfejlesztő az Azure IoT-felfedező eszközzel anélkül, hogy bármilyen eszközkódot kellene megtekintenie, az IoT Plug and Play eszközök képességeit.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához telepítenie kell a .NET Core 3.0-t a fejlesztői gépre. A .NET Core SDK ezen verzióját több platformra is letöltheti a [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)letöltéséből.

A fejlesztőgépen található .NET verzióját a következő parancs helyi terminálablakban való futtatásával ellenőrizheti: 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>Az Azure IoT-felfedező telepítése

Töltse le és telepítse az **Azure IoT Explorer** legújabb kiadását az eszköz [tárházlapjáról,](https://github.com/Azure/azure-iot-explorer/releases) válassza ki az .msi fájlt az "Eszközök" alatt a legújabb frissítéshez.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot az _IoT hub kapcsolati karakterláncának_ lekérnia a hubhoz (megjegyzés későbbi használatra):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban előkészíti a fejlesztői környezetet, amely nek segítségével klónozhatja és létrehozhatja az Azure IoT-mintákc# (.NET) számára.

Nyisson meg egy parancssort az Ön által választott könyvtárban. A következő parancs végrehajtása az [Azure IoT-minták c# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-tárházhoz ehhez a helyhez történő klónozásához:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

Ez a művelet több percig is eltarthat.

## <a name="run-the-device-sample"></a>Az eszközminta futtatása

A klónozott mintakódot egy iT-hubhoz csatlakozó eszközt szimuláló alkalmazás létrehozásához használja. Az alkalmazás telemetriai adatokat és tulajdonságokat küld, és parancsokat fogad.

1. Egy helyi terminálablakban nyissa meg a klónozott tárház mappáját, és keresse meg az **azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample** mappát. 

1. Az _eszköz kapcsolati karakterláncának konfigurálása_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Egy mintaalkalmazás szimulálása ioT Plug and Play eszköz, amely telemetriai adatokat küld az IoT hub. Ugyanebben a terminálablakban a szükséges csomagok létrehozásához és a mintaalkalmazás futtatásához használja a következő parancsot:

    ```cmd\sh
    dotnet run --framework=netcoreapp3.0
    ```

Megjelenik az üzenetek, amelyek arról szólnak, hogy az eszköz sikeresen regisztrált, és a felhőből érkező frissítésekre vár. Ez azt jelzi, hogy az eszköz most már készen áll a parancsok és a tulajdonságfrissítések fogadására, és megkezdte a telemetriai adatok küldését a hubra. A következő lépések végrehajtásával tartsa a mintát.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítéséhez használja az Azure IoT-felfedezőt

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Annak érdekében, hogy az eszköz le tudja olvasni a felületmodell-definíciókat az eszközről, válassza a **Beállítások lehetőséget.** A Beállítások menüben **előfordulhat,** hogy a csatlakoztatott eszközön már megjelennek a Plug and Play konfigurációkban; ha nem, válassza a **+ Add module definition source** and then On the connected **device** to add it.

1. Az **Eszközök** áttekintése lapon keresse meg a korábban létrehozott eszközidentitást. Ha az eszközalkalmazás továbbra is fut a parancssorban, ellenőrizze, hogy az eszköz **kapcsolati állapota** az Azure IoT Explorer _ben csatlakoztatottként jelent-e_ (ha nem, nyomja meg a **Frissítés** gombot, amíg nincs). A további részletek megtekintéséhez válassza ki az eszközt.

1. Bontsa ki a felületet az ID **urn:csharp_sdk_sample:EnvironmentalSensor:1** paranccsal, hogy felfedje a felületet és az IoT Plug and Play primitíveket – tulajdonságokat, parancsokat és telemetriai adatokat.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat ioT Plug and Play-eszközt egy IoT-központhoz. Ha többet szeretne tudni arról, hogyan hozhat létre olyan megoldást, amely kölcsönhatásba lép az IoT Plug and Play eszközeivel, olvassa el a következő témaköröket:

> [!div class="nextstepaction"]
> [Útmutató: Csatlakozás IoT Plug and Play előzetes verziójához és használata](howto-develop-solution.md)
