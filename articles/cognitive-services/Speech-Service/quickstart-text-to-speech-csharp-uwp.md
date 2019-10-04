---
title: 'Gyors útmutató: Beszédszintetizátor-beszéd, C# (UWP) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben létrehoz egy C# univerzális Windows Platform (UWP-) alkalmazások a Cognitive Services beszédfelismerő SDK használatával. A beszédet valós időben szintetizálhatja a szövegből az eszköz hangszóróján. Az alkalmazás a Speech SDK NuGet csomagjával és a Microsoft Visual Studio 2019-mel készült.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 65b65c9af377b6a9951f9f328e0732850d3b9c1d
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382213"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Gyors útmutató: Beszéd készítése UWP alkalmazásban a Speech SDK használatával

A gyors útmutatók a [beszédfelismerés](quickstart-csharp-uwp.md), a [beszédfelismerés](quickstart-translate-speech-uwp.md)és a [hang-első virtuális asszisztens](quickstart-virtual-assistant-csharp-uwp.md)számára is elérhetők.

Ebben a cikkben egy C# univerzális Windows-platform (UWP) alkalmazást fejleszt ki a Cognitive Services [Speech SDK](speech-sdk.md)használatával. A program valós időben szintetizálja a beszédet a szövegből az eszköz hangszórójának. Az alkalmazást a [SPEECH SDK NuGet csomag](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2019 (bármely kiadás) használatával építheti ki.

> [!NOTE]
> A Universal Windows Platformon olyan alkalmazásokat fejleszthet, amelyek a Windows 10-et támogató minden eszközön futtathatók: PC-n, Xboxon, Surface Hubon stb.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be az egyiket ingyenesen](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

Most adja hozzá az alkalmazás felhasználói felületét meghatározó XAML-kódot, és adja hozzá a C# kód mögötti implementációt.

1. A **megoldáskezelő**megnyitásához `MainPage.xaml`nyissa meg a t.

1. A tervező XAML nézetében szúrja be a következő XAML-kódrészletet a **Grid** címkébe `<Grid>` ( `</Grid>`és között):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. A **megoldáskezelő**nyissa meg a kód mögötti forrásfájlt `MainPage.xaml.cs`. (Ez a következő alá `MainPage.xaml`van csoportosítva:.)

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. A forrásfájl `Speak_ButtonClicked` kezelőjében keresse meg a karakterláncot `YourSubscriptionKey`, és cserélje le az előfizetési kulcsra.

1. A kezelőben keresse meg a karakterláncot `YourServiceRegion`, és cserélje le az előfizetéséhez tartozó [régióra](regions.md) . `Speak_ButtonClicked` (Például használja `westus` az ingyenes próbaverziós előfizetést.)

1. A menüsávban válassza a **fájl** > **Mentés** lehetőséget a módosítások mentéséhez.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Most már készen áll az alkalmazás létrehozására és tesztelésére.

1. Az alkalmazás létrehozásához a menüsávon válassza a **Build** > **Build megoldás** elemet. A kód fordításának hiba nélkül végbe kell mennie.

1. Az alkalmazás **indításához válassza a hibakeresés** **indítása hibakeresést** (vagy nyomja le az F5 billentyűt). >  Megjelenik a **HelloWorld** ablak.

   ![Példa UWP Speech szintézis alkalmazásra C# – gyors üzembe helyezés](media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Írjon be egy szöveget a szövegmezőbe, majd kattintson a **Speak (beszéd**) gombra. A rendszer továbbítja a szöveget a Speech Servicesnek, és szintetizálta a beszédet, amely a beszélőn játszik.

    ![Speech szintézis felhasználói felület](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Egyéni hangmodellek létrehozása és használata](how-to-custom-voice-create-voice.md)
- [Hangminták rögzítése](record-custom-voice-samples.md)
