---
title: Az Azure IoT Hub rövid (Android) eszköz vezérlése |} A Microsoft Docs
description: Ebben a rövid útmutatóban két Java-mintaalkalmazást fog futtatni. Egy alkalmazás egy szolgáltatásalkalmazást, amely az IOT hubhoz csatlakoztatott eszközök távoli vezérlését is. A másik alkalmazást futtat az IOT hubhoz, amely távolról vezérelt csatlakoztatott fizikai vagy szimulált eszköz.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: e3b0c0703cb46087db38121055117b50f97ad03f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006570"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Gyors útmutató: A vezérlőelem egy eszköz csatlakozik az IoT hub (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését az IoT-eszközökről a felhőbe, valamint az eszközök kezelését a felhőből. Ebben a rövid útmutatóban egy *közvetlen metódussal* fogja vezérelni az IoT Hubhoz csatlakoztatott szimulált eszközt. A közvetlen metódusok használatával távolról módosíthatja az IoT Hubhoz csatlakoztatott eszköz működését.

Ez a rövid útmutató két előre megírt Java-alkalmazást használ:

* Egy szimulált eszközalkalmazástól közvetlen metódusok válaszoló háttér-service-alkalmazás neve. A közvetlen metódusok meghívásának fogadásához ez az alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik.

* Egy szolgáltatásalkalmazást az Android-eszközön a közvetlen metódus meghívásához. A közvetlen metódus egy eszközre való meghívásához ez az alkalmazás az IoT Hubon található szolgáltatásoldali végponthoz csatlakozik.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Az Android studio https://developer.android.com/studio/. Az Android Studio telepítési kapcsolatos további információkért lásd: [android-telepítés](https://developer.android.com/studio/install).

* Android SDK 27 ebben a cikkben a mintát használja.

* Futtassa a következő parancsot a Microsoft Azure IoT-bővítmény hozzáadása a Cloud Shell-példány Azure CLI-hez. Az IOT-bővítmény hozzáadása Azure CLI-vel az IoT Hub, IoT Edge és IoT Device Provisioning Service (DPS) parancsok.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* Ez a rövid útmutató két mintaalkalmazások szükségesek: A [eszközoldali SDK Android mintaalkalmazás](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) és a [Service SDK-t Androidos mintaalkalmazás](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample). Mindkét ezeket a mintákat a Githubon az azure-iot-minták – java adattár részét képezik. Töltse le vagy klónozza a [azure-iot-minták – java](https://github.com/Azure-Samples/azure-iot-samples-java) tárház.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha elvégezte az előző [a rövid útmutató: Telemetria küldése egy eszközről IoT hubra](quickstart-send-telemetry-android.md), ezt a lépést kihagyhatja, és használja az IoT hub már létrehozott.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha elvégezte az előző [a rövid útmutató: Telemetria küldése egy eszközről IoT hubra](quickstart-send-telemetry-android.md), ezt a lépést kihagyhatja, és használja a korábbi rövid útmutatóban regisztrált ugyanazon az eszközön.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa a következő parancsot az Azure Cloud Shellben, hozza létre az eszközidentitást.

   **YourIoTHubName**: Alább a helyőrzőt cserélje le az IoT hub számára is választott nevét.

   **MyAndroidDevice**: Ezt az értéket az eszköz a megadott név. Használjon MyAndroidDevice látható módon. Ha úgy dönt, hogy az eszköz egy másik nevet, szükség lehet során ez a cikk ezt a nevet, és frissíti az eszköz nevére a mintaalkalmazások őket futtatása előtt a.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

   **YourIoTHubName**: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyAndroidDevice \
      --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

## <a name="retrieve-the-service-connection-string"></a>A szolgáltatás kapcsolati sztringjének lekérése

Is szükség van egy _szolgáltatáskapcsolati karakterláncra_ a háttérszolgáltatás alkalmazások metódusok végrehajtása, és kérnek le üzeneteket az IoT hubhoz való kapcsolódáshoz. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

**YourIoTHubName**: Alább a helyőrzőt cserélje le az IoT hub számára is választott nevét.

```azurecli-interactive
az iot hub show-connection-string --name YourIoTHubName --output table
```

Jegyezze fel a szolgáltatáskapcsolati sztringet, amely a következőképpen néz ki:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

Ezt az értéket használni fogja a rövid útmutató későbbi részében. A szolgáltatáskapcsolati sztring nem azonos az eszközkapcsolati sztringgel.

## <a name="listen-for-direct-method-calls"></a>Közvetlen metódusok hívásának figyelése

Az eszköz SDK-mintaalkalmazás futtatni a fizikai Android-eszköz vagy egy Android-emulátoron. A minta az IoT hub eszközspecifikus végponthoz csatlakozik, szimulált telemetriát küld, és figyeli a közvetlen metódust hívja a hubról. Ebben a rövid útmutatóban a hubról érkező közvetlenmetódus-hívás arra utasítja az eszközt, hogy módosítsa a telemetriaküldések közötti időintervallumot. A szimulált eszköz nyugtázást küld vissza a hubra a közvetlen metódus végrehajtása után.

1. Az Android Studióban nyissa meg a GitHub-minta Androidos projekt. Klónozott vagy letöltött példány a következő könyvtárban található a projekt [azure-iot-minta – java](https://github.com/Azure-Samples/azure-iot-samples-java) tárház.

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. Az Android Studióban nyissa meg a *gradle.properties* a mintaprojektet, és cserélje le a **Device_Connection_String** helyőrző eszköz korábban lejegyzett kapcsolati karakterláncra.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
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

-Nak kell hagyni a phycial eszközt vagy emulátort a fut a szolgáltatás SDK minta a telemetriai időköz módosítása során futásidejű végrehajtása közben.


## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról

Ebben a szakaszban az Azure Cloud Shell-fogja használni a [IoT-bővítmény](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) az eszköz az Androidos eszköz által küldött üzenetek figyeléséhez.

1. Az Azure Cloud Shell használatával futtassa a következő parancsot az IoT Hubhoz történő csatlakozáshoz és az üzenetek olvasásához:

   **YourIoTHubName**: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```
    Az alábbi képernyőképen az IoT-központ kap az Android-eszköz által küldött telemetriát, a kimenet látható:

      ![Olvassa el az eszköz üzeneteit az Azure CLI használatával](media/quickstart-send-telemetry-android/read-data.png)

Alapértelmezés szerint a telemetria app telemetriai adatokat küldenek az Androidos eszközről 5 másodpercenként. A következő szakaszban a telemetriai időköz az Android IoT-eszköz frissítéséhez használandó a közvetlen metódus hívása.


## <a name="call-the-direct-method"></a>A közvetlen metódus meghívása

A service-alkalmazás csatlakozik az IoT hub a Szolgáltatásoldali végpont. Az alkalmazás közvetlen metódusokat hív meg egy eszközre az IoT Hubon keresztül, és figyeli a nyugtázásokat.

Az alkalmazás futtatásához egy külön fizikai Android-eszköz vagy az Android-emulátorban.

Egy IoT Hub háttérszolgáltatás alkalmazás általában fut a felhőben, könnyebben a bizalmas kapcsolati karakterlánccal, amely szabályozza, minden eszköz egy IoT hubhoz a kockázatok csökkentése érdekében. Ebben a példában azt futtatja azt, Android-alkalmazás csak bemutató célokra. Ez a rövid útmutató a más nyelvű verzióiban más példákkal, amelyek jobban igazodnak a háttér-service-alkalmazás.

1. Nyissa meg a GitHub szolgáltatás minta Androidos projekt az Android Studióban. Klónozott vagy letöltött példány a következő könyvtárban található a projekt [azure-iot-minta – java](https://github.com/Azure-Samples/azure-iot-samples-java) tárház.

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. Az Android Studióban nyissa meg a *gradle.properties* a minta projektre, és módosítsa a **ConnectionString** és **DeviceId** szolgáltatás kapcsolati tulajdonságok karakterlánc, amelyet korábban feljegyzett és a regisztrált Android-eszköz azonosító.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. Az Android Studióban kattintson **fájl** > **projekt szinkronizálása a Gradle-fájlokkal**. Ellenőrizze, hogy a build létrehozása után.

   > [!NOTE]
   > Ha a projekt szinkronizálása sikertelen, a következő okok valamelyike lehet:
   >
   > * Az Android Gradle beépülő modul és a gradle-t a projekt hivatkozott verziók elavultak az Android Studio verziójának. Hajtsa végre a [ezek az utasítások](https://developer.android.com/studio/releases/gradle-plugin) való hivatkozást, és telepítheti a megfelelő verzióját a beépülő modul és a gradle-t a telepítéshez.
   > * A licencszerződés feltételeit, az Android SDK még nincs aláírva. Kövesse az utasításokat a felépítési művelet kimenetében a licencszerződés feltételeit és az SDK letöltéséhez.


4. Kattintson a létrehozás befejezése után **futtatása** > **"alkalmazás" futtatása**. Állítsa be az alkalmazás egy külön fizikai Android-eszköz vagy egy Android-emulátorban való futtatásához. Az Android-alkalmazás futtatása egy fizikai eszközt vagy emulátort a további információkért lásd: [az alkalmazás futtatása](https://developer.android.com/training/basics/firstapp/running-app).

5. Miután betölti az alkalmazást, frissítse a **üzenetkezelési időköz beállítása** értéket a következőre **1000** , és kattintson a **Invoke**.

    TH telemetriai üzenetkezelési időköz ezredmásodpercben. Az eszköz minta telemetriai alapértelmezésben 5 másodperc van beállítva. Ez a változás, hogy másodpercenként küldött telemetriai adatok frissíteni fogja az Androidos IoT-eszköz.

    ![Adja meg a telemetriai időköz](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Az alkalmazás jelzi, hogy a metódus végre sikeresen vagy sikertelenül nyugtázást fog kapni.

    ![Közvetlen metódus nyugtázása](media/quickstart-control-device-android/direct-method-ack.png)



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy közvetlen metódust hívott az eszközön egy háttér-alkalmazásból, és a közvetlen metódus meghívása egy szimulált eszközalkalmazástól az válaszolt.

Ha szeretné megtudni, hogy hogyan irányíthatók az eszközről felhőbe irányuló üzenetek különböző felhőbeli célokhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Útvonal telemetriai adatokat a feldolgozáshoz különböző végpontok](tutorial-routing.md)
