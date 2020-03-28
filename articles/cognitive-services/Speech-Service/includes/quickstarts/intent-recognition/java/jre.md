---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 3481d751aacd4183bf5a999422b9631ec0e21edb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925568"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene járni:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre" target="_blank">Telepítse a beszédfelismerési SDK-t a<span class="docon docon-navigate-external x-hidden-focus"></span>fejlesztői környezethez, és hozzon létre egy üres mintaprojektet.</a>

## <a name="create-a-luis-app-for-intent-recognition"></a>LUIS-alkalmazás létrehozása a szándékfelismeréshez

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>A projekt megnyitása

1. Nyissa meg a kívánt IDE-t.
2. Töltse be a `Main.java`projektet, és nyissa meg a programot.

## <a name="start-with-some-boilerplate-code"></a>Kezdje néhány sablonkóddal.

Adjunk hozzá néhány kódot, ami csontvázként működik a projektünkhöz.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

Egy `IntentRecognizer` objektum inicializálása előtt létre kell hoznia egy konfigurációt, amely a LUIS előrejelzési erőforrás kulcsát és helyét használja.  

Helyezze be ezt a kódot `main()`a try / catch blokkba. Győződjön meg arról, hogy frissíti ezeket az értékeket:

* Cserélje `"YourLanguageUnderstandingSubscriptionKey"` le a LUIS előrejelzési kulcs.
* Cserélje `"YourLanguageUnderstandingServiceRegion"` le a LUIS helyét. **Régióazonosító** használata [régióból](https://aka.ms/speech/sdkregion)

>[!TIP]
> Ha segítségre van szüksége ezen értékek megkereséséhez, olvassa [el a LEKÉPEZési alkalmazás létrehozása című témakört.](#create-a-luis-app-for-intent-recognition)

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

Ez a `FromSubscription()` minta a `SpeechConfig`módszerrel építi a. Az elérhető módszerek teljes listáját a [SpeechConfig Class ( Beszédkonfigurációs osztály](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)) tartalmazza.

A beszédsdk alapértelmezés szerint felismeri a nyelv en-us használatát, olvassa el a forrásnyelv megadása a [beszéd szöveghez című témakört](../../../../how-to-specify-source-language.md) a forrásnyelv kiválasztásával kapcsolatos információkért.

## <a name="initialize-an-intentrecognizer"></a>Szándékfelismerő inicializálása

Most hozzunk létre `IntentRecognizer`egy . Szúrja be ezt a kódot közvetlenül a beszédfelismerési konfiguráció alá.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel és szándékok hozzáadása

Társítson egy `LanguageUnderstandingModel` a szándékfelismerő, és adja hozzá a kívánt szándékfelismerésre. Az előre összeállított tartomány leképezéseit fogjuk használni az otthoni automatizáláshoz.

Szúrja be ezt `IntentRecognizer`a kódot a . Győződjön meg `"YourLanguageUnderstandingAppId"` arról, hogy lecseréli a LUIS-alkalmazásazonosítóját.

>[!TIP]
> Ha segítségre van szüksége ennek az értéknek a megkereséséhez, olvassa [el a LEKÉPEZési alkalmazás létrehozása című témakört.](#create-a-luis-app-for-intent-recognition)

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

## <a name="recognize-an-intent"></a>Szándék felismerése

Az `IntentRecognizer` objektumból meg kell adni `recognizeOnceAsync()` a metódust. Ez a módszer lehetővé teszi a beszédszolgáltatás számára, hogy egyetlen kifejezést küld a felismeréshez, és hogy miután a kifejezés azonosítása után a beszéd felismerésének leállítására kerül.

Helyezze be ezt a kódot a modell alá:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>A felismerési eredmények (vagy hibák) megjelenítése

Amikor a beszédfelismerési szolgáltatás visszaadja az elismerés eredményét, érdemes valamit kezdenie vele. Egyszerűnek fogjuk tartani, és kinyomtatjuk az eredményt a konzolra.

Szúrja be ezt a `recognizeOnceAsync()`kódot a hívás a alá.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>Erőforrások felszabadítása

Fontos, hogy amikor befejezte azok használatát, felszabadítsa a beszédforrásokat. Helyezze be ezt a kódot a try / catch blokk végén:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>A kód ellenőrzése

Ezen a ponton a kódnak így kell kinéznie:

> [!NOTE]
> Van hozzá néhány megjegyzést, hogy ezt a verziót.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Nyomja <kbd>le az F11</kbd>billentyűt, vagy válassza **a Debug futtatása** > **Debug**lehetőséget.
A mikrofonból érkező következő 15 másodpercnyi beszédet a rendszer felismeri, és a konzolablakban naplózza.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
