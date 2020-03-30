---
title: IoT Plug and Play előzetes verziójú eszközkód csatlakoztatása az IoT Hubhoz | Microsoft dokumentumok
description: Java használatával hozhat létre és futtathatja az IoT Plug and Play előzetes verzióablak-kezelőeszköz-kódot, amely egy IoT-központhoz csatlakozik. Az Azure IoT-kezelő eszközzel megtekintheti az eszköz által a hubra küldött információkat.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: aa676dd374eccf2a4b5c4622689ed402c8679e5a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964821"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-java"></a>Rövid útmutató: Csatlakoztasson egy minta IoT Plug and Play előzetes verziójú eszközalkalmazást az IoT Hubhoz (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy minta IoT Plug and Play eszközalkalmazást, hogyan csatlakoztathatja az IoT-központhoz, és az Azure IoT Explorer eszközzel megtekintheti a hubnak küldött információkat. A mintaalkalmazás Java nyelven íródott, és az Azure IoT-minták Java-hoz gyűjtemény részeként biztosított. A megoldásfejlesztő az Azure IoT-felfedező eszközzel anélkül, hogy bármilyen eszközkódot kellene megtekintenie, az IoT Plug and Play eszközök képességeit.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához java SE 8-ra van szüksége a fejlesztői gépen. Azt is telepítenie kell Maven 3.

A rról, hogy miként állíthatja be ezeket, [olvassa el a fejlesztői környezet előkészítése](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) a Microsoft Azure IoT-eszköz Java-hoz sdk-ban című témakört.

### <a name="install-the-azure-iot-explorer"></a>Az Azure IoT-felfedező telepítése

Töltse le és telepítse az **Azure IoT Explorer** legújabb kiadását az eszköz [tárházlapjáról,](https://github.com/Azure/azure-iot-explorer/releases) válassza ki az .msi fájlt az "Eszközök" alatt a legújabb frissítéshez.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot az _IoT hub kapcsolati karakterláncának_ lekérnia a hubhoz (megjegyzés későbbi használatra):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban előkészíti a fejlesztői környezetet, amely nek segítségével klónozhatja és létrehozhatja az Azure IoT-minták java-hoz.

Nyisson meg egy terminálablakot az Ön által választott könyvtárban. A következő parancs végrehajtása a Java GitHub-tárház [Azure IoT-mintáinak](https://github.com/Azure-Samples/azure-iot-samples-java) ezen a helyre történő klónozásához:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-java
```

Ez a művelet több percig is eltarthat.

## <a name="build-the-code"></a>A kód létrehozása

A klónozott mintakódot egy iT-hubhoz csatlakozó eszközt szimuláló alkalmazás létrehozásához használja. Az alkalmazás telemetriai adatokat és tulajdonságokat küld, és parancsokat fogad.

1. Egy helyi terminálablakban nyissa meg a klónozott tárház mappáját, és keresse meg az **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample** mappát. Ezután futtassa a következő parancsot a szükséges kódtárak telepítéséhez és a szimulált eszközalkalmazás létrehozásához:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Az _eszköz kapcsolati karakterláncának konfigurálása_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-device-sample"></a>Az eszközminta futtatása

Egy mintaalkalmazás szimulálása ioT Plug and Play eszköz, amely telemetriai adatokat küld az IoT hub. A mintaalkalmazás futtatásához használja a következő parancsot:

```cmd\sh
java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
```

Megjelenik az üzenetek, amelyek arról szólnak, hogy az eszköz csatlakoztatva van, különböző telepítési lépéseket hajt végre, és várja a szolgáltatás frissítéseit, majd telemetriai naplók. Ez azt jelzi, hogy az eszköz most már készen áll a parancsok és a tulajdonságfrissítések fogadására, és megkezdte a telemetriai adatok küldését a hubra. A következő lépések végrehajtásával tartsa a mintát.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítéséhez használja az Azure IoT-felfedezőt

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Annak érdekében, hogy az eszköz le tudja olvasni a felületmodell-definíciókat az eszközről, válassza a **Beállítások lehetőséget.** A Beállítások menüben **előfordulhat,** hogy a csatlakoztatott eszközön már megjelennek a Plug and Play konfigurációkban; ha nem, válassza a **+ Add module definition source** and then On the connected **device** to add it.

1. Az **Eszközök** áttekintése lapon keresse meg a korábban létrehozott eszközidentitást. Ha az eszközalkalmazás továbbra is fut a parancssorban, ellenőrizze, hogy az eszköz **kapcsolati állapota** az Azure IoT Explorer _ben csatlakoztatottként jelent-e_ (ha nem, nyomja meg a **Frissítés** gombot, amíg nincs). A további részletek megtekintéséhez válassza ki az eszközt.

1. Bontsa ki a felületet az ID **urn:java_sdk_sample:EnvironmentalSensor:1** paranccsal, hogy felfedje a felületet és az IoT Plug and Play primitíveket – tulajdonságokat, parancsokat és telemetriai adatokat.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat ioT Plug and Play-eszközt egy IoT-központhoz. Ha többet szeretne tudni arról, hogyan hozhat létre olyan megoldást, amely kölcsönhatásba lép az IoT Plug and Play eszközeivel, olvassa el a következő témaköröket:

> [!div class="nextstepaction"]
> [Útmutató: Csatlakozás IoT Plug and Play előzetes verziójához és használata](howto-develop-solution.md)
