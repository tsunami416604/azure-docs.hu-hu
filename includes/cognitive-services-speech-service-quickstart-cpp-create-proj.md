---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: b5a24d83faef5a895317f162178f8bd588a1466d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620070"
---
1. Indítsa el a Visual Studio 2017-et.

1. Győződjön róla meg, hogy elérhető az **Asztali fejlesztés a C++ segítségével** számítási feladat. A Visual Studio telepítőjének megnyitásához válassza az **Eszközök** > **Eszközök és funkciók beszerzése** elemet a Visual Studio menüsorából. Ha már engedélyezve van ez a számítási feladat, ugorjon a következő lépésre.

    ![A Visual Studio Számítási feladatok lapjának képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

    Ha még nincs, jelölje be az **Asztali fejlesztés a C++ segítségével** lehetőség mellett található jelölőnégyzetet.

1. Ellenőrizze, hogy a **NuGet-csomagkezelő** összetevő elérhető-e. Váltson a Visual Studio telepítési párbeszédpaneljének **Egyéni összetevők** lapjára, és válassza ki a **NuGet-csomagkezelő** lehetőséget, ha még nincs engedélyezve.

      ![A Visual Studio Egyéni összetevők lapjának képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-nuget-package-manager.png)

1. Ha engedélyeznie kell a C++ tevékenységprofilt vagy a NuGetet, válassza a **Módosítás** lehetőséget (a párbeszédpanel jobb alsó sarkában). Az új funkciók telepítése eltarthat egy kis ideig. Ha már mindkét funkció engedélyezve van, csak zárja be a párbeszédpanelt.

1. Hozzon létre egy új Visual C++ asztali Windows-konzolalkalmazást. Először válassza a **Fájl** > **Új** > **Projekt** lehetőséget a menüben. Az **Új projekt** párbeszédpanelen bontsa ki a **Telepítve** > **Visual C++** > **Windows asztali verzió** elemet a bal oldali panelen. Válassza a **Windows-konzolalkalmazás** lehetőséget. A projekt neve legyen *helloworld*.

    ![Képernyőkép az Új projekt párbeszédpanelről](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Ha 64 bites Windowst használ, a Visual Studio eszköztárában található legördülő menüt használva átválthatja a build platformját `x64` értékre. (A 64 bites Windows rendszer 32 bites alkalmazásokat is képes futtatni, ezért ez nem tartozik az előfeltételek közé.)

    ![A Visual Studio eszköztárának képernyőképe, amelyen kiemelve szerepel az x64-es beállítás](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, majd válassza a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése a megoldáshoz) parancsot.

    ![A Megoldáskezelő képernyőképe, amelyen kiemelve szerepel a Manage NuGet Packages for Solution lehetőség](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. A jobb felső sarokban lévő **Package Source** (Csomag forrása) mezőben jelölje ki a **nuget.org** fájlt. Keressen rá a `Microsoft.CognitiveServices.Speech` csomagra, és telepítse a **helloworld** nevű projektbe.

    ![A megoldás csomagjainak kezelésére szolgáló párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > A Cognitive Services Speech SDK jelenlegi verziója az `1.4.0`.

1. A NuGet-csomag telepítésének indításához fogadja el a képernyőn megjelenő licencet.

    ![A licencelfogadási párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-05-nuget-license.png)

A csomag telepítése után a Package Manager konzolban egy megerősítési üzenet jelenik meg.
