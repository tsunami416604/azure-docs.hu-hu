---
title: A IoT csatlakoztatása Plug and Play minta-eszköz kódja a IoT Hubhoz (Windows) | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play előzetes minta-eszköz kódját olyan Windows rendszeren, amely egy IoT hubhoz csatlakozik. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0d89be9da55c97a5b49157251896d3a513c2c6db
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152069"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Gyors útmutató: a Windows rendszeren futó minta IoT Plug and Play előzetes verziójú eszköz csatlakoztatása IoT Hub (C Windows)

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Plug and Play-alkalmazást, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti a központnak küldött adatokat. A minta alkalmazás C nyelven íródott, és az Azure IoT Hub Device C SDK részét képezi. A megoldás fejlesztője az Azure IoT Explorer eszköz használatával megismerheti egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez telepítenie kell a következő szoftvereket a helyi gépre:

* [Visual Studio (közösségi, szakmai vagy vállalati)](https://visualstudio.microsoft.com/downloads/) – ügyeljen arra, hogy a **NuGet csomagkezelő** összetevőjét és az asztali fejlesztést a Visual Studio telepítésekor számítási **feladatokkal C++**  végezze el.
* [Git](https://git-scm.com/download/).
* [CMAK](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Az Azure IoT Explorer telepítése

Töltse le és telepítse az **Azure IoT Explorer** legújabb kiadását az eszköz [tárházának](https://github.com/Azure/azure-iot-explorer/releases) oldaláról, ehhez válassza ki az. msi fájlt a legutóbbi frissítéshez az "eszközök" területen.

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban olyan fejlesztési környezetet készít elő, amellyel klónozott és felépítheti az Azure IoT Hub Device C SDK-t.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa az alábbi parancsot az [Azure IoT C SDK-k és könyvtárak GitHub-](https://github.com/Azure/azure-iot-sdk-c) tárházának klónozásához a következő helyre:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Ez a művelet várhatóan több percig is eltarthat.

## <a name="build-the-code"></a>A kód létrehozása

Az eszköz SDK-val felépítheti a mellékelt mintakód-kódot. Az Ön által létrehozott alkalmazás szimulál egy olyan eszközt, amely egy IoT hubhoz csatlakozik. Az alkalmazás telemetria és tulajdonságokat küld, és parancsokat fogad.

1. Hozzon létre egy `cmake` alkönyvtárat az eszköz SDK gyökérkönyvtárában, és navigáljon a következő mappába:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. A következő parancsok futtatásával hozza létre az eszköz SDK-t és a generált kódot:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Ha a Csatlakozáskezelő felügyeleti csomag nem C++ találja a fordítót, az előző parancs futtatásakor hibaüzeneteket kaphat. Ha ez történik, próbálja meg futtatni ezt a parancsot a [Visual Studio parancssorában](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Az eszköz mintájának futtatása

Futtasson egy minta alkalmazást az SDK-ban egy IoT Plug and Play-eszköz szimulálásához, amely telemetria küld az IoT hubhoz. A minta alkalmazás futtatásához használja az alábbi parancsokat, és adja át az _eszköz kapcsolódási karakterláncát_ paraméterként.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

1. Nyissa meg az Azure IoT Explorert. Megjelenik az **alkalmazás-konfigurációk** lap.

1. Adja meg _IoT hub kapcsolati karakterláncát_ , és válassza a **Csatlakoztatás**lehetőséget.

1. A csatlakoztatása után megjelenik az **eszközök** áttekintése oldal.

1. A **Beállítások**lehetőségre kattintva ellenőrizheti, hogy az eszköz képes-e az eszközön található felületi modell-definíciók beolvasására. Előfordulhat, hogy a beállítások menüben a **csatlakoztatott eszközön** már szerepel a Plug and Play konfigurációk; Ha nem, válassza a **+ modul-definíciós forrás hozzáadása** lehetőséget, majd a **csatlakoztatott eszközön** a hozzáadáshoz.

1. Az **eszközök** áttekintése lapon keresse meg a korábban létrehozott eszköz identitását. Ha az eszköz még fut a parancssorban, ellenőrizze, hogy az eszköz **kapcsolati állapota** az Azure IoT Explorerben _csatlakoztatva_ van-e (ha nem, nyomja meg a **frissítést** , amíg meg nem történik). Válassza ki az eszközt a további részletek megtekintéséhez.

1. Bontsa ki az **urn: YOUR_COMPANY_NAME_HERE: EnvironmentalSensor: 1** azonosítójú felületet, amely felfedi a felületet és a IoT Plug and Play primitívek – tulajdonságok, parancsok és telemetria.

1. Válassza ki a **telemetria** lapot, és kattintson a _Start_ gombra az eszköz által küldött telemetria-adatok megtekintéséhez.

1. Válassza a **Tulajdonságok (nem írható)** lapot az eszköz által jelentett nem írható tulajdonságok megtekintéséhez.

1. Válassza a **Tulajdonságok (írható)** lapot a frissíteni kívánt írható tulajdonságok megtekintéséhez.

1. Bontsa **ki a tulajdonságnév, a**frissítés új névvel elemet, majd válassza az **írható tulajdonság frissítése**lehetőséget. 

1. Ha meg szeretné jeleníteni az új nevet a **jelentett tulajdonság** oszlopban, válassza a lap tetején található **frissítés** gombot.

1. Válassza a **parancsok** lapot az eszköz által támogatott összes parancs megtekintéséhez.

1. Bontsa ki a **Blink** parancsot, és állítson be egy új villogási időközt. Válassza a **Küldés parancs** lehetőséget a parancs meghívásához az eszközön.

1. Nyissa meg a szimulált eszköz parancssorát, és olvassa el a kinyomtatott megerősítő üzeneteket, és ellenőrizze, hogy a parancsok a várt módon lettek-e végrehajtva.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hubhoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Útmutató: Kapcsolódás egy IoT Plug and Play előnézeti eszközhöz](howto-develop-solution.md)
