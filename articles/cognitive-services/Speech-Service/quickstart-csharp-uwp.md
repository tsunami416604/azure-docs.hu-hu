---
title: 'Rövid útmutató: Beszéd felismerése C# nyelven egy UWP-alkalmazásban a Cognitive Services Speech SDK segítségével'
titleSuffix: Microsoft Cognitive Services
description: A beszédfelismerés elsajátítása egy UWP-alkalmazásban a Cognitive Services Speech SDK segítségével
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 2eb6bda7066a01e5532fe7c0e20b0ee13f4289b6
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432987"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Rövid útmutató: Beszéd felismerése egy UWP-alkalmazásban a Speech SDK segítségével

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben a cikkben létre fog hozni egy C# Universal Windows Platform- (UWP-) alkalmazást a Cognitive Services [Speech SDK](speech-sdk.md) használatával. Az eszköz mikrofonjába beszélve valós időben konvertálhat át beszédet szöveggé. Az alkalmazást a [Speech SDK NuGet-csomaggal](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-tel (annak bármely kiadásával) lehet összeállítani.

> [!NOTE]
> A Universal Windows Platformon olyan alkalmazásokat fejleszthet, amelyek a Windows 10-et támogató minden eszközön futtathatók: PC-n, Xboxon, Surface Hubon stb.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége van a Speech Service előfizetői azonosítójára, amelyet ingyenesen is beszerezhet. További részletekért tekintse át a [Speech Service ingyenes kipróbálását](get-started.md) ismertető részt.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Indítsa el a Visual Studio 2017-et.

1. Győződjön meg arról, hogy elérhető a **Universal Windows Platform-fejlesztés** számítási feladat. A Visual Studio telepítőjének megnyitásához válassza az **Eszközök** > **Eszközök és funkciók beszerzése** elemet a Visual Studio menüsorából. Ha ez a számítási feladat már engedélyezve van, zárja be a párbeszédpanelt. 

    ![A Visual Studio telepítőjének képernyőképe, amelyen ki van emelve a Számítási feladatok lap](media/sdk/vs-enable-uwp-workload.png)

    Ha nincs engedélyezve, jelölje be a **Platformfüggetlen .NET-fejlesztés** lehetőség jelölőnégyzetét, majd válassza a **Módosítás** elemet a párbeszédpanel jobb alsó sarkában. Az új funkció telepítése eltarthat egy kis ideig.

1. Hozzon létre egy üres Visual C# Universal Windows-alkalmazást. Először válassza a **Fájl** > **Új** > **Projekt** lehetőséget a menüben. Az **Új projekt** párbeszédpanelen bontsa ki a **Telepítve** > **Visual C#** > **Windows Universal** elemet a bal oldali panelen. Ezután válassza az **Üres alkalmazás (Universal Windows)** elemet. A projekt neve legyen *helloworld*.

    ![Képernyőkép az Új projekt párbeszédpanelről](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. A Speech SDK használata érdekében az alkalmazást a Windows 10 Fall Creators Update vagy újabb verziójához kell összeállítani. A felugró **Új Universal Windows Platform-projekt** ablak **Minimális verzió** beállításánál válassza a **Windows 10 Fall Creators Update (10.0; Build 16299)** értéket. A **Célverzió** mezőben válassza ki ezt vagy egy későbbi verziót, és kattintson az **OK** gombra.

    ![Az Új Universal Windows Platform-projekt ablak képernyőképe](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Ha 64 bites Windowst használ, a Visual Studio eszköztárában található legördülő menüt használva átválthatja a build platformját `x64` értékre. (A 64 bites Windows 32 bites alkalmazásokat is képes futtatni, így megtarthatja az `x86` beállítást, ha szeretné.)

   ![A Visual Studio eszköztárának képernyőképe, amelyen kiemelve szerepel az x64-es beállítás](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > A Speech SDK csak az Intel-kompatibilis processzorokat támogatja. Az ARM jelenleg nem támogatott.

1. Telepítse a [Speech SDK NuGet-csomagot](https://aka.ms/csspeech/nuget), és hivatkozzon rá. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, majd válassza a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése a megoldáshoz) lehetőséget.

    ![A Megoldáskezelő képernyőképe, amelyen kiemelve szerepel a Manage NuGet Packages for Solution lehetőség](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. A jobb felső sarokban lévő **Package Source** (Csomag forrása) mezőben jelölje ki a **nuget.org** fájlt. Keressen rá a `Microsoft.CognitiveServices.Speech` csomagra, és telepítse a **helloworld** nevű projektbe.

    ![A megoldás csomagjainak kezelésére szolgáló párbeszédpanel képernyőképe](media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "NuGet-csomag telepítése")

1. A NuGet-csomag telepítésének indításához fogadja el a képernyőn megjelenő licencet.

    ![A licencelfogadási párbeszédpanel képernyőképe](media/sdk/qs-csharp-uwp-06-nuget-license.png "A licenc elfogadása")

1. A következő kimeneti sor a Csomagkezelő konzolban jelenik meg.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.0.0' to helloworld
   ```

1. Mivel az alkalmazás mikrofont használ a beszédbevitelhez, hozzá kell adnia a **Mikrofon** képességet a projekthez. A Megoldáskezelőben kattintson duplán a **Package.appxmanifest** elemre az alkalmazásjegyzék szerkesztéséhez. Ezután váltson a **Képességek** lapra, jelölje be a **Mikrofon** képesség jelölőnégyzetét, és mentse a módosításokat.

   ![A Visual Studio alkalmazásjegyzékének képernyőképe a kiemelt Képességek és Mikrofon lehetőségekkel](media/sdk/qs-csharp-uwp-07-capabilities.png)


## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Az alkalmazás felhasználói felületét az XAML-lel lehet definiálni. Nyissa meg a `MainPage.xaml` fájlt a Megoldáskezelőben. A tervezői XAML-nézetben illessze be a következő XAML-kódrészletet a Grid címkébe (a `<Grid>` és a `</Grid>` közé).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Nyissa meg a `MainPage.xaml.cs` háttérkód-forrásfájlt (a `MainPage.xaml` alatt található meg). Cserélje a teljes kódot a következőre.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. A fájl `SpeechRecognitionFromMicrophone_ButtonClicked` kezelőjében cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. A `SpeechRecognitionFromMicrophone_ButtonClicked` kezelőben cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekten végrehajtott összes módosítást.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül végbe kell mennie.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-csharp-uwp-08-build.png "Sikeres létrehozás")

1. Indítsa el az alkalmazást. A menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** elemet, vagy nyomja le az **F5** billentyűt.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-csharp-uwp-09-start-debugging.png "Hibakeresés indítása az alkalmazáson")

1. Felugrik egy ablak. Jelölje be a **Mikrofon engedélyezése** lehetőséget, majd hagyja jóvá a felugró engedélykérést.

    ![Az engedélykérés képernyőképe](media/sdk/qs-csharp-uwp-10-access-prompt.png "Hibakeresés indítása az alkalmazáson")

1. Válassza a **Beszédfelismerés mikrofon bemenettel** lehetőséget, és mondjon egy angol kifejezést vagy mondatot a mikrofonba. A rendszer továbbítja a beszédet a Speech Service-be, majd szöveggé alakítja át, amely ugyanabban az ablakban meg is jelenik.

    ![A beszédfelismerés felhasználói felületének képernyőképe](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
A jelen útmutatóban használt mintát a `quickstart/csharp-uwp` mappában találja.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szándék felismerése beszédből a C#-hez készült Speech SDK használatával](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Lásd még

- [Beszéd fordítása](how-to-translate-speech-csharp.md)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
