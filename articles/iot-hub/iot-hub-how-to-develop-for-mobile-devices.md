---
title: Azure IoT SDK-k használatával kialakított |} Microsoft Docs
description: Fejlesztői útmutató – megtudhatja, hogyan fejlesztése az Azure IoT Hub SDK-k használatával.
author: yzhong94
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 9c4b4a61698556f14a6362984b04e5cc409b5763
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634947"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Az Azure IoT SDK-k használatával fejlesztése

Az eszközök internetes hálózatát dolgot nézze meg az eszközök széles különböző alkalmas: érzékelők, mikrovezérlők, intelligens eszközöket, ipari átjárók, valamint még akkor is.  A mobileszköz lehet egy IoT-eszközök, ha az eszköz-felhő telemetriai adatokat küld, és a felhő felügyeli.  Egy háttér-szolgáltatásalkalmazást, amely kezeli a más IoT-eszközök futtató eszköznek az is lehet.  Mindkét esetben [Azure IoT Hub SDK-k] [ lnk-sdk-overview] is használható a mobileszközök kezeléséhez használható alkalmazások fejlesztéséhez.  

## <a name="develop-for-native-ios-platform"></a>Natív iOS platform fejlesztéséhez

Az Azure IoT Hub SDK-k támogatása natív iOS platform Azure IoT Hub C SDK használatával.  Az eltolásokat tekintheti, mivel az egy iOS SDK, amely a Swift vagy Objective C XCode projekt is használhatja.  A C SDK használatához az IOS rendszerű eszközökön két módja van:
- Az XCode projekt CocoaPod tárak közvetlenül használható.  
- Töltse le a forráskód C SDK-ban, és iOS platform következő összeállítása a [utasítás build] [ lnk-c-devbox] MacOS a.  

Az Azure IoT Hub C SDK a különböző platformokon maximális hordozhatóság C99 lett írva.  A furatok magában foglalja a platform-specifikus összetevők, amely itt található a vékony elfogadása réteg írása [iOS][lnk-ios-pal].  A C SDK funkciók is javítható az iOS platform, beleértve a támogatott Azure IoT Hub primitívek és SDK-specifikus szolgáltatások, mint újrapróbálkozási házirendet a hálózati megbízhatóság.  Az IOS SDK objektumfelület is az Azure IoT Hub C SDK felületéhez hasonló.  

A dokumentáció ismerteti, hogyan fejleszthet egy alkalmazást vagy a szolgáltatás alkalmazás iOS-eszközön keresztül:
- [Gyors üzembe helyezés: Telemetriai adatokat küldhet egy eszközről IoT hubhoz][lnk-device-ios-quickstart]  
- [A felhő üzenetek küldése az eszközt az IoT hubbal][lnk-service-ios-quickstart]  

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Azure IoT Hub CocoaPod könyvtárakkal fejlesztése

Az Azure IoT Hub SDK Objective-C CocoaPod szalagtárak iOS-fejlesztésre készlete kiadását.  CocoaPod szalagtárak legfrissebb listájának megtekintéséhez lásd: [a Microsoft Azure IoT CocoaPods][lnk-cocoapod-list].  Miután a megfelelő könyvtárak be vannak építve az XCode-projektjéhez, két módon az IoT-központ kapcsolódó kód írása:
- Az Objective C-funkció: a projekt Objective-C nyelven van megírva, ha hívása API-kat az Azure IoT Hub C SDK közvetlenül.  A projekt Swift nyelven van megírva, ha hívása ``@objc func`` előtt a függvény létrehozásához, és folytathatja a Azure IoT Hub C vagy Objective-C kóddal kapcsolatos összes logics írása.  Található minták is, amely tartalmazza a [minta tárház][lnk-ios-samples-repo].  
- C minták tartalmaznia: Ha egy C eszköz alkalmazás írt, hivatkozhasson rá az XCode-projektjéhez közvetlenül a:
    - Adjon hozzá a sample.c fájlt az XCode-projektjéhez az xcode-ban.  
    - A fejléc fájl hozzáadása a függőség.  A fejléc szerepel a [minta tárház] [ lnk-ios-samples-repo] példaként.  További információkért látogasson el az Apple dokumentációs lapja, [Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="next-steps"></a>További lépések
* [Az IoT Hub REST API-referencia][lnk-rest-ref]
* [Az Azure IoT C SDK forráskódja][lnk-c-sdk]

[lnk-sdk-overview]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks
[lnk-c-devbox]: https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-device-ios-quickstart]:https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-ios
[lnk-service-ios-quickstart]: https://docs.microsoft.com/azure/iot-hub/iot-hub-ios-swift-c2d
[lnk-cocoapod-list]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md
[lnk-ios-samples-repo]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-ios-pal]: https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx
[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
