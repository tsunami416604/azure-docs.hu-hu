---
title: Eszköz vezérlése az Azure IoT Hub rövid útmutatójából (Android) | Microsoft dokumentumok
description: Ebben a rövid útmutatóban két Java-mintaalkalmazást fog futtatni. Az egyik alkalmazás egy olyan szolgáltatásalkalmazás, amely távolról vezérelheti a hubhoz csatlakoztatott eszközöket. A másik alkalmazás fut egy fizikai vagy szimulált eszköz csatlakozik a hubhoz, amely távolról vezérelhető.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: 8c3a9c6c5e835104675239882d2a1929a3c07c82
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771028"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Rövid útmutató: IoT-központhoz (Android) csatlakoztatott eszköz vezérlése

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Ebben a rövid útmutatóban egy közvetlen módszerrel vezérelheti az Azure IoT Hubhoz csatlakoztatott szimulált eszközt. Az IoT Hub egy Azure-szolgáltatás, amely lehetővé teszi az IoT-eszközök kezelését a felhőből, és nagy mennyiségű eszköztelemetriát a felhőbe való betöltésére tárolás vagy feldolgozás érdekében. A közvetlen metódusok használatával távolról módosíthatja az IoT Hubhoz csatlakoztatott eszköz működését. Ez a rövid útmutató két alkalmazást használ: egy szimulált eszközalkalmazást, amely válaszol egy háttérszolgáltatás-alkalmazásból és egy olyan szolgáltatásalkalmazásból, amely meghívja a közvetlen metódust az Android-eszközön.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android Studio Android SDK 27](https://developer.android.com/studio/). További információ: [Install Android Studio](https://developer.android.com/studio/install).

* [Git](https://git-scm.com/download/).

* [Device SDK minta Android-alkalmazás](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample), az [Azure IoT-minták (Java)](https://github.com/Azure-Samples/azure-iot-samples-java)része.

* [Szolgáltatás SDK minta Android-alkalmazás](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample), az Azure IoT-minták (Java) tartalmazza.

* A 8883-as port megnyílik a tűzfalon. A rövid útmutatóban szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-bővítmény hozzáadása

Futtassa a következő parancsot, és adja hozzá a Microsoft Azure IoT-bővítményt az Azure CLI-hez a Cloud Shell-példányhoz. Az IoT-bővítmény hozzáadja az IoT Hub, az IoT Edge és az IoT-eszközlétesítési szolgáltatás (DPS) adott parancsokat az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha befejezte az előző [gyorsútmutatót: Telemetriai adatok küldése egy eszközről egy IoT-központba,](quickstart-send-telemetry-android.md)kihagyhatja ezt a lépést, és használhatja a már létrehozott IoT-központot.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha befejezte az előző [rövid útmutató: Telemetriai adatok küldése egy eszközről egy IoT hub,](quickstart-send-telemetry-android.md)kihagyhatja ezt a lépést, és használja ugyanazt az eszközt regisztrált az előző rövid útmutató.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa a következő parancsot az Azure Cloud Shellben az eszközidentitás létrehozásához.

   **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz választott névvel.

   **MyAndroidDevice**: Ez a regisztráló eszköz neve. Javasoljuk, hogy használja **a MyAndroidDevice-t az** ábrán látható módon. Ha más nevet választ az eszközhöz, akkor ezt a nevet is használnia kell a cikkben, és frissítenie kell az eszköz nevét a mintaalkalmazásokban, mielőtt futtatna őket.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

   **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz kiválasztott névvel.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

## <a name="retrieve-the-service-connection-string"></a>A szolgáltatás kapcsolati sztringjének lekérése

Emellett egy _szolgáltatás kapcsolati karakterláncra_ is szüksége van ahhoz, hogy a háttérszolgáltatás-alkalmazások a metódusok végrehajtásához és az üzenetek lekéréséhez csatlakozzanak az IoT hubhoz. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

**YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz választott névvel.

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Jegyezze fel a szolgáltatáskapcsolati sztringet, amely a következőképpen néz ki:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Ezt az értéket használni fogja a rövid útmutató későbbi részében. Ez a szolgáltatáskapcsolati karakterlánc eltér az előző lépésben megjegyzett eszközkapcsolati karakterlánctól.

## <a name="listen-for-direct-method-calls"></a>Közvetlen metódusok hívásának figyelése

A rövid útmutató mindkét minta része az azure-iot-samples-java tárház a GitHubon. Töltse le vagy klónozza az [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) tárházat.

Az eszköz SDK mintaalkalmazás futtatható fizikai Android-eszközön vagy Android-emulátoron. A minta csatlakozik egy eszköz-specifikus végpont az IoT hubon, szimulált telemetriai adatokat küld, és figyeli a közvetlen metódus hívásokat a hubról. Ebben a rövid útmutatóban a hubról érkező közvetlenmetódus-hívás arra utasítja az eszközt, hogy módosítsa a telemetriaküldések közötti időintervallumot. A szimulált eszköz a közvetlen metódus végrehajtása után nyugtát küld vissza a hubnak.

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

    ![Minta képernyőkép az ügyféleszköz android alkalmazásáról](media/quickstart-control-device-android/sample-screenshot.png)

Ezt az alkalmazást egy fizikai eszközön vagy emulátoron kell futnia, miközben végrehajtja a szolgáltatás SDK-mintáját a telemetriai időköz futásközbeni frissítéséhez.

## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról

Ebben a szakaszban az Azure Cloud Shell az [IoT-bővítmény](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) t fogja használni az Android-eszköz által küldött üzenetek figyelésére.

1. Az Azure Cloud Shell használatával futtassa a következő parancsot az IoT Hubhoz történő csatlakozáshoz és az üzenetek olvasásához:

   **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz kiválasztott névvel.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    A következő képernyőkép a kimenetet mutatja, ahogy az IoT hub megkapja az Android-eszköz által küldött telemetriai adatokat:

      ![Olvassa el az eszköz üzeneteit az Azure CLI használatával](media/quickstart-control-device-android/read-data.png)

Alapértelmezés szerint a telemetriai alkalmazás öt másodpercenként telemetriai adatokat küld az Android-eszközről. A következő szakaszban egy közvetlen metódushívás az Android IoT-eszköz telemetriai időközének frissítéséhez fog használni.

## <a name="call-the-direct-method"></a>A közvetlen metódus meghívása

A szolgáltatásalkalmazás csatlakozik egy szolgáltatásoldali végpont hoz az IoT Hub. Az alkalmazás közvetlen metódushívásokat tesz egy eszközre az IoT hubon keresztül, és figyeli a nyugtákat.

Futtassa ezt az alkalmazást egy külön fizikai Android-eszközön vagy Android emulátoron.

Az IoT Hub háttérszolgáltatás-alkalmazás általában a felhőben fut, ahol könnyebb en y a bizalmas kapcsolati karakterlánc, amely az IoT Hub összes eszközét vezérli. Ebben a példában csak demonstrációs célokra futtatjuk Android-alkalmazásként. A rövid útmutató más nyelvű verziói olyan példákat tartalmaznak, amelyek jobban illeszkednek egy tipikus háttérszolgáltatás-alkalmazáshoz.

1. Nyissa meg a GitHub-szolgáltatás minta Android-projektet az Android Studio-ban. A projekt az [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) tárház klónozott vagy letöltött példányának következő könyvtárában található.

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. Az Android Studio alkalmazásban nyissa meg a *mintaprojekt gradle.properties.properties.in.* Frissítse a **ConnectionString** és **deviceId-tulajdonságok** értékeit a korábban feljegyzett szolgáltatáskapcsolati karakterlánccal és a regisztrált Android-eszközazonosítóval.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. Az Android Studio alkalmazásban kattintson a > **Fájlszinkronizálási projekt a Gradle-fájlokkal**elemre. **File** Ellenőrizze a build befejeződését.

   > [!NOTE]
   > Ha a projekt szinkronizálása sikertelen, annak a következő okai lehetnek:
   >
   > * A változat -ból Android Gradle dugó és Gradle hivatkozott -ban tervez van kikésett -ból időpont részére -a változat -ból Android Műterem. Kövesse [az alábbi utasításokat,](https://developer.android.com/studio/releases/gradle-plugin) hogy hivatkozzon, és telepítse a megfelelő verziókat a plugin és Gradle a telepítéshez.
   > * Az Android SDK licencszerződése nincs aláírva. Kövesse a Build kimenet ben található utasításokat a licencszerződés aláírásához és az SDK letöltéséhez.

4. Miután a build befejeződött, kattintson **a Run Run** > **'app'** gombra. Állítsa be az alkalmazást úgy, hogy egy külön fizikai Android-eszközön vagy Android-emulátoron fusson. Az Android-alkalmazások fizikai eszközön vagy emulátoron való futtatásáról az [Alkalmazás futtatása című](https://developer.android.com/training/basics/firstapp/running-app)témakörben talál további információt.

5. Az alkalmazás betöltése után frissítse az **Üzenetküldési időköz beállítása** értéket **1000-re,** majd kattintson az **Invoke gombra.**

    A telemetriai üzenetküldési időköz ezredmásodpercben van megadva. Az eszközminta alapértelmezett telemetriai időköze 5 másodpercre van beállítva. Ez a módosítás frissíti az Android IoT-eszközt, hogy a telemetriai adatok másodpercenként elküldve.

    ![Telemetriai időköz megadása](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Az alkalmazás nyugtát kap, amely jelzi, hogy a metódus sikeresen végrehajtott-e vagy sem.

    ![Közvetlen metódus nyugtázása](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban közvetlen metódust hívott meg egy eszközön egy háttéralkalmazásból, és válaszolt a szimulált eszközalkalmazásban a közvetlen metódushívásra.

Ha szeretné megtudni, hogy hogyan irányíthatók az eszközről felhőbe irányuló üzenetek különböző felhőbeli célokhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Telemetria irányítása különböző végpontokra feldolgozás céljából](tutorial-routing.md)
