---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5e6e2c7686c61fa03639566b2b37dc0fb8dbc924
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671654"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene járni:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp" target="_blank">Telepítse a beszédfelismerési SDK-t a<span class="docon docon-navigate-external x-hidden-focus"></span>fejlesztői környezethez egy létrehozási és üres mintaprojekthez.</a>

## <a name="create-a-luis-app-for-intent-recognition"></a>LUIS-alkalmazás létrehozása a szándékfelismeréshez

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Ezután nyissa meg a projektet a Visual Studióban.

1. Indítsa el a Visual Studio 2019-et.
2. Töltse be a `Program.cs`projektet, és nyissa meg a programot.

## <a name="start-with-some-boilerplate-code"></a>Kezdje néhány sablonkóddal.

Adjunk hozzá néhány kódot, ami csontvázként működik a projektünkhöz. Vegye figyelembe, hogy létrehozott egy aszinkron metódust, amelynek neve `RecognizeIntentAsync()`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

Egy `IntentRecognizer` objektum inicializálása előtt létre kell hoznia egy konfigurációt, amely a LUIS előrejelzési erőforrás kulcsát és helyét használja.

> [!IMPORTANT]
> Az indítókulcs és a szerzői kulcsok nem fognak működni. Az előrejelzési kulcsot és a korábban létrehozott helyet kell használnia. További információ: [Létrehozás egy LUIS alkalmazás szándékfelismerés.](#create-a-luis-app-for-intent-recognition)

Szúrja be ezt `RecognizeIntentAsync()` a kódot a metódusba. Győződjön meg arról, hogy frissíti ezeket az értékeket:

* Cserélje `"YourLanguageUnderstandingSubscriptionKey"` le a LUIS előrejelzési kulcs.
* Cserélje `"YourLanguageUnderstandingServiceRegion"` le a LUIS helyét. Használja a [region](https://aka.ms/speech/sdkregion)régiórégiórégióból származó **régióazonosítót.**

>[!TIP]
> Ha segítségre van szüksége ezen értékek megkereséséhez, olvassa [el a LEKÉPEZési alkalmazás létrehozása című témakört.](#create-a-luis-app-for-intent-recognition)

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

Ez a `FromSubscription()` minta a `SpeechConfig`módszerrel építi a. Az elérhető módszerek teljes listáját a [SpeechConfig Class ( Beszédkonfigurációs osztály](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)) tartalmazza.

A beszédsdk alapértelmezés szerint felismeri a nyelv en-us használatát, olvassa el a forrásnyelv megadása a [beszéd szöveghez című témakört](../../../../how-to-specify-source-language.md) a forrásnyelv kiválasztásával kapcsolatos információkért.

## <a name="initialize-an-intentrecognizer"></a>Szándékfelismerő inicializálása

Most hozzunk létre `IntentRecognizer`egy . Ez az objektum egy using utasításon belül jön létre a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt `RecognizeIntentAsync()` a kódot a metódusba, közvetlenül a beszédfelismerési konfiguráció alatt.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=29-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel és szándékok hozzáadása

Társítson egy `LanguageUnderstandingModel` et a szándékfelismerőhöz, és adja hozzá a felismert szándékokat. Az előre összeállított tartomány leképezéseit fogjuk használni az otthoni automatizáláshoz. Szúrja be ezt a kódot az előző szakasz ban található felhasználási utasításba. Győződjön meg `"YourLanguageUnderstandingAppId"` arról, hogy lecseréli a LUIS-alkalmazásazonosítóját.

>[!TIP]
> Ha segítségre van szüksége ennek az értéknek a megkereséséhez, olvassa [el a LEKÉPEZési alkalmazás létrehozása című témakört.](#create-a-luis-app-for-intent-recognition)

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=33-35)]

## <a name="recognize-an-intent"></a>Szándék felismerése

Az `IntentRecognizer` objektumból meg kell adni `RecognizeOnceAsync()` a metódust. Ez a módszer lehetővé teszi a beszédszolgáltatás számára, hogy egyetlen kifejezést küld a felismeréshez, és hogy miután a kifejezés azonosítása után a beszéd felismerésének leállítására kerül.

A using utasításon belül adja hozzá ezt a kódot a modell alatt.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-recognition-results-or-errors"></a>Megjelenítési eredmények (vagy hibák) megjelenítése

Amikor a beszédfelismerési szolgáltatás visszaadja az elismerés eredményét, érdemes valamit kezdenie vele. Egyszerűnek fogjuk tartani, és kinyomtatjuk az eredményeket a konzolra.

Belül a használó `RecognizeOnceAsync()`utasítás, az alábbi , add meg ezt a kódot:

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=49-75)]

## <a name="check-your-code"></a>A kód ellenőrzése

Ezen a ponton a kódnak így kell kinéznie:

> [!NOTE]
> Van hozzá néhány megjegyzést, hogy ezt a verziót.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-86)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll arra, hogy létrehozza az alkalmazást, és tesztelje a beszédfelismerést a Beszédszolgáltatás használatával.

1. **A kód fordítása** - A Visual Studio menüsorából válassza a Build Solution **(Build** > Solution )**lehetőséget.**
2. **Az alkalmazás indítása** – A menüsorban válassza a Debug Start Debugging **(Hibakeresés** > **indítása)** lehetőséget, vagy nyomja le az <kbd>F5 billentyűt</kbd>.
3. **Start felismerés** - Ez kérni fogja, hogy beszéljen egy mondatot angolul. A rendszer elküldi a beszédfelismerési szolgáltatást, szövegként átírja, és megjeleníti a konzolon.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
