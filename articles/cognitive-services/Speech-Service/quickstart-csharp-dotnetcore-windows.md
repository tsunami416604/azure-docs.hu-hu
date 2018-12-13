---
title: 'Gyors útmutató: Recognize speech, .NET Core (Windows) – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Megismerheti a beszédfelismerés használatát a .NET Core-ban Windowson, C# nyelven a Speech Service SDK segítségével.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: e82e39eb3fc6c7ebaf4798ad10038bfd2fa9a41b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085484"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-by-using-the-speech-sdk"></a>Rövid útmutató: Beszéd felismerése Windowson, C# nyelven, a .NET Core-ban a Speech SDK segítségével

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben a cikkben létre fog hozni a Windowson egy C# konzolalkalmazást a .NET Core-hoz a Cognitive Services [Speech SDK](speech-sdk.md) használatával. A számítógép mikrofonjába beszélve valós időben konvertálhat át beszédet szöveggé. Az alkalmazást a [Speech SDK NuGet-csomaggal](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-tel (annak bármely kiadásával) lehet összeállítani.

> [!NOTE]
> A .NET Core egy nyílt forráskódú, platformfüggetlen .NET-platform, amely implementálja a [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) specifikációt.

A rövid útmutató elvégzéséhez szüksége van a Speech Service előfizetői azonosítójára, amelyet ingyenesen is beszerezhet. További részletekért tekintse át a [Speech Service ingyenes kipróbálását](get-started.md) ismertető részt.


## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Indítsa el a Visual Studio 2017-et.

1. Győződjön róla meg, hogy elérhető a **Platformfüggetlen .NET-fejlesztés** számítási feladat. A Visual Studio telepítőjének megnyitásához válassza az **Eszközök** > **Eszközök és funkciók beszerzése** elemet a Visual Studio menüsorából. Ha ez a számítási feladat már engedélyezve van, zárja be a párbeszédpanelt.

    ![A Visual Studio telepítőjének képernyőképe, amelyen ki van emelve a Számítási feladatok lap](media/sdk/vs-enable-net-core-workload.png)

    Ha nincs engedélyezve, jelölje ki a **Platformfüggetlen .NET Core-fejlesztés** lehetőség jelölőnégyzetét, majd válassza a **Módosítás** elemet a párbeszédpanel jobb alsó sarkában. Az új funkció telepítése hosszabb ideig tart.

1. Hozzon létre egy új Visual C# .NET Core-konzolalkalmazást. Az **Új projekt** párbeszédpanel bal oldali ablaktáblájában bontsa ki a **Telepítve** > **Visual C#** > **.Net Core** elemet. Válassza a **Console App (.NET Core)** (Konzolalkalmazás (.NET Core)) lehetőséget. A projekt neve legyen *helloworld*.

    ![Képernyőkép az Új projekt párbeszédpanelről](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Visual C# konzolalkalmazás (.NET Core) létrehozása")

1. Telepítse a [Speech SDK NuGet-csomagot](https://aka.ms/csspeech/nuget), és hivatkozzon rá. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, majd válassza a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése a megoldáshoz) lehetőséget.

    ![A Megoldáskezelő képernyőképe, melyen kiemelve szerepel a Manage NuGet Packages for Solution lehetőség](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "NuGet-csomagok kezelése a megoldáshoz")

1. A jobb felső sarokban lévő **Package Source** (Csomag forrása) mezőben jelölje ki a **nuget.org** fájlt. Keressen rá a `Microsoft.CognitiveServices.Speech` csomagra, és telepítse a **helloworld** nevű projektbe.

    ![A megoldás csomagjainak kezelésére szolgáló párbeszédpanel képernyőképe](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "NuGet-csomag telepítése")

1. A NuGet-csomag telepítésének indításához fogadja el a képernyőn megjelenő licencet.

    ![A licencelfogadási párbeszédpanel képernyőképe](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "A licenc elfogadása")

A csomag telepítése után a Package Manager konzolban egy megerősítési üzenet jelenik meg.


## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `Program.cs` fájlt, és cserélje le a benne lévő teljes kódot a következőre.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül kell végbe mennie.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Sikeres létrehozás")

1. Indítsa el az alkalmazást. A menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** lehetőséget, vagy nyomja le az **F5** billentyűt.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Hibakeresés indítása az alkalmazáson")

1. Megjelenik egy konzolablak, amely arra kéri Önt, hogy mondjon valamit. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. A rendszer továbbítja a beszédet a Speech Service-be, majd szöveggé alakítja át, amely ugyanabban az ablakban meg is jelenik.

    ![Képernyőkép a konzolról a sikeres felismerést követően](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "A konzol a sikeres felismerést követően")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
A jelen útmutatóban használt mintát a `quickstart/csharp-dotnetcore-windows` mappában találja.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szándék felismerése beszédből a C#-hez készült Speech SDK használatával](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Lásd még

- [Beszéd fordítása](how-to-translate-speech-csharp.md)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
