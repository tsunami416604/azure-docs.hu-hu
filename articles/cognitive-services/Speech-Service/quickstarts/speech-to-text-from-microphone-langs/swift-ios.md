---
title: 'Rövid útmutató: A mikrofon beszédének felismerése, Swift – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerheti fel a beszédfelismerést a Swift ben iOS rendszeren a BeszédSDK használatával
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: 31f5339c70d52019400ca5f1fe873de4790a3bd6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380524"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Rövid útmutató: A beszédfelismerés a Swift ben iOS rendszeren a beszédska khasználatával

Rövid útmutatók is rendelkezésre állnak a [beszédszintézishez.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md)

Ebben a cikkben megtudhatja, hogyan hozhat létre iOS-alkalmazást a Swiftben az Azure Cognitive Services Speech SDK használatával a mikrofonból szöveggé rögzített beszédát.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, a következőkre lesz szüksége:

* A beszédfelismerési szolgáltatás [előfizetési kulcsa.](~/articles/cognitive-services/Speech-Service/get-started.md)
* MacOS-gép [Xcode 9.4.1-es](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb kóddal és [CocoaPods-mal.](https://cocoapods.org/)

## <a name="get-the-speech-sdk-for-ios"></a>Az iOS-re szánt Speech SDK letöltése

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Ez az oktatóanyag nem működik az SDK 1.6.0-nál korábbi verziójával.

Az iOS-hez tartozó Cognitive Services beszédbeszéd SDK-ja keretrendszercsomagként van elosztva. Ezt fel lehet használni az Xcode projektek, https://aka.ms/csspeech/iosbinary mint a [CocoaPod](https://cocoapods.org/) vagy letölthető és manuálisan kapcsolódik. Ez a cikk cocoapodot használ.

## <a name="create-an-xcode-project"></a>Xcode projekt létrehozása

Indítsa el az Xcode programot, és indítson új projektet az**Új** > **projekt** **fájlja** > gombra kattintva.
A sablonkijelölési párbeszédpanelen jelölje ki az **iOS egynézetes** alkalmazássablont.

A következő párbeszédpaneleken adja meg a következő beállításokat.

1. A **Projekt beállításai** párbeszédpanelen:
    1. Adja meg a rövid útmutató alkalmazás nevét, például *helloworld*.
    1. Ha már rendelkezik Apple fejlesztői fiókkal, adja meg a megfelelő szervezetnevet és a szervezeti azonosítót. Tesztelési célokra használjon olyan nevet, mint a *testorg*. Az alkalmazás aláírásához megfelelő létesítési profilra van szükség. További információt az [Apple fejlesztői webhelyén talál.](https://developer.apple.com/)
    1. Győződjön meg arról, hogy a **Swift** lesz a projekt nyelve.
    1. Tiltsa le a jelölőnégyzeteket a szövegegység használatához és dokumentumalapú alkalmazás létrehozásához. A mintaalkalmazás egyszerű felhasználói felülete programozott módon jön létre.
    1. Törölje a jelet a tesztek és az alapadatok jelölőnégyzetéből.
1. Projektkönyvtár kiválasztása:
    1. Válassza ki azt a könyvtárat, amelybe a projektet be szeretné helyezni. Ez a lépés létrehoz egy helloworld könyvtárat a kiválasztott könyvtárban, amely tartalmazza az Xcode projekt összes fájlját.
    1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
1. Az alkalmazásnak deklarálnia kell `Info.plist` a mikrofon használatát a fájlban. Jelölje ki a fájlt az áttekintésben, és adja hozzá az **Adatvédelem – Mikrofon használati leírás** gombot egy olyan értékkel, mint a Mikrofon a *beszédfelismeréshez*.

    ![Beállítások az Info.plist-ben](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Zárja be az Xcode projektet. A CocoaPods beállítása után később egy másik példányt is használhat.

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Helyezzen egy új fejlécfájlt a helloworld `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` könyvtárba a helloworld projekten belül. Illessze be a következő kódot a következőbe:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Adja hozzá `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` az áthidaló fejléc relatív elérési útját a Helloworld cél Swift projektbeállításaihoz a **C objektív áthidaló fejléc** mezőben.

   ![Fejléc tulajdonságai](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. Cserélje le az automatikusan `AppDelegate.swift` létrehozott fájl tartalmát a következő kódra:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Cserélje le az automatikusan `ViewController.swift` létrehozott fájl tartalmát a következő kódra:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. A `ViewController.swift`alkalmazásban cserélje `YourSubscriptionKey` le a karakterláncot az előfizetési kulcsra.
1. Cserélje le `YourServiceRegion` a karakterláncot az előfizetéshez társított [régióra.](~/articles/cognitive-services/Speech-Service/regions.md) Például használja `westus` az ingyenes próba-előfizetés.

## <a name="install-the-sdk-as-a-cocoapod"></a>Telepítse az SDK-t CocoaPod-ként

1. Telepítse a CocoaPod függőségkezelőt a [telepítési útmutatóban](https://guides.cocoapods.org/using/getting-started.html)leírtak szerint.
1. Nyissa meg a mintaalkalmazás könyvtárát, amely a helloworld. Helyezzen el egy *Podfile* nevű szövegfájlt és a könyvtárban a következő tartalmat:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. Nyissa meg a helloworld könyvtárat egy `pod install`terminálon, és futtassa a parancsot . Ez a parancs `helloworld.xcworkspace` létrehoz egy Xcode munkaterületet, amely a mintaalkalmazást és a beszédfelismerési SDK-t függőségként is tartalmazza. Ez a munkaterület a következő lépésekben használatos.

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Nyissa meg `helloworld.xcworkspace` a munkaterületet Xcode-ban.
1. Tegye láthatóvá a hibakeresési kimenetet a **Konzol megtekintése** > terület**aktiválása** > **lehetőséget**választva.
1. Válassza ki az iOS-szimulátort vagy a fejlesztői géphez csatlakoztatott iOS-eszközt az alkalmazás célhelyeként a **Termék** > **cél menüjének** listájából.
1. A modellkódot az iOS-szimulátorban a > **Termékfuttatás** menüben történő kiválasztásával hozhatja létre és futtathatja. **Product** A Lejátszás gombot is **kiválaszthatja.**
1. Miután **kiválasztotta** az Alkalmazás Felismerés gombját, és mondott néhány szót, a képernyő alsó részén látnia kell a kimondott szöveget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fedezze fel mintáinkat a GitHubon](https://aka.ms/csspeech/samples)
