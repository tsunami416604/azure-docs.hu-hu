---
title: 'Gyors útmutató: szintetizálás beszéd, Objective-C-Speech Service'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan szintetizálhat beszédet a Objective-C-ben iOS-ben a Speech SDK használatával
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 1f5a569f9c3bfa91b78c836e37a22642238674be
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74975941"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Gyors útmutató: beszédfelismerési célú beszéd a Objective-C-ben iOS-ben a Speech SDK használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre egy iOS-alkalmazást a Objective-C-ben a Cognitive Services Speech SDK használatával a beszéd szövegből való szintetizálása érdekében.

## <a name="prerequisites"></a>Előfeltételek

Az első lépések megkezdése előtt tekintse meg az előfeltételek listáját:

* A beszédfelismerési szolgáltatáshoz tartozó [előfizetési kulcs](~/articles/cognitive-services/Speech-Service/get-started.md)
* MacOS rendszerű gép [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb verzióval
* A cél az iOS 9,3-es vagy újabb verziójára van beállítva

## <a name="get-the-speech-sdk-for-ios"></a>Az iOS-re szánt Speech SDK letöltése

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Vegye figyelembe, hogy ez az oktatóanyag nem fog működni a 1.7.0-nál korábbi SDK-verzióval.

Az iOS-hez készült Cognitive Services Speech SDK jelenleg kakaó-keretrendszerként van terjesztve.
Xcode-projektekben használható [CocoaPod](https://cocoapods.org/), illetve manuálisan is letölthető https://aka.ms/csspeech/iosbinary és csatolható. Ez az útmutató egy CocoaPod használ.

## <a name="create-an-xcode-project"></a>Xcode-projekt létrehozása

Indítsa el a Xcode, és indítsa el az új projektet a **fájl** > **új** > **projekt**elemre kattintva.
A sablonválasztó párbeszédpanelen válassza az „iOS Single View App” sablont.

A további párbeszédpaneleken válassza az alábbi lehetőségeket:

1. Project Options párbeszédpanel
    1. Adja meg a gyorsindítási alkalmazás nevét, például: `helloworld`.
    1. Ha már rendelkezik Apple fejlesztői fiókkal, adja meg a megfelelő szervezetnevet és szervezetazonosítót. Kipróbálás céljából választhat bármilyen nevet, például: `testorg`. Az alkalmazás aláírásához megfelelő kiépítési profilra van szükség. További részletekért tekintse meg az [Apple Developer webhelyét](https://developer.apple.com/) .
    1. Ügyeljen arra, hogy a projekt nyelveként az Objective-C-t válassza ki.
    1. Távolítsa el a jelölést a tesztekre és az alapvető adatokra vonatkozó jelölőnégyzetekből.
    ![Projektbeállítások](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)
1. A projektkönyvtár kiválasztása
    1. Válassza ki a projekt tárolására használt kezdőkönyvtárat. Ez létrehoz egy `helloworld` könyvtárat a saját könyvtárában, amely a Xcode projekt összes fájlját tartalmazza.
    1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.

## <a name="install-the-sdk-as-a-cocoapod"></a>Az SDK telepítése CocoaPod

1. Telepítse a CocoaPod-függőség kezelőjét a [telepítési utasításokban](https://guides.cocoapods.org/using/getting-started.html)leírtak szerint.
1. Navigáljon a minta alkalmazás (`helloworld`) könyvtárába. Helyezzen el egy szövegfájlt a névvel `Podfile` és a következő tartalommal a könyvtárban:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/Podfile)]
1. Navigáljon a `helloworld` címtárhoz egy terminálon, és futtassa `pod install`a parancsot. Ez egy `helloworld.xcworkspace` Xcode-munkaterületet hoz majd, amely a minta alkalmazást és a Speech SDK-t is függőségként fogja tartalmazni. Ezt a munkaterületet a következőben fogjuk használni.

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Nyissa `helloworld.xcworkspace` meg a munkaterületet a Xcode.
1. Cserélje le az automatikusan létrehozott `AppDelegate.m` fájl tartalmát az alábbi kódra:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Cserélje le az automatikusan létrehozott `ViewController.m` fájl tartalmát az alábbi kódra:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/ViewController.m#code)]
1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.
1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](~/articles/cognitive-services/Speech-Service/regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. A hibakeresési kimenet láthatóvá tétele **(** > a**hibakeresési területek** > **aktiválása konzolon**).
1. Válassza ki az iOS-szimulátort vagy egy olyan iOS-eszközt, amely a fejlesztői géphez van csatlakoztatva, mint az alkalmazás célhelye a **termék** > **cél** menüjének listájában.
1. Hozza létre és futtassa a példában szereplő kódot az iOS-szimulátorban. Ehhez válassza a **termék** > **futtatása** lehetőséget a menüből, vagy kattintson a **Lejátszás** gombra.

   ![Szimulált iOS-alkalmazás](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app-tts.png)

1. Miután beírt valamilyen szöveget, és rákattintott a gombra az alkalmazásban, hallania kell a szintetizált hanglejátszást.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Objective-C minták megismerése a GitHubon](https://aka.ms/csspeech/samples)

