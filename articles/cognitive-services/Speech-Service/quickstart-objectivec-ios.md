---
title: 'Gyors útmutató: Beszédfelismerést Objective-C nyelven a Cognitive Services beszédfelismerő SDK használatával iOS rendszeren'
titleSuffix: Microsoft Cognitive Services
description: Ismerje meg, hogyan beszédfelismerést Objective-C nyelven a Cognitive Services beszédfelismerő SDK használatával iOS rendszeren
services: cognitive-services
author: chlandsi
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 09/24/2018
ms.author: chlandsi
ms.openlocfilehash: e21348ccd694baf6b7eccf2787ec0a9f21a73b11
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985645"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-cognitive-services-speech-sdk"></a>Gyors útmutató: Beszédfelismerést Objective-C nyelven a Cognitive Services beszédfelismerő SDK használatával iOS rendszeren

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebből a cikkből megismerheti, hogyan hozhat létre iOS-alkalmazások a Cognitive Services beszédfelismerő SDK segítségével a rögzített beszédfelismerés hangfájl lefényképezze Objective-C nyelven.

## <a name="prerequisites"></a>Előfeltételek

* A beszédfelismerési szolgáltatás egy előfizetési kulcsot. Lásd: [próbálja ki ingyenesen a speech service](get-started.md).
* Az Xcode telepítése IOS-es fejlesztési környezetként 9.4.1 Mac. Ebben az oktatóanyagban az IOS-es verziók 11.4 célozza. Ha az xcode-ban nem kell még telepítheti azt a [App Store](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="get-the-speech-sdk-for-ios"></a>IOS-hez a Speech SDK beszerzése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A Cognitive Services beszédfelismerő SDK jelenlegi verziója `1.0.0`.

A Cognitive Services beszédfelismerő SDK for Mac és IOS-es terjesztése zajlik, Cocoa keretrendszert.
Le is tölthetők: https://aka.ms/csspeech/iosbinary. Töltse le a fájlt a kezdőkönyvtárban.


## <a name="create-an-xcode-project"></a>Az Xcode-projekt létrehozása 

Indítsa el az xcode-ban, és a egy új projekt indításához kattintson **fájl** > **új** > **projekt**.
A sablon kiválasztása párbeszédpanelen válassza az "iOS egyetlen alkalmazás megtekintése" sablont.

A párbeszédpanelek útmutatásait a, hogy hajtsa végre, a következő beállításokat:

1. Projekt beállításai párbeszédpanel
    1. Adja meg egy nevet a gyorsindítási alkalmazást például `helloworld`.
    1. Adja meg a szervezet neve például `TestOrg`, és a egy szervezeti azonosítóval, mint például `testorg`.
    1. Ellenőrizze, hogy a projekt a nyelvet választja Objective-C.
    1. Tiltsa le a jelölőnégyzetek tesztek és alapvető adatokat.
    ![Projektbeállítások](media/sdk/qs-objectivec-project-settings.png)
1. Válassza ki a projekt könyvtárában
    1. Válassza ki a kezdőkönyvtár a helyezi a projektet. Ezzel létrehoz egy `helloworld` a kezdőkönyvtárban, az Xcode projekt a fájlokat tartalmazó könyvtárat.
    1. Tiltsa le a példában projekt Git-adattár létrehozása.
    1. Állítsa be a elérési utakat az SDK-t a *Projektbeállítások*.
        1. Az a **általános** lapjára az **csatolt keretrendszerek és könyvtárak** fejléc, adja hozzá az SDK-könyvtár keretet másként: **Add framework** > **hozzáadása más...**  > Nyissa meg a verzió kezdőkönyvtárához navigál, és válassza ki a fájlt `MicrosoftCognitiveServicesSpeech.framework`.
        ![A hozzáadott keretrendszer](media/sdk/qs-objectivec-framework.png)
        1. Nyissa meg a **Build Settings** lapra, és aktiválja **összes** beállításait.
        1. A könyvtár hozzáadása `$(SRCROOT)/..` , a *keretrendszer cesty Pro Hledání* alatt a **cesty Pro Hledání** fejléc.
        ![Keretrendszer keresési útvonalat beállítás](media/sdk/qs-objectivec-framework-search-paths.png)
        1. A könyvtár hozzáadása `$(SRCROOT)/..` , a *Runpath cesty Pro Hledání* alatt a **Linking** fejléc.
        ![Runpath keresési útvonalat beállítás](media/sdk/qs-objectivec-runpaths.png)


## <a name="set-up-the-ui"></a>Állítsa be a felhasználói felületen

A példa alkalmazás tartalmaz egy nagyon egyszerű felhasználói felület: egy gombot, amellyel indítsa el a fájlt, és a egy szöveges feliratot az eredmény megjelenítéséhez feldolgozását.
A felhasználói felület beállítása a `Main.storyboard` a projekt része.
A történet XML-nézet megnyitásához kattintson a jobb gombbal a `Main.storyboard` bejegyzést a projekt fa, és kiválasztja **Megnyitás másként...**   >  **Forráskódját**.
Cserélje le az automatikusan létrehozott nevet XML ezt:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Töltse le a [wav mintafájl](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) kattintson a jobb gombbal a hivatkozásra, majd válassza a **cél mentése másként...** . Adja hozzá a wav fájlt a projekt erőforrásként a Finder ablakból húz a projekt nézetben a legfelső szintű.
Kattintson a **Befejezés** a következő párbeszédpanelen, a beállítások módosítása nélkül.
1. Cserélje le az automatikusan létrehozott nevet tartalmát `ViewController.m` által fájlt:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Cserélje le a karakterláncot `YourSubscriptionKey` az előfizetési kulccsal végzett.

1. Cserélje le a karakterláncot `YourServiceRegion` együtt a [régió](regions.md) az előfizetéséhez tartozó (például `westus` az ingyenes próba-előfizetésre).


## <a name="building-and-running-the-sample"></a>Épület és a minta futtatása

1. A hibakeresési kimeneti láthatóvá (**nézet** > **Debug terület** > **konzol aktiválása**).
1. Hozhat létre és futtassa a példakód az iOS-szimulátorban történő kiválasztásával **termék** -> **futtatása** a menüből, vagy kattintson a **lejátszása** gombra.
1. Miután rákattintott a "Recognize!" gombra az alkalmazás megtekintheti a hanganyag tartalmát a fájl "Mi az az időjárás, például a?" a szimulált a képernyő alsó részén.

 ![Szimulált iOS-alkalmazás](media/sdk/qs-objectivec-simulated-app.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Keresse meg az ehhez a mintához a `quickstart/objectivec-ios` mappát.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A minták letöltése](speech-sdk.md#get-the-samples)
