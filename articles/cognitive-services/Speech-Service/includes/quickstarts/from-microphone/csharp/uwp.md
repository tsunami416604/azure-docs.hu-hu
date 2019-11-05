---
title: 'Gyors útmutató: beszéd felismerése mikrofonból C# (UWP) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 0d1da9a9ef32aed1975595bb15909b9531ab2400
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503555"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md?tabs=uwp)

Ha már megtette ezt, remek. Folytasd a munkát.

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Első lépésként győződjön meg arról, hogy a projekt meg van nyitva a Visual Studióban.

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez.

1. **Megoldáskezelő**nyissa meg a `MainPage.xaml`.

2. A tervező XAML nézetében szúrja be a következő XAML-kódrészletet a **Grid** címkébe (`<Grid>` és `</Grid>`között):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. A **megoldáskezelő**nyissa meg a kód mögötti forrásfájl `MainPage.xaml.cs`. (`MainPage.xaml`alatt van csoportosítva.)

4. Cserélje le a kódot a következő alapkóddal:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

`SpeechRecognizer` objektum inicializálásához létre kell hoznia egy olyan konfigurációt, amely az előfizetési kulcsot és az előfizetési régiót használja. Szúrja be ezt a kódot a `RecognizeSpeechAsync()` metódusba.

> [!NOTE]
> Ez a példa a `FromSubscription()` metódust használja a `SpeechConfig`létrehozásához. Az elérhető módszerek teljes listáját lásd: [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>SpeechRecognizer inicializálása

Most hozzon létre egy `SpeechRecognizer`. Ez az objektum egy using utasításon belül jön létre a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt a kódot a `RecognizeSpeechAsync()` metódusba, közvetlenül a beszédfelismerési konfiguráció alatt.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Kifejezés felismerése

A `SpeechRecognizer` objektumban meg kell hívnia a `RecognizeOnceAsync()` metódust. Ez a módszer lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy egyetlen kifejezést küld az észleléshez, és ha a kifejezést azonosította a beszédfelismerés felismerésének leállításához.

A using utasításon belül adja hozzá a következő kódot: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Az elismerési eredmények (vagy hibák) megjelenítése

Ha a beszédfelismerési szolgáltatás visszaadja a felismerés eredményét, érdemes megtennie a dolgot. Megtartjuk az egyszerűséget, és kinyomtatjuk az eredményt az állapotjelző panelre.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Most már készen áll az alkalmazás létrehozására és tesztelésére.

1. Az alkalmazás létrehozásához a menüsávon válassza a **build** > **Build megoldás** elemet. A kód fordításának hiba nélkül végbe kell mennie.

1. Az alkalmazás indításához válassza a **hibakeresés** > a **hibakeresés megkezdése** (vagy az **F5**billentyű lenyomása) lehetőséget. Megjelenik a **HelloWorld** ablak.

   ![Példa UWP Speech Recognition C# -alkalmazásra – gyors üzembe helyezés](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Válassza a **mikrofon engedélyezése**lehetőséget, és ha a hozzáférési engedély kérése megjelenik, válassza az **Igen**lehetőséget.

   ![Mikrofon-hozzáférési engedély kérése](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Válassza a **Beszédfelismerés mikrofon bemenettel** lehetőséget, és mondjon egy angol kifejezést vagy mondatot a mikrofonba. A beszéd a beszédfelismerési szolgáltatásokhoz lett továbbítva, és szövegbe kerül, amely megjelenik az ablakban.

   ![Beszédfelismerés felhasználói felülete](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
