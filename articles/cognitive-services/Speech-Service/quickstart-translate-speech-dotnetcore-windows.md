---
title: 'Gyors útmutató: Beszéd átalakítás, lefordítása C# (.NET Core-Windows)'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy egyszerű, rögzítheti a felhasználó speech, azt fordítása más nyelvre és a parancssorhoz szöveget akarjuk .NET Core-alkalmazást fog létrehozni. Ez az útmutató Windows-felhasználók számára tervezték.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 5e4b8bbd84b16f74943d8958c4153fb1546bdb32
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110557"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-core"></a>Gyors útmutató: A .NET Core lefordítja a beszéd, a beszéd SDK-val

Ebben a rövid útmutatóban fog létrehozni egy egyszerű .NET Core-alkalmazást, amely rögzíti a felhasználó beszéd, a számítógép mikrofon, a rendszer lefordítja a beszédfelismerési és transcribes a lefordított szöveg valós időben a parancssorba. Ez az alkalmazás a 64 bites Windows rendszerhez készült, és a beépített a [beszéd SDK NuGet-csomagot](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-ben.

Beszédalapú fordítási elérhető nyelvek teljes listáját lásd: [nyelvi támogatás](language-support.md).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcs. [Igényeljen ingyenesen egy](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `Program.cs` fájlt, és cserélje le a benne lévő teljes kódot a következőre.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül kell végbe mennie.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Sikeres létrehozás")

1. Indítsa el az alkalmazást. A menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** lehetőséget, vagy nyomja le az **F5** billentyűt.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Hibakeresés indítása az alkalmazáson")

1. Megjelenik egy konzolablak, amely arra kéri Önt, hogy mondjon valamit. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. A beszéd a Speech továbbításakor, lefordított, és megjelenített érzéseket szöveg, amely ugyanabban az ablakban jelenik meg.

    ![Képernyőfelvétel a konzol kimenete a sikeres fordítás után](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png "Konzolkimenetet sikeres fordítás után")


## <a name="next-steps"></a>További lépések

További példákat, beszéd olvasni hangfájl, és a lefordított szöveg szintetizált, mint például a Githubon érhetők el.

> [!div class="nextstepaction"]
> [Ismerkedés a C# példák a Githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
