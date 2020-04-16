---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 770e037641ac8fbf75989dc94b66fd1df0689e50
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422122"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene járni:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp" target="_blank">Telepítse a beszédfelismerési SDK-t a<span class="docon docon-navigate-external x-hidden-focus"></span>fejlesztői környezethez, és hozzon létre egy üres mintaprojektet.</a>

## <a name="create-a-luis-app-for-intent-recognition"></a>LUIS-alkalmazás létrehozása a szándékfelismeréshez

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Ezután nyissa meg a projektet a Visual Studióban.

1. Indítsa el a Visual Studio 2019-et.
2. Töltse be a `helloworld.cpp`projektet, és nyissa meg a programot.

## <a name="start-with-some-boilerplate-code"></a>Kezdje néhány sablonkóddal.

Adjunk hozzá néhány kódot, ami csontvázként működik a projektünkhöz. Vegye figyelembe, hogy létrehozott egy aszinkron metódust, amelynek neve `recognizeIntent()`.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,72-80)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

Egy `IntentRecognizer` objektum inicializálása előtt létre kell hoznia egy konfigurációt, amely a LUIS előrejelzési erőforrás kulcsát és helyét használja.

> [!IMPORTANT]
> Az indítókulcs és a szerzői kulcsok nem fognak működni. Az előrejelzési kulcsot és a korábban létrehozott helyet kell használnia. További információ: [Létrehozás egy LUIS alkalmazás szándékfelismerés.](#create-a-luis-app-for-intent-recognition)

Szúrja be ezt `recognizeIntent()` a kódot a metódusba. Győződjön meg arról, hogy frissíti ezeket az értékeket:

* Cserélje `"YourLanguageUnderstandingSubscriptionKey"` le a LUIS előrejelzési kulcs.
* Cserélje `"YourLanguageUnderstandingServiceRegion"` le a LUIS helyét.  Használja a [region](https://aka.ms/speech/sdkregion)régiórégiórégióból származó **régióazonosítót.**

>[!TIP]
> Ha segítségre van szüksége ezen értékek megkereséséhez, olvassa [el a LEKÉPEZési alkalmazás létrehozása című témakört.](#create-a-luis-app-for-intent-recognition)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

Ez a `FromSubscription()` minta a `SpeechConfig`módszerrel építi a. Az elérhető módszerek teljes listáját a [SpeechConfig Class ( Beszédkonfigurációs osztály](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)) tartalmazza.

A beszédsdk alapértelmezés szerint felismeri a nyelv en-us használatát, olvassa el a forrásnyelv megadása a [beszéd szöveghez című témakört](../../../../how-to-specify-source-language.md) a forrásnyelv kiválasztásával kapcsolatos információkért.

## <a name="initialize-an-intentrecognizer"></a>Szándékfelismerő inicializálása

Most hozzunk létre `IntentRecognizer`egy . Szúrja be ezt `recognizeIntent()` a kódot a metódusba, közvetlenül a beszédfelismerési konfiguráció alatt.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel és szándékok hozzáadása

Társítson egy `LanguageUnderstandingModel` a szándékfelismerő, és adja hozzá a kívánt szándékfelismerésre. Az előre összeállított tartomány leképezéseit fogjuk használni az otthoni automatizáláshoz.

Szúrja be ezt `IntentRecognizer`a kódot a . Győződjön meg `"YourLanguageUnderstandingAppId"` arról, hogy lecseréli a LUIS-alkalmazásazonosítóját.

>[!TIP]
> Ha segítségre van szüksége ennek az értéknek a megkereséséhez, olvassa [el a LEKÉPEZési alkalmazás létrehozása című témakört.](#create-a-luis-app-for-intent-recognition)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-33)]

## <a name="recognize-an-intent"></a>Szándék felismerése

Az `IntentRecognizer` objektumból meg kell adni `RecognizeOnceAsync()` a metódust. Ez a módszer lehetővé teszi a beszédszolgáltatás számára, hogy egyetlen kifejezést küld a felismeréshez, és hogy miután a kifejezés azonosítása után a beszéd felismerésének leállítására kerül. Az egyszerűség kedvéért megvárjuk a jövőt, hogy befejezzük.

Helyezze be ezt a kódot a modell alá:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=43)]

## <a name="display-the-recognition-results-or-errors"></a>A felismerési eredmények (vagy hibák) megjelenítése

Amikor a beszédfelismerési szolgáltatás visszaadja az elismerés eredményét, érdemes valamit kezdenie vele. Egyszerűnek fogjuk tartani, és kinyomtatjuk az eredményt a konzolra.

Írja be `auto result = recognizer->RecognizeOnceAsync().get();`ezt a kódot az alábbi ba:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=46-71)]

## <a name="check-your-code"></a>A kód ellenőrzése

Ezen a ponton a kódnak így kell kinéznie:

> [!NOTE]
> Van hozzá néhány megjegyzést, hogy ezt a verziót.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-79)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll arra, hogy létrehozza az alkalmazást, és tesztelje a beszédfelismerést a Beszédszolgáltatás használatával.

1. **A kód fordítása** - A Visual Studio menüsorából válassza a Build Solution **(Build** > Solution )**lehetőséget.**
2. **Az alkalmazás indítása** – A menüsorban válassza a Debug Start Debugging **(Hibakeresés** > **indítása)** lehetőséget, vagy nyomja le az <kbd>F5 billentyűt</kbd>.
3. **Start felismerés** - Ez kérni fogja, hogy beszéljen egy mondatot angolul. A rendszer elküldi a beszédfelismerési szolgáltatást, szövegként átírja, és megjeleníti a konzolon.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
