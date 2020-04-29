---
title: 'Gyors útmutató: beszédfelismerési beszéd, C# (UWP) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben létre fog hozni egy C# Universal Windows Platform- (UWP-) alkalmazást a Cognitive Services Speech SDK használatával. A beszédet valós időben szintetizálhatja a szövegből az eszköz hangszóróján. Az alkalmazás a Speech SDK NuGet csomagjával és a Microsoft Visual Studio 2019-mel készült.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 89fc5fddffb291942b8f3a4db3dfdf4ccd6cf46a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275413"
---
> [!NOTE]
> A Universal Windows Platformon olyan alkalmazásokat fejleszthet, amelyek a Windows 10-et támogató minden eszközön futtathatók: PC-n, Xboxon, Surface Hubon stb.

## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Mintakód hozzáadása

Most adja hozzá az alkalmazás felhasználói felületét meghatározó XAML-kódot, és adja hozzá a C#-kódot a megvalósítás mögött.

1. A **megoldáskezelő**megnyitásához `MainPage.xaml`nyissa meg a t.

1. A tervező XAML nézetében szúrja be a következő XAML-kódrészletet a **Grid** címkébe `<Grid>` ( `</Grid>`és között):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. A **megoldáskezelő**nyissa meg a kód mögötti forrásfájlt `MainPage.xaml.cs`. (Ez a következő alá `MainPage.xaml`van csoportosítva:.)

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. A forrásfájl `Speak_ButtonClicked` kezelőjében keresse meg a karakterláncot `YourSubscriptionKey`, és cserélje le az előfizetési kulcsra.

1. A `Speak_ButtonClicked` kezelőben keresse meg a karakterláncot `YourServiceRegion`, és cserélje le az előfizetéséhez tartozó [régióra](~/articles/cognitive-services/Speech-Service/regions.md) . (Például használja `westus` az ingyenes próbaverziós előfizetést.)

1. A menüsávban válassza a **fájl** > **Mentés** lehetőséget a módosítások mentéséhez.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Most már készen áll az alkalmazás létrehozására és tesztelésére.

1. Az alkalmazás létrehozásához a menüsávon válassza a **Build** > **Build megoldás** elemet. A kód fordításának hiba nélkül végbe kell mennie.

1. Az alkalmazás **indításához válassza a hibakeresés****indítása hibakeresést** (vagy nyomja le az F5 billentyűt). **F5** >  Megjelenik a **HelloWorld** ablak.

   ![Minta UWP Speech szintézis alkalmazása a C#-ben – gyors útmutató](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Írjon be egy szöveget a szövegmezőbe, majd kattintson a **Speak (beszéd**) gombra. A rendszer továbbítja a szöveget a Speech Service-nek, és szintetizálta a beszédet, amely a beszélőn játszik.

    ![Speech szintézis felhasználói felület](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
