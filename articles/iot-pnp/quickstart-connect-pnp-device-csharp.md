---
title: A IoT Plug and Play előnézeti kód összekötése IoT Hub-C#-be | Microsoft Docs
description: A C# (.NET) használatával hozzon létre és futtasson IoT Plug and Play előzetes minta-eszköz kódját, amely egy IoT hubhoz csatlakozik. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d598bbb792c071db281ebdc562402ba0198305ab
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044198"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Gyors útmutató: minta IoT csatlakoztatása Plug and Play Preview-eszköz alkalmazásának IoT Hub (C#)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Plug and Play-alkalmazást, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti a központnak küldött adatokat. A minta alkalmazás C# nyelven (.NET) van megírva, és a C# (.NET) gyűjteményhez készült Azure IoT-minták részeként van megadva. A megoldás fejlesztője az Azure IoT Explorer eszköz használatával megismerheti egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez telepítenie kell a .NET Core 3,0-et a fejlesztői gépre. A .NET Core SDK ezen verzióját több platformra is letöltheti a [.net Core 3,0 letöltésével](https://dotnet.microsoft.com/download/dotnet-core/3.0).

A fejlesztői gépen található .NET-verzió ellenőrzéséhez futtassa a következő parancsot egy helyi terminál ablakban: 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>Az Azure IoT Explorer telepítése

Töltse le és telepítse az **Azure IoT Explorer** legújabb kiadását az eszköz [tárházának](https://github.com/Azure/azure-iot-explorer/releases) oldaláról, ehhez válassza ki az. msi fájlt a legutóbbi frissítéshez az "eszközök" területen.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot a hub _IoT hub-beli kapcsolódási karakterláncának_ lekéréséhez (jegyezze fel később a használatra):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban egy fejlesztési környezetet készít elő, amellyel klónozott és felépítheti a C# (.NET) Azure IoT-mintákat.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa az alábbi parancsot a [C# (.net) GitHub-tárház Azure IoT-mintáinak](https://github.com/Azure-Samples/azure-iot-samples-csharp) klónozásához a következő helyre:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

A művelet végrehajtása több percet is igénybe vehet.

## <a name="run-the-device-sample"></a>Az eszköz mintájának futtatása

A klónozott mintakód használatával olyan alkalmazást hozhat létre, amely egy IoT hubhoz csatlakozó eszközt szimulál. Az alkalmazás telemetria és tulajdonságokat küld, és parancsokat fogad.

1. A helyi terminál ablakban lépjen a klónozott adattár mappájába, és navigáljon az **Azure-IOT-Samples-csharp/digitaltwin/Samples/Device/EnvironmentalSensorSample** mappába. 

1. Az _eszköz-kapcsolatok karakterláncának_konfigurálása:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Futtasson egy IoT Plug and Play eszközt, amely telemetria küld az IoT hubhoz. Ugyanebben a terminálban a szükséges csomagok kiépítéséhez és a minta alkalmazás futtatásához használja a következő parancsot:

    ```cmd\sh
    dotnet run --framework=netcoreapp3.0
    ```

Láthatja, hogy az eszköz regisztrálása sikeresen megtörtént, és a rendszer frissítéseket vár a felhőből. Ez azt jelzi, hogy az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. A **Beállítások**lehetőségre kattintva ellenőrizheti, hogy az eszköz képes-e az eszközön található felületi modell-definíciók beolvasására. Előfordulhat, hogy a beállítások menüben a **csatlakoztatott eszközön** már szerepel a Plug and Play konfigurációk; Ha nem, válassza a **+ modul-definíciós forrás hozzáadása** lehetőséget, majd a **csatlakoztatott eszközön** a hozzáadáshoz.

1. Az **eszközök** áttekintése lapon keresse meg a korábban létrehozott eszköz identitását. Ha az eszköz még fut a parancssorban, ellenőrizze, hogy az eszköz **kapcsolati állapota** az Azure IoT Explorerben _csatlakoztatva_ van-e (ha nem, nyomja meg a **frissítést** , amíg meg nem történik). Válassza ki az eszközt a további részletek megtekintéséhez.

1. Bontsa ki az **urn: csharp_sdk_sample: EnvironmentalSensor: 1** azonosítójú felületet, amely felfedi a felületet és a IoT Plug and Play primitívek – tulajdonságok, parancsok és telemetria.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hubhoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Útmutató: Kapcsolódás egy IoT Plug and Play előnézeti eszközhöz](howto-develop-solution.md)
