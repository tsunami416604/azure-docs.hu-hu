---
title: 'Gyors útmutató: Beszédfelismerés felismerése C# , (UWP) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben létrehoz egy C# univerzális Windows Platform (UWP-) alkalmazások a Cognitive Services beszédfelismerő SDK használatával. Az eszköz mikrofonjába beszélve valós időben konvertálhat át beszédet szöveggé. Az alkalmazás a Speech SDK NuGet csomagjával és a Microsoft Visual Studio 2019-mel készült.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: fe5ff376a7895e2ca5246c0b9eb575752b07c7a1
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382281"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Gyors útmutató: Beszédfelismerés felismerése UWP alkalmazásban a Speech SDK használatával

A gyors útmutatók a [beszédfelismerés](quickstart-text-to-speech-csharp-uwp.md), a [beszéd fordítás](quickstart-translate-speech-uwp.md)és a [hangvezérelt virtuális asszisztens](quickstart-virtual-assistant-csharp-uwp.md)számára is elérhetők.

Ha szeretné, válasszon másik programozási nyelvet és/vagy környezetet:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben a cikkben egy C# univerzális Windows-platform (UWP) alkalmazást fejleszt ki a Cognitive Services [Speech SDK](speech-sdk.md)használatával. A program valós időben átmásolja a beszédet az eszköz mikrofonjában lévő szövegbe. Az alkalmazás a [SPEECH SDK NuGet csomagjával](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2019-es verziójával (bármely kiadással) készült.

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. A **megoldáskezelő**nyissa meg a kód mögötti forrásfájlt `MainPage.xaml.cs`. (Ez a következő alá `MainPage.xaml`van csoportosítva:.)

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. A forrásfájl `SpeechRecognitionFromMicrophone_ButtonClicked` kezelőjében keresse meg a karakterláncot `YourSubscriptionKey`, és cserélje le az előfizetési kulcsra.

1. A kezelőben keresse meg a karakterláncot `YourServiceRegion`, és cserélje le az előfizetéséhez tartozó [régióra](regions.md) . `SpeechRecognitionFromMicrophone_ButtonClicked` (Például használja `westus` az ingyenes próbaverziós előfizetést.)

1. A menüsávban válassza a **fájl** > **Mentés** lehetőséget a módosítások mentéséhez.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Most már készen áll az alkalmazás létrehozására és tesztelésére.

1. Az alkalmazás létrehozásához a menüsávon válassza a **Build** > **Build megoldás** elemet. A kód fordításának hiba nélkül végbe kell mennie.

1. Az alkalmazás **indításához válassza a hibakeresés** **indítása hibakeresést** (vagy nyomja le az F5 billentyűt). >  Megjelenik a **HelloWorld** ablak.

   ![Példa UWP Speech Recognition C# -alkalmazásra – gyors üzembe helyezés](media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Válassza a **mikrofon engedélyezése**lehetőséget, és ha a hozzáférési engedély kérése megjelenik, válassza az **Igen**lehetőséget.

   ![Mikrofon-hozzáférési engedély kérése](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Válassza a **Beszédfelismerés mikrofon bemenettel** lehetőséget, és mondjon egy angol kifejezést vagy mondatot a mikrofonba. A beszéd a beszédfelismerési szolgáltatásokhoz lett továbbítva, és szövegbe kerül, amely megjelenik az ablakban.

   ![Beszédfelismerés felhasználói felülete](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Rövid útmutató: Beszéd fordítása a Speech SDK for C# (UWP) használatával](quickstart-translate-speech-uwp.md)
- [Custom Speech modell betanítása](how-to-custom-speech-train-model.md)
