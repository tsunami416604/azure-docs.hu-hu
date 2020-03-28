---
title: 'Rövid útmutató: Beszéd szintetizálása, Objective-C - Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési SDK használatával hogyan szintetizálható a beszédfelismerés az Objective-C-ben macOS rendszeren
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 37eed03ed839411f1acf5d963d4118a3c6d2c379
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975924"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Rövid útmutató: Beszédfelismerés idoszakának szintetizálása A C-ben macOS rendszeren a Beszéd SDK használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre macOS-alkalmazást a C-ben a Cognitive Services beszédfelismerési SDK használatával a beszéd szövegből való szintetizálásához és az alapértelmezett hangkimenettel való lejátszásához.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, az alábbi előfeltételek listája:

* A beszédfelismerési szolgáltatás [előfizetési kulcsa](~/articles/cognitive-services/Speech-Service/get-started.md)
* MacOS-gép [Xcode 9.4.1-es](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb kóddal, valamint macOS 10.13-as vagy újabb verzióval

## <a name="get-the-speech-sdk-for-macos"></a>A beszédfelismerési SDK beolvasása macOS-hez

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Ne feledje, hogy ez az oktatóanyag nem fog működni az SDK 1.7.0-nál korábbi verziójával.

A Cognitive Services Mac-hez futó beszédbeszéd SDK-ja keretrendszercsomagként van elosztva.
Ezt fel lehet használni az Xcode projektek, mint https://aka.ms/csspeech/macosbinary a [CocoaPod](https://cocoapods.org/), vagy letölthető, és manuálisan kapcsolódik. Ez az útmutató cocoapodot használ.

## <a name="create-an-xcode-project"></a>Xcode projekt létrehozása

Indítsa el az Xcode programot, és indítson új projektet az**Új** > **projekt** **fájlja** > gombra kattintva.
A sablonkijelölési párbeszédpanelen válassza a "Kakaóalkalmazás" sablont.

A további párbeszédpaneleken válassza az alábbi lehetőségeket:

1. Project Options párbeszédpanel
    1. Adja meg a gyorsindítási alkalmazás nevét, például: `helloworld`.
    1. Adja meg a megfelelő szervezetnevet és a szervezeti azonosítót, ha már rendelkezik Apple fejlesztői fiókkal. Kipróbálás céljából választhat bármilyen nevet, például: `testorg`. Az alkalmazás aláírásához megfelelő létesítési profilra van szükség. A részleteket az [Apple fejlesztői webhelyén](https://developer.apple.com/) találja.
    1. Ügyeljen arra, hogy a projekt nyelveként az Objective-C-t válassza ki.
    1. Tiltsa le a jelölőnégyzeteket a szövegegység használatához és dokumentumalapú alkalmazás létrehozásához. A mintaalkalmazás egyszerű felhasználói felülete programozott módon jön létre.
    1. Távolítsa el a jelölést a tesztekre és az alapvető adatokra vonatkozó jelölőnégyzetekből.
    ![Projektbeállítások](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)
1. A projektkönyvtár kiválasztása
    1. Válassza ki azt a könyvtárat, amelybe a projektet be szeretné helyezni. Ezzel létrehoz `helloworld` egy könyvtárat a kezdőkönyvtárban, amely tartalmazza az Xcode projekt összes fájlját.
    1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
1. Állítsa be a hálózati hozzáférés jogosultságait. Kattintson az alkalmazás nevére a bal oldali áttekintés első sorában az alkalmazás konfigurációjának megolvasásához, majd válassza a "Képességek" lapot.
    1. Engedélyezze az "Alkalmazás sandbox" beállítását az alkalmazáshoz.
    1. Engedélyezze a "Kimenő kapcsolatok" hozzáférés jelölőnégyzetét.
    ![Sandbox-beállítások](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox-tts.png)
1. Zárja be az Xcode projektet. A CocoaPods beállítása után később egy másik példányt fog használni.

## <a name="install-the-sdk-as-a-cocoapod"></a>Telepítse az SDK-t CocoaPod-ként

1. Telepítse a CocoaPod függőségkezelőt a [telepítési útmutatóban](https://guides.cocoapods.org/using/getting-started.html)leírtak szerint.
1. Keresse meg a mintaalkalmazás`helloworld`könyvtárát ( ). Helyezzen el egy `Podfile` szöveges fájlt a könyvtárban a nevével és tartalmával:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/Podfile)]
1. Nyissa meg `helloworld` a terminál könyvtárát, `pod install`és futtassa a parancsot . Ez létrehoz `helloworld.xcworkspace` egy Xcode munkaterületet, amely mind a mintaalkalmazást, mind a beszédfelismerési SDK-t függőségként tartalmazza. Ez a munkaterület a következőkben lesz használva.

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Nyissa `helloworld.xcworkspace` meg a munkaterületet Xcode-ban.
1. Cserélje le az automatikusan létrehozott `AppDelegate.m` fájl tartalmát az alábbi kódra:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.
1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](~/articles/cognitive-services/Speech-Service/regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. A hibakeresési kimenet láthatóvá tétele (**View** > **Debug Area** > **Activate Console**).
1. A példakódot úgy építheti ki és futtathatja, hogy a **menüben** -> vagy a **Lejátszás** gombra kattintva kiválasztja a Termék**futtatása** lehetőséget.
1. Miután bevitt egy szöveget, és rákattintott az alkalmazás gombjára, hallania kell a lejátszott szintetizált hangot.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Objektív C-minták felfedezése a GitHubon](https://aka.ms/csspeech/samples)

