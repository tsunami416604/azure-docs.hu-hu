---
title: Az Azure IoT Hub (iOS) felhő eszközre üzenetek |} Microsoft Docs
description: Hogyan küldhetők felhő-eszközre küldött üzenetek eszközökre az Azure IoT-központ az Azure IoT SDK-k használata iOS-hez.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 23dbd1f359f947b8e87ab4115887120dfd55907a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Az IoT-központ (iOS) felhő eszközre-üzenetek
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amellyel eszközök millióira közötti megbízható és biztonságos kétirányú kommunikáció engedélyezése és a megoldás háttérrendszere. A [telemetriai adatokat küldhet egy eszközről IoT hubhoz] a cikk bemutatja, hogyan létrehoz egy IoT-központot, azt egy eszközidentitás kiépítése és kód egy szimulált eszköz alkalmazást, amelyet az eszköz a felhőbe küldött üzeneteket küld.

Ez a cikk bemutatja, hogyan számára:

* A megoldás háttérrendszeréhez, az IoT-központ keresztül egyetlen eszközt felhő eszközre üzeneteket küldeni.
* Az eszközön a felhőből eszközre üzeneteket fogadni.
* A megoldás háttérrendszeréhez, a kérelem kézbesítési nyugtázási (*visszajelzés*) IoT-központot egy eszközre küldött üzenetek esetében.

Felhő eszközre üzenetek további információt a [IoT Hub fejlesztői útmutató][IoT Hub developer guide - C2D].

Ez a cikk végén futtatása két Swift iOS-projektek:

* **a minta-eszköz**, ugyanahhoz az alkalmazáshoz létrehozott [telemetriai adatokat küldhet egy eszközről IoT hubhoz], amely csatlakozik az IoT hub és felhő eszközre üzeneteket fogad.
* **a minta-szolgáltatás**, melyik felhőalapú eszközre üzenetet küld az IoT-központ szimulált eszköz alkalmazás, és a szállítási nyugtázási majd megkapja.

> [!NOTE]
> Az IoT-központ rendelkezik sok eszköz platformok és nyelvek (például C, Java és Javascript) keresztül Azure IoT eszközoldali SDK-k SDK támogatása. Csatlakoztassa az eszközt, az oktatóanyag kódot, és általában Azure IoT Hub kapcsolatos lépésenkénti útmutatót lásd: a [Azure IoT fejlesztői központ].

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

- Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)
- Az aktív IoT-központ az Azure-ban. 
- A kód mintát [Azure-minták](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip) .
- Az iOS SDK legújabb verzióját futtató [XCode](https://developer.apple.com/xcode/) legújabb verziója. A rövid útmutató tesztelése az XCode 9.3-as és az iOS 11.3-as verziójával történt.
- A [CocoaPods](https://guides.cocoapods.org/using/getting-started.html) legújabb verziója.


## <a name="simulate-an-iot-device"></a>Az IoT-eszközök szimulálása
Ebben a szakaszban szimulálása felhő eszközre üzenetek fogadása az IoT hub Swift alkalmazást futtat egy iOS-eszközön. 

Ez az a cikk a minta-minta eszköz [telemetriai adatokat küldhet egy eszközről IoT hubhoz]. Ha már rendelkezik, hogy fut, ugorjon ebben a szakaszban.

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

### <a name="run-the-sample-device-application"></a>Futtassa a mintaalkalmazást eszköz 

1. Az eszköz a kapcsolati karakterlánc lekéréséhez. Másolja ezt a karakterláncot az eszköz részleteit megjelenítő panelen az Azure-portálról, vagy a következő paranccsal CLI lekéréséhez: 

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Nyissa meg a minta munkaterületet az XCode-ban.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Bontsa ki a **MQTT ügyfél minta** projekt és az azonos nevű mappa.  
3. Nyissa meg **ViewController.swift** fájlt az XCode-ban való szerkesztéshez. 
4. Keresse meg a **connectionString** változót, és frissítse az értéket, az eszköz kapcsolattal az első lépésben másolt karakterlánc.
5. Mentse a módosításokat. 
6. Futtassa a projektet az eszközemulátorban a **Létrehozás és futtatás** gombbal vagy a **command + r** billentyűkombinációval. 

   ![A projekt futtatása](media/quickstart-send-telemetry-ios/run-sample.png)


## <a name="simulate-a-service-device"></a>A szolgáltatás eszköze szimulálása

Ebben a szakaszban egy második iOS-eszközön, amely a felhő-eszközön keresztül küld üzeneteket az IoT hub Swift alkalmazással szimulálásához. Ez a konfiguráció akkor hasznos, az IoT-forgatókönyvek esetén, ahol van egy iPhone vagy iPad tartományvezérlőként működik-e az egyéb iOS-eszközök csatlakozik az IoT-központ. 

### <a name="install-cocoapods"></a>A CocoaPods telepítése

A CocoaPods a külső gyártótól származó kódtárakat használó iOS-projektek függőségeit kezeli.

Keresse meg az iOS Azure IoT-mintákat az előfeltételeket a mappát. Ezt követően navigáljon a minta-projekt:

```sh
cd quickstart/sample-service
```

Győződjön meg arról, hogy az XCode be van zárva, majd futtassa az alábbi parancsot a **podfile** fájlban szereplő CocoaPods telepítéséhez:

```sh
pod install
```

A projekthez szükséges podok telepítésén kívül a telepítési parancs egy XCode-munkaterületfájlt is létrehozott, amely már konfigurálva van a podok függőségekhez való használatára.

### <a name="run-the-sample-service-application"></a>Futtassa a mintaalkalmazást szolgáltatás

1. Az IoT hub a szolgáltatás kapcsolati karakterláncának beolvasása. Ez a karakterlánc másolhatja az Azure-portálról a **iothubowner** a házirend a **megosztott elérési házirendek** panelen, vagy a következő paranccsal CLI lekéréséhez:  

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

2. Nyissa meg a minta munkaterületet az XCode-ban.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

3. Bontsa ki a **AzureIoTServiceSample** projektre, majd bontsa ki az azonos nevű mappát.  
4. Nyissa meg **ViewController.swift** fájlt az XCode-ban való szerkesztéshez. 
5. Keresse meg a **connectionString** változót, és frissítse az értéket a korábban kimásolt kapcsolati karakterláncot.
6. Mentse a módosításokat. 
7. Az xcode-ban módosítsa az emulátor beállításokat egy másik iOS-eszközön, mint amennyit az IoT-eszközök futtatásához használt. XCode nem futtatható több emulátorok ugyanabba a típusba tartozik. 

   ![Az emulátor eszköz módosítása](media/iot-hub-ios-swift-c2d/change-device.png)

8. A projekt az eszköz-emulátorban való futtatásához a **létrehozása és futtatása** gomb vagy a kulcs kombinált **parancs + r**. 

   ![A projekt futtatása](media/iot-hub-ios-swift-c2d/run-app.png)


## <a name="send-a-cloud-to-device-message"></a>Felhő eszközre üzenet küldése
Most már készen áll a két alkalmazás segítségével felhő eszközre üzeneteket küldjön és fogadjon.

1. Az a **iOS-alkalmazás-minta** a szimulált IoT-eszközök futó alkalmazáshoz kattintson **Start**. Az alkalmazás elindul, eszköz-felhő üzenetküldésre, de is elindítja a felhő-eszközre küldött üzenetek figyeli. 

   ![A minta IoT eszköz alkalmazás megtekintése](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. A a **IOT hubbal szolgáltatás ügyfél minta** a szimulált szolgáltatás eszközön futó alkalmazáshoz meg az Azonosítót az IoT-eszközök, amelyet egy üzenetet küldeni. 
3. Egy egyszerű szöveges üzenetet, majd kattintson az **küldése**. 

Számos műveletet fordulhat elő, amint a Küldés gombra kattint. A szolgáltatás minta az üzenet elküldése az IoT hub, amely az alkalmazás által elérhető miatt a szolgáltatás kapcsolódási karakterlánc során megadott. Az IoT hub ellenőrzi az eszköz azonosítója, az üzenet elküldése a céleszköz és egy megerősítő visszaigazolást küld a forráseszközt. Az alkalmazás a szimulált IoT-eszközök futó ellenőrzi az IoT-központ az üzeneteket, és kiírja a szöveget a legfrissebb ezen a képernyőn.

A kimenet az alábbihoz hasonlóan kell kinéznie:

   ![Felhő-eszközre küldött üzenetek megtekintése](media/iot-hub-ios-swift-c2d/view-c2d.png)


## <a name="next-steps"></a>További lépések
Ebben az oktatóprogramban megismerte felhő eszközre üzeneteket küldjön és fogadjon. 

Példák teljes végpontok közötti megoldások, amelyek használják az IoT-központot, lásd: [megoldásgyorsító Azure IoT távoli megfigyelési].

Az IoT hubbal megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [IoT Hub fejlesztői útmutató].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[telemetriai adatokat küldhet egy eszközről IoT hubhoz]: quickstart-send-telemetry-ios.md

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT Hub fejlesztői útmutató]: iot-hub-devguide.md
[Azure IoT fejlesztői központ]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure portal]: https://portal.azure.com
[megoldásgyorsító Azure IoT távoli megfigyelési]: https://azure.microsoft.com/documentation/suites/iot-suite/
