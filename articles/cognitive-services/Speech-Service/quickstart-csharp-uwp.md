---
title: 'Gyors útmutató: Beszédfelismerés felismerése C# , (UWP) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben létrehoz egy C# univerzális Windows Platform (UWP-) alkalmazások a Cognitive Services beszédfelismerő SDK használatával. Az eszköz mikrofonjába beszélve valós időben konvertálhat át beszédet szöveggé. Az alkalmazást a létrehozása a Speech SDK NuGet-csomagot és a Microsoft Visual Studio 2017-ben.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 5ea75f1956b70b6bfebebcf29e27542eba0ca0cf
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839952"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Gyors útmutató: Beszédfelismerés felismerése UWP alkalmazásban a Speech SDK használatával

A gyors útmutatók [szöveg-beszéd](quickstart-text-to-speech-csharp-uwp.md), beszédfelismerési és hangvezérelt [](quickstart-translate-speech-uwp.md) [virtuális asszisztensek](quickstart-virtual-assistant-csharp-uwp.md)esetén is elérhetők.

Ha szükséges, válasszon másik programozási nyelvet és/vagy környezetet:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben a cikkben egy C# univerzális Windows-platform FEJLESZT (UWP; Windows Version 1709 újabb verzió) alkalmazás a Cognitive Services [SPEECH SDK](speech-sdk.md)használatával. A program valós időben átmásolja a beszédet az eszköz mikrofonjában lévő szövegbe. Az alkalmazás a [SPEECH SDK NuGet csomagjával](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-es vagy újabb verziójával (bármely kiadással) készült.

> [!NOTE]
> A Universal Windows Platformon olyan alkalmazásokat fejleszthet, amelyek a Windows 10-et támogató minden eszközön futtathatók: PC-n, Xboxon, Surface Hubon stb.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) vagy újabb
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be az egyiket ingyenesen](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

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

1. Válassza a **Beszédfelismerés mikrofon bemenettel** lehetőséget, és mondjon egy angol kifejezést vagy mondatot a mikrofonba. A beszéd a beszédfelismerési szolgáltatásokhoz lett továbbítva, és szövegbe kerül, amely megjelenik az ablakban.

    ![A beszédfelismerés felhasználói felületének képernyőképe](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Beszéd fordítása](how-to-translate-speech-csharp.md)
- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
