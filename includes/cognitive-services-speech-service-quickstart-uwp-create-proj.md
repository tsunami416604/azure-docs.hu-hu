---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 79fbe2db1ec9758d1e15ba7d89363429415918c7
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729475"
---
1. Indítsa el a Visual Studio 2017-et.

1. Győződjön meg arról, hogy elérhető a **Universal Windows Platform-fejlesztés** számítási feladat. A Visual Studio telepítőjének megnyitásához válassza az **Eszközök** > **Eszközök és funkciók beszerzése** elemet a Visual Studio menüsorából. Ha ez a számítási feladat már engedélyezve van, zárja be a párbeszédpanelt.

    ![A Visual Studio telepítőjének képernyőképe, amelyen ki van emelve a Számítási feladatok lap](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

    Ha nincs engedélyezve, jelölje be a **Platformfüggetlen .NET-fejlesztés** lehetőség jelölőnégyzetét, majd válassza a **Módosítás** elemet a párbeszédpanel jobb alsó sarkában. Az új funkció telepítése eltarthat egy kis ideig.

1. Hozzon létre egy üres Visual C# Universal Windows-alkalmazást. Először válassza a **Fájl** > **Új** > **Projekt** lehetőséget a menüben. Az **Új projekt** párbeszédpanelen bontsa ki a **Telepítve** > **Visual C#** > **Windows Universal** elemet a bal oldali panelen. Ezután válassza az **Üres alkalmazás (Universal Windows)** elemet. A projekt neve legyen *helloworld*.

    ![Képernyőkép az Új projekt párbeszédpanelről](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. A Speech SDK használata érdekében az alkalmazást a Windows 10 Fall Creators Update vagy újabb verziójához kell összeállítani. A felugró **Új Universal Windows Platform-projekt** ablak **Minimális verzió** beállításánál válassza a **Windows 10 Fall Creators Update (10.0; Build 16299)** értéket. A **Célverzió** mezőben válassza ki ezt vagy egy későbbi verziót, és kattintson az **OK** gombra.

    ![Az Új Universal Windows Platform-projekt ablak képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Ha 64 bites Windowst használ, a Visual Studio eszköztárában található legördülő menüt használva átválthatja a build platformját `x64` értékre. (A 64 bites Windows 32 bites alkalmazásokat is képes futtatni, így megtarthatja az `x86` beállítást, ha szeretné.)

   ![A Visual Studio eszköztárának képernyőképe, amelyen kiemelve szerepel az x64-es beállítás](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > A beszédfelismerés SDK csak Intel-kompatibilis processzor támogatja. Az ARM jelenleg nem támogatott.

1. Telepítse a [Speech SDK NuGet-csomagot](https://aka.ms/csspeech/nuget), és hivatkozzon rá. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, majd válassza a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése a megoldáshoz) lehetőséget.

    ![A Megoldáskezelő képernyőképe, amelyen kiemelve szerepel a Manage NuGet Packages for Solution lehetőség](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. A jobb felső sarokban lévő **Package Source** (Csomag forrása) mezőben jelölje ki a **nuget.org** fájlt. Keressen rá a `Microsoft.CognitiveServices.Speech` csomagra, és telepítse a **helloworld** nevű projektbe.

    ![A megoldás csomagjainak kezelésére szolgáló párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "NuGet-csomag telepítése")

1. A NuGet-csomag telepítésének indításához fogadja el a képernyőn megjelenő licencet.

    ![A licencelfogadási párbeszédpanel képernyőképe](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-06-nuget-license.png "A licenc elfogadása")

1. A következő kimeneti sor a Csomagkezelő konzolban jelenik meg.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.2.0' to helloworld
   ```

1. Mivel az alkalmazás mikrofont használ a beszédbevitelhez, hozzá kell adnia a **Mikrofon** képességet a projekthez. A Megoldáskezelőben kattintson duplán a **Package.appxmanifest** elemre az alkalmazásjegyzék szerkesztéséhez. Ezután váltson a **Képességek** lapra, jelölje be a **Mikrofon** képesség jelölőnégyzetét, és mentse a módosításokat.

   ![A Visual Studio alkalmazásjegyzékének képernyőképe a kiemelt Képességek és Mikrofon lehetőségekkel](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)
