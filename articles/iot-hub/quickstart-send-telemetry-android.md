---
title: Telemetria küldése (Android). az Azure IoT Hub rövid |} A Microsoft Docs
description: Ebben a rövid útmutatóban egy Android-mintaalkalmazás egy IoT hubra szimulált telemetria küldése és telemetria olvasásához az IoT hub feldolgozásához a felhőben futtat.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/05/2018
ms.author: wesmc
ms.openlocfilehash: 8f30918a73e1e7012ad3be151e778eb073627b84
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726685"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Gyors útmutató: IoT-telemetriát küld egy Android-eszközön

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ez a rövid útmutatóban telemetriát küld az IoT Hub egy Android-alkalmazás futtatása egy fizikai vagy szimulált eszközön.

A rövid útmutatóban egy előre megírt Android-alkalmazás használatával a telemetriai adatokat küldhet. A telemetriai rendszer nem olvas be az IoT hubon az Azure Cloud Shell használatával. Az alkalmazás futtatása előtt hozzon létre egy IoT hubot, és eszköz regisztrálása az agyhoz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Az Android studio https://developer.android.com/studio/. Az Android Studio telepítési kapcsolatos további információkért lásd: [android-telepítés](https://developer.android.com/studio/install). 

* Android SDK 27 ebben a cikkben a mintát használja. 

* A [mintaalkalmazás Android](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) futtatása ebből a rövid útmutató az azure-iot-minták – java tárházban a Githubon részét képezi. Töltse le vagy klónozza a [azure-iot-minták – java](https://github.com/Azure-Samples/azure-iot-samples-java) tárház.



## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsokat az Azure Cloud Shellben az IoT Hub CLI-bővítmény hozzáadásához és az eszközidentitás létrehozásához. 

   **YourIoTHubName**: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

   **MyAndroidDevice**: MyAndroidDevice az eszköz a megadott név. Használjon MyAndroidDevice látható módon. Ha úgy dönt, hogy eszközének egy másik nevet választ, akkor az egész cikkben azt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell bennük az eszköznevet.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

    **YourIoTHubName**: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Telemetriát küldjön az útmutató későbbi részében használni ezt az értéket.

## <a name="send-telemetry"></a>Telemetria küldése

1. Az Android Studióban nyissa meg a GitHub-minta Androidos projekt. Klónozott vagy letöltött példány a következő könyvtárban található a projekt [azure-iot-minta – java](https://github.com/Azure-Samples/azure-iot-samples-java) tárház.

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. Az Android Studióban nyissa meg a *gradle.properties* a mintaprojektet, és cserélje le a **Device_Connection_String** helyőrző eszköz korábban lejegyzett kapcsolati karakterláncra.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Az Android Studióban kattintson **fájl** > **projekt szinkronizálása a Gradle-fájlokkal**. Ellenőrizze, hogy a build létrehozása után.

   > [!NOTE]
   > Ha a projekt szinkronizálása sikertelen, a következő okok valamelyike lehet:
   >
   > * Az Android Gradle beépülő modul és a gradle-t a projekt hivatkozott verziók elavultak az Android Studio verziójának. Hajtsa végre a [ezek az utasítások](https://developer.android.com/studio/releases/gradle-plugin) való hivatkozást, és telepítheti a megfelelő verzióját a beépülő modul és a gradle-t a telepítéshez.
   > * A licencszerződés feltételeit, az Android SDK még nincs aláírva. Kövesse az utasításokat a felépítési művelet kimenetében a licencszerződés feltételeit és az SDK letöltéséhez.

4. Kattintson a létrehozás befejezése után **futtatása** > **"alkalmazás" futtatása**. Állítsa be az alkalmazást egy fizikai Android-eszköz vagy egy Android-emulátorban való futtatásához. Az Android-alkalmazás futtatása egy fizikai eszközt vagy emulátort a további információkért lásd: [az alkalmazás futtatása](https://developer.android.com/training/basics/firstapp/running-app).

5. Ha az alkalmazás betöltött, kattintson a **Start** gombra kattintva indítsa el a telemetriai adatokat küldenek az IoT hubnak:

    ![Alkalmazás](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról

Ebben a szakaszban az Azure Cloud Shell-fogja használni a [IoT-bővítmény](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) az eszköz az Androidos eszköz által küldött üzenetek figyeléséhez.

1. Az Azure Cloud Shell használatával futtassa a következő parancsot az IoT Hubhoz történő csatlakozáshoz és az üzenetek olvasásához:

   **YourIoTHubName**: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```
    Az alábbi képernyőképen az IoT-központ kap az Android-eszköz által küldött telemetriát, a kimenet látható:

      ![Olvassa el az eszköz üzeneteit az Azure CLI használatával](media/quickstart-send-telemetry-android/read-data.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban beállított egy IoT hubot, regisztrált egy eszközt, szimulált telemetriát küldött a hubra, Android-alkalmazás használatával, és a telemetriai adatok olvasása a hubról, az Azure Cloud Shell használatával.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: Csatlakozik az IoT hub eszköz vezérlése](quickstart-control-device-android.md)

