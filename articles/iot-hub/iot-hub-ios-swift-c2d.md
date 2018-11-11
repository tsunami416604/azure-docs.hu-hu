---
title: Az Azure IoT Hub (iOS) a felhőből az eszközre irányuló üzenetek |} A Microsoft Docs
description: Annak a felhőből az eszközre irányuló üzeneteket küld egy eszköz az Azure IoT SDK-k használata iOS-hez készült Azure IoT hubról.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: cccffb2f63fe0c929f88d172070ede1c12bcec5b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239457"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Az IoT Hub (iOS) a felhőből az eszközre irányuló üzenetek küldése

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amellyel engedélyezheti a megbízható és biztonságos kétirányú kommunikációt több millió eszköz között, és megoldást biztosít a háttérrendszer. A [telemetriát küldjön az eszközről az IoT hub](quickstart-send-telemetry-ios.md) a cikk bemutatja, hogyan hozzon létre egy IoT hubot, azt az eszközidentitás létrehozását és kód az eszköz a felhőbe irányuló üzeneteket küld egy szimulált eszközalkalmazás.

Ez a cikk bemutatja, hogyan való:

* A megoldás háttérrendszerének, a felhőből az eszközre irányuló üzenetek küldése IoT hubon keresztül egy adott eszköz.

* Az eszközön a felhőből az eszközre irányuló üzeneteket fogadni.

* A megoldás háttérrendszerének, a kérelmek kézbesítési nyugtázás (*visszajelzés*) az IoT Hub az eszközökre küldött üzenetek.

A felhőből az eszközre irányuló üzenetek további tájékoztatást talál a [üzenetkezelés az IoT Hub fejlesztői útmutatójának szakaszában](iot-hub-devguide-messaging.md).

Ez a cikk végén található iOS-projektek két Swift, futtassa:

* **minta-eszköz**, ugyanazt az alkalmazást a létrehozott [telemetriát küldjön az eszközről az IoT hub](quickstart-send-telemetry-ios.md), amely csatlakozik az IoT hubhoz, és megkapja a felhőből az eszközre.

* **minta-szolgáltatás**, amely a felhőből az eszközre üzenetet küld az IoT hubon keresztül a szimulált eszközalkalmazásnak, és annak kézbesítési nyugtázási majd kap.

> [!NOTE]
> Az IoT Hub SDK számos eszközplatformok és nyelveken (például a C, Java és Javascript) keresztül az Azure IoT eszközoldali SDK-k támogatással rendelkezik. Az eszköz csatlakoztatása, ebben az oktatóanyagban a kódot, és általában az Azure IoT hubba a részletes útmutatót lásd: a [Azure IoT fejlesztői központ](http://www.azure.com/develop/iot).

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

- Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.)

- Az aktív IoT hubra az Azure-ban. 

- A mintakód a [Azure-minták](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip) .

- Az iOS SDK legújabb verzióját futtató [XCode](https://developer.apple.com/xcode/) legújabb verziója. A rövid útmutató tesztelése az XCode 9.3-as és az iOS 11.3-as verziójával történt.

- A [CocoaPods](https://guides.cocoapods.org/using/getting-started.html) legújabb verziója.

## <a name="simulate-an-iot-device"></a>Egy IoT-eszköz szimulálása

Ebben a szakaszban a felhőből az eszközre irányuló üzenetek fogadása az IoT hubról Swift-alkalmazást futtató iOS-eszköz szimulálása. 

Ez a minta eszközt ebben a cikkben a [telemetriát küldjön az eszközről az IoT hub](quickstart-send-telemetry-ios.md). Ha már rendelkezik, hogy fut, a szakaszt kihagyhatja.

### <a name="install-cocoapods"></a>A CocoaPods telepítése

A CocoaPods a külső gyártótól származó kódtárakat használó iOS-projektek függőségeit kezeli.

Egy terminálablakban lépjen az Azure-IoT-Samples-iOS mappához, amelyet az előfeltételek részben töltött le. Ezután lépjen a mintaprojekthez:

```sh
cd quickstart/sample-device
```

Győződjön meg arról, hogy az XCode be van zárva, majd futtassa az alábbi parancsot a **podfile** fájlban szereplő CocoaPods telepítéséhez:

```sh
pod install
```

A projekthez szükséges podok telepítésén kívül a telepítési parancs egy XCode-munkaterületfájlt is létrehozott, amely már konfigurálva van a podok függőségekhez való használatára. 

### <a name="run-the-sample-device-application"></a>A minta eszközalkalmazás futtatása 

1. Az eszköz a kapcsolati karakterlánc lekéréséhez. A következő karakterláncot is másolhatja a [az Azure portal](https://portal.azure.com) az eszköz részleteit megjelenítő panelen vagy lekérni a következő CLI-paranccsal: 

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Nyissa meg a minta munkaterületet az XCode-ban.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Bontsa ki a **MQTT-Ügyfélminta** projektet és az azonos nevű mappát.  

3. Nyissa meg **ViewController.swift** fájlt az XCode-ban való szerkesztéshez. 

4. Keresse meg a **connectionString** változót, és frissítse az értéket az eszköz kapcsolati karakterlánc az első lépésben másolt.

5. Mentse a módosításokat. 

6. Futtassa a projektet az eszközemulátorban a **Létrehozás és futtatás** gombbal vagy a **command + r** billentyűkombinációval. 

   ![A projekt futtatása](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="simulate-a-service-device"></a>Szolgáltatás-eszköz szimulálása

Ebben a szakaszban egy Swift-alkalmazást, amely a felhőből az eszközre irányuló üzeneteket az IoT hubon keresztül második iOS eszköz szimulálása. Ez a konfiguráció IoT-forgatókönyvek esetén hasznos, ahol van egy iPhone vagy iPad tartományvezérlőként működik-e más iOS-eszközök csatlakozik az IoT hubra. 

### <a name="install-cocoapods"></a>A CocoaPods telepítése

A CocoaPods a külső gyártótól származó kódtárakat használó iOS-projektek függőségeit kezeli.

Keresse meg az IOS-es Azure IoT-minták, ahonnan letöltötte az előfeltételeket mappát. Ezután lépjen a mintaprojekt szolgáltatás:

```sh
cd quickstart/sample-service
```

Győződjön meg arról, hogy az XCode be van zárva, majd futtassa az alábbi parancsot a **podfile** fájlban szereplő CocoaPods telepítéséhez:

```sh
pod install
```

A projekthez szükséges podok telepítésén kívül a telepítési parancs egy XCode-munkaterületfájlt is létrehozott, amely már konfigurálva van a podok függőségekhez való használatára.

### <a name="run-the-sample-service-application"></a>A szolgáltatás-mintaalkalmazás futtatása

1. Az IoT hub szolgáltatás kapcsolati karakterláncára lekéréséhez. Másolhatja a karakterláncot, a [az Azure portal](https://portal.azure.com) származó a **iothubowner** szabályzat a **megosztott elérési házirendek** panelen vagy lekérni a következő CLI-paranccsal:  

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

2. Nyissa meg a minta munkaterületet az XCode-ban.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

3. Bontsa ki a **AzureIoTServiceSample** projektre, majd bontsa ki a mappát annak ugyanazzal a névvel.  

4. Nyissa meg **ViewController.swift** fájlt az XCode-ban való szerkesztéshez. 

5. Keresse meg a **connectionString** változót, és frissítse az értéket a szolgáltatás korábban kimásolt kapcsolati karakterláncot.

6. Mentse a módosításokat. 

7. Az xcode-ban, mint amennyi az IoT eszköz futtatásához használt egy másik iOS-eszköz emulátor beállításainak módosításával. Xcode-ban több emulátory systému azonos típusú nem futtatható. 

   ![Az emulator eszköz módosítása](media/iot-hub-ios-swift-c2d/change-device.png)

8. Futtassa a projektet az eszközemulátorban a **létrehozásához és futtatásához** gomb vagy a fő kombinált **Command + r**. 

   ![A projekt futtatása](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Felhőből az eszközre irányuló üzenet küldése

Most már készen áll a két alkalmazás használatával a felhőből az eszközre irányuló üzenetek küldése és fogadása.

1. Az a **iOS-alkalmazás-minta** kattintson az IoT szimulált eszközön futó alkalmazás **Start**. Az alkalmazás elindítja az eszköz a felhőbe irányuló üzenetek küldéséhez, de megkezdi a felhőből az eszközre irányuló üzenetek figyeli. 

   ![Mintául szolgáló IoT eszközfelügyeleti alkalmazás megtekintése](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. Az a **IoTHub-Service Ügyfélminta** a szolgáltatás szimulált eszközön futó alkalmazáshoz, amelyet szeretne elküldeni egy üzenetet az IoT-eszköz Azonosítóját adja meg. 

3. Egy egyszerű szöveges üzenetet, majd kattintson a **küldése**. 

    Több művelet fordulhat elő, amint kattintson a Küldés gombra. A szolgáltatás minta az üzenetet küld az IoT hub, az alkalmazás, amely miatt a szolgáltatáskapcsolati karakterlánc, amikor a megadott hozzáférést. Az IoT hub ellenőrzi az eszköz azonosítója, az üzenetet küld a céleszköz és egy visszaigazolását küld a forráseszközt. Az alkalmazás szimulált IoT-eszközök futó ellenőrzi az üzeneteket az IoT hubról, és a szöveget jelenít meg a legfrissebb ezen a képernyőn a.

    A kimenet az alábbihoz hasonlóan kell kinéznie:

   ![Felhőből az eszközre irányuló üzenetek megtekintése](media/iot-hub-ios-swift-c2d/view-c2d.png)


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan a felhőből az eszközre irányuló üzenetek küldése és fogadása. 

Példák a teljes végpontok közötti megoldások, amely használja az IoT hubot, olvassa el a [Azure IoT-Megoldásgyorsítók](https://azure.microsoft.com/documentation/suites/iot-suite/) dokumentációját.

Az IoT Hub megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [IoT Hub fejlesztői útmutatójának](iot-hub-devguide.md).
