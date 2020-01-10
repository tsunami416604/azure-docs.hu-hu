---
title: 'Gyors útmutató: beszédek, szándékok és entitások felismerése, C++ beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/02/2020
ms.topic: include
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: d421de69f856790b89c866a6c8b7221e9214aef4
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772943"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt:

* Ha ez az első C++ projektje, az útmutató segítségével <a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md?tabs=windows" target="_blank">hozzon létre egy üres minta projektet</a>.
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows" target="_blank">Telepítse a fejlesztői környezethez készült SPEECH SDK</a>-t.

## <a name="create-a-luis-app-for-intent-recognition"></a>LUIS-alkalmazás létrehozása a szándék-felismeréshez

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Ezután nyissa meg a projektet a Visual Studióban.

1. Indítsa el a Visual Studio 2019-es kiadását.
2. Töltse be a projektet, és nyissa meg `helloworld.cpp`.

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez. Jegyezze fel, hogy létrehozott egy `recognizeIntent()`nevű aszinkron metódust.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,73-81)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

`IntentRecognizer` objektum inicializálásához létre kell hoznia egy olyan konfigurációt, amely a LUIS-előrejelzési erőforrás kulcsát és helyét használja. 

> [!IMPORTANT]
> Az alapszintű kulcs és a szerzői kulcsok nem fognak működni. A korábban létrehozott előrejelzési kulcsot és helyet kell használnia. További információ: LUIS- [alkalmazás létrehozása a szándék-felismeréshez](#create-a-luis-app-for-intent-recognition). 

Szúrja be ezt a kódot a `recognizeIntent()` metódusba. Győződjön meg róla, hogy frissíti ezeket az értékeket:

* Cserélje le a `"YourLanguageUnderstandingSubscriptionKey"`t a LUIS-előrejelzési kulcsra. 
* Cserélje le a `"YourLanguageUnderstandingServiceRegion"`t a LUIS helyére. 

>[!TIP]
> Ha segítségre van szüksége ezeknek az értékeknek a megtalálásához, tekintse meg [a Luis-alkalmazás létrehozása a szándék felismeréséhez](#create-a-luis-app-for-intent-recognition)című témakört.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

Ez a példa a `FromSubscription()` metódust használja a `SpeechConfig`létrehozásához. Az elérhető módszerek teljes listáját lásd: [SpeechConfig osztály](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

A Speech SDK alapértelmezés szerint az en-us nyelv használatával ismeri fel a nyelvet, a forrás nyelvének kiválasztásával kapcsolatos információkért lásd: nyelv [megadása a beszédhez szöveghez](../../../../how-to-specify-source-language.md) .

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer inicializálása

Most hozzon létre egy `IntentRecognizer`. Szúrja be ezt a kódot a `recognizeIntent()` metódusba, közvetlenül a beszédfelismerési konfiguráció alatt.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel és szándékok hozzáadása

Hozzá kell rendelnie egy `LanguageUnderstandingModel`t a szándék-felismerővel, és fel kell vennie a felismerni kívánt leképezéseket. Az előre elkészített tartományból szándékait fogjuk használni a Home Automation számára. 

Szúrja be ezt a kódot a `IntentRecognizer`alá. Győződjön meg arról, hogy a `"YourLanguageUnderstandingAppId"`t a LUIS-alkalmazás azonosítójával helyettesíti. 

>[!TIP]
> Ha segítségre van szüksége az érték megkereséséhez, tekintse meg [a Luis-alkalmazás létrehozása a szándék-felismeréshez](#create-a-luis-app-for-intent-recognition)című témakört.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-34)]

## <a name="recognize-an-intent"></a>Szándék felismerése

A `IntentRecognizer` objektumban meg kell hívnia a `RecognizeOnceAsync()` metódust. Ez a módszer lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy egyetlen kifejezést küld az észleléshez, és ha a kifejezést azonosította a beszédfelismerés felismerésének leállításához. Az egyszerűség kedvéért várni fogjuk, amíg a jövő befejeződik.

Szúrja be a kódot a modell alá:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=44)]

## <a name="display-the-recognition-results-or-errors"></a>Az elismerési eredmények (vagy hibák) megjelenítése

Ha a beszédfelismerési szolgáltatás visszaadja a felismerés eredményét, érdemes megtennie a dolgot. Megtartjuk az egyszerűséget, és kinyomtathatjuk az eredményt a konzolon.

Szúrja be a következő kódot `auto result = recognizer->RecognizeOnceAsync().get();`:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=47-72)]

## <a name="check-your-code"></a>A kód megkeresése

Ezen a ponton a kódnak így kell kinéznie:  

> [!NOTE]
> Ehhez a verzióhoz hozzáadott néhány megjegyzést.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-81)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll az alkalmazás felépítésére és a beszédfelismerési szolgáltatás használatával történő tesztelésre.

1. **Fordítsa le a kódot** – a Visual Studio menüsávján válassza a **Build** > **Build megoldás**elemet.
2. **Indítsa el az alkalmazást** – a menüsávban válassza a **hibakeresés** > a **hibakeresés indítása** vagy az **F5**billentyű lenyomása lehetőséget.
3. **Felismerés elindítása** – felszólítja, hogy beszéljen egy angol nyelvű kifejezéssel. A rendszer elküldje a beszédet a beszédfelismerési szolgáltatásnak, amelyet szövegként leír, és a konzolon jeleníti meg.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]