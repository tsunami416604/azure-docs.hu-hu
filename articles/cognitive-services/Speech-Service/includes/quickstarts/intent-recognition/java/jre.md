---
title: 'Gyors útmutató: beszédek, szándékok és entitások felismerése, Java-Speech Service'
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
ms.openlocfilehash: 9d847bbc27710a510564363281b65ac57f57ed7e
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795883"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
>
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Language Understanding (LUIS) alkalmazás létrehozása és végponti kulcs beszerzése](../../../../quickstarts/create-luis.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="open-your-project"></a>A projekt megnyitása

Töltse be a projektet, és nyissa meg `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,69-76)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

`IntentRecognizer` objektum inicializálásához létre kell hoznia egy olyan konfigurációt, amely a LUIS Endpoint kulcsát és régióját használja. A kód beillesztése a kipróbálás/Catch blokkba a Mainban

Ez a példa a `FromSubscription()` metódust használja a `SpeechConfig`létrehozásához. Az elérhető módszerek teljes listáját lásd: [SpeechConfig osztály](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
A Speech SDK alapértelmezés szerint az en-us nyelv használatával ismeri fel a nyelvet, a forrás nyelvének kiválasztásával kapcsolatos információkért lásd: nyelv [megadása a beszédhez szöveghez](../../../../how-to-specify-source-language.md) .

> [!NOTE]
> Fontos, hogy a LUIS Endpoint kulcsot használja, nem pedig a kezdő vagy a szerzői kulcsot, mert csak a végpont kulcsa érvényes a beszédfelismerési szándék felismerésére. A megfelelő kulcs beszerzésével kapcsolatos útmutatásért lásd: [Luis-alkalmazás létrehozása és végponti kulcs beszerzése](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) .

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer inicializálása

Most hozzon létre egy `IntentRecognizer`. Szúrja be a kódot közvetlenül a beszédfelismerési konfiguráció alá.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel és szándékok hozzáadása

Most hozzá kell rendelnie egy `LanguageUnderstandingModel`t a szándék-felismerővel, és fel kell vennie a felismerni kívánt leképezéseket.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-36)]

## <a name="recognize-an-intent"></a>Szándék felismerése

A `IntentRecognizer` objektumban meg kell hívnia a `recognizeOnceAsync()` metódust. Ez a módszer lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy egyetlen kifejezést küld az észleléshez, és ha a kifejezést azonosította a beszédfelismerés felismerésének leállításához.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=41)]

## <a name="display-the-recognition-results-or-errors"></a>Az elismerési eredmények (vagy hibák) megjelenítése

Ha a beszédfelismerési szolgáltatás visszaadja a felismerés eredményét, érdemes megtennie a dolgot. Megtartjuk az egyszerűséget, és kinyomtathatjuk az eredményt a konzolon.

A `recognizeOnceAsync()`hívása alatt adja hozzá a következő kódot: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=44-65)]

## <a name="release-resources"></a>Kiadási erőforrások

Fontos, hogy a használat során kiszabadítsa a beszédfelismerési erőforrásokat. Szúrja be ezt a kódot a try/catch blokk végén: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=67-68)]

## <a name="check-your-code"></a>A kód megkeresése

Ezen a ponton a kódnak így kell kinéznie:  
(Ehhez a verzióhoz hozzáadott néhány megjegyzést) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-76)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Nyomja le az F11 billentyűt, vagy válassza a **Run** > **Debug** (Futtatás, Hibakeresés) lehetőséget.
A mikrofonból érkező következő 15 másodpercnyi beszédet a rendszer felismeri, és a konzolablakban naplózza.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]