---
title: IoT Plug and Play előzetes verziójú eszközkód csatlakoztatása az IoT Hubhoz | Microsoft dokumentumok
description: A Node.js használatával hozzon létre és futtasson IoT Plug and Play preview mintaeszköz-kódot, amely egy IoT-központhoz csatlakozik. Az Azure IoT-kezelő eszközzel megtekintheti az eszköz által a hubra küldött információkat.
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c5d69ae21cd240b0c68b9694a55de2cf879a1966
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964787"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Rövid útmutató: Minta IoT Plug and Play előzetes verziójú eszközalkalmazás csatlakoztatása az IoT Hubhoz (Node.js)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy minta IoT Plug and Play eszközalkalmazást, hogyan csatlakoztathatja az IoT-központhoz, és az Azure IoT Explorer eszközzel megtekintheti a hubnak küldött információkat. A mintaalkalmazás a Node.js,és szerepel az Azure IoT Hub Device SDK node.js. A megoldásfejlesztő az Azure IoT-felfedező eszközzel anélkül, hogy bármilyen eszközkódot kellene megtekintenie, az IoT Plug and Play eszközök képességeit.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához szüksége van a node.js a fejlesztői gépen. Letöltheti a legújabb ajánlott verziót több platformra [a nodejs.org.](https://nodejs.org)

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

### <a name="install-the-azure-iot-explorer"></a>Az Azure IoT-felfedező telepítése

Töltse le és telepítse az **Azure IoT Explorer** legújabb kiadását az eszköz [tárházlapjáról,](https://github.com/Azure/azure-iot-explorer/releases) válassza ki az .msi fájlt az "Eszközök" alatt a legújabb frissítéshez.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot az _IoT hub kapcsolati karakterláncának_ lekérnia a hubhoz (megjegyzés későbbi használatra):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban előkészíti a fejlesztői környezetet, amely nek segítségével klónozhatja és létrehozhatja az Azure IoT Hub-eszköz SDK-t a Node.js számára.

Nyisson meg egy parancssort az Ön által választott könyvtárban. A következő parancs végrehajtása a [Microsoft Azure IoT SDK node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub-tárházának ezen a helyre történő klónozásához:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node --recursive -b digitaltwins-preview
```

Ez a művelet több percig is eltarthat.

## <a name="install-required-libraries"></a>Szükséges tárak telepítése

Az sdk eszköz segítségével hozhatja létre a mellékelt mintakódot. Az alkalmazást hoz létre szimulálja egy eszköz, amely csatlakozik egy IoT hub. Az alkalmazás telemetriai adatokat és tulajdonságokat küld, és parancsokat fogad.

1. Egy helyi terminálablakban nyissa meg a klónozott tárház mappáját, és keresse meg az **/azure-iot-sdk-node/digitaltwins/samples/device/javascript** mappát. Ezután futtassa a következő parancsot a szükséges könyvtárak telepítéséhez:

    ```cmd/sh
    npm install
    ```
1. Az _eszköz kapcsolati karakterláncának konfigurálása_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>A mintaeszköz futtatása

Futtasson egy mintaalkalmazást az SDK-ban egy IoT Plug and Play-eszköz szimulálására, amely telemetriát küld az IoT hubnak. A mintaalkalmazás futtatásához használja a következő parancsot:

```cmd\sh
    node sample_device.js
```

A következő kimenet jelenik meg, jelezve, hogy az eszköz megkezdte a telemetriai adatok küldését a hubra, és most már készen áll a parancsok és a tulajdonságfrissítések fogadására.

   ![Eszköz-megerősítő üzenetek](media/quickstart-connect-pnp-device/device-confirmation-node.png)

 A következő lépések végrehajtásával tartsa a mintát.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítéséhez használja az Azure IoT-felfedezőt

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Annak érdekében, hogy az eszköz le tudja olvasni a felületmodell-definíciókat az eszközről, válassza a **Beállítások lehetőséget.** A Beállítások menüben **előfordulhat,** hogy a csatlakoztatott eszközön már megjelennek a Plug and Play konfigurációkban; ha nem, válassza a **+ Add module definition source** and then On the connected **device** to add it.

1. Az **Eszközök** áttekintése lapon keresse meg a korábban létrehozott eszközidentitást. Ha az eszközalkalmazás továbbra is fut a parancssorban, ellenőrizze, hogy az eszköz **kapcsolati állapota** az Azure IoT Explorer _ben csatlakoztatottként jelent-e_ (ha nem, nyomja meg a **Frissítés** gombot, amíg nincs). A további részletek megtekintéséhez válassza ki az eszközt.

1. Bontsa ki a felületet az ID **urn:contoso:com:EnvironmentalSensor:1** paranccsal, hogy felfedje a felületet és az IoT Plug and Play primitíveket – tulajdonságokat, parancsokat és telemetriai adatokat.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat ioT Plug and Play-eszközt egy IoT-központhoz. Ha többet szeretne tudni arról, hogyan hozhat létre olyan megoldást, amely kölcsönhatásba lép az IoT Plug and Play eszközeivel, olvassa el a következő témaköröket:

> [!div class="nextstepaction"]
> [Útmutató: Csatlakozás IoT Plug and Play előzetes verziójához és használata](howto-develop-solution.md)
