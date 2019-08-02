---
title: 'Gyors útmutató: Beszédszintetizátor-beszéd, C# (UWP) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben létrehoz egy C# univerzális Windows Platform (UWP-) alkalmazások a Cognitive Services beszédfelismerő SDK használatával. A beszédet valós időben szintetizálhatja a szövegből az eszköz hangszóróján. Az alkalmazást a létrehozása a Speech SDK NuGet-csomagot és a Microsoft Visual Studio 2017-ben.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 5e1f8aea1b00cbba7fec6c7ca416a965458ab526
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607755"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Gyors útmutató: Beszéd készítése UWP alkalmazásban a Speech SDK használatával

A gyors útmutatókat beszédfelismerési [](quickstart-csharp-uwp.md), beszédfelismerési és [](quickstart-translate-speech-uwp.md) [hang-első virtuális asszisztensként](quickstart-virtual-assistant-csharp-uwp.md)is elérhetővé teheti.

Ebben a cikkben egy C# univerzális Windows-platform FEJLESZT (UWP; Windows Version 1709 újabb verzió) alkalmazás a Cognitive Services [SPEECH SDK](speech-sdk.md)használatával. A program valós időben készít beszédet a szövegből az eszköz hangszóróján. Az alkalmazás a [SPEECH SDK NuGet csomagjával](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-es vagy újabb verziójával (bármely kiadással) készült.

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Nyissa meg a `MainPage.xaml.cs` háttérkód-forrásfájlt (a `MainPage.xaml` alatt található meg). Cserélje a teljes kódot a következőre.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. A fájl `Speak_ButtonClicked` kezelőjében cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. A `Speak_ButtonClicked` kezelőben cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekten végrehajtott összes módosítást.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül végbe kell mennie.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-csharp-uwp-08-build.png "Sikeres létrehozás")

1. Indítsa el az alkalmazást. A menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** elemet, vagy nyomja le az **F5** billentyűt.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-csharp-uwp-09-start-debugging.png "Hibakeresés indítása az alkalmazáson")

1. Írjon be egy szöveget a szövegmezőbe, majd kattintson a **Speak (beszéd**) gombra. A rendszer továbbítja a szöveget a Speech Servicesnek, és szintetizálta a beszédet, amely a beszélőn játszik.

    ![A Speech szintézis felhasználói felületének képernyőképe](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Hangbetűkészletek testreszabása](how-to-customize-voice-font.md)
- [Hangminták rögzítése](record-custom-voice-samples.md)
