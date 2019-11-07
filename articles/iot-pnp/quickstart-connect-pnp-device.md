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
ms.openlocfilehash: 2dd5d197851b0090ac1af7bbde5a1ad1b951c785
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569916"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub"></a>Rövid útmutató: a Windows rendszeren futó minta IoT Plug and Play előzetes verziójú eszköz csatlakoztatása IoT Hub

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Plug and Play-alkalmazást, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti a központnak küldött adatokat. A minta alkalmazás C nyelven íródott, amely a C Azure IoT Device SDK-ban szerepel. A megoldás fejlesztője az Azure IoT Explorer eszköz használatával megismerheti egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez telepítenie kell a következő szoftvereket a helyi gépre:

* [Hozzon létre eszközöket a Visual studióhoz](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) ,  **C++ és hozzon létre eszközöket** és **NuGet csomagkezelő összetevő** -számítási feladatokat. Ha már rendelkezik a [Visual Studióval (Közösség, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 vagy 2015-val, és ugyanazokat a számítási feladatokat telepítette.
* [Git](https://git-scm.com/download/).
* [CMAK](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Az Azure IoT Explorer telepítése

Töltse le és telepítse az Azure IoT Explorer eszközt a [legújabb kiadási](https://github.com/Azure/azure-iot-explorer/releases) oldalról.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>IoT hub előkészítése

A rövid útmutató elvégzéséhez szüksége lesz egy Azure IoT hub-ra is az Azure-előfizetésében. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> A nyilvános előzetes verzióban a IoT Plug and Play funkciói csak az **USA középső**régiójában, Észak- **Európában**és Kelet- **japán** régióban létrehozott IoT-hubokon érhetők el.

Adja hozzá a Microsoft Azure IoT bővítményt az Azure CLI-hez:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

A következő parancs futtatásával hozza létre az eszköz identitását az IoT hub-ban. Cserélje le a **YourIoTHubName** és a **YourDevice** helyőrzőket a tényleges nevekre. Ha nem rendelkezik IoT Hubval, [az alábbi útmutatást követve hozhat létre egyet](../iot-hub/iot-hub-create-using-cli.md):

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Futtassa az alábbi parancsokat az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Futtassa az alábbi parancsokat a hub _IoT hub-kapcsolódási karakterláncának_ lekéréséhez:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

### <a name="get-azure-iot-device-sdk-for-c"></a>A C Azure IoT Device SDK beszerzése

Ebben a rövid útmutatóban egy fejlesztési környezetet készít elő, amellyel klónozott és felépítheti az Azure IoT C Device SDK-t.

Nyisson meg egy parancssort. A következő parancs végrehajtásával klónozza az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattárat:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Ez a művelet várhatóan több percig is eltarthat.

## <a name="build-the-code"></a>A kód létrehozása

Az Ön által létrehozott alkalmazás szimulál egy olyan eszközt, amely egy IoT hubhoz csatlakozik. Az alkalmazás telemetria és tulajdonságokat küld, és parancsokat fogad.

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

Futtassa az alkalmazást úgy, hogy az IoT hub-eszköz kapcsolatok sztringjét paraméterként adja át.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

Az eszköz megkezdi az adatok küldését a IoT Hubba.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

1. Nyissa meg az Azure IoT Explorert, és megjelenik az **alkalmazás konfigurációja** lap.

1. Adja meg IoT Hub kapcsolati karakterláncát, és kattintson a **Csatlakoztatás**gombra.

1. A csatlakoztatása után megjelenik az eszköz áttekintő lapja.

1. A vállalati tárház hozzáadásához válassza a **Beállítások**, majd az **+ új**, majd a **csatlakoztatott eszköz**elemet.

1. Az eszköz áttekintése lapon keresse meg a korábban létrehozott eszköz identitását, és válassza ki a további részletek megtekintéséhez.

1. Bontsa ki az **urn: YOUR_COMPANY_NAME_HERE: EnvironmentalSensor: 1** azonosítójú felületet a IoT Plug and Play primitívek – tulajdonságok, parancsok és telemetria megtekintéséhez.

1. Válassza ki a **telemetria** lapot, és tekintse meg az eszköz által küldött telemetria adatokat.

1. Válassza a **Tulajdonságok (nem írható)** lapot az eszköz által jelentett nem írható tulajdonságok megtekintéséhez.

1. Válassza a **Tulajdonságok (írható)** lapot a frissíteni kívánt írható tulajdonságok megtekintéséhez.

1. Bontsa **ki a tulajdonságnév, a**frissítés új névvel elemet, majd válassza az **írható tulajdonság frissítése**lehetőséget. 

1. Ha látni szeretné, hogy az új név megjelenik a **jelentett tulajdonság** oszlopban, kattintson a lap tetején található **frissítés** gombra.

1. Kattintson a **parancs** lapra az eszköz által támogatott összes parancs megtekintéséhez.

1. Bontsa ki a **Blink** parancsot, és állítson be egy új villogási időközt. Válassza a **Küldés parancs** lehetőséget a parancs meghívásához az eszközön.

1. Lépjen a szimulált eszközre, és győződjön meg arról, hogy a parancs a várt módon lett végrehajtva.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hubhoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Útmutató: Kapcsolódás egy IoT Plug and Play előnézeti eszközhöz](howto-develop-solution.md)
