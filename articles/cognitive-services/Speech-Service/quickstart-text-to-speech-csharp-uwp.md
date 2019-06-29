---
title: 'Gyors útmutató: Beszéd átalakítás, szintetizálásához C# (UWP) – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben létrehoz egy C# univerzális Windows Platform (UWP-) alkalmazások a Cognitive Services beszédfelismerő SDK használatával. Az eszköz speaker szöveg valós idejű hang, szintetizálásához. Az alkalmazást a létrehozása a Speech SDK NuGet-csomagot és a Microsoft Visual Studio 2017-ben.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: c11fbff883d2699bdd0a107fc462524d92ee410d
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467445"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Gyors útmutató: A beszédfelismerés SDK-val egy UWP-alkalmazásban speech szintetizálásához

Rövid útmutatók érhetők el is [beszédfelismerés](quickstart-csharp-uwp.md), [tolmácsolás –](quickstart-translate-speech-uwp.md) és [hang-és felhőközpontú virtuális asszisztensek](quickstart-virtual-assistant-csharp-uwp.md).

Ebben a cikkben fejleszt egy C# univerzális Windows Platform (UWP; 1709-es Windows később) alkalmazást a Cognitive Services használatával [beszéd SDK](speech-sdk.md). A program az eszköz speaker szöveg valós idejű hang fog szintetizálásához. Az alkalmazást a [Speech SDK NuGet-csomaggal](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-tel (annak bármely kiadásával) lehet összeállítani.

> [!NOTE]
> A Universal Windows Platformon olyan alkalmazásokat fejleszthet, amelyek a Windows 10-et támogató minden eszközön futtathatók: PC-n, Xboxon, Surface Hubon stb.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcs. [Igényeljen ingyenesen egy](get-started.md).

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

1. Adjon meg szöveget a szövegmezőbe, majd kattintson **Speak**. A szöveg a beszédszolgáltatások továbbítani, és a Speech, amely a a hangfelismerő játszik synthesized.

    ![Beszéd összefoglaló felhasználói felület képernyőképe](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a C# példák a Githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Hangtípust testreszabása](how-to-customize-voice-font.md)
- [Rekord voice-minták](record-custom-voice-samples.md)
