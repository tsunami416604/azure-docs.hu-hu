---
title: 'Gyors útmutató: Beszéd, a C++ (Windows) – beszédszolgáltatások fordítása'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban fog létrehozni egy egyszerű C++-alkalmazás felhasználói speech rögzítése, azt fordítása más nyelvre, és a szöveg a parancssorba. Ez az útmutató Windows-felhasználók számára tervezték.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 70a5e05416be3dd12f391c67c6046546b3c564f7
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883236"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c"></a>Gyors útmutató: Beszédfelismerés, beszédfelismerési SDK-val fordítása Pro C++

Ebben a rövid útmutatóban fog létrehozni egy egyszerű C++-alkalmazás, amely rögzíti a felhasználó beszéd, a számítógép mikrofon, a rendszer lefordítja a beszédfelismerési és transcribes a lefordított szöveg valós időben a parancssorba. Ez az alkalmazás a 64 bites Windows rendszerhez készült, és a beépített a [beszéd SDK NuGet-csomagot](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-ben.

Beszédalapú fordítási elérhető nyelvek teljes listáját lásd: [nyelvi támogatás](language-support.md).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcs. [Igényeljen ingyenesen egy](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a *helloworld.cpp* forrásfájlt. Cserélje le a kezdeti „include” állítás (`#include "stdafx.h"` vagy `#include "pch.h"`) alatt található teljes kódot a következőre:

    ```cpp
    #include "pch.h"
    #include <iostream>
    #include <vector>
    #include <speechapi_cxx.h>

    using namespace std;
    using namespace Microsoft::CognitiveServices::Speech;
    using namespace Microsoft::CognitiveServices::Speech::Translation;

    // Translation with microphone input.
    void TranslationWithMicrophone()
    {
        // Creates an instance of a speech translation config with specified subscription key and service region.
        // Replace with your own subscription key and service region (e.g., "westus").
        auto config = SpeechTranslationConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

        // Sets source and target languages
        // Replace with the languages of your choice.
        auto fromLanguage = "en-US";
        config->SetSpeechRecognitionLanguage(fromLanguage);
        config->AddTargetLanguage("de");
        config->AddTargetLanguage("fr");

        // Creates a translation recognizer using microphone as audio input.
        auto recognizer = TranslationRecognizer::FromConfig(config);
        cout << "Say something...\n";

        // Starts translation. RecognizeOnceAsync() returns when the first utterance has been recognized,
        // so it is suitable only for single shot recognition like command or query. For long-running
        // recognition, use StartContinuousRecognitionAsync() instead.
        auto result = recognizer->RecognizeOnceAsync().get();

        // Checks result.
        if (result->Reason == ResultReason::TranslatedSpeech)
        {
            cout << "RECOGNIZED: Text=" << result->Text << std::endl
                << "  Language=" << fromLanguage << std::endl;

            for (const auto& it : result->Translations)
            {
                cout << "TRANSLATED into '" << it.first.c_str() << "': " << it.second.c_str() << std::endl;
            }
        }
        else if (result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << result->Text << " (text could not be translated)" << std::endl;
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
        TranslationWithMicrophone();
        cout << "Please press a key to continue.\n";
        cin.get();
        return 0;
    }
    ```

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekt módosításait.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül kell végbe mennie.

   ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-cpp-windows-06-build.png)

1. Indítsa el az alkalmazást. A menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** lehetőséget, vagy nyomja le az **F5** billentyűt.

   ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Megjelenik egy konzolablak, amely arra kéri Önt, hogy mondjon valamit. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. A beszéd a Speech továbbításakor, lefordított és megjelenített érzéseket szöveg, amely ugyanabban az ablakban jelenik meg.

   ![Képernyőfelvétel a konzol kimenete a sikeres fordítás után](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>További lépések

További példákat, beszéd olvasni hangfájl, és a lefordított szöveg szintetizált, mint például a Githubon érhetők el.

> [!div class="nextstepaction"]
> [Ismerkedés a C++-mintákat a Githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
