---
title: 'Gyors útmutató: A beszédfelismerést, Objective-C – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan működik a beszédfelismerés Objective-C nyelven írt iOS-alkalmazásokban a Speech Service SDK használatával
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: chlandsi
ms.openlocfilehash: 75411ebd50448c5f490a1f03fbbf25a61dbffaf8
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718112"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-service-sdk"></a>Gyors útmutató: Beszédfelismerést Objective-C nyelven a Speech Service SDK használatával iOS rendszeren

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebből a cikkből megtudhatja, hogyan írhat Objective-C nyelven olyan iOS-alkalmazást, amely a Cognitive Services Speech SDK-val átírja a felvett beszédet tartalmazó hangfájlokat szöveggé.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következő előfeltételek listáját:

* A [előfizetési kulcs](get-started.md) a beszédfelismerési szolgáltatás
* Olyan macOS-gépeken, [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb
* A cél 11.4 vagy újabb IOS-verzió beállítása

## <a name="get-the-speech-sdk-for-ios"></a>Az iOS-re szánt Speech SDK letöltése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A Cognitive Services Speech SDK jelenlegi verziója az `1.2.0`.

A Macre és iOS-re szánt Cognitive Services Speech SDK-t jelenleg Cocoa-keretrendszerként áll rendelkezésre.
Innen tölthető le: https://aka.ms/csspeech/iosbinary. Töltse le a fájlt a kezdőkönyvtárába.

## <a name="create-an-xcode-project"></a>Xcode-projekt létrehozása

Indítsa el az Xcode-ot, majd a **File** > **New** > **Project** lehetőséget választva kezdjen új projektet.
A sablonválasztó párbeszédpanelen válassza az „iOS Single View App” sablont.

A további párbeszédpaneleken válassza az alábbi lehetőségeket:

1. Project Options párbeszédpanel
    1. Adja meg a gyorsindítási alkalmazás nevét, például: `helloworld`.
    1. Ha már rendelkezik Apple fejlesztői fiókkal, adja meg a megfelelő szervezetnevet és szervezetazonosítót. Kipróbálás céljából választhat bármilyen nevet, például: `testorg`. Az alkalmazás aláírásához, egy megfelelő kiépítési profil szükséges. Tekintse meg a [Apple fejlesztői webhely](https://developer.apple.com/) részleteiről.
    1. Ügyeljen arra, hogy a projekt nyelveként az Objective-C-t válassza ki.
    1. Távolítsa el a jelölést a tesztekre és az alapvető adatokra vonatkozó jelölőnégyzetekből.
    ![Projektbeállítások](media/sdk/qs-objectivec-project-settings.png)
1. A projektkönyvtár kiválasztása
    1. Válassza ki a projekt tárolására használt kezdőkönyvtárat. Ez létrehoz egy `helloworld` a kezdőkönyvtárban, az Xcode projekt a fájlokat tartalmazó könyvtárat.
    1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
    1. A *Project Settings* alatt állítsa be az SDK elérési útjait.
        1. Az a **általános** lapjára az **beágyazott bináris fájlok** fejléc, adja hozzá az SDK-könyvtár keretet másként: **Adja hozzá beágyazott bináris fájlok** > **más hozzáadása...**  > Nyissa meg a verzió kezdőkönyvtárához navigál, és válassza ki a fájlt `MicrosoftCognitiveServicesSpeech.framework`. Ez hozzáadja az SDK-könyvtár a fejléc **társított keretrendszer és a szalagtárak** automatikusan.
        ![A hozzáadott keretrendszer](media/sdk/qs-objectivec-framework.png)
        1. Lépjen a **Build Settings** lapra, és engedélyezze az **összes** beállítást.
        1. Vegye fel a könyvtárat `$(SRCROOT)/..` a keretrendszer-keresési útvonalak közé (*Framework Search Paths* a **Search Paths** részben).
        ![Keretrendszer-keresési útvonal beállítása](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>A felhasználói felület beállítása

A PéldaAlkalmazás egy nagyon egyszerű felhasználói felületen fog rendelkezni: Két gomb beszédfelismerés elindításához, vagy fájlból, vagy a mikrofon bemeneti és a egy szöveges feliratot az eredmény megjelenítéséhez.
A felhasználói felületet a projekt `Main.storyboard` részében kell beállítani.
Nyissa meg a vázlat XML-nézetét úgy, hogy jobb egérgombbal a projektfa `Main.storyboard` elemére kattint, majd az **Open As...** > **Source Code** lehetőséget választja.
Cserélje le az automatikusan létrehozott nevet XML ezt a kódot:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Töltse le a [wav-mintafájlt](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) úgy, hogy jobb egérgombbal a hivatkozásra kattint, majd a **Save target as...** lehetőséget választja. Adja hozzá a wav-fájlt a projekthez erőforrásként úgy, hogy a Finder ablakból a projektnézet gyökérszintjére húzza.
A következő párbeszédpanelen a beállítások módosítása nélkül kattintson a **Finish** gombra.
1. Cserélje le az automatikusan létrehozott `ViewController.m` fájl tartalmát az alábbi kódra:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.
1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).
1. Adja hozzá a mikrofonhoz való hozzáférésre vonatkozó kérelmet. Kattintson a jobb gombbal a `Info.plist` a projekt fát, majd válassza a bejegyzés **Megnyitás másként...**   >  **Forráskódját**. Adja hozzá az alábbi sorokat a `<dict>` részhez, majd mentse a fájlt.
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="building-and-running-the-sample"></a>A minta lefordítása és futtatása

1. Tegye láthatóvá a hibakeresési kimenetet (**View** > **Debug Area** > **Activate Console**).
1. A **Product** -> **Destination** menüpontban található listáról válassza ki az alkalmazás célhelyeként használni kívánt iOS-szimulátort vagy fejlesztőgépéhez csatlakoztatott iOS-eszközt.
1. Fordítsa le és futtassa a példakódot az iOS-szimulátorban úgy, hogy a **Product** -> **Run** lehetőséget választja a menüből, vagy a **Play** gombra kattint.
A Speech SDK jelenleg csak a 64 bites iOS-platformokat támogatja.
1. Miután a „Recognize (File)” (Felismerés (Fájl)) gombra kattint az alkalmazásban, a képernyő alsó részén meg kell jelennie a „What's the weather like?” (Milyen az időjárás?) hangfájl tartalmának.

 ![Szimulált iOS-alkalmazás](media/sdk/qs-objectivec-simulated-app.png)

1. Miután a "Recognize (Microphone)" gombra kattint az alkalmazásban, és mond néhány szót, a képernyő alsó részén meg kell jelenniük a mondottaknak.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Objective-C-minták megtekintése a Githubon](https://aka.ms/csspeech/samples)
