---
title: 'Rövid útmutató: A mikrofon beszédének felismerése, Swift – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerheti fel a beszédfelismerést a Swift ben macOS rendszeren a Beszéd SDK használatával
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: cbasoglu
ms.openlocfilehash: 7c5611a142087cff06eefb0277b12ff786074e1a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446833"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Rövid útmutató: A Swift beszédfelismerése macOS rendszeren a Beszéd SDK használatával

Rövid útmutatók is rendelkezésre állnak a [beszédszintézishez.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-macos.md)

Ebből a cikkből megtudhatja, hogyan hozhat létre macOS-alkalmazást a Swiftben a Cognitive Services beszédfelismerési SDK használatával a mikrofonból szöveggé rögzített beszédát.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, az alábbi előfeltételek listája:

* A beszédfelismerési szolgáltatás [előfizetési kulcsa.](~/articles/cognitive-services/Speech-Service/get-started.md)
* MacOS-gép [Xcode 9.4.1-es](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb kóddal és [CocoaPods-mal.](https://cocoapods.org/)

## <a name="get-the-speech-sdk-for-macos"></a>A beszédfelismerési SDK beolvasása macOS-hez

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Ne feledje, hogy ez az oktatóanyag nem fog működni az SDK 1.6.0-nál korábbi verziójával.

A Cognitive Services macOS-hez való beszédfelismerési SDK-t keretrendszercsomagként terjesztik.
Ezt fel lehet használni az Xcode projektek, mint https://aka.ms/csspeech/macosbinary a [CocoaPod](https://cocoapods.org/), vagy letölthető, és manuálisan kapcsolódik. Ez az útmutató cocoapodot használ.

## <a name="create-an-xcode-project"></a>Xcode projekt létrehozása

Indítsa el az Xcode programot, és indítson új projektet az**Új** > **projekt** **fájlja** > gombra kattintva.
A sablonkijelölési párbeszédpanelen válassza a "Kakaóalkalmazás" sablont.

A további párbeszédpaneleken válassza az alábbi lehetőségeket:

1. Project Options párbeszédpanel
    1. Adja meg a gyorsindítási alkalmazás nevét, például: `helloworld`.
    1. Adja meg a megfelelő szervezetnevet és a szervezeti azonosítót, ha már rendelkezik Apple fejlesztői fiókkal. Kipróbálás céljából választhat bármilyen nevet, például: `testorg`. Az alkalmazás aláírásához megfelelő létesítési profilra van szükség. A részleteket az [Apple fejlesztői webhelyén](https://developer.apple.com/) találja.
    1. Győződjön meg arról, hogy a Swift lesz a projekt nyelve.
    1. Tiltsa le a jelölőnégyzeteket a szövegegység használatához és dokumentumalapú alkalmazás létrehozásához. A mintaalkalmazás egyszerű felhasználói felülete programozott módon jön létre.
    1. Távolítsa el a jelölést a tesztekre és az alapvető adatokra vonatkozó jelölőnégyzetekből.
1. A projektkönyvtár kiválasztása
    1. Válassza ki azt a könyvtárat, amelybe a projektet be szeretné helyezni. Ezzel létrehoz `helloworld` egy könyvtárat a kiválasztott könyvtárban, amely tartalmazza az Xcode projekt összes fájlját.
    1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
1. Állítsa be a hálózati és mikrofon-hozzáférési jogosultságokat. Kattintson az alkalmazás nevére a bal oldali áttekintés első sorában az alkalmazás konfigurációjának megolvasásához, majd válassza a "Képességek" lapot.
    1. Engedélyezze az "Alkalmazás sandbox" beállítását az alkalmazáshoz.
    1. Engedélyezze a "Kimenő kapcsolatok" és a "Mikrofon" hozzáférés jelölőnégyzetét.
    ![Sandbox-beállítások](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-sandbox.png)
1. Az alkalmazásnak deklarálnia kell `Info.plist` a mikrofon használatát a fájlban. Kattintson a fájlra az áttekintésben, és adja hozzá az "Adatvédelem - Mikrofon használati leírás" gombot, olyan értékkel, mint a "Mikrofon szükséges a beszédfelismeréshez".
    ![Beállítások az Info.plist-ben](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-info-plist.png)
1. Zárja be az Xcode projektet. A CocoaPods beállítása után később egy másik példányt fog használni.

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Helyezzen egy új fejlécfájlt a helloworld `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` projekt `helloworld` könyvtárába, és illessze be a következő kódot:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Adja hozzá `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` az áthidaló fejléc relatív elérési útját a helloworld cél Swift ![projektbeállításaihoz a C célkitűzés *áthidaló fejlécmező* tulajdonságaiközött](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-bridging-header.png)
1. Cserélje le az automatikusan létrehozott `AppDelegate.swift` fájl tartalmát az alábbi kódra:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. A `AppDelegate.swift`alkalmazásban cserélje `YourSubscriptionKey` le a karakterláncot az előfizetési kulcsra.
1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított régióra (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

## <a name="install-the-sdk-as-a-cocoapod"></a>Telepítse az SDK-t CocoaPod-ként

1. Telepítse a CocoaPod függőségkezelőt a [telepítési útmutatóban](https://guides.cocoapods.org/using/getting-started.html)leírtak szerint.
1. Keresse meg a mintaalkalmazás`helloworld`könyvtárát ( ). Helyezzen el egy `Podfile` szöveges fájlt a könyvtárban a nevével és tartalmával:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/Podfile)]
1. Nyissa meg `helloworld` a terminál könyvtárát, `pod install`és futtassa a parancsot . Ez létrehoz `helloworld.xcworkspace` egy Xcode munkaterületet, amely mind a mintaalkalmazást, mind a beszédfelismerési SDK-t függőségként tartalmazza. Ez a munkaterület a következőkben lesz használva.

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Nyissa `helloworld.xcworkspace` meg a munkaterületet Xcode-ban.
1. A hibakeresési kimenet láthatóvá tétele (**View** > **Debug Area** > **Activate Console**).
1. A példakódot úgy építheti ki és futtathatja, hogy a **menüben** > vagy a **Lejátszás** gombra kattintva kiválasztja a Termék**futtatása** lehetőséget.
1. Miután az alkalmazásban a "Felismerés" gombra kattintott, és mondott néhány szót, az alkalmazásablak alsó részén látnia kell a kimondott szöveget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fedezze fel mintáinkat a GitHubon](https://aka.ms/csspeech/samples)

