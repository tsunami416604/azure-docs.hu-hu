---
title: 'Gyors útmutató: hangfelismerés a hangfájlba C++ (Windows) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: wolfma
ms.openlocfilehash: 17e986954e98b7a3c0ea85324afbf77ce1428d68
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467105"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a **helloworld.cpp** forrásfájlt.

1. Cserélje le az összes kódot a következő kódrészletre:

   ```C++

    // Creates an instance of a speech config with specified subscription key and service region.
    // Replace with your own subscription key and service region (e.g., "westus").
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Creates a speech synthesizer using file as audio output.
    // Replace with your own audio file name.
    auto fileName = "helloworld.wav";
    auto fileOutput = AudioConfig::FromWavFileOutput(fileName);
    auto synthesizer = SpeechSynthesizer::FromConfig(config, fileOutput);

    // Converts the specified text to speech, saving the audio data in the file specified above.
    // Replace with your own text.
    auto text = "Hello world!";
    auto result = synthesizer->SpeakTextAsync(text).get();

    // Checks result for successful completion or errors.
    if (result->Reason == ResultReason::SynthesizingAudioCompleted)
    {
        cout << "Speech synthesized to [" << fileName << "] for text [" << text << "]" << std::endl;
    }
    else if (result->Reason == ResultReason::Canceled)
    {
        auto cancellation = SpeechSynthesisCancellationDetails::FromResult(result);
        cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

        if (cancellation->Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
            cout << "CANCELED: ErrorDetails=[" << cancellation->ErrorDetails << "]" << std::endl;
            cout << "CANCELED: Did you update the subscription info?" << std::endl;
        }
    }

   ```

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](~/articles/cognitive-services/Speech-Service/regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Cserélje le a karakterláncot a saját fájlnévre `helloworld.wav`.

1. A menüsávban válassza a **fájl** > **az összes mentése**lehetőséget.

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

1. A menüsávban válassza a **build** > Build **megoldás** elemet az alkalmazás létrehozásához. A kód fordításának hiba nélkül végbe kell mennie.

1. A **HelloWorld** alkalmazás **indításához válassza a hibakeresés > ** a **hibakeresés indítása** (vagy az **F5**billentyű lenyomása) lehetőséget.

1. A szöveget a rendszer beszédre konvertálja, és a megadott hangadatokba menti.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Lásd még:

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
