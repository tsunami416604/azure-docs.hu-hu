---
title: 'Gyors útmutató: beszédek, szándékok és entitások felismerése, Python-Speech szolgáltatás'
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
ms.openlocfilehash: 4efa535118d075addf78b2e9be6a645c458d6bb4
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125503"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
>
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [LUIS-alkalmazás létrehozása és végponti kulcs beszerzése](../../../../quickstarts/create-luis.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md)

## <a name="open-your-project"></a>A projekt megnyitása

Nyissa meg a Quickstart.py a Python-szerkesztőben.

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

`IntentRecognizer` objektum inicializálásához létre kell hoznia egy olyan konfigurációt, amely a LUIS Endpoint kulcsát és régióját használja. Szúrja be a következő kódot.

Ez a minta a `SpeechConfig` objektumot a LUIS kulcs és a régió használatával hozza létre. Az elérhető módszerek teljes listáját lásd: [SpeechConfig osztály](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

> [!NOTE]
> Fontos, hogy a LUIS Endpoint kulcsot használja, nem pedig a kezdő vagy a szerzői kulcsot, mert csak a végpont kulcsa érvényes a beszédfelismerési szándék felismerésére. A megfelelő kulcs beszerzésével kapcsolatos útmutatásért lásd: [Luis-alkalmazás létrehozása és végponti kulcs beszerzése](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) .

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer inicializálása

Most hozzon létre egy `IntentRecognizer`. Szúrja be a kódot közvetlenül a beszédfelismerési konfiguráció alá.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel és szándékok hozzáadása

Most hozzá kell rendelnie egy `LanguageUnderstandingModel`t a szándék-felismerővel, és fel kell vennie a felismerni kívánt leképezéseket.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Szándék felismerése

A `IntentRecognizer` objektumban meg kell hívnia a `recognize_once()` metódust. Ez a módszer lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy egyetlen kifejezést küld az észleléshez, és ha a kifejezést azonosította a beszédfelismerés felismerésének leállításához.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Az elismerési eredmények (vagy hibák) megjelenítése

Ha a beszédfelismerési szolgáltatás visszaadja a felismerés eredményét, érdemes megtennie a dolgot. Megtartjuk az egyszerűséget, és kinyomtathatjuk az eredményt a konzolon.

A using utasításon belül, a `recognize_once()`hívása alatt adja hozzá a következő kódot: [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>A kód megkeresése

Ezen a ponton a kódnak így kell kinéznie:  
(Ehhez a verzióhoz hozzáadott néhány megjegyzést) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Futtassa a mintát a konzolról vagy az IDE-ben:

    ````
    python quickstart.py
    ````

A mikrofonból érkező következő 15 másodpercnyi beszédet a rendszer felismeri, és a konzolablakban naplózza.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
