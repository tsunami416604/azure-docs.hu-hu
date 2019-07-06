---
title: 'Gyors útmutató: Swift - beszédszolgáltatások beszédfelismerési, felismerése'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan beszédfelismerést SWIFT nyelvű IOS rendszerű eszközökön, a beszéd SDK-val
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 98c37d4a39235e5ed1a82df72886d196cbf19e49
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605044"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-using-the-speech-sdk"></a>Gyors útmutató: Beszédfelismerést SWIFT nyelvű IOS rendszerű eszközökön, a beszéd SDK-val

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebből a cikkből elsajátíthatja a Cognitive Services beszédfelismerő SDK segítségével a beszéd szöveggé mikrofon gépről rögzített lefényképezze SWIFT nyelvű iOS-alkalmazás létrehozása.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következő előfeltételek listáját:

* A [előfizetési kulcs](get-started.md) a beszédfelismerési szolgáltatás.
* Olyan macOS-gépeken, [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb és [CocoaPods](https://cocoapods.org/) telepítve.

## <a name="get-the-speech-sdk-for-ios"></a>Az iOS-re szánt Speech SDK letöltése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A Cognitive Services Speech SDK jelenlegi verziója az `1.6.0`. Vegye figyelembe, hogy ebben az oktatóanyagban nem fog működni az SDK-t bármely korábbi verzióját módosítása nélkül.

A Cognitive Services beszédfelismerő SDK iOS-hez egy keretrendszer csomag terjesztése.
Az Xcode-projektekhez, használható egy [CocoaPod](https://cocoapods.org/), vagy a letöltött https://aka.ms/csspeech/macosbinary és a manuálisan csatolt. Ez az útmutató egy CocoaPod használja.

## <a name="create-an-xcode-project"></a>Az Xcode-projekt létrehozása

Indítsa el az Xcode-ot, majd a **File** > **New** > **Project** lehetőséget választva kezdjen új projektet.
A sablonválasztó párbeszédpanelen válassza az „iOS Single View App” sablont.

A további párbeszédpaneleken válassza az alábbi lehetőségeket:

1. Project Options párbeszédpanel
    1. Adja meg a gyorsindítási alkalmazás nevét, például: `helloworld`.
    1. Ha már rendelkezik egy Apple developer-fiókot adjon meg egy megfelelő nevet és a egy szervezet azonosítója. Kipróbálás céljából választhat bármilyen nevet, például: `testorg`. Az alkalmazás aláírásához, egy megfelelő kiépítési profil szükséges. Tekintse meg a [Apple fejlesztői webhely](https://developer.apple.com/) részleteiről.
    1. Ellenőrizze, hogy a Swift van kiválasztva a nyelve a projekthez.
    1. Tiltsa le a jelölőnégyzetek storyboards használatához és a egy dokumentum-alapú alkalmazás létrehozása. A mintaalkalmazás egyszerű felhasználói felülete programozott módon jön létre.
    1. Távolítsa el a jelölést a tesztekre és az alapvető adatokra vonatkozó jelölőnégyzetekből.
1. A projektkönyvtár kiválasztása
    1. Válasszon egy könyvtárat a helyezi a projektet. Ez létrehoz egy `helloworld` könyvtárat a kiválasztott az Xcode-projektben az összes fájlt tartalmazó könyvtárba.
    1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
1. Az alkalmazás is kell deklarálnia az a mikrofon használatát a `Info.plist` fájlt. Kattintson a fájlra a az áttekintést, és adja meg az "Adatvédelem – mikrofon használatának Description" kulcsot, például "Mikrofon szükséges beszédfelismerés" értékkel.
    ![Az Info.plist fájlban beállításai](media/sdk/qs-swift-ios-info-plist.png)
1. Zárja be az Xcode-projektben. Később a CocoaPods beállítása után az azt egy másik példányára fogja használni.

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Helyezze el egy nevű új fejlécfájlt `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` be a `helloworld` könyvtárat a helloworld-projektet, és illessze be az alábbi kódot oda belül: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. A relatív elérési út hozzáadása `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` az áthidalási fejlécet a Swift a beállítások a helloworld cél a projekt a *Objective-C Bridging fejléc* mező ![fejléc tulajdonságai](media/sdk/qs-swift-ios-bridging-header.png)
1. Cserélje le az automatikusan létrehozott nevet tartalmát `AppDelegate.swift` által fájlt: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Cserélje le az automatikusan létrehozott nevet tartalmát `ViewController.swift` által fájlt: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. A `ViewController.swift`, cserélje le a karakterláncot `YourSubscriptionKey` az előfizetési kulccsal végzett.
1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

## <a name="install-the-sdk-as-a-cocoapod"></a>Egy CocoaPod, az SDK telepítése

1. Telepítse a CocoaPod Függőségkezelő leírtak szerint a [telepítési utasításokat](https://guides.cocoapods.org/using/getting-started.html).
1. Lépjen abba a könyvtárba, a mintaalkalmazást (`helloworld`). Helyezze el egy szövegfájlt nevű `Podfile` és ebben a könyvtárban az alábbi tartalommal: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Keresse meg a `helloworld` könyvtárat egy terminált, és futtassa a parancsot a `pod install`. A művelet létrehoz egy `helloworld.xcworkspace` mind a mintaalkalmazást, és a Speech SDK függőségként tartalmazó Xcode-munkaterületet. Ez a munkaterület a következő használható.

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Nyissa meg a `helloworld.xcworkspace` munkaterületet az xcode-ban.
1. Tegye láthatóvá a hibakeresési kimenetet (**View** > **Debug Area** > **Activate Console**).
1. IOS-szimulátorban vagy egy iOS-eszközt a fejlesztői gépére csatlakoztatva a cél az alkalmazás számára listájából válassza ki a **termék** > **cél** menü.
1. Fordítsa le és futtassa a példakódot az iOS-szimulátorban úgy, hogy a **Product** > **Run** lehetőséget választja a menüből, vagy a **Play** gombra kattint.
1. Miután a "Recognize" gombra kattintva az alkalmazásban, és tegyük fel, hogy néhány szót, megjelenik a képernyő alsó részén rendelkezik kimondott szöveg.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg a minták a Githubon](https://aka.ms/csspeech/samples)
