---
title: 'Gyors útmutató: Beszéd, Objective-C-Speech szolgáltatás felismerése'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerheti fel a beszédfelismerést az iOS-es Objective-C-ben a Speech SDK használatával
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: d87fd06b45d1d3636c442d962daa79cf526e74f2
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803284"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Gyors útmutató: Az iOS-es Objective-C beszédének felismerése a Speech SDK használatával

A hangfelismerések is elérhetők a [Speech szintézishez](quickstart-text-to-speech-objectivec-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre egy iOS-alkalmazást a Objective-C-ben a Cognitive Services Speech SDK-val, hogy a beszédet a mikrofonból vagy egy rögzített hanggal rendelkező fájlból írja át.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következő előfeltételek listáját:

* A [előfizetési kulcs](get-started.md) a beszédfelismerési szolgáltatás
* Olyan macOS-gépeken, [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb
* A cél az iOS 9,3-es vagy újabb verziójára van beállítva

## <a name="get-the-speech-sdk-for-ios"></a>Az iOS-re szánt Speech SDK letöltése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Az iOS-hez készült Cognitive Services Speech SDK jelenleg kakaó-keretrendszerként van terjesztve.
[Innen tölthető le.](https://aka.ms/csspeech/iosbinary) Töltse le a fájlt a kezdőkönyvtárába.

## <a name="create-an-xcode-project"></a>Xcode-projekt létrehozása

Indítsa el az Xcode-ot, majd a **File** > **New** > **Project** lehetőséget választva kezdjen új projektet.
A sablonválasztó párbeszédpanelen válassza az „iOS Single View App” sablont.

A további párbeszédpaneleken válassza az alábbi lehetőségeket:

1. Project Options párbeszédpanel
    1. Adja meg a gyorsindítási alkalmazás nevét, például: `helloworld`.
    1. Ha már rendelkezik Apple fejlesztői fiókkal, adja meg a megfelelő szervezetnevet és szervezetazonosítót. Kipróbálás céljából választhat bármilyen nevet, például: `testorg`. Az alkalmazás aláírásához megfelelő kiépítési profilra van szükség. További részletekért tekintse meg az [Apple Developer webhelyét](https://developer.apple.com/) .
    1. Ügyeljen arra, hogy a projekt nyelveként az Objective-C-t válassza ki.
    1. Távolítsa el a jelölést a tesztekre és az alapvető adatokra vonatkozó jelölőnégyzetekből.
    ![Projektbeállítások](media/sdk/qs-objectivec-project-settings.png)
1. A projektkönyvtár kiválasztása
    1. Válassza ki a projekt tárolására használt kezdőkönyvtárat. Ezzel létrehoz egy `helloworld` könyvtárat a saját könyvtárában, amely a Xcode-projekt összes fájlját tartalmazza.
    1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
    1. A *Project Settings* alatt állítsa be az SDK elérési útjait.
        1. A **beágyazott bináris fájlok** fejlécének **általános** LAPJÁN adja hozzá az SDK-tárat keretrendszerként: **Beágyazott bináris fájlok hozzáadása** >  **...** > navigáljon a saját könyvtárára, és válassza a `MicrosoftCognitiveServicesSpeech.framework` fájlt. Ezzel hozzáadja az SDK-tárat a **csatolt keretrendszerhez és a kódtárak** automatikus fejlécéhez.
        ![A hozzáadott keretrendszer](media/sdk/qs-objectivec-framework.png)
        1. Lépjen a **Build Settings** lapra, és engedélyezze az **összes** beállítást.
        1. Vegye fel a könyvtárat `$(SRCROOT)/..` a keretrendszer-keresési útvonalak közé (*Framework Search Paths* a **Search Paths** részben).
        ![Keretrendszer-keresési útvonal beállítása](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>A felhasználói felület beállítása

A példában szereplő alkalmazásnak nagyon egyszerű felhasználói felülete lesz: Két gomb a beszédfelismerés elindításához fájlból vagy mikrofonos bemenetből, valamint egy szöveges címkéből az eredmény megjelenítéséhez.
A felhasználói felületet a projekt `Main.storyboard` részében kell beállítani.
Nyissa meg a vázlat XML-nézetét úgy, hogy jobb egérgombbal a projektfa `Main.storyboard` elemére kattint, majd az **Open As...**  > **Source Code** lehetőséget választja.
Cserélje le az automatikusan létrehozott XML-kódot a következő kódra:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Töltse le a [wav-mintafájlt](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) úgy, hogy jobb egérgombbal a hivatkozásra kattint, majd a **Save target as...** lehetőséget választja. Adja hozzá a wav-fájlt a projekthez erőforrásként úgy, hogy a Finder ablakból a projektnézet gyökérszintjére húzza.
   A következő párbeszédpanelen a beállítások módosítása nélkül kattintson a **Finish** gombra.
1. Cserélje le az automatikusan létrehozott `ViewController.m` fájl tartalmát az alábbi kódra:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.
1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).
1. Adja hozzá a mikrofonhoz való hozzáférésre vonatkozó kérelmet. Kattintson a jobb gombbal a projekt fájának `Info.plist` bejegyzésére, majd válassza a **Megnyitás másként lehetőséget.**  > **forráskód**. Adja hozzá az alábbi sorokat a `<dict>` részhez, majd mentse a fájlt.
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Tegye láthatóvá a hibakeresési kimenetet (**View** > **Debug Area** > **Activate Console**).
1. Válassza ki az iOS-szimulátort vagy egy olyan iOS-eszközt, amely a fejlesztői géphez van csatlakoztatva, mint az alkalmazás célhelye a **Product** > **cél** menüben.
1. Fordítsa le és futtassa a példakódot az iOS-szimulátorban úgy, hogy a **Product** > **Run** lehetőséget választja a menüből, vagy a **Play** gombra kattint.
1. Miután a „Recognize (File)” (Felismerés (Fájl)) gombra kattint az alkalmazásban, a képernyő alsó részén meg kell jelennie a „What's the weather like?” (Milyen az időjárás?) hangfájl tartalmának.

   ![Szimulált iOS-alkalmazás](media/sdk/qs-objectivec-simulated-app.png)

1. Miután a "Recognize (Microphone)" gombra kattint az alkalmazásban, és mond néhány szót, a képernyő alsó részén meg kell jelenniük a mondottaknak.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Objective-C minták megismerése a GitHubon](https://aka.ms/csspeech/samples)
