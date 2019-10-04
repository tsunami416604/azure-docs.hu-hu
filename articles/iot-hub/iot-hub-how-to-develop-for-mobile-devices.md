---
title: Fejlesztés a mobileszközök számára az Azure IoT SDK-k használatával | Microsoft Docs
description: Fejlesztői útmutató – Ismerje meg, hogyan fejleszthet a mobileszközök az Azure IoT Hub SDK-k használatával.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 945b02003a443c04e692fdc06ca5714de362d074
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883084"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Fejlesztés a mobileszközök számára az Azure IoT SDK-k használatával

A eszközök internetes hálózataban szereplő dolgok számos különböző képességgel rendelkező eszközre vonatkozhatnak: érzékelők, mikroprocesszorok, intelligens eszközök, ipari átjárók és akár mobileszközök is.  A mobileszköz lehet egy IoT-eszköz, amely az eszközről a felhőbe irányuló telemetria és a felhő által felügyelt adatokat küld.  Azt is megteheti, hogy az eszköz olyan háttér-szolgáltatási alkalmazást futtat, amely más IoT-eszközöket kezel.  Az [Azure IoT hub SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) -k mindkét esetben használhatók a mobileszközökön dolgozó alkalmazások fejlesztéséhez.  

## <a name="develop-for-native-ios-platform"></a>Fejlesztés natív iOS platformon

Az Azure IoT Hub SDK-k natív iOS platform-támogatást biztosítanak az Azure IoT Hub C SDK-n keresztül.  Azt is megteheti, hogy egy iOS SDK-t használ, amelyet a Swift vagy Objective C XCode projektbe építhet be.  A C SDK-t kétféleképpen használhatja iOS rendszeren:

* Használja közvetlenül a CocoaPod-kódtárakat a XCode-projektben.  
* Töltse le a C SDK forráskódját, és készítsen iOS platformra a MacOS-hez készült [Build utasítás](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) után.  

Az Azure IoT Hub C SDK-t a C99 a különböző platformokra való maximális hordozhatóság érdekében írták.  A portolás folyamata egy vékony bevezetési réteget ír elő a platform-specifikus összetevőkhöz, amelyek itt találhatók az [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx)-hez.  A C SDK funkciói az iOS platformon is használhatók, beleértve az Azure IoT Hub primitíveket és az SDK-specifikus funkciókat, például a hálózati megbízhatóság újrapróbálkozási szabályzatát.  Az iOS SDK felülete az Azure IoT Hub C SDK felületéhez is hasonló.  

Ezek a dokumentumok végigvezetik egy eszköz alkalmazásának vagy szolgáltatásalkalmazás iOS-eszközön való fejlesztésének lépésén:

* [Rövid útmutató: Telemetria küldése egy eszközről egy IoT-hubhoz](quickstart-send-telemetry-ios.md)  
* [Üzenetek küldése a felhőből az eszközre az IoT hub használatával](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Fejlesztés az Azure IoT Hub CocoaPod-kódtárakkal

Az Azure IoT Hub SDK-k az iOS-alapú fejlesztéshez Objective-C CocoaPod-kódtárakat bocsátanak ki.  A CocoaPod-kódtárak legújabb listájának megtekintéséhez lásd: [CocoaPods for Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  Ha a kapcsolódó kódtárak bekerültek a XCode-projektbe, kétféleképpen írhat IoT Hub kapcsolódó kódot:

* Objective C-függvény: Ha a projekt a Objective-C-ben van megírva, akkor közvetlenül az Azure IoT Hub C SDK-ból hívhat API-kat.  Ha a projekt a Swift-ben íródott, meghívhatja `@objc func` a függvény létrehozása előtt, és folytassa az Azure IoT hub kapcsolódó összes logika írását C vagy Objective-c kód használatával.  Mindkét [példa a minta tárházban](https://github.com/Azure-Samples/azure-iot-samples-ios)található példákat tartalmaz.  

* C minta beépítése: Ha írt C-eszköz alkalmazást, a XCode-projektben közvetlenül is hivatkozhat rá:
    * Adja hozzá a sample. c fájlt a XCode projekthez a XCode-ből.  
    * Adja hozzá a fejléc fájlját a függőséghez.  Egy header-fájl példaként szerepel a [minta tárházban](https://github.com/Azure-Samples/azure-iot-samples-ios) . További információkért tekintse meg az Apple dokumentációs oldalát az [Objective-C-](https://developer.apple.com/documentation/objectivec)hez.

## <a name="develop-for-android-platform"></a>Fejlesztés Android platformon
Az Azure IoT Hub Java SDK támogatja az Android platformot.  Az adott API-verzió teszteléséhez látogasson el a legújabb frissítés [platform-támogatási oldalára](iot-hub-device-sdk-platform-support.md) .

Ezek a dokumentumok bemutatják, hogyan fejleszthető egy eszköz-alkalmazás vagy szolgáltatásalkalmazás egy Android-eszközön a Gradle és a Android Studio használatával:

* [Rövid útmutató: Telemetria küldése egy eszközről egy IoT-hubhoz](quickstart-send-telemetry-android.md)  
* [Rövid útmutató: Eszköz vezérlése](quickstart-control-device-android.md) 

## <a name="next-steps"></a>További lépések

* [IoT Hub REST API-hivatkozás](https://docs.microsoft.com/rest/api/iothub/)
* [Azure IoT C SDK-forráskód](https://github.com/Azure/azure-iot-sdk-c)
