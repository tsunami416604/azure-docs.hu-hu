---
title: 'Gyors útmutató: beszédfelismerés felismerése, Objective-C-Speech Service (iOS)'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hozhat létre egy olyan alkalmazást, amely a Cognitive Services Speech SDK-val egy iOS-eszközön beszédet ismer fel a Objective-C használatával.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: chlandsi
ms.openlocfilehash: ba12f258ce7b0db60d3e7343bf12ffc28e8548ab
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524284"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Gyors útmutató: az iOS-ben a Objective-C beszédének felismerése a Speech SDK használatával

A hangfelismerések is elérhetők a [Speech szintézishez](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md).

Ebből a cikkből megtudhatja, hogyan hozhat létre egy iOS-alkalmazást az Objective-C-ben az Azure Cognitive Services Speech SDK használatával beszédet készíthet egy mikrofonból vagy egy rögzített hanggal rendelkező fájlból.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következőkre lesz szüksége:

* A beszédfelismerési szolgáltatáshoz tartozó [előfizetési kulcs](~/articles/cognitive-services/Speech-Service/get-started.md) .
* Egy [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb rendszerű MacOS-gép.
* A cél az iOS 9,3-es vagy újabb verziójára van beállítva.

## <a name="get-the-speech-sdk-for-ios"></a>Az iOS-re szánt Speech SDK letöltése

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Az iOS-hez készült Cognitive Services Speech SDK jelenleg kakaó-keretrendszerként van terjesztve.
[Ezt a webhelyről](https://aka.ms/csspeech/iosbinary)töltheti le. Töltse le a fájlt a kezdőkönyvtárába.

## <a name="create-an-xcode-project"></a>Xcode-projekt létrehozása

Indítsa el a Xcode, és indítson el egy új projektet a **fájl**  >  **új**  >  **projekt**lehetőség kiválasztásával.
A sablon kiválasztása párbeszédpanelen válassza ki az **iOS Egynézetű alkalmazás** sablonját.

Az alábbi párbeszédpaneleken végezze el a következő beállításokat.

1. A **Project Options (projekt beállításai** ) párbeszédpanelen:
    1. Adja meg a Gyorsindítás alkalmazás nevét, például *HelloWorld*.
    1. Ha már rendelkezik Apple Developer-fiókkal, adjon meg egy megfelelő szervezet nevét és szervezeti azonosítóját. Tesztelési célból használjon olyan nevet, mint például a *testorg*. Az alkalmazás aláírásához megfelelő kiépítési profilra van szükség. További információt az [Apple fejlesztői webhelyén](https://developer.apple.com/)talál.
    1. Ügyeljen arra, hogy a projekt nyelve legyen a **Objective-C** beállítás.
    1. Törölje az összes jelölőnégyzetet a tesztekhez és az alapértékekhez.

    ![Projekt beállításai](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. Válasszon egy projekt könyvtárat:
   1. Válassza ki a projekt tárolására használt kezdőkönyvtárat. Ez a lépés létrehoz egy HelloWorld könyvtárat a saját könyvtárában, amely a Xcode projekt összes fájlját tartalmazza.
   1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
   1. Állítsa be az SDK elérési útját a projekt beállításai képernyőn.
      1. A **beágyazott bináris fájlok** fejlécének **általános** lapján adja hozzá az SDK-tárat keretrendszerként a **beágyazott bináris fájlok**hozzáadása  >  **másik hozzáadása**lehetőség kiválasztásával. Nyissa meg a saját könyvtárat, és válassza ki a fájlt `MicrosoftCognitiveServicesSpeech.framework` . Ezzel a művelettel az SDK-függvénytárat a rendszer automatikusan hozzáadja a **csatolt keretrendszerhez és a könyvtárakhoz** .
         ![Keretrendszer hozzáadva](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. Lépjen a **létrehozási beállítások** lapra, és válassza az **összes** beállítást.
      1. Adja hozzá a (SRCROOT)/.. könyvtárat. a **keretrendszer keresési** elérési útjai a **keresési útvonalak** fejlécében.

      ![Framework keresési útvonalak beállítása](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>A felhasználói felület beállítása

A példában szereplő alkalmazás nagyon egyszerű felhasználói felülettel rendelkezik. Az eredmény megjelenítéséhez két gomb szükséges a beszédfelismerés elindításához a fájlból vagy a mikrofon bemenetből és egy szöveges címkéből. A felhasználói felületet a projekt `Main.storyboard` részében kell beállítani. A történet XML-nézetének megnyitásához kattintson a jobb gombbal a `Main.storyboard` projekt fájának bejegyzésére, és válassza a **Megnyitás**  >  **forrásként kód**lehetőséget.

Cserélje le az automatikusan létrehozott XML-kódot a következő kódra:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. A [minta WAV-fájl](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) letöltéséhez kattintson a jobb gombbal a hivatkozásra, és válassza a **cél mentése másként**lehetőséget.
   Adja hozzá a wav-fájlt a projekthez erőforrásként úgy, hogy a Finder ablakból a projektnézet gyökérszintjére húzza.
   A beállítások módosítása nélkül válassza a **Befejezés** lehetőséget a következő párbeszédpanelen.
1. Cserélje le az automatikusan létrehozott `ViewController.m` fájl tartalmát a következő kódra:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.
1. Cserélje le a karakterláncot `YourServiceRegion` az előfizetéséhez társított [régióra](~/articles/cognitive-services/Speech-Service/regions.md) . Használja például `westus` az ingyenes próbaverziós előfizetést.
1. Adja hozzá a mikrofonhoz való hozzáférésre vonatkozó kérelmet. Kattintson a jobb gombbal a `Info.plist` projekt fájának bejegyzésére, és válassza a **Megnyitás**  >  **forrásként kód**lehetőséget. Adja hozzá a következő sorokat a `<dict>` szakaszhoz, majd mentse a fájlt.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. A hibakeresési kimenet láthatóvá tétele a **View**  >  **hibakeresési területek**  >  **aktiválása konzol**kiválasztásával.
1. Válassza ki az iOS-szimulátort vagy egy olyan iOS-eszközt, amely a fejlesztői géphez van csatlakoztatva, mint az alkalmazás célhelye a **termék**  >  **cél** menüjének listájában.
1. Hozza létre és futtassa a példában szereplő kódot az iOS-szimulátorban. Ehhez válassza a menü **termék**  >  **futtatása** elemét. A **Lejátszás** gombot is kiválaszthatja.
1. Miután kiválasztotta a **felismerés (fájl)** gombot az alkalmazásban, meg kell jelennie a következő hangfájl tartalmának: "What 's The Weather Like?" hangfájl tartalmának.

   ![Szimulált iOS-alkalmazás](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. Miután kiválasztotta a **felismerés (mikrofon)** gombot az alkalmazásban, és mondjuk egy pár szót, meg kell jelennie a képernyő alsó részén látható szövegnek.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A Objective-C minták megismerése a GitHubon](https://aka.ms/csspeech/samples)
