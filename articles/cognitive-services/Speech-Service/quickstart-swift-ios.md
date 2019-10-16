---
title: 'Gyors útmutató: beszédfelismerés felismerése, Swift-Speech szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerheti fel az iOS-beli Swift-beszédfelismerést a Speech SDK használatával
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 0a62e670587f271eb9a1beba034886c5f95976a7
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327752"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Gyors útmutató: az iOS-es Swift-beszéd felismerése a Speech SDK használatával

A hangfelismerések is elérhetők a [Speech szintézishez](quickstart-text-to-speech-swift-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre egy iOS-alkalmazást Swift-ben az Azure Cognitive Services Speech SDK használatával, amely a mikrofonról szövegre rögzített beszédet mutatja be.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következőkre lesz szüksége:

* A beszédfelismerési szolgáltatáshoz tartozó [előfizetési kulcs](get-started.md) .
* A [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb verzióval és a [CocoaPods](https://cocoapods.org/) -mel telepített MacOS-es gép.

## <a name="get-the-speech-sdk-for-ios"></a>Az iOS-re szánt Speech SDK letöltése

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Ez az oktatóanyag a 1.6.0-nél korábbi SDK-verzióval nem működik.

Az iOS-hez készült Cognitive Services Speech SDK-t keretrendszer-csomagként terjesztik. A Xcode-projektekben [CocoaPod](https://cocoapods.org/) vagy https://aka.ms/csspeech/macosbinary címről tölthető le, és manuálisan is használható. Ez a cikk egy CocoaPod használ.

## <a name="create-an-xcode-project"></a>Xcode-projekt létrehozása

Indítsa el a Xcode, és indítson el egy új projektet a **fájl** > **új** > **projekt**kiválasztásával.
A sablon kiválasztása párbeszédpanelen válassza ki az **iOS Egynézetű alkalmazás** sablonját.

Az alábbi párbeszédpaneleken végezze el a következő beállításokat.

1. A **Project Options (projekt beállításai** ) párbeszédpanelen:
    1. Adja meg a Gyorsindítás alkalmazás nevét, például *HelloWorld*.
    1. Ha már rendelkezik Apple Developer-fiókkal, adja meg a megfelelő szervezet nevét és a szervezet azonosítóját. Tesztelési célból használjon olyan nevet, mint például a *testorg*. Az alkalmazás aláírásához megfelelő kiépítési profilra van szükség. További információt az [Apple fejlesztői webhelyén](https://developer.apple.com/)talál.
    1. Győződjön meg arról, hogy a **Swift** a projekt nyelve van-e kiválasztva.
    1. Tiltsa le a jelölőnégyzeteket a storyboardok használatához és egy dokumentum alapú alkalmazás létrehozásához. A minta alkalmazás egyszerű felhasználói felületét programozott módon hozza létre.
    1. Törölje az összes jelölőnégyzetet a tesztekhez és az alapértékekhez.
1. Válasszon egy projekt könyvtárat:
    1. Válassza ki azt a könyvtárat, ahová a projektet be szeretné állítani. Ez a lépés létrehoz egy HelloWorld könyvtárat a kiválasztott könyvtárban, amely a Xcode projekt összes fájlját tartalmazza.
    1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
1. Az alkalmazásnak meg kell adnia a mikrofon használatát a `Info.plist` fájlban. Válassza ki a fájlt az áttekintésben, és adja hozzá az **Adatvédelem-mikrofon használati Leírás** kulcsát egy olyan értékkel, mint például a *beszédfelismeréshez a mikrofon szükséges*.

    ![Beállítások az info. plist fájlban](media/sdk/qs-swift-ios-info-plist.png)

1. A Xcode projekt lezárása. A CocoaPods beállítása után később egy másik példányt fog használni.

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Helyezzen egy új, `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` nevű fejlécet a HelloWorld könyvtárba a HelloWorld projekten belül. Illessze be a következő kódot:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Adja hozzá a relatív elérési utat @no__t – 0 értéket az áthidaló fejléchez a HelloWorld cél **– C áthidaló fejléc** mezőjében található Swift-projekt beállításainál.

   ![Fejléc tulajdonságai](media/sdk/qs-swift-ios-bridging-header.png)

1. Cserélje le az automatikusan generált `AppDelegate.swift` fájl tartalmát a következő kódra:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Cserélje le az automatikusan generált `ViewController.swift` fájl tartalmát a következő kódra:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. A `ViewController.swift` értéknél cserélje le a `YourSubscriptionKey` karakterláncot az előfizetési kulcsra.
1. Cserélje le a `YourServiceRegion` karakterláncot az előfizetéséhez társított [régióra](regions.md) . Használja például a `westus` értéket az ingyenes próbaverziós előfizetéshez.

## <a name="install-the-sdk-as-a-cocoapod"></a>Az SDK telepítése CocoaPod

1. Telepítse a CocoaPod-függőség kezelőjét a [telepítési utasításokban](https://guides.cocoapods.org/using/getting-started.html)leírtak szerint.
1. Nyissa meg a HelloWorld, amely a minta alkalmazás könyvtára. Helyezzen egy *cocoapods* nevű szövegfájlt és a következő tartalmat a könyvtárba:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Nyissa meg a HelloWorld könyvtárat egy terminálon, és futtassa a következő parancsot: `pod install`. Ez a parancs létrehoz egy `helloworld.xcworkspace` Xcode-munkaterületet, amely a minta alkalmazást és a Speech SDK-t is tartalmazza függőségként. Ezt a munkaterületet a következő lépésekben lehet használni.

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Nyissa meg `helloworld.xcworkspace` munkaterületet a Xcode.
1. A hibakeresési kimenet láthatóvá tételéhez válassza a **nézet** > **hibakeresési körzet** > **aktiválási konzol**lehetőséget.
1. Válassza ki az iOS-szimulátort vagy egy olyan iOS-eszközt, amely a fejlesztői géphez van csatlakoztatva, mint az alkalmazás célhelye a **Product** > **cél** menüben.
1. Hozza létre és futtassa a példában szereplő kódot az iOS-szimulátorban. Ehhez válassza a menü **termék** > **futtatása** elemét. A **Lejátszás** gombot is kiválaszthatja.
1. Miután kiválasztotta a **felismerés** gombot az alkalmazásban, és néhány szót kimondottan, a képernyő alsó részén megjelenő szöveget kell látnia.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A GitHubon található minták megismerése](https://aka.ms/csspeech/samples)
