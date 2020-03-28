---
title: 'Rövid útmutató: Beszéd felismerése mikrofonból, Objective-C - Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési SDK használatával ismerje fel a beszédfelismerést a C-es célban macOS rendszeren
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c2f0fbe66b26c6eca6e0c0b2530efacba9bae958
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380592"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Rövid útmutató: Beszédfelismerés a C-ben macOS rendszeren a Beszéd SDK használatával

Rövid útmutatók is rendelkezésre állnak a [beszédszintézishez.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md)

Ebben a cikkben megtudhatja, hogyan hozhat létre egy macOS-alkalmazást a C-ben az Azure Cognitive Services Speech SDK használatával a mikrofonból szöveggé rögzített beszéd átírásához.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, a következőkre lesz szüksége:

* A beszédfelismerési szolgáltatás [előfizetési kulcsa.](~/articles/cognitive-services/Speech-Service/get-started.md)
* [Xcode 9.4.1-es](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb és macOS 10.13-as vagy újabb rendszerű macOS-gép.

## <a name="get-the-speech-sdk-for-macos"></a>A beszédfelismerési SDK beolvasása macOS-hez

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

A Cognitive Services Mac-hez futó beszédbeszéd SDK-ja keretrendszercsomagként van elosztva. Ezt fel lehet használni az Xcode projektek, https://aka.ms/csspeech/macosbinary mint a [CocoaPod](https://cocoapods.org/) vagy letölthető és manuálisan kapcsolódik. Ez a cikk cocoapodot használ.

## <a name="create-an-xcode-project"></a>Xcode projekt létrehozása

Indítsa el az Xcode programot, és indítson új projektet az**Új** > **projekt** **fájlja** > gombra kattintva. A sablonkijelölési párbeszédpanelen jelölje ki a **Kakaóalkalmazás** sablont.

A következő párbeszédpaneleken adja meg a következő beállításokat.

1. A **Projekt beállításai** párbeszédpanelen:
    1. Adja meg a rövid útmutató alkalmazás nevét, például *helloworld*.
    1. Ha már rendelkezik Apple fejlesztői fiókkal, adja meg a megfelelő szervezetnevet és a szervezeti azonosítót. Tesztelési célokra használjon olyan nevet, mint a *testorg*. Az alkalmazás aláírásához megfelelő létesítési profilra van szükség. További információt az [Apple fejlesztői webhelyén talál.](https://developer.apple.com/)
    1. Győződjön meg arról, hogy a **C célkitűzés** van kiválasztva a projekt nyelveként.
    1. Törölje a jelet a jelölőnégyzetekből a szövegegység használatához és dokumentumalapú alkalmazás létrehozásához. A mintaalkalmazás egyszerű felhasználói felülete programozott módon jön létre.
    1. Törölje a jelet a tesztek és az alapadatok jelölőnégyzetéből.

    ![Projekt beállításai](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Projektkönyvtár kiválasztása:
    1. Válassza ki azt a könyvtárat, amelybe a projektet be szeretné helyezni. Ez a lépés létrehoz egy helloworld könyvtárat a kezdőkönyvtárban, amely tartalmazza az Xcode projekt összes fájlját.
    1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
1. Állítsa be a hálózati és mikrofon-hozzáférési jogosultságokat. Válassza ki az alkalmazás nevét az első sorban a bal oldali áttekintésben az alkalmazás konfigurációjának. Ezután válassza a **Képességek** lapot.
    1. Engedélyezze az **alkalmazás sandbox-beállítását** az alkalmazáshoz.
    1. Jelölje be a **Kimenő kapcsolatok** és **a Mikrofon** eléréséhez jelölőnégyzetet.

    ![Sandbox-beállítások](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. Az alkalmazásnak deklarálnia kell `Info.plist` a mikrofon használatát a fájlban. Jelölje ki a fájlt az áttekintésben, és adja hozzá az **Adatvédelem – Mikrofon használati leírás** gombot egy olyan értékkel, mint a Mikrofon a *beszédfelismeréshez*.

    ![Beállítások az Info.plist-ben](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Zárja be az Xcode projektet. A CocoaPods beállítása után később egy másik példányt is használhat.

## <a name="install-the-sdk-as-a-cocoapod"></a>Telepítse az SDK-t CocoaPod-ként

1. Telepítse a CocoaPod függőségkezelőt a [telepítési útmutatóban](https://guides.cocoapods.org/using/getting-started.html)leírtak szerint.
1. Nyissa meg a mintaalkalmazás könyvtárát, amely a helloworld. Helyezzen el egy *Podfile* nevű szövegfájlt és a könyvtárban a következő tartalmat:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Nyissa meg a helloworld könyvtárat egy `pod install`terminálon, és futtassa a parancsot . Ez a parancs `helloworld.xcworkspace` létrehoz egy Xcode munkaterületet, amely a mintaalkalmazást és a beszédfelismerési SDK-t függőségként is tartalmazza. Ez a munkaterület a következő lépésekben használatos.

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Nyissa meg `helloworld.xcworkspace` a munkaterületet Xcode-ban.
1. Cserélje le az automatikusan `AppDelegate.m` létrehozott fájl tartalmát a következő kódra:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.
1. Cserélje le `YourServiceRegion` a karakterláncot az előfizetéshez társított [régióra.](~/articles/cognitive-services/Speech-Service/regions.md) Például használja `westus` az ingyenes próba-előfizetés.

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Tegye láthatóvá a hibakeresési kimenetet a **Konzol megtekintése** > terület**aktiválása** > **lehetőséget**választva.
1. A példakód létrehozása és futtatása a menü **Termékfuttatás** > **parancsának** kiválasztásával. A Lejátszás lehetőséget is **választhatja.**
1. Miután kiválasztotta a gombot, és mondott néhány szót, a képernyő alsó részén láthatja a kimondott szöveget. Amikor első alkalommal futtatja az alkalmazást, a rendszer kéri, hogy adjon hozzáférést az alkalmazásnak a számítógép mikrofonjához.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Objektív C-minták felfedezése a GitHubon](https://aka.ms/csspeech/samples)
