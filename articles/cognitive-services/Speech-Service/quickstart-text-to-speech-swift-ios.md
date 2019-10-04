---
title: 'Gyors útmutató: Hangszintézisi beszéd, Swift-Speech Service'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan szintetizálhat beszédeket a Swift-ben iOS rendszeren a Speech SDK használatával
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 1cede1ced4d1973a5c1da59c5e53f888c54f22b9
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803956"
---
# <a name="quickstart-synthesize-speech-in-swift-on-ios-using-the-speech-sdk"></a>Gyors útmutató: Gyors beszédfelismerés az iOS-ben a Speech SDK használatával

A [beszédfelismerés](quickstart-swift-ios.md)is elérhetővé teszi a gyors útmutatókat.

Ebből a cikkből megtudhatja, hogyan hozhat létre egy iOS-alkalmazást Swift használatával a Cognitive Services Speech SDK-val a beszéd szövegből való szintéziséhez.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következő előfeltételek listáját:

* A beszédfelismerési szolgáltatáshoz tartozó [előfizetési kulcs](get-started.md) .
* A [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb verzióval és a [CocoaPods](https://cocoapods.org/) -mel telepített MacOS-es gép.

## <a name="get-the-speech-sdk-for-ios"></a>Az iOS-re szánt Speech SDK letöltése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Vegye figyelembe, hogy ez az oktatóanyag nem fog működni a 1.7.0-nál korábbi SDK-verzióval.

Az iOS-hez készült Cognitive Services Speech SDK-t keretrendszer-csomagként terjesztik.
Xcode-projektekben használható [CocoaPod](https://cocoapods.org/), vagy a https://aka.ms/csspeech/macosbinary címről tölthető le, és manuálisan is csatolható. Ez az útmutató egy CocoaPod használ.

## <a name="create-an-xcode-project"></a>Xcode-projekt létrehozása

Indítsa el az Xcode-ot, majd a **File** > **New** > **Project** lehetőséget választva kezdjen új projektet.
A sablonválasztó párbeszédpanelen válassza az „iOS Single View App” sablont.

A további párbeszédpaneleken válassza az alábbi lehetőségeket:

1. Project Options párbeszédpanel
    1. Adja meg a gyorsindítási alkalmazás nevét, például: `helloworld`.
    1. Ha már rendelkezik Apple Developer-fiókkal, adja meg a megfelelő szervezet nevét és a szervezet azonosítóját. Kipróbálás céljából választhat bármilyen nevet, például: `testorg`. Az alkalmazás aláírásához megfelelő kiépítési profilra van szükség. További részletekért tekintse meg az [Apple Developer webhelyét](https://developer.apple.com/) .
    1. Győződjön meg arról, hogy a Swift a projekt nyelve van-e kiválasztva.
    1. Tiltsa le a jelölőnégyzeteket a forgatókönyvek használatához és egy dokumentum alapú alkalmazás létrehozásához. A minta alkalmazás egyszerű felhasználói felülete programozott módon lesz létrehozva.
    1. Távolítsa el a jelölést a tesztekre és az alapvető adatokra vonatkozó jelölőnégyzetekből.
1. A projektkönyvtár kiválasztása
    1. Válassza ki azt a könyvtárat, ahová a projektet be szeretné állítani. Ezzel létrehoz egy `helloworld` könyvtárat a kiválasztott könyvtárban, amely a Xcode-projekt összes fájlját tartalmazza.
    1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
1. A Xcode projekt lezárása. A CocoaPods beállítása után később egy másik példányt fog használni.

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Helyezzen egy új, `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` nevű fejlécet a HelloWorld projektben található `helloworld` könyvtárba, és illessze be a következő kódot a fájlba:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Adja hozzá a relatív elérési utat @no__t – 0 értéket az áthidaló fejléchez a HelloWorld-cél Swift-projekt beállításainál az *Objective-C áthidaló fejléc* mezőjében ![Header Properties @ no__t-3
1. Cserélje le az automatikusan létrehozott `AppDelegate.swift` fájl tartalmát az alábbi kódra:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Cserélje le az automatikusan létrehozott `ViewController.swift` fájl tartalmát az alábbi kódra:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. A `ViewController.swift` értéknél cserélje le a `YourSubscriptionKey` karakterláncot az előfizetési kulcsra.
1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

## <a name="install-the-sdk-as-a-cocoapod"></a>Az SDK telepítése CocoaPod

1. Telepítse a CocoaPod-függőség kezelőjét a [telepítési utasításokban](https://guides.cocoapods.org/using/getting-started.html)leírtak szerint.
1. Navigáljon a minta alkalmazás könyvtárába (`helloworld`). Helyezzen egy `Podfile` nevű szövegfájlt, valamint a következő tartalmat a könyvtárba:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/swift-ios/helloworld/Podfile)]
1. Navigáljon a `helloworld` könyvtárba egy terminálon, és futtassa a parancsot `pod install`. Ez egy `helloworld.xcworkspace` Xcode-munkaterületet hoz majd, amely a minta alkalmazást és a Speech SDK-t is függőségként fogja tartalmazni. Ezt a munkaterületet a következőben fogjuk használni.

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Nyissa meg a `helloworld.xcworkspace` munkaterületet a Xcode.
1. Tegye láthatóvá a hibakeresési kimenetet (**View** > **Debug Area** > **Activate Console**).
1. Válassza ki az iOS-szimulátort vagy egy olyan iOS-eszközt, amely a fejlesztői géphez van csatlakoztatva, mint az alkalmazás célhelye a **Product** > **cél** menüben.
1. Fordítsa le és futtassa a példakódot az iOS-szimulátorban úgy, hogy a **Product** > **Run** lehetőséget választja a menüből, vagy a **Play** gombra kattint.
1. Miután beírt valamilyen szöveget, és rákattintott a gombra az alkalmazásban, hallania kell a szintetizált hanglejátszást.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A GitHubon található minták megismerése](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Hangbetűkészletek testreszabása](how-to-customize-voice-font.md)
- [Hangminták rögzítése](record-custom-voice-samples.md)
