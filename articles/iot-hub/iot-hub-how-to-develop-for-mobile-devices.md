---
title: Fejlesztés az Azure IoT SDK-k használatával mobileszközökhöz |} A Microsoft Docs
description: Fejlesztői útmutató – ismerje meg, hogyan hozhat létre a mobileszközök az Azure IoT Hub SDK-k használatával.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 5256a58a2b68584888abcac915392d8e389e9772
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054537"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Fejlesztés a mobil eszközök Azure IoT SDK-k használatával

Is az IOT-eszközök számos különböző képesség hivatkozhatnak: érzékelők, mikrovezérlőktől, az intelligens eszközök, átjárók ipari és még a mobileszközök.  A mobileszköz-IoT-eszköz, ahol eszköz – felhő telemetriát küld és a felhő által felügyelt lehet.  A háttérszolgáltatás alkalmazást, amely kezeli az egyéb IoT-eszközök futtató eszköz is lehet.  Mindkét esetben [Azure IoT Hub SDK-k](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) mobileszközökhöz használható alkalmazások fejlesztéséhez használható.  

## <a name="develop-for-native-ios-platform"></a>Fejlesztés a natív iOS platform

Az Azure IoT Hub SDK-k natív iOS eszközplatform-támogatás révén az Azure IoT Hub C SDK-t adjon meg.  Felfogható, azt egy iOS SDK-t, amely a következő lépésekben a Swift vagy az Objective C XCode-projektben.  IOS-eszközökön az C SDK használatának két módja van:

* Az XCode-projektben CocoaPod kódtárak közvetlenül használni.  
* Letöltheti a forráskódot a C SDK-t és hozhat létre iOS platform következő a [hozhat létre utasítás](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) MacOS-hez.  

Az Azure IoT Hub C SDK a különböző platformokon maximális hordozhatóságot C99 nyelven van megírva.  A portolási folyamata magában foglalja az egyes platformokra vonatkozó összetevők, amely itt található a dinamikus kiosztás bevezetési réteg írása [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  A szolgáltatásokat a C SDK-ban is javítható az iOS platformot, többek között az Azure IoT Hub primitívek támogatott, és SDK-specifikus szolgáltatások például újrapróbálkozási szabályzat a hálózati megbízhatóság.  A felület iOS-hez készült SDK egyben az Azure IoT Hub C SDK-felülethez hasonló.  

Ezek a dokumentációra végig, hogyan hozhat létre egy alkalmazást vagy service-alkalmazás iOS-eszközön:

* [Gyors útmutató: Telemetria küldése egy eszközről IoT hubra](quickstart-send-telemetry-ios.md)  
* [Üzenetküldés a felhőből az eszközre az IoT hub](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Fejlesztés az Azure IoT Hub CocoaPod kódtárak használatával

Az Azure IoT Hub SDK-k kiadások Objective-C CocoaPod kódtárak iOS-fejlesztésre.  CocoaPod kódtárak legfrissebb listáját lásd: [CocoaPods a Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  Miután a megfelelő kódtárak be vannak építve az XCode-projektben, két módja van az IoT Hub kapcsolatos kódot írni:

* Objective C függvény: Ha a projekt Objective-C nyelven írt, meghívhatja API-k az Azure IoT Hub C SDK-t közvetlenül.  Ha a projekt Swift nyelven van megírva, meghívhatja `@objc func` előtt a függvény létrehozása, és folytassa az Azure IoT hubra C vagy az Objective-C kóddal kapcsolatos összes logics írása.  Minták bemutatására is megtalálható a [mintaadattár](https://github.com/Azure-Samples/azure-iot-samples-ios).  

* C-minták foglalják magukban: Ha egy alkalmazást C írt, közvetlenül az XCode-projektben a lehet hivatkozni:
    * Adjon hozzá a sample.c fájlt az XCode-projektben az xcode-ban.  
    * Adja hozzá a fejlécfájlt a függőség.  A fejléc szerepel a [mintaadattár](https://github.com/Azure-Samples/azure-iot-samples-ios) példaként. További információkért látogasson el az Apple dokumentációjában [Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="develop-for-android-platform"></a>Fejlesztés az Android platformhoz
Az Azure IoT Hub Java SDK támogatja az Android platformhoz.  A megadott API-verzió tesztelése, látogasson el a [platform támogatási oldal](iot-hub-device-sdk-platform-support.md) a legújabb frissítést.

Ezek a dokumentációra végig, hogyan hozhat létre egy alkalmazást vagy a szolgáltatás alkalmazás Android-eszközön gradle-t és az Android Studio segítségével:

* [Gyors útmutató: Telemetria küldése egy eszközről IoT hubra](quickstart-send-telemetry-android.md)  
* [Gyors útmutató: Eszköz vezérlése](quickstart-control-device-android.md) 

## <a name="next-steps"></a>További lépések

* [IoT Hub – REST API-referencia](https://docs.microsoft.com/rest/api/iothub/)
* [Az Azure IoT C SDK forráskódját](https://github.com/Azure/azure-iot-sdk-c)
