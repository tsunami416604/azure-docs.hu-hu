---
title: 'Gyors útmutató: beszédszintetizátor-beszédfelismerés C# , (UWP) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben egy C# univerzális Windows-platform-(UWP-) alkalmazást hoz létre a Cognitive Services Speech SDK használatával. A beszédet valós időben szintetizálhatja a szövegből az eszköz hangszóróján. Az alkalmazás a Speech SDK NuGet csomagjával és a Microsoft Visual Studio 2019-mel készült.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 3a88f4fc14286a60feb9d72676827b0cce91eb25
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503128"
---
> [!NOTE]
> A Universal Windows Platformon olyan alkalmazásokat fejleszthet, amelyek a Windows 10-et támogató minden eszközön futtathatók: PC-n, Xboxon, Surface Hubon stb.

## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md?tabs=uwp)

## <a name="add-sample-code"></a>Mintakód hozzáadása

Most adja hozzá az alkalmazás felhasználói felületét meghatározó XAML-kódot, és adja hozzá a C# kód mögötti implementációt.

1. **Megoldáskezelő**nyissa meg a `MainPage.xaml`.

1. A tervező XAML nézetében szúrja be a következő XAML-kódrészletet a **Grid** címkébe (`<Grid>` és `</Grid>`között):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. A **megoldáskezelő**nyissa meg a kód mögötti forrásfájl `MainPage.xaml.cs`. (`MainPage.xaml`alatt van csoportosítva.)

1. Cserélje le az összes kódot a következő kódrészletre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. A forrásfájl `Speak_ButtonClicked` kezelőjében keresse meg a karakterláncot `YourSubscriptionKey`, és cserélje le az előfizetési kulcsra.

1. A `Speak_ButtonClicked` kezelőben keresse meg a karakterláncot `YourServiceRegion`, és cserélje le az előfizetéséhez tartozó [régióra](~/articles/cognitive-services/Speech-Service/regions.md) . (Például az ingyenes próbaverziós előfizetés `westus` használata.)

1. A menüsávban válassza a **fájl** > az **összes mentése** lehetőséget a módosítások mentéséhez.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Most már készen áll az alkalmazás létrehozására és tesztelésére.

1. Az alkalmazás létrehozásához a menüsávon válassza a **build** > **Build megoldás** elemet. A kód fordításának hiba nélkül végbe kell mennie.

1. Az alkalmazás indításához válassza a **hibakeresés** > a **hibakeresés megkezdése** (vagy az **F5**billentyű lenyomása) lehetőséget. Megjelenik a **HelloWorld** ablak.

   ![Példa UWP Speech szintézis alkalmazásra C# – gyors üzembe helyezés](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Írjon be egy szöveget a szövegmezőbe, majd kattintson a **Speak (beszéd**) gombra. A rendszer továbbítja a szöveget a Speech Servicesnek, és szintetizálta a beszédet, amely a beszélőn játszik.

    ![Speech szintézis felhasználói felület](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
