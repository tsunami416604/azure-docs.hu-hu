---
title: 'Gyors útmutató: beszédek, szándékok és entitások felismerése, C# beszédfelismerési szolgáltatás'
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
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 6cb970a59118a93c7a9023cc7c0448019dc77362
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125505"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
>
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [LUIS-alkalmazás létrehozása és végponti kulcs beszerzése](../../../../quickstarts/create-luis.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Első lépésként győződjön meg arról, hogy a projekt meg van nyitva a Visual Studióban.

1. Indítsa el a Visual Studio 2019-es kiadását.
2. Töltse be a projektet, és nyissa meg `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez. Jegyezze fel, hogy létrehozott egy `RecognizeIntentAsync()`nevű aszinkron metódust.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

`IntentRecognizer` objektum inicializálásához létre kell hoznia egy olyan konfigurációt, amely a LUIS Endpoint kulcsát és régióját használja. Szúrja be ezt a kódot a `RecognizeIntentAsync()` metódusba.

Ez a példa a `FromSubscription()` metódust használja a `SpeechConfig`létrehozásához. Az elérhető módszerek teljes listáját lásd: [SpeechConfig osztály](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

> [!NOTE]
> Fontos, hogy a LUIS Endpoint kulcsot használja, nem pedig a kezdő vagy a szerzői kulcsot, mert csak a végpont kulcsa érvényes a beszédfelismerési szándék felismerésére. A megfelelő kulcs beszerzésével kapcsolatos útmutatásért lásd: [Luis-alkalmazás létrehozása és végponti kulcs beszerzése](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) .

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer inicializálása

Most hozzon létre egy `IntentRecognizer`. Ez az objektum egy using utasításon belül jön létre a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt a kódot a `RecognizeIntentAsync()` metódusba, közvetlenül a beszédfelismerési konfiguráció alatt.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=28-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel és szándékok hozzáadása

Most hozzá kell rendelnie egy `LanguageUnderstandingModel`t a szándék-felismerővel, és fel kell vennie a felismerni kívánt leképezéseket.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=31-35)]

## <a name="recognize-an-intent"></a>Szándék felismerése

A `IntentRecognizer` objektumban meg kell hívnia a `RecognizeOnceAsync()` metódust. Ez a módszer lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy egyetlen kifejezést küld az észleléshez, és ha a kifejezést azonosította a beszédfelismerés felismerésének leállításához.

A using utasításon belül adja hozzá a következő kódot: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-the-recognition-results-or-errors"></a>Az elismerési eredmények (vagy hibák) megjelenítése

Ha a beszédfelismerési szolgáltatás visszaadja a felismerés eredményét, érdemes megtennie a dolgot. Megtartjuk az egyszerűséget, és kinyomtathatjuk az eredményt a konzolon.

A using utasításon belül `RecognizeOnceAsync()`alatt adja hozzá a következő kódot: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=48-75)]

## <a name="check-your-code"></a>A kód megkeresése

Ezen a ponton a kódnak így kell kinéznie:  
(Ehhez a verzióhoz hozzáadott néhány megjegyzést) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=5-86)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll az alkalmazás felépítésére és a beszédfelismerési szolgáltatás használatával történő tesztelésre.

1. **Fordítsa le a kódot** – a Visual Studio menüsávján válassza a **Build** > **Build megoldás**elemet.
2. **Indítsa el az alkalmazást** – a menüsávban válassza a **hibakeresés** > a **hibakeresés indítása** vagy az **F5**billentyű lenyomása lehetőséget.
3. **Felismerés elindítása** – felszólítja, hogy beszéljen egy angol nyelvű kifejezéssel. A rendszer elküldje a beszédet a beszédfelismerési szolgáltatásnak, amelyet szövegként leír, és a konzolon jeleníti meg.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
