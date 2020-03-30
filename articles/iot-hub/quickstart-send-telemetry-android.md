---
title: Telemetria küldése az Azure IoT Hub rövid útmutató (Android) | Microsoft dokumentumok
description: Ebben a rövid útmutatóban futtat egy minta Android-alkalmazást szimulált telemetriai adatok küldéséhez egy IoT hubba, és az IoT hubt a felhőben történő feldolgozáshoz.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: e239f8c6a7738500176b84589f2032636eeb71e5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675440"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Rövid útmutató: IoT-telemetria küldése Android-eszközről

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Ebben a rövid útmutatóban telemetriai adatokat küld egy Azure IoT Hub egy fizikai vagy szimulált eszközön futó Android-alkalmazásból. Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ez a rövid útmutató egy előre megírt Android-alkalmazást használ a telemetriai adatok küldéséhez. A telemetriai adatokat az IoT Hub az Azure Cloud Shell használatával fogja olvasni. Az alkalmazás futtatása előtt hozzon létre egy IoT hubot, és regisztráljon egy eszközt a hub.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android Studio Android SDK 27](https://developer.android.com/studio/). További információ: [android-installation](https://developer.android.com/studio/install). Az Android SDK 27-et a cikkben szereplő minta használja.

* [A minta Android alkalmazás](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample). Ez a minta az [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) tárház része.

* A 8883-as port megnyílik a tűzfalon. A rövid útmutatóban szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-bővítmény hozzáadása

Futtassa a következő parancsot, és adja hozzá a Microsoft Azure IoT-bővítményt az Azure CLI-hez a Cloud Shell-példányhoz. Az IOT-bővítmény hozzáadja az IoT Hub, az IoT Edge és az IoT-eszközlétesítési szolgáltatás (DPS) adott parancsokat az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa a következő parancsot az Azure Cloud Shellben az eszközidentitás létrehozásához.

   **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz választott névvel.

   **MyAndroidDevice**: Ez a regisztráló eszköz neve. Javasoljuk, hogy használja **a MyAndroidDevice-t az** ábrán látható módon. Ha más nevet választ az eszközhöz, akkor ezt a nevet is használnia kell a cikkben, és frissítenie kell az eszköz nevét a mintaalkalmazásokban, mielőtt futtatna őket.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Futtassa a következő parancsot az Azure Cloud Shellben az _eszköz kapcsolati karakterláncának_ lekérni az imént regisztrált eszközhöz:

    **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz választott névvel.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket fogja használni később ebben a rövid útmutatóban telemetriai adatok küldéséhez.

## <a name="send-simulated-telemetry"></a>Szimulált telemetria küldése

1. Nyissa meg a GitHub minta Android-projektet az Android Studio-ban. A projekt az [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) tárház klónozott vagy letöltött példányának következő könyvtárában található.

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. Az Android Studio alkalmazásban nyissa meg a *mintaprojekt gradle.properties* szolgáltatását, és cserélje le a **Device_Connection_String** helyőrzőt a korábban feljegyezni eszközkapcsolati karakterláncra.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Az Android Studio alkalmazásban kattintson a > **Fájlszinkronizálási projekt a Gradle-fájlokkal**elemre. **File** Ellenőrizze a build befejeződését.

   > [!NOTE]
   > Ha a projekt szinkronizálása sikertelen, annak a következő okai lehetnek:
   >
   > * A változat -ból Android Gradle dugó és Gradle hivatkozott -ban tervez van kikésett -ból időpont részére -a változat -ból Android Műterem. Kövesse [az alábbi utasításokat,](https://developer.android.com/studio/releases/gradle-plugin) hogy hivatkozzon, és telepítse a megfelelő verziókat a plugin és Gradle a telepítéshez.
   > * Az Android SDK licencszerződése nincs aláírva. Kövesse a Build kimenet ben található utasításokat a licencszerződés aláírásához és az SDK letöltéséhez.

4. Miután a build befejeződött, kattintson **a Run Run** > **'app'** gombra. Állítsa be az alkalmazást úgy, hogy fizikai Android-eszközön vagy Android-emulátoron fusson. Az Android-alkalmazások fizikai eszközön vagy emulátoron való futtatásáról az [Alkalmazás futtatása című](https://developer.android.com/training/basics/firstapp/running-app)témakörben talál további információt.

5. Miután az alkalmazás betöltődik, kattintson a **Start** gombra a telemetriai adatok küldésének megkezdéséhez az IoT Hub:

    ![Alkalmazás](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról

Ebben a szakaszban az Azure Cloud Shell az [IoT-bővítmény](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) segítségével figyelheti az Android-eszköz által küldött eszközüzeneteket.

1. Az Azure Cloud Shell használatával futtassa a következő parancsot az IoT Hubhoz történő csatlakozáshoz és az üzenetek olvasásához:

   **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz választott névvel.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    A következő képernyőkép a kimenetet mutatja, ahogy az IoT hub megkapja az Android-eszköz által küldött telemetriai adatokat:

      ![Olvassa el az eszköz üzeneteit az Azure CLI használatával](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban beállít egy IoT hubot, regisztrált egy eszközt, szimulált telemetriai adatokat küldött a hubnak egy Android-alkalmazás használatával, és elolvassa a telemetriai adatokat a központból az Azure Cloud Shell használatával.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése](quickstart-control-device-android.md)