---
title: 'Gyors útmutató: Ismeri fel a beszéd, a C++ (Windows) – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Megismerheti a beszédfelismerés használatát asztali Windows rendszeren, C++ nyelven a Speech Service SDK segítségével.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: wolfma
ms.openlocfilehash: 1a26cbea3aaca700f5e501d3b6be0f7d1c942f36
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53607540"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Gyors útmutató: A beszédfelismerés SDK-val a Windows a C++ beszédfelismerést

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben az útmutatóban egy C++ konzolalkalmazást fog létrehozni a Windowshoz. A Cognitive Services [Speech SDK](speech-sdk.md) használatával a számítógép mikrofonjába beszélve valós időben konvertálhat át beszédet szöveggé. Az alkalmazást a [Speech SDK NuGet-csomaggal](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-tel (annak bármely kiadásával) lehet összeállítani.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége van a Speech Service előfizetői azonosítójára, amelyet ingyenesen is beszerezhet. További részletekért tekintse át a [Speech Service ingyenes kipróbálását](get-started.md) ismertető részt.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Indítsa el a Visual Studio 2017-et.

1. Győződjön róla meg, hogy elérhető az **Asztali fejlesztés a C++ segítségével** számítási feladat. A Visual Studio telepítőjének megnyitásához válassza az **Eszközök** > **Eszközök és funkciók beszerzése** elemet a Visual Studio menüsorából. Ha már engedélyezve van ez a számítási feladat, ugorjon a következő lépésre.

    ![A Visual Studio Számítási feladatok lapjának képernyőképe](media/sdk/vs-enable-cpp-workload.png)

    Ha még nincs, jelölje be az **Asztali fejlesztés a C++ segítségével** lehetőség mellett található jelölőnégyzetet.

1. Ellenőrizze, hogy a **NuGet-csomagkezelő** összetevő elérhető-e. Váltson a **az egyes összetevők** a Visual Studio telepítőjének párbeszédpanel lapján. Válassza ki **NuGet-Csomagkezelő** Ha ez nem engedélyezett.

      ![A Visual Studio Egyéni összetevők lapjának képernyőképe](media/sdk/vs-enable-nuget-package-manager.png)

1. Ha engedélyeznie kell a C++ tevékenységprofilt vagy a NuGetet, válassza a **Módosítás** lehetőséget (a párbeszédpanel jobb alsó sarkában). Az új funkciók telepítése eltarthat egy kis ideig. Ha már mindkét funkció engedélyezve van, csak zárja be a párbeszédpanelt.

1. Hozzon létre egy új Visual C++ asztali Windows-konzolalkalmazást. Először válassza a **Fájl** > **Új** > **Projekt** lehetőséget a menüben. Az **Új projekt** párbeszédpanelen bontsa ki a **Telepítve** > **Visual C++** > **Windows asztali verzió** elemet a bal oldali panelen. Válassza a **Windows-konzolalkalmazás** lehetőséget. A projekt neve legyen *helloworld*.

    ![Képernyőkép az Új projekt párbeszédpanelről](media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Ha 64 bites Windowst használ, a Visual Studio eszköztárában található legördülő menüt használva átválthatja a build platformját `x64` értékre. (64 bites Windows 32 bites alkalmazásokat futtathatnak közvetlenül.)

    ![A Visual Studio eszköztárának képernyőképe, amelyen kiemelve szerepel az x64-es beállítás](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, majd válassza a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése a megoldáshoz) parancsot.

    ![A Megoldáskezelő képernyőképe, amelyen kiemelve szerepel a Manage NuGet Packages for Solution lehetőség](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. A jobb felső sarokban lévő **Package Source** (Csomag forrása) mezőben jelölje ki a **nuget.org** fájlt. Keressen rá a `Microsoft.CognitiveServices.Speech` csomagra, és telepítse a **helloworld** nevű projektbe.

    ![A megoldás csomagjainak kezelésére szolgáló párbeszédpanel képernyőképe](media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > A Cognitive Services Speech SDK jelenlegi verziója az `1.2.0`.

1. A NuGet-csomag telepítésének indításához fogadja el a képernyőn megjelenő licencet.

    ![A licencelfogadási párbeszédpanel képernyőképe](media/sdk/qs-cpp-windows-05-nuget-license.png)

A csomag telepítése után a Package Manager konzolban egy megerősítési üzenet jelenik meg.

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a *helloworld.cpp* forrásfájlt. Cserélje le a kezdeti „include” állítás (`#include "stdafx.h"` vagy `#include "pch.h"`) alatt található teljes kódot a következőre:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül kell végbe mennie.

   ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-cpp-windows-06-build.png)

1. Indítsa el az alkalmazást. A menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** lehetőséget, vagy nyomja le az **F5** billentyűt.

   ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Megjelenik egy konzolablak, amely arra kéri Önt, hogy mondjon valamit. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. A rendszer továbbítja a beszédet a Speech Service-be, majd szöveggé alakítja át, amely ugyanabban az ablakban meg is jelenik.

   ![Képernyőkép a konzolról a sikeres felismerést követően](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
A jelen útmutatóban használt mintát a `quickstart/cpp-windows` mappában találja.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szándék felismerése beszédből a C++-hoz készült Speech SDK használatával](how-to-recognize-intents-from-speech-cpp.md)

## <a name="see-also"></a>Lásd még

- [Beszéd fordítása](how-to-translate-speech-csharp.md)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
