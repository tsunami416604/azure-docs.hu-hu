---
title: Fejlesztés mobileszközökre az Azure IoT SDK-k használatával | Microsoft dokumentumok
description: Fejlesztői útmutató – Ismerje meg, hogyan fejleszthet mobileszközökre az Azure IoT Hub SDK-k használatával.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 945b02003a443c04e692fdc06ca5714de362d074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883084"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Fejlesztés mobileszközökre az Azure IoT SDK-k használatával

A dolgok internetében a dolgok számos különböző képességgel rendelkező eszközre utalhatnak: érzékelőkre, mikrovezérlőkre, intelligens eszközökre, ipari átjárókra és még mobileszközökre is.  A mobileszköz lehet Egy IoT-eszköz, ahol az eszköz-felhőtelemetriai és a felhő által felügyelt küld.  Ez is lehet az eszköz fut egy háttér-szolgáltatás alkalmazás, amely kezeli a többi IoT-eszközök.  Mindkét esetben [az Azure IoT Hub SDK-k](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) mobileszközökön használható alkalmazások fejlesztésére használhatók.  

## <a name="develop-for-native-ios-platform"></a>Fejlesztés natív iOS platformra

Az Azure IoT Hub SDK-k natív iOS platformtámogatást nyújtanak az Azure IoT Hub C SDK-n keresztül.  Gondoljrá úgy, mint egy iOS SDK-ra, amelyet beépíthet a Swift vagy A C XCode projektbe.  A C SDK kétféleképpen használható iOS rendszeren:

* Használja közvetlenül az XCode projekt CocoaPod könyvtárait.  
* Töltse le a C SDK forráskódját, és építse ni az iOS platformra a MacOS [buildutasítását](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) követve.  

Az Azure IoT Hub C SDK C99-ben íródott a maximális hordozhatóság érdekében a különböző platformokon.  A portolási folyamat magában foglalja az írás egy vékony elfogadása réteg a platform-specifikus alkatrészek, amely megtalálható itt [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  A C SDK funkciói az iOS platformon is használhatók, beleértve az Azure IoT Hub által támogatott primitíveket és az SDK-specifikus funkciókat, például a hálózati megbízhatóságújrapróbálkozási házirendet.  Az iOS SDK felülete is hasonló az Azure IoT Hub C SDK felületéhez.  

Ezek a dokumentációk bemutatják, hogyan fejleszthet eszközalkalmazást vagy -szolgáltatást iOS-eszközön:

* [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-ios.md)  
* [Üzenetek küldése a felhőből az eszközére az IoT hubsegítségével](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Fejlesztés az Azure IoT Hub CocoaPod-kódtárakkal

Az Azure IoT Hub SDK-k objektív C CocoaPod-könyvtárakat adki iOS-fejlesztéshez.  A CocoaPod-könyvtárak legfrissebb listáját a [Microsoft Azure IoT-hez való CocoaPods](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md)című témakörben láthatja.  Miután a megfelelő kódtárak beépülnek az XCode projektbe, kétféleképpen írhat IoT Hub-hoz kapcsolódó kódot:

* C célkitűzés funkció: Ha a projekt c-cél ban íródott, közvetlenül hívhatja az Azure IoT Hub C SDK API-kat.  Ha a projekt swift nyelven íródott, a függvény létrehozása előtt hívhatja `@objc func` meg, és c vagy Objective-C kód használatával folytathatja az Azure IoT Hubhoz kapcsolódó összes logika írását.  A [mintatárban](https://github.com/Azure-Samples/azure-iot-samples-ios)egy olyan mintakészlet található, amely mindkettőt bemutatja.  

* C-minták belefoglalása: Ha C eszközalkalmazást írt, közvetlenül az XCode projektben hivatkozhat rá:
    * Adja hozzá a sample.c fájlt az XCode projekthez az XCode programból.  
    * Adja hozzá a fejlécfájlt a függőséghez.  A [mintatárban](https://github.com/Azure-Samples/azure-iot-samples-ios) példaként egy fejlécfájl is szerepel. További információkért kérjük, látogasson el az Apple dokumentációs oldalára [az Objective-C.](https://developer.apple.com/documentation/objectivec)

## <a name="develop-for-android-platform"></a>Fejlesztés Android platformra
Az Azure IoT Hub Java SDK támogatja az Android platformot.  Az adott API-verzió tesztelt, kérjük, látogasson el [a platform támogatási oldalon](iot-hub-device-sdk-platform-support.md) a legújabb frissítést.

Ezek a dokumentációk bemutatják, hogyan fejleszthet eszközalkalmazást vagy -szolgáltatást Android-eszközön a Gradle és az Android Studio használatával:

* [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-android.md)  
* [Rövid útmutató: Eszköz vezérlése](quickstart-control-device-android.md) 

## <a name="next-steps"></a>További lépések

* [IoT Hub REST API-referencia](https://docs.microsoft.com/rest/api/iothub/)
* [Azure IoT C SDK forráskód](https://github.com/Azure/azure-iot-sdk-c)
