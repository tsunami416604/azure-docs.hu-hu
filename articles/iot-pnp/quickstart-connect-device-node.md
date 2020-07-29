---
title: A IoT csatlakoztatása Plug and Play Preview minta Node.js eszköz kódja az Azure IoT Hub | Microsoft Docs
description: A Node.js használatával hozhat létre és futtathat IoT Plug and Play előzetes minta-eszköz kódját, amely egy IoT hubhoz csatlakozik. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: ericmitt
ms.author: ericmitt
ms.date: 07/10/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: f6a3190ec87c0b06aba8f065a3e7518f4a76cf24
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352944"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Gyors útmutató: minta IoT csatlakoztatása Plug and Play Preview-eszköz alkalmazásának IoT Hub (Node.js)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Plug and Play-alkalmazást, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti az általa küldött telemetria. A minta alkalmazás Node.js, és a Node.js Azure IoT eszközoldali SDK-ban szerepel. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez Node.jsra van szükség a fejlesztői gépen. A [NodeJS.org](https://nodejs.org)több platformon is letöltheti a legújabb ajánlott verziót.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

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

Ebben a rövid útmutatóban egy fejlesztési környezetet készít elő, amellyel klónozott és felépítheti a Node.jshez készült Azure IoT Hub eszközoldali SDK-t.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa a következő parancsot a Node.jsGitHub-adattárhoz [tartozó Microsoft Azure IOT SDK](https://github.com/Azure/azure-iot-sdk-node) ezen a helyen történő klónozásához:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>A szükséges kódtárak telepítése

Az eszköz SDK-val felépítheti a mellékelt mintakód-kódot. Az Ön által létrehozott alkalmazás szimulál egy olyan eszközt, amely egy IoT hubhoz csatlakozik. Az alkalmazás telemetria és tulajdonságokat küld, és parancsokat fogad.

1. A helyi terminál ablakban lépjen a klónozott adattár mappájába, és keresse meg a */Azure-IOT-SDK-Node/Device/Samples/PnP* mappát. Ezután futtassa a következő parancsot a szükséges kódtárak telepítéséhez:

    ```cmd/sh
    npm install
    ```

1. Konfigurálja a környezeti változót a korábban megjegyzett eszköz-csatlakoztatási karakterlánccal:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>A minta eszköz futtatása

Nyissa meg a _simple_thermostat.js_ fájlt. Ebben a fájlban a következőket láthatja:

1. Importálja a szükséges adaptereket.
1. Egy tulajdonság-frissítési kezelőt és egy parancssori kezelőt írhat.
1. A kívánt tulajdonság-javítások kezelése és telemetria küldése.
1. Igény szerint kiépítheti az eszközt az Azure Device kiépítési szolgáltatás (DPS) használatával.

A fő függvényben láthatja, hogy az összes hogyan jön össze:

1. Hozza létre az eszközt a kapcsolatok karakterláncában, vagy adja meg a DPS használatával.)
1. A **ModelID** kapcsoló használatával megadhatja a IoT Plug and Play eszköz modelljét.
1. Engedélyezze a parancsot kezelőt.
1. Telemetria küldése az eszközről a hubhoz.
1. Töltse le az eszközöket a Twin és frissítse a jelentett tulajdonságokat.
1. Engedélyezze a kívánt tulajdonság-frissítési kezelőt.

Futtassa a minta alkalmazást egy IoT Plug and Play-eszköz szimulálásához, amely telemetria küld az IoT hubhoz. A minta alkalmazás futtatásához használja a következő parancsot:

```cmd\sh
node simple_thermostat.js
```

A következő kimenet jelenik meg, amely azt jelzi, hogy az eszköz megkezdte a telemetria adatok küldését a központba, és most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására.

![Eszköz megerősítő üzenetei](media/quickstart-connect-device-node/device-confirmation-node.png)

A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hubhoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A megoldáshoz csatlakoztatott IoT Plug and Play előnézeti eszköz használata](quickstart-service-node.md)
