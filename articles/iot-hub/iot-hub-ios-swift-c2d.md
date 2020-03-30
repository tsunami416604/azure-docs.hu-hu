---
title: Felhőből eszközre irányuló üzenetek az Azure IoT Hub (iOS) használatával | Microsoft dokumentumok
description: Felhőből az eszközre irányuló üzenetek küldése egy eszközre egy Azure IoT-központból az iOS-alapú Azure IoT SDK-k használatával.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 544b1108a3c79da50969f7fdd33cfb9af373d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110867"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Felhőből eszközre irányuló üzenetek küldése az IoT Hubbal (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás háttérkiszolgáló között. A [Telemetriaküldése egy eszközről egy IoT hub](quickstart-send-telemetry-ios.md) rövid útmutató bemutatja, hogyan hozhat létre egy IoT hub, egy eszköz identitását, és a kód egy szimulált eszköz alkalmazás, amely elküldi az eszköz-felhő üzeneteket.

Ez az oktatóanyag a következőket mutatja be:

* A megoldás háttér-tartalék, felhőből az eszközre üzeneteket küldhet egyetlen eszközre az IoT Hubon keresztül.

* Felhőből az eszközre irányuló üzenetek fogadása az eszközön.

* A megoldás háttérrendszeréről kérje a kézbesítési nyugtázást (*visszajelzést)* az IoT Hubról az eszközre küldött üzenetekhez.

A felhőből az eszközre irányuló üzenetekről az [IoT Hub fejlesztői útmutatójának üzenetküldési szakaszában](iot-hub-devguide-messaging.md)talál további információt.

A cikk végén két Swift iOS-projektet futtat:

* **minta-eszköz,** ugyanazt az alkalmazást hozott létre a [telemetriai adatok küldése egy eszközről egy IoT hub,](quickstart-send-telemetry-ios.md)amely csatlakozik az IoT hub hoz, és fogadja a felhőből az eszközre üzeneteket.

* **mintaszolgáltatás**, amely egy felhőből az eszközre üzenetet küld a szimulált eszközalkalmazásnak az IoT Hubon keresztül, majd megkapja a kézbesítési nyugtázást.

> [!NOTE]
> Az IoT Hub sdk-támogatással rendelkezik számos eszközplatformhoz és nyelvhez (beleértve a C, Java, Python és Javascript- et) az Azure IoT-eszköz SDK-kon keresztül. Az Azure [IoT Developer Center](https://www.azure.com/develop/iot)című témakörben olvashat részletesen arról, hogyan csatlakoztathatja az eszközt az oktatóanyag kódjához, és általában az Azure IoT Hubhoz.

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Egy aktív IoT-központ az Azure-ban.

* Az [Azure-mintákból](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)származó kódminta.

* Az iOS SDK legújabb verzióját futtató [XCode](https://developer.apple.com/xcode/) legújabb verziója. A rövid útmutató tesztelése az XCode 9.3-as és az iOS 11.3-as verziójával történt.

* A [CocoaPods](https://guides.cocoapods.org/using/getting-started.html) legújabb verziója.

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="simulate-an-iot-device"></a>IoT-eszköz szimulálása

Ebben a szakaszban egy Swift-alkalmazást futtató iOS-eszközt szimulál, amely felhőből az eszközre irányuló üzeneteket fogad az IoT hubról. 

Ez az a mintaeszköz, amelyet a cikkben létrehozott [Telemetriai adatok küldése egy eszközről egy IoT hubra.](quickstart-send-telemetry-ios.md) Ha már fut, akkor kihagyhatja ezt a szakaszt.

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

### <a name="run-the-sample-device-application"></a>A mintaeszköz-alkalmazás futtatása

1. Az eszköz kapcsolati karakterláncának beolvasása. Ezt a karakterláncot az [Azure Portalról](https://portal.azure.com) másolhatja az eszköz részletek panelén, vagy lekérheti a következő CLI paranccsal:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Nyissa meg a minta munkaterületet az XCode-ban.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Bontsa ki az **MQTT ügyfélminta projektet,** majd az azonos nevű mappát.  

3. Nyissa meg **ViewController.swift** fájlt az XCode-ban való szerkesztéshez. 

4. Keresse meg a **connectionString** változót, és frissítse az értéket az első lépésben másolt eszközkapcsolati karakterlánccal.

5. Mentse a módosításokat. 

6. Futtassa a projektet az eszközemulátorban a **Létrehozás és futtatás** gombbal vagy a **command + r** billentyűkombinációval.

   ![A projekt futtatása](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

Ebben a cikkben létrehoz egy háttérszolgáltatás felhőből az eszközre üzeneteket az IoT hub on létrehozott [Telemetriai adatok küldése egy eszközről egy IoT hub.](quickstart-send-telemetry-ios.md) Felhőből az eszközre irányuló üzenetek küldéséhez a szolgáltatásnak szüksége van a **szolgáltatás csatlakozási engedélyére.** Alapértelmezés szerint minden IoT Hub jön létre egy megosztott hozzáférési szabályzat nevű **szolgáltatás,** amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Szolgáltatási eszköz szimulálása

Ebben a szakaszban egy második iOS-eszközt szimulál egy Swift alkalmazással, amely felhőből az eszközre irányuló üzeneteket küld az IoT hubon keresztül. Ez a konfiguráció olyan IoT-forgatókönyvek esetén hasznos, ahol egy iPhone vagy iPad működik vezérlőként más IoT-központhoz csatlakoztatott iOS-eszközökhöz.

### <a name="install-cocoapods"></a>A CocoaPods telepítése

A CocoaPods a külső gyártótól származó kódtárakat használó iOS-projektek függőségeit kezeli.

Keresse meg az Azure IoT iOS-minták mappát, amelyet az előfeltételekben letöltött. Ezután keresse meg a mintaszolgáltatás-projektet:

```sh
cd quickstart/sample-service
```

Győződjön meg arról, hogy az XCode be van zárva, majd futtassa az alábbi parancsot a **podfile** fájlban szereplő CocoaPods telepítéséhez:

```sh
pod install
```

A projekthez szükséges podok telepítésén kívül a telepítési parancs egy XCode-munkaterületfájlt is létrehozott, amely már konfigurálva van a podok függőségekhez való használatára.

### <a name="run-the-sample-service-application"></a>A mintaszolgáltatás-alkalmazás futtatása

1. Nyissa meg a minta munkaterületet az XCode-ban.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. Bontsa ki az **AzureIoTServiceSample projektet,** majd bontsa ki az azonos nevű mappát.  

3. Nyissa meg **ViewController.swift** fájlt az XCode-ban való szerkesztéshez. 

4. Keresse meg a **connectionString** változót, és frissítse az értéket a korábban az [IoT hub kapcsolati karakterláncának bekérése](#get-the-iot-hub-connection-string)című szolgáltatáskapcsolati karakterláncával.

5. Mentse a módosításokat.

6. Az Xcode-ban módosítsa az emulátor beállításait egy másik iOS-eszközre, mint az IoT-eszköz futtatásához. Az XCode nem tud több azonos típusú emulátort futtatni.

   ![Az emulátor eszköz ének módosítása](media/iot-hub-ios-swift-c2d/change-device.png)

7. Futtassa a projektet az eszköz emulátorában a **Build and run** gombbal vagy a command + r billentyűkombinációval. **Command + r**

   ![A projekt futtatása](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Felhőből eszközre irányuló üzenet küldése

Most már készen áll arra, hogy a két alkalmazás segítségével küldjön és fogadjon felhőből az eszközre irányuló üzeneteket.

1. A szimulált IoT-eszközön futó **iOS-alkalmazásminta** alkalmazásban kattintson a **Start**gombra. Az alkalmazás elkezdi az eszközről a felhőbe irányuló üzenetek küldését, de elkezdi hallgatni a felhőből az eszközre irányuló üzeneteket is.

   ![Minta IoT-eszközalkalmazás megtekintése](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. A szimulált szolgáltatáseszközön futó **IoTHub-szolgáltatás ügyfélminta** alkalmazásában adja meg annak az IoT-eszköznek az azonosítóját, amelynek üzenetet szeretne küldeni. 

3. Írjon egy egyszerű szöveges üzenetet, majd kattintson **a Küldés gombra.**

    Számos művelet történik, amint a küldés gombra kattint. A szolgáltatásminta elküldi az üzenetet az IoT hub, amely az alkalmazás a megadott szolgáltatás kapcsolati karakterlánc miatt rendelkezik hozzáféréssel. Az IoT hub ellenőrzi az eszköz azonosítóját, elküldi az üzenetet a céleszköznek, és visszaigazolást küld a forráseszköznek. A szimulált IoT-eszközön futó alkalmazás ellenőrzi az IoT Hubból érkező üzeneteket, és kinyomtatja a szöveget a képernyőn a legutóbbiból.

    A kimenetnek a következő példához hasonlóan kell kinéznie:

   ![Felhőből az eszközre irányuló üzenetek megtekintése](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan küldhet és fogadhat felhőből az eszközre irányuló üzeneteket.

Az IoT Hubot használó teljes körű megoldások példáinak megtekintéséhez tekintse meg az [Azure IoT-megoldásgyorsítók dokumentációját.](https://azure.microsoft.com/documentation/suites/iot-suite/)

Ha többet szeretne megtudni a megoldások IoT Hubkal való fejlesztéséről, tekintse meg az [IoT Hub fejlesztői útmutatóját.](iot-hub-devguide.md)
