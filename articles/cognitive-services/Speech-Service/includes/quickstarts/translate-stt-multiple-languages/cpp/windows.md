---
title: 'Rövid útmutató: Beszéd fordítása több nyelvre, C++ (Windows) – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: b2f0bbe56d31d583f1a4328460c55591261a2aeb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925428"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [Azure-beszédfelismerési erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=windows)

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a **helloworld.cpp** forrásfájlt.

1. Cserélje le az összes kódot a következő kódrészletre:

   ```C++
   #include <iostream>
   #include <vector>
   #include <speechapi_cxx.h>

   using namespace std;
   using namespace Microsoft::CognitiveServices::Speech;
   using namespace Microsoft::CognitiveServices::Speech::Translation;

   void TranslateSpeechToText()
   {
       // Creates an instance of a speech translation config with specified subscription key and service region.
       // Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
       auto config = SpeechTranslationConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

       // Sets source and target languages.
       // Replace with the languages of your choice, from list found here: https://aka.ms/speech/sttt-languages
       auto fromLanguage = "en-US";
       config->SetSpeechRecognitionLanguage(fromLanguage);
       config->AddTargetLanguage("de");
       config->AddTargetLanguage("fr");

       // Creates a translation recognizer using the default microphone audio input device.
       auto recognizer = TranslationRecognizer::FromConfig(config);

       // Starts translation, and returns after a single utterance is recognized. The end of a
       // single utterance is determined by listening for silence at the end or until a maximum of 15
       // seconds of audio is processed. The task returns the recognized text as well as the translation.
       // Note: Since RecognizeOnceAsync() returns only a single utterance, it is suitable only for single
       // shot recognition like command or query.
       // For long-running multi-utterance recognition, use StartContinuousRecognitionAsync() instead.
       cout << "Say something...\n";
       auto result = recognizer->RecognizeOnceAsync().get();

       // Checks result.
       if (result->Reason == ResultReason::TranslatedSpeech)
       {
           cout << "RECOGNIZED '" << fromLanguage << "': " << result->Text << std::endl;
           for (const auto& it : result->Translations)
           {
               cout << "TRANSLATED into '" << it.first.c_str() << "': " << it.second.c_str() << std::endl;
           }
       }
       else if (result->Reason == ResultReason::RecognizedSpeech)
       {
           cout << "RECOGNIZED '" << fromLanguage << "' " << result->Text << " (text could not be translated)" << std::endl;
       }
       else if (result->Reason == ResultReason::NoMatch)
       {
           cout << "NOMATCH: Speech could not be recognized." << std::endl;
       }
       else if (result->Reason == ResultReason::Canceled)
       {
           auto cancellation = CancellationDetails::FromResult(result);
           cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

           if (cancellation->Reason == CancellationReason::Error)
           {
               cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
               cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
               cout << "CANCELED: Did you update the subscription info?" << std::endl;
           }
       }
   }

   int wmain()
   {
       TranslateSpeechToText();
       return 0;
   }
   ```

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](~/articles/cognitive-services/Speech-Service/regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. A menüsorban válassza az > **Összes** **fájlmentése**lehetőséget.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

1. A menüsorban válassza **buildmegoldás összeállítása** > **az** alkalmazás létrehozásához. A kód fordításának hiba nélkül végbe kell mennie.

1. A **helloworld** alkalmazás elindításához válassza a **Debug** > **Start Debugging** (vagy **az F5)** billentyűt.

1. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. Az alkalmazás továbbítja a beszédet a beszéd szolgáltatás, amely lefordítja és átírja a szöveget (ebben az esetben a francia és a német). A beszédszolgáltatás ezután elküldi a szöveget az alkalmazásnak megjelenítésre.

````
Say something...
RECOGNIZED 'en-US': What's the weather in Seattle?
TRANSLATED into 'de': Wie ist das Wetter in Seattle?
TRANSLATED into 'fr': Quel temps fait-il à Seattle ?
````

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
