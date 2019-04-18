---
title: 'Gyors útmutató: A beszédfelismerést, Objective-C – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan beszédfelismerést Objective-C nyelven a Speech SDK-val macOS rendszeren
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/03/2019
ms.author: chlandsi
ms.openlocfilehash: 55fc671d926880375b0420e0eafb6dc63f170ba6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59012198"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Gyors útmutató: A beszédfelismerés SDK-val macOS rendszeren beszédfelismerést Objective-C nyelven

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebből a cikkből megismerheti, hogyan hozhat létre macOS-alkalmazásokat a Cognitive Services beszédfelismerő SDK segítségével a beszéd szöveggé mikrofon gépről rögzített lefényképezze Objective-C nyelven.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következő előfeltételek listáját:

* A [előfizetési kulcs](get-started.md) a beszédfelismerési szolgáltatás
* Olyan macOS-gépeken, [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb verzió és a macOS 10.13 vagy újabb

## <a name="get-the-speech-sdk-for-macos"></a>MacOS-hez a Speech SDK beszerzése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A Cognitive Services Speech SDK jelenlegi verziója az `1.4.0`.

A Cognitive Services beszédfelismerő SDK machez egy keretrendszer csomag terjesztése.
Az Xcode-projektekhez, használható egy [CocoaPod](https://cocoapods.org/), vagy a letöltött https://aka.ms/csspeech/macosbinary és a manuálisan csatolt. Ez az útmutató egy CocoaPod használja.

## <a name="create-an-xcode-project"></a>Az Xcode-projekt létrehozása

Indítsa el az Xcode-ot, majd a **File** > **New** > **Project** lehetőséget választva kezdjen új projektet.
A sablon kiválasztása párbeszédpanelen válassza ki a "Cocoa alkalmazás" sablont.

A további párbeszédpaneleken válassza az alábbi lehetőségeket:

1. Project Options párbeszédpanel
    1. Adja meg a gyorsindítási alkalmazás nevét, például: `helloworld`.
    1. Ha már rendelkezik egy Apple developer-fiókot adjon meg egy megfelelő nevet és a egy szervezet azonosítója. Kipróbálás céljából választhat bármilyen nevet, például: `testorg`. Az alkalmazás aláírásához, egy megfelelő kiépítési profil szükséges. Tekintse meg a [Apple fejlesztői webhely](https://developer.apple.com/) részleteiről.
    1. Ügyeljen arra, hogy a projekt nyelveként az Objective-C-t válassza ki.
    1. Tiltsa le a jelölőnégyzetek storyboards használatához és a egy dokumentum-alapú alkalmazás létrehozása. A mintaalkalmazás egyszerű felhasználói felülete programozott módon jön létre.
    1. Távolítsa el a jelölést a tesztekre és az alapvető adatokra vonatkozó jelölőnégyzetekből.
    ![Projektbeállítások](media/sdk/qs-objectivec-macos-project-settings.png)
1. A projektkönyvtár kiválasztása
    1. Válasszon egy könyvtárat a helyezi a projektet. Ez létrehoz egy `helloworld` a kezdőkönyvtárban, az Xcode projekt a fájlokat tartalmazó könyvtárat.
    1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
1. Állítsa be a jogosultságok, a hálózati és a mikrofon-hozzáférés. Kattintson az Áttekintés a bal oldalon az alkalmazáskonfigurációt való első sorában az alkalmazás nevét, és válassza a "Képességek" fülre.
    1. Engedélyezze az alkalmazás a "Alkalmazás védőfal" beállítást.
    1. Engedélyezze a "Kimenő kapcsolatok" és "Mikrofon" hozzáférést a jelölőnégyzeteket.
    ![A védőfal beállításai](media/sdk/qs-objectivec-macos-sandbox.png)
1. Az alkalmazás is kell deklarálnia az a mikrofon használatát a `Info.plist` fájlt. Kattintson a fájlra a az áttekintést, és adja meg az "Adatvédelem – mikrofon használatának Description" kulcsot, például "Mikrofon szükséges beszédfelismerés" értékkel.
    ![Az Info.plist fájlban beállításai](media/sdk/qs-objectivec-macos-info-plist.png)
1. Zárja be az Xcode-projektben. Később a CocoaPods beállítása után az azt egy másik példányára fogja használni.

## <a name="install-the-sdk-as-a-cocoapod"></a>Egy CocoaPod, az SDK telepítése

1. Telepítse a CocoaPod Függőségkezelő leírtak szerint a [telepítési utasításokat](https://guides.cocoapods.org/using/getting-started.html).
1. Lépjen abba a könyvtárba, a mintaalkalmazást (`helloworld`). Helyezze el egy szövegfájlt nevű `Podfile` és ebben a könyvtárban az alábbi tartalommal:
    ```
    target 'helloworld' do
        platform :osx, '10.13'
        pod 'MicrosoftCognitiveServicesSpeech-macOS', '~> 1.4.0'
    end
    ```
1. Keresse meg a `helloworld` könyvtárat egy terminált, és futtassa a parancsot a `pod install`. A művelet létrehoz egy `helloworld.xcworkspace` mind a mintaalkalmazást, és a Speech SDK függőségként tartalmazó Xcode-munkaterületet. Ez a munkaterület a következő használható.

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Nyissa meg a `helloworld.xcworkspace` munkaterületet az xcode-ban.
1. Cserélje le az automatikusan létrehozott nevet tartalmát `AppDelegate.m` által fájlt: [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.
1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Tegye láthatóvá a hibakeresési kimenetet (**View** > **Debug Area** > **Activate Console**).
1. Hozhat létre és futtassa a példakód kiválasztásával **termék** -> **futtatása** a menüből, vagy kattintson a **lejátszása** gombra.
1. Után kattintson a gombra, és mondja ki néhány szót, megjelenik a szöveg van írva a képernyő alsó részén. Amikor első alkalommal futtatja az alkalmazást, a rendszer az alkalmazás hozzáférést biztosít a számítógép mikrofon kell kérni.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Explore Objective-C samples on GitHub](https://aka.ms/csspeech/samples)

