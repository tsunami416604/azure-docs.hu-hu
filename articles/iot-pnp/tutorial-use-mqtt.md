---
title: A MQTT használata IoT Plug and Play előzetes verziójú eszköz ügyfelének létrehozásához | Microsoft Docs
description: A MQTT protokoll használatával hozzon létre egy IoT Plug and Play előzetes verziójú eszköz ügyfelet az Azure IoT-eszköz SDK-k használata nélkül
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 56463b03fe633959585e14271050bcdaacb25663
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535680"
---
# <a name="use-mqtt-to-develop-an-iot-plug-and-play-preview-device-client"></a>A MQTT használata IoT Plug and Play előzetes verziójú eszköz ügyfelének fejlesztéséhez

Az Azure IoT-eszközök SDK-k egyikével kell felépíteni a IoT Plug and Play-eszköz ügyfeleit, ha ez egyáltalán lehetséges. Olyan forgatókönyvekben azonban, mint például a memóriában korlátozott eszköz használata, előfordulhat, hogy egy MQTT-függvénytárat kell használnia az IoT hub-vel való kommunikációhoz.

Az oktatóanyagban szereplő minta az [Eclipse Mosquitto](http://mosquitto.org/) MQTT könyvtárat és a Visual studiót használja. Az oktatóanyag lépései azt feltételezik, hogy a Windowst használja a fejlesztői gépen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag Windows rendszeren történő elvégzéséhez telepítse a következő szoftvereket a helyi Windows-környezetbe:

* [Visual Studio (közösségi, szakmai vagy vállalati)](https://visualstudio.microsoft.com/downloads/) – ügyeljen arra, hogy a Visual Studio [telepítésekor](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) a C++ számítási feladatait is tartalmazza az **asztali fejlesztés**
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)
* [Azure IoT Explorer](howto-install-iot-explorer.md)

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot, hogy lekérje a megosztott hozzáférés aláírását az eszköznek a hubhoz való kapcsolódáshoz. Jegyezze fel ezt a karakterláncot, amelyet később az oktatóanyagban fog használni:

```azurecli-interactive
az iot hub generate-sas-token -d <YourDeviceID> -n <YourIoTHubName>
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Az **Azure IoT Explorer** eszközének konfigurálásához használja az IoT hub kapcsolódási karakterláncát:

1. Indítsa el az **Azure IoT Explorer** eszközt.
1. A **Beállítások** lapon illessze be az IoT hub-kapcsolatok karakterláncát az **alkalmazás-konfigurációk** beállításaiba.
1. Válassza **a Mentés és a kapcsolat**lehetőséget.
1. A korábban hozzáadott eszköz a Főoldal eszközök listájában található.

## <a name="clone-sample-repo"></a>A klónozott minta tárháza

Használja a következő parancsot a minta tárház klónozásához a helyi gépen található megfelelő helyre:

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>A MQTT-könyvtár telepítése

Az `vcpkg` eszköz használatával letöltheti és felépítheti az Eclipse Mosquitto könyvtárat.

Használja a következő parancsot a **Vcpkg** -tárháznak a helyi gépen található megfelelő helyre történő klónozásához:

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

A környezet előkészítéséhez használja a következő parancsokat `vcpkg` . A futtatásakor rendszergazda jogú parancssor szükséges `vcpkg integrate install` :

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

Az Eclipse Mosquitto könyvtárának letöltéséhez és létrehozásához használja a következő parancsot:

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>A minta migrálása a IoT-Plug and Play

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>A nem IoT Plug and Play mintakód áttekintése

Frissítse a kódot az IoT hub és az eszköz részletes adataival, mielőtt felépíti és futtatja.

Ha a Visual Studióban szeretné megtekinteni a mintakódt, nyissa meg a *MQTTWin32. SLN* Solution fájlt a *IoTMQTTSample\src\Windows* mappában.

**Megoldáskezelő**kattintson a jobb gombbal a **TelemetryMQTTWin32** projektre, és válassza a **beállítás indítási projektként**lehetőséget.

A **TelemetryMQTTWin32** projektben nyissa meg a **MQTT_Mosquitto. cpp** forrásfájlt. Frissítse a kapcsolati adatok definícióit a korábban létrehozott eszköz adataival. Cserélje le a jogkivonat-karakterlánc helyőrzőjét a következőre:

* `IOTHUBNAME`az azonosító a létrehozott IoT hub nevével.
* `DEVICEID`az azonosító a létrehozott eszköz nevével.
* `PWD`az eszközhöz generált megosztott hozzáférés-aláírási értékkel rendelkező azonosító.

Győződjön meg arról, hogy a kód megfelelően működik, az Azure IoT Explorer indításával megkezdheti a telemetria figyelését.

Futtassa az alkalmazást (CTRL + F5), néhány másodperc elteltével a következőhöz hasonló kimenet jelenik meg:

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="MQTT-minta alkalmazás kimenete":::

Az Azure IoT Explorerben láthatja, hogy az eszköz nem IoT Plug and Play eszköz:

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Nem IoT Plug and Play eszköz az Azure IoT Explorerben":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>Az eszköz IoT Plug and Play eszközvé tétele

A IoT Plug and Play eszköznek egyszerű konvenciókat kell követnie. Ha egy eszköz a csatlakozáskor egy modell-azonosítót küld, a IoT Plug and Play eszköz lesz.

Ebben a példában a * * modell AZONOSÍTÓját adja hozzá a MQTT-kapcsolati csomaghoz. Adja át a modell AZONOSÍTÓját a querystring paraméterként, `USERNAME` és módosítsa a következőt `api-version` `2020-05-31-preview` :

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-05-31-preview&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

A minta újraépítése és futtatása.

Az eszköz Twin csomag tartalmazza a modell AZONOSÍTÓját:

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="A modell AZONOSÍTÓjának megtekintése az Azure IoT Explorerben":::

Most már megtekintheti a IoT Plug and Play összetevőjét:

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="Összetevők megtekintése az Azure IoT Explorerben":::

Mostantól módosíthatja az eszköz kódját a modellben definiált telemetria, tulajdonságok és parancsok megvalósításához. Ha szeretné megtekinteni egy példát a termosztátos eszköz Mosquitto-függvénytár használatával történő megvalósítására, tekintse [meg a MQTT PnP használata az Azure IoTHub szolgáltatással a IOT SDK nélkül](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32) a githubon.

> [!NOTE]
> Alapértelmezés szerint a közös hozzáférésű aláírások csak 60 percre érvényesek.

> [!NOTE]
>Az ügyfél a `IoTHubRootCA_Baltimore.pem` Főtanúsítvány fájljának használatával ellenőrzi, hogy az IoT hub milyen identitással csatlakozik.

### <a name="mqtt-topics"></a>MQTT témakörök

A következő definíciók az eszköz által az IoT hub-ra való adatküldéshez használt MQTT-témakörök. További információ: kommunikáció az [IoT hub használatával a MQTT protokollal](../iot-hub/iot-hub-mqtt-support.md):

* A `DEVICE_CAPABILITIES_MESSAGE` meghatározza az eszköz által a által megvalósított felületek jelentésére használt témakört.
* A `DEVICETWIN_PATCH_MESSAGE` meghatározza azt a témakört, amelyet az eszköz használ a IoT hub tulajdonság-frissítéseinek jelentéséhez.
* A `DEVICE_TELEMETRY_MESSAGE` meghatározza azt a témakört, amelyet az eszköz használ a telemetria az IoT hubhoz való küldéséhez.

A MQTT kapcsolatos további információkért tekintse meg az Azure IoT GitHub-tárház [MQTT-mintáit](https://github.com/Azure-Samples/IoTMQTTSample/) .

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan módosíthat egy MQTT-eszköz ügyfelet a IoT Plug and Play konvenciók követéséhez. A IoT Plug and Play további tudnivalókért lásd:

> [!div class="nextstepaction"]
> [Architektúra](concepts-architecture.md)

Ha többet szeretne megtudni a MQTT protokoll IoT Hub támogatásáról, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Kommunikáció az IoT hub használatával a MQTT protokollal](../iot-hub/iot-hub-mqtt-support.md)
