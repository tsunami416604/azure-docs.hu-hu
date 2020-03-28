---
title: 'Rövid útmutató: Beszéd felismerése mikrofonból, Objective-C - Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerheti fel a beszédfelismerést az Objective-C-ben iOS rendszeren a Beszéd SDK használatával
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c1246b19670a18f8dadc0c5e1c64dd5af4c1b210
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380779"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Rövid útmutató: Beszédfelismerés az Objective-C-ben iOS rendszeren a beszédfelismerési SDK használatával

Rövid útmutatók is rendelkezésre állnak a [beszédszintézishez.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md)

Ebben a cikkben megtudhatja, hogyan hozhat létre iOS-alkalmazást a C-ben az Azure Cognitive Services Speech SDK használatával, amely a beszédeket egy mikrofonból vagy rögzített hanggal rendelkező fájlból írja át a beszédszöveggé.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, a következőkre lesz szüksége:

* A beszédfelismerési szolgáltatás [előfizetési kulcsa.](~/articles/cognitive-services/Speech-Service/get-started.md)
* [Xcode 9.4.1-es](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) vagy újabb rendszerű macOS-gép.
* A cél beállítása iOS 9.3-as vagy újabb verziója.

## <a name="get-the-speech-sdk-for-ios"></a>Az iOS-re szánt Speech SDK letöltése

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

A Cognitive Services beszédstidk az iOS jelenleg kakaókeretrendszerként van elosztva.
Meg lehet letölteni [erről a honlapról](https://aka.ms/csspeech/iosbinary). Töltse le a fájlt a kezdőkönyvtárába.

## <a name="create-an-xcode-project"></a>Xcode projekt létrehozása

Indítsa el az Xcode programot, és indítson új projektet az**Új** > **projekt** **fájlja** > gombra kattintva.
A sablonkijelölési párbeszédpanelen jelölje ki az **iOS egynézetes** alkalmazássablont.

A következő párbeszédpaneleken adja meg a következő beállításokat.

1. A **Projekt beállításai** párbeszédpanelen:
    1. Adja meg a rövid útmutató alkalmazás nevét, például *helloworld*.
    1. Ha már rendelkezik Apple fejlesztői fiókkal, adja meg a megfelelő szervezeti nevet és szervezeti azonosítót. Tesztelési célokra használjon olyan nevet, mint a *testorg*. Az alkalmazás aláírásához megfelelő létesítési profilra van szükség. További információt az [Apple fejlesztői webhelyén talál.](https://developer.apple.com/)
    1. Győződjön meg arról, hogy a **C célkitűzés** van kiválasztva a projekt nyelveként.
    1. Törölje a jelet a tesztek és az alapadatok jelölőnégyzetéből.

    ![Projekt beállításai](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. Projektkönyvtár kiválasztása:
   1. Válassza ki a projekt tárolására használt kezdőkönyvtárat. Ez a lépés létrehoz egy helloworld könyvtárat a kezdőkönyvtárban, amely tartalmazza az Xcode projekt összes fájlját.
   1. Ennél a próbaprojektnél tiltsa le a Git-adattár létrehozását.
   1. Állítsa be az SDK elérési útjait a projekt beállítások képernyőjén.
      1. A Beágyazott bináris **fájlok** fejlécének **Általános** lapján adja hozzá az SDK-könyvtárat keretrendszerként a **Beágyazott bináris fájlok** > **hozzáadása hozzáadása egyéb lehetőség**kiválasztásával. Nyissa meg a kezdőkönyvtárat, és jelölje ki a fájlt `MicrosoftCognitiveServicesSpeech.framework`. Ez a művelet automatikusan hozzáadja az SDK-függvénytárat a **fejléccsatolt keretrendszerhez és könyvtárakhoz.**
         ![Hozzáadott keret](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. Nyissa meg a **Build Settings (Build Settings)** lapot, és válassza a **Mind** beállítást.
      1. Adja hozzá a könyvtár $(SRCROOT)/. **a Keretútvonalak** elemre a **Keresési útvonalak** fejléc alatt.

      ![Keretrendszer keresési útvonalának beállítása](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>A felhasználói felület beállítása

A példa alkalmazás egy nagyon egyszerű felhasználói felület. Két gombbal indíthatja el a beszédfelismerést fájlból vagy mikrofonbemenetből, és egy szöveges címkével rendelkezik az eredmény megjelenítéséhez. A felhasználói felületet a projekt `Main.storyboard` részében kell beállítani. A történet XML-nézetének megnyitásához kattintson `Main.storyboard` a jobb gombbal a projektfa bejegyzésére, és válassza a **Nyitott forráskód** > **lehetőséget.**

Cserélje le az automatikusan létrehozott XML-t erre a kódra:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Töltse le a [mintawavfájlt](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) úgy, hogy a jobb gombbal a hivatkozásra kattint, és a **Cél mentése másként parancsot választja.**
   Adja hozzá a wav-fájlt a projekthez erőforrásként úgy, hogy a Finder ablakból a projektnézet gyökérszintjére húzza.
   A beállítások módosítása nélkül válassza a **Befejezés** lehetőséget a következő párbeszédpanelen.
1. Cserélje le az automatikusan `ViewController.m` létrehozott fájl tartalmát a következő kódra:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. Cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.
1. Cserélje le `YourServiceRegion` a karakterláncot az előfizetéshez társított [régióra.](~/articles/cognitive-services/Speech-Service/regions.md) Például használja `westus` az ingyenes próba-előfizetés.
1. Adja hozzá a mikrofonhoz való hozzáférésre vonatkozó kérelmet. Kattintson a `Info.plist` jobb gombbal a projektfa bejegyzésére, és válassza a **Nyitott forráskódként** > **parancsot.** Adja hozzá a `<dict>` következő sorokat a szakaszhoz, majd mentse a fájlt.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Tegye láthatóvá a hibakeresési kimenetet a **Konzol megtekintése** > terület**aktiválása** > **lehetőséget**választva.
1. Válassza ki az iOS-szimulátort vagy a fejlesztői géphez csatlakoztatott iOS-eszközt az alkalmazás célhelyeként a **Termék** > **cél menüjének** listájából.
1. A modellkódot az iOS-szimulátorban a > **Termékfuttatás** menüben történő kiválasztásával hozhatja létre és futtathatja. **Product** A Lejátszás gombot is **kiválaszthatja.**
1. Miután kiválasztotta az **alkalmazás Felismerni (fájl)** gombját, látnia kell a "Milyen az időjárás?" hangfájl tartalmát. hangfájl tartalmának.

   ![Szimulált iOS-alkalmazás](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. Miután kiválasztotta az **Alkalmazás Felismerése (mikrofon)** gombját, és mondott néhány szót, a képernyő alsó részén látnia kell a kimondott szöveget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Objektív C-minták felfedezése a GitHubon](https://aka.ms/csspeech/samples)
