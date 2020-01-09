---
title: A IoT csatlakoztatása Plug and Play minta-eszköz kódja a IoT Hubhoz | Microsoft Docs
description: A C# (.net) használatával hozzon létre és futtasson IoT Plug and Play előzetes minta-eszköz kódját, amely egy IoT hubhoz csatlakozik. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: baanders
ms.author: baanders
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b32fc103b4ed4d7058c8af42ffa126ee5527f45a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550845"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Gyors útmutató: minta IoT csatlakoztatása Plug and Play előnézeti eszköz alkalmazásaC#a IoT hub ()

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Plug and Play-alkalmazást, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti a központnak küldött adatokat. A minta alkalmazás C# (.net-tel) van beírva, és az Azure IoT Samples for C# (.net) gyűjtemény részeként van megadva. A megoldás fejlesztője az Azure IoT Explorer eszköz használatával megismerheti egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez telepítenie kell a .NET Core 2,2-et a fejlesztői gépre. A .NET Core SDK ezen verzióját több platformra is letöltheti a [.net Core 2,2 letöltésével](https://dotnet.microsoft.com/download/dotnet-core/2.2).

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

Ebben a rövid útmutatóban egy fejlesztési környezetet készít elő, amellyel klónozott és felépítheti az Azure IoT- C# mintákat (.net).

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa a következő parancsot az [Azure IoT Samples for C# (.net)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-adattár ezen a helyen történő klónozásához:

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
        dotnet run
    ```

Láthatja, hogy az eszköz regisztrálása sikeresen megtörtént, és a rendszer frissítéseket vár a felhőből. Ez azt jelzi, hogy az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. A **Beállítások**lehetőségre kattintva ellenőrizheti, hogy az eszköz képes-e az eszközön található felületi modell-definíciók beolvasására. Előfordulhat, hogy a beállítások menüben a **csatlakoztatott eszközön** már szerepel a Plug and Play konfigurációk; Ha nem, válassza a **+ modul-definíciós forrás hozzáadása** lehetőséget, majd a **csatlakoztatott eszközön** a hozzáadáshoz.

1. Az **eszközök** áttekintése lapon keresse meg a korábban létrehozott eszköz identitását. Ha az eszköz még fut a parancssorban, ellenőrizze, hogy az eszköz **kapcsolati állapota** az Azure IoT Explorerben _csatlakoztatva_ van-e (ha nem, nyomja meg a **frissítést** , amíg meg nem történik). Válassza ki az eszközt a további részletek megtekintéséhez.

1. Bontsa ki az **urn: csharp_sdk_sample: EnvironmentalSensor: 1** azonosítójú felületet, amely felfedi a felületet és a IoT Plug and Play primitívek – tulajdonságok, parancsok és telemetria.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hubhoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Útmutató: Kapcsolódás egy IoT Plug and Play előnézeti eszközhöz](howto-develop-solution.md)
