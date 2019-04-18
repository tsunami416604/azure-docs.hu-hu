---
title: Fejlesztés az Azure IoT SDK-k használatával Android dolgot platform |} A Microsoft Docs
description: Fejlesztői útmutató – ismerje meg, hogyan hozhat létre az Azure IoT Hub SDK-k használatával Android dolog.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: yizhon
ms.openlocfilehash: 8e36cee9857c00fcb618a8491595432fb0fd60fd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264573"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Fejlesztés az Azure IoT SDK-k használatával Android dolgot platform

[Az Azure IoT Hub SDK-k](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) első szintű támogatást nyújt a népszerű platformokhoz, mint a Windows, Linux, OSX, MBED és mobil platformokon, például az Android és IOS rendszerek esetén.  Ahhoz, hogy nagyobb rugalmasságának és szabadságának IoT üzemelő elkötelezettségünk részeként, a Java SDK-t is támogatja [Android dolgot](https://developer.android.com/things/) platform.  A fejlesztők kihasználhatják a dolgok Android operációs rendszer eszköz oldalán előnyeinek használata során [Azure IoT Hub](about-iot-hub.md) a központi üzenetnek számít hub méretezhető több millió egyszerre csatlakoztatott eszközök.

Ez az oktatóanyag ismerteti, egy eszköz kiszolgálóoldali alkalmazások gépen az Azure IoT-Java SDK használatával Android dolog.

## <a name="prerequisites"></a>Előfeltételek

* Egy Android dolgot Android dolgot operációs rendszert futtató hardver támogatott.  Követheti [Android dolgot dokumentáció](https://developer.android.com/things/get-started/kits#flash-at) Android dolgot operációs rendszer flash való.  Ellenőrizze, hogy az Android dolgot eszköz csatlakoztatva van például a billentyűzet, megjelenítési és csatlakoztatott egér alapvető perifériák az interneten keresztül.  Ebben az oktatóanyagban a Raspberry Pi 3.

* Legújabb verziójának [Android Studio](https://developer.android.com/studio/)

* Legújabb verziójának [Git](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsokat az Azure Cloud Shellben az IoT Hub CLI-bővítmény hozzáadásához és az eszközidentitás létrehozásához.

   **YourIoTHubName** : Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

   **MyAndroidThingsDevice** : Ez az eszköz a megadott név. Használjon MyAndroidThingsDevice látható módon. Ha úgy dönt, hogy eszközének egy másik nevet választ, akkor az egész cikkben azt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell bennük az eszköznevet.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben beolvasni a *eszköz kapcsolati karakterláncának* az imént regisztrált eszköz. Cserélje le `YourIoTHubName` alább nevét, válassza ki az IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

## <a name="building-an-android-things-application"></a>Egy dolog Android-alkalmazás létrehozása

1. Az Android dolgot alkalmazások készítésének első lépése a dolgok Androidos eszközök csatlakoztatása. Az Android dolgot eszköz csatlakoztatása a megjelenítési, és csatlakoztassa az internethez. Android dolgot biztosítanak [dokumentáció](https://developer.android.com/things/get-started/kits) való csatlakozás Wi-Fi. Miután csatlakozott az internethez, jegyezze fel a hálózatokhoz tartozó IP-cím.

2. Használja a [adb](https://developer.android.com/studio/command-line/adb) eszközzel csatlakozzon az Android dolgot eszközre a fentebb feltüntetett IP-címmel. Ellenőrizze a kapcsolatot a következő paranccsal a terminálról. Az eszközök "csatlakoztatva" állapottal kell megjelennie.

   ```
   adb devices
   ```

3. Töltse le ebben a mintában Ez az Android és Android-eszközök hálózatához [tárház](https://github.com/Azure-Samples/azure-iot-samples-java) vagy a Git használatával.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. Az Android Studióban nyissa meg a található "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample" az Android-projekt.

5. Nyissa meg gradle.properties fájlt, és cserélje le a "Device_connection_string" az eszköz kapcsolati karakterláncát a korábban feljegyzett.
 
6. Kattintson a Run - hibakeresés, és válassza ki az eszközt, ezt a kódot a dolgok Android-eszközökre telepíteni kívánt.

7. Amikor az alkalmazás sikeresen elindult, megjelenik a dolgok Androidos eszközön futó alkalmazások. Ez a mintaalkalmazás véletlenszerűen generált hőmérsékletét küld.

## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról

Megtekintheti az adatokat az IoT hub segítségével, kapott formában. Az IoT Hub CLI-bővítmény csatlakozhat a szolgáltatásoldali **Események** végponthoz az IoT Hubon. A bővítmény fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Az IoT Hub-háttéralkalmazások általában a felhőben futnak, hogy fogadják és feldolgozzák az eszközről a felhőbe küldött üzeneteket.

Futtassa a következő parancsokat az Azure Cloud Shellben úgy, hogy a `YourIoTHubName` helyére az IoT Hub neve kerüljön:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

* Ismerje meg [kezelése, kapcsolatok és megbízható üzenetküldést](iot-hub-reliability-features-in-sdks.md) az IoT Hub SDK-k használatával.
* Hogyan [mobilplatformokra fejlesztése](iot-hub-how-to-develop-for-mobile-devices.md) például az iOS és Android.
* [Az Azure IoT SDK-val eszközplatform-támogatás](iot-hub-device-sdk-platform-support.md)
