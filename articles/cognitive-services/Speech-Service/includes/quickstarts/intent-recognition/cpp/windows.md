---
title: 'Gyors útmutató: beszédek, szándékok és entitások felismerése, C++ beszédfelismerési szolgáltatás'
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
ms.openlocfilehash: b26b5edeaac1f6305ed2db920c711f906eb10384
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503905"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [LUIS-alkalmazás létrehozása és végponti kulcs beszerzése](../../../../quickstarts/create-luis.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Első lépésként győződjön meg arról, hogy a projekt meg van nyitva a Visual Studióban.

1. Indítsa el a Visual Studio 2019-es kiadását.
2. Töltse be a projektet, és nyissa meg `helloworld.cpp`.

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez. Jegyezze fel, hogy létrehozott egy `recognizeIntent()`nevű aszinkron metódust.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,73-81)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

`IntentRecognizer` objektum inicializálásához létre kell hoznia egy olyan konfigurációt, amely a LUIS Endpoing kulcsot és régiót használja. Szúrja be ezt a kódot a `recognizeIntent()` metódusba.

Ez a példa a `FromSubscription()` metódust használja a `SpeechConfig`létrehozásához. Az elérhető módszerek teljes listáját lásd: [SpeechConfig osztály](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

> [!NOTE]
> Fontos, hogy a LUIS Endpoint kulcsot használja, nem pedig az indító vagy a Authroing kulcsokat, mivel csak a végpont kulcsa érvényes a beszédfelismerési szándék felismerésére. A megfelelő kulcs beszerzésével kapcsolatos útmutatásért lásd: [Luis-alkalmazás létrehozása és végponti kulcs beszerzése](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) .

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

## <a name="initialize-a-intentrecognizer"></a>IntentRecognizer inicializálása

Most hozzon létre egy `IntentRecognizer`. Szúrja be ezt a kódot a `recognizeIntent()` metódusba, közvetlenül a beszédfelismerési konfiguráció alatt.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel és szándékok hozzáadása

Most hozzá kell rendelnie egy `LanguageUnderstandingModel`t a szándék-felismerővel, és fel kell vennie a felismerni kívánt leképezéseket.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-34)]

## <a name="recognize-an-intent"></a>Szándék felismerése

A `IntentRecognizer` objektumban meg kell hívnia a `RecognizeOnceAsync()` metódust. Ez a módszer lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy egyetlen kifejezést küld az észleléshez, és ha a kifejezést azonosította a reconizing beszédének leállításához.
A similicity várni fogjuk, amíg a jövő befejeződik.

A using utasításon belül adja hozzá a következő kódot: [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=44)]

## <a name="display-the-recognition-results-or-errors"></a>Az elismerési eredmények (vagy hibák) megjelenítése

Ha a beszédfelismerési szolgáltatás visszaadja a felismerés eredményét, érdemes megtennie a dolgot. Megtartjuk az egyszerűséget, és kinyomtathatjuk az eredményt a konzolon.

A using utasításon belül `RecognizeOnceAsync()`alatt adja hozzá a következő kódot: [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=47-72)]

## <a name="check-your-code"></a>A kód megkeresése

Ezen a ponton a kódnak így kell kinéznie: (adtunk hozzá néhány megjegyzést ehhez a verzióhoz) [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-81)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll az alkalmazás felépítésére és a beszédfelismerési szolgáltatás használatával történő tesztelésre.

1. **Fordítsa le a kódot** a vizuális Stuio menüsávján, majd válassza a **Build** > **Build megoldás**elemet.
2. **Indítsa el az alkalmazást** – a menüsávban válassza a **hibakeresés** > a **hibakeresés indítása** vagy az **F5**billentyű lenyomása lehetőséget.
3. **Felismerés elindítása** – felszólítja, hogy beszéljen egy angol nyelvű kifejezéssel. A rendszer elküldje a beszédet a beszédfelismerési szolgáltatásnak, amelyet szövegként leír, és a konzolon jeleníti meg.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]