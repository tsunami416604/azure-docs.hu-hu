---
title: 'Gyors útmutató: Beszédfelismerés felismerése, Swift-Speech szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerheti fel a macOS-beli Swift-beszédfelismerést a Speech SDK használatával
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/28/2019
ms.author: cbasoglu
ms.openlocfilehash: 5ccfecc2bc61940f72844f43ec81826536cfe89a
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803234"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Gyors útmutató: Gyors beszédfelismerés a macOS-ben a Speech SDK használatával

A hangfelismerések is elérhetők a [Speech szintézishez](quickstart-text-to-speech-swift-macos.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre egy macOS-alkalmazást Swift használatával a Cognitive Services Speech SDK-val a mikrofonról szövegre rögzített beszéd átírásához.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következő előfeltételek listáját:

* A beszédfelismerési szolgáltatáshoz tartozó [előfizetési kulcs](get-started.md) .
* A [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb verzióval és a [CocoaPods](https://cocoapods.org/) -mel telepített MacOS-es gép.

## <a name="get-the-speech-sdk-for-macos"></a>A macOS-hez készült Speech SDK beszerzése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Vegye figyelembe, hogy ez az oktatóanyag nem fog működni a 1.6.0-nál korábbi SDK-verzióval.

A macOS rendszerhez készült Cognitive Services Speech SDK-t keretrendszer-csomagként terjesztik.
Xcode-projektekben használható [CocoaPod](https://cocoapods.org/), vagy a https://aka.ms/csspeech/macosbinary címről tölthető le, és manuálisan is csatolható. Ez az útmutató egy CocoaPod használ.

## <a name="create-an-xcode-project"></a>Xcode-projekt létrehozása

Indítsa el az Xcode-ot, majd a **File** > **New** > **Project** lehetőséget választva kezdjen új projektet.
A sablon kiválasztása párbeszédpanelen válassza a "kakaó-alkalmazás" sablont.

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
1. Állítsa be a jogosultságokat a hálózati és a mikrofonhoz való hozzáféréshez. Kattintson az alkalmazás nevére a bal oldali áttekintés első sorában, hogy beolvassa az alkalmazás konfigurációját, majd válassza a "képességek" fület.
    1. Engedélyezze az alkalmazáshoz tartozó "homokozó" beállítást.
    1. Engedélyezze a "kimenő kapcsolatok" és a "mikrofon" hozzáférés jelölőnégyzeteit.
    ![Sandbox beállítások @ no__t-1
1. Az alkalmazásnak meg kell adnia a mikrofon használatát a `Info.plist` fájlban. Kattintson a fájlra az áttekintésben, és adja hozzá a "privacy-mikrofon használati Leírás" kulcsot, amelynek értéke például "mikrofon szükséges a beszédfelismeréshez".
    @no__t – 0Settings az info. plist @ no__t-1
1. A Xcode projekt lezárása. A CocoaPods beállítása után később egy másik példányt fog használni.

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Helyezzen egy új, `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` nevű fejlécet a HelloWorld projektben található `helloworld` könyvtárba, és illessze be a következő kódot a fájlba:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Adja hozzá a relatív elérési utat @no__t – 0 értéket az áthidaló fejléchez a HelloWorld-cél Swift-projekt beállításainál az *Objective-C áthidaló fejléc* mezőjében ![Header Properties @ no__t-3
1. Cserélje le az automatikusan létrehozott `AppDelegate.swift` fájl tartalmát az alábbi kódra:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/helloworld/AppDelegate.swift#code)]
1. A `AppDelegate.swift` értéknél cserélje le a `YourSubscriptionKey` karakterláncot az előfizetési kulcsra.
1. Cserélje le a `YourServiceRegion` karakterláncot az előfizetéshez társított régióra (például `westus` az ingyenes próbaverziós előfizetéshez).

## <a name="install-the-sdk-as-a-cocoapod"></a>Az SDK telepítése CocoaPod

1. Telepítse a CocoaPod-függőség kezelőjét a [telepítési utasításokban](https://guides.cocoapods.org/using/getting-started.html)leírtak szerint.
1. Navigáljon a minta alkalmazás könyvtárába (`helloworld`). Helyezzen egy `Podfile` nevű szövegfájlt, valamint a következő tartalmat a könyvtárba:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/Podfile)]
1. Navigáljon a `helloworld` könyvtárba egy terminálon, és futtassa a parancsot `pod install`. Ez egy `helloworld.xcworkspace` Xcode-munkaterületet hoz majd, amely a minta alkalmazást és a Speech SDK-t is függőségként fogja tartalmazni. Ezt a munkaterületet a következőben fogjuk használni.

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Nyissa meg a `helloworld.xcworkspace` munkaterületet a Xcode.
1. Tegye láthatóvá a hibakeresési kimenetet (**View** > **Debug Area** > **Activate Console**).
1. A mintakód létrehozásához és futtatásához válassza ki a **Product** > **parancsot** a menüből, vagy kattintson a **Lejátszás** gombra.
1. Miután rákattintott az alkalmazás "felismerés" gombjára, és néhány szót mondott, az alkalmazás ablakának alsó részén látható szöveget kell látnia.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A GitHubon található minták megismerése](https://aka.ms/csspeech/samples)

