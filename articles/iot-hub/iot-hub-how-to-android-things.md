---
title: Fejlesztés androidos dolgokhoz platformhoz az Azure IoT SDK-k használatával | Microsoft dokumentumok
description: Fejlesztői útmutató – Ismerje meg, hogyan fejleszthet androidos műveleteken az Azure IoT Hub SDK-k használatával.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: a06583e9aab4b082517d47c1022f7bec5184b9bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673382"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Fejlesztés az Android Things platformhoz az Azure IoT SDK-k használatával

[Az Azure IoT Hub SDK-k](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) első szintű támogatást nyújtanak olyan népszerű platformokhoz, mint a Windows, linuxos, OSX, MBED és mobil platformok, például az Android és az iOS.  Az IoT-telepítések nagyobb választékának és rugalmasságának lehetővé tétele iránti elkötelezettségünk részeként a Java SDK támogatja az [Android Things](https://developer.android.com/things/) platformot is.  A fejlesztők kihasználhatják az Android Things operációs rendszer előnyeit az eszköz oldalán, miközben az [Azure IoT Hubot](about-iot-hub.md) használják központi üzenetközpontként, amely egyszerre több millió csatlakoztatott eszközre méretezhető.

Ez az oktatóanyag ismerteti az eszközoldali alkalmazás létrehozásának lépéseit az Azure IoT Java SDK használatával az Android Things szolgáltatásban.

## <a name="prerequisites"></a>Előfeltételek

* Android Things által támogatott hardver androidos operációs rendszer futtatásával.  Tudod követ [Android Dolog dokumentáció](https://developer.android.com/things/get-started/kits#flash-at) -ra hogyan -hoz villanás Android Dolog OS.  Győződjön meg arról, hogy az Android Things eszköz csatlakozik az internethez az alapvető perifériák, például a billentyűzet, a kijelző és az egér csatlakoztatva.  Ez az oktatóanyag a Raspberry Pi 3-at használja.

* Az [Android Studio](https://developer.android.com/studio/) legújabb verziója

* A [Git](https://git-scm.com/) legújabb verziója

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsokat az Azure Cloud Shellben az IoT Hub CLI-bővítmény hozzáadásához és az eszközidentitás létrehozásához.

   **YourIoTHubName** : az alábbi helyőrző helyére írja be az IoT Hubjához választott nevet.

   **MyAndroidThingsDevice** : Ez a regisztrált eszköz neve. Használja a MyAndroidThingsDevice-t az ábrán látható módon. Ha úgy dönt, hogy eszközének egy másik nevet választ, akkor az egész cikkben azt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell bennük az eszköznevet.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Futtassa a következő parancsokat az Azure Cloud Shellben az *eszköz kapcsolati karakterláncának* lekérni az imént regisztrált eszközhöz. Cserélje `YourIoTHubName` le az alábbi nevet az IoT hub hoz kiválasztott.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

## <a name="building-an-android-things-application"></a>Android Things alkalmazás létrehozása

1. Az androidos things alkalmazás létrehozásának első lépése az Android Things eszközökhöz való csatlakozás. Csatlakoztassa androidos eszközök eszközét egy kijelzőhöz, és csatlakoztassa az internethez. Az Android Things [dokumentációt](https://developer.android.com/things/get-started/kits) nyújt a WiFi-hez való csatlakozásról. Miután csatlakozott az internethez, jegyezze fel a Hálózatok csoportban felsorolt IP-címet.

2. Az [ADB](https://developer.android.com/studio/command-line/adb) eszközzel csatlakozzon androidos eszközökhöz a fent említett IP-címmel. Ellenőrizze a kapcsolatot ezzel a paranccsal a terminálról. Az eszközöknek "csatlakoztatva" kell lennie.

   ```
   adb devices
   ```

3. Töltse le a mintát az Android / Android things ebből [a tárházból,](https://github.com/Azure-Samples/azure-iot-samples-java) vagy használja Git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. Az Android Studio alkalmazásban nyissa meg az Android-projektet az "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample" mappában.

5. Nyissa meg a gradle.properties fájlt, és cserélje le a "Device_connection_string" szót a korábban feljegyzett eszközkapcsolati karakterláncra.
 
6. Kattintson a Futtatás - Hibakeresés gombra, és válassza ki a készüléket, hogy telepítse ezt a kódot az Android Things eszközökre.

7. Amikor az alkalmazás sikeresen elindult, láthatja az Android Things eszközön futó alkalmazást. Ez a mintaalkalmazás véletlenszerűen generált hőmérséklet-értékeket küld.

## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról

Az adatokat az IoT hubon keresztül tekintheti meg, ahogy azok fogadása történik. Az IoT Hub CLI-bővítmény csatlakozhat a szolgáltatásoldali **Események** végponthoz az IoT Hubon. A bővítmény fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Az IoT Hub-háttéralkalmazások általában a felhőben futnak, hogy fogadják és feldolgozzák az eszközről a felhőbe küldött üzeneteket.

Futtassa a következő parancsokat az Azure Cloud Shellben úgy, hogy a `YourIoTHubName` helyére az IoT Hub neve kerüljön:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

* Ismerje [meg, hogyan kezelheti a kapcsolatot és a megbízható üzenetküldést](iot-hub-reliability-features-in-sdks.md) az IoT Hub SDK-k használatával.
* További információ [arról, hogyan fejleszthet mobilplatformokra,](iot-hub-how-to-develop-for-mobile-devices.md) például iOS-re és Androidra.
* [Az Azure IoT SDK platformtámogatása](iot-hub-device-sdk-platform-support.md)
