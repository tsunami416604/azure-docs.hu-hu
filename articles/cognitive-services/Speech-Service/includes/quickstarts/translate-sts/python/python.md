---
title: 'Gyors útmutató: beszédfelismerési beszéd – beszédfelismerés, Python – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: chlandsi
ms.openlocfilehash: 80f4535bd5716e1506ed9a862787030111392d77
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467971"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=python)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md?tabs=python)

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `quickstart.py` fájlt, és cserélje le a benne lévő teljes kódot a következőre.

    ````python
    import azure.cognitiveservices.speech as speechsdk

    speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"

    def translate_speech_to_speech():

        # Creates an instance of a speech translation config with specified subscription key and service region.
        # Replace with your own subscription key and service region (e.g., "westus").
        translation_config = speechsdk.translation.SpeechTranslationConfig(subscription=speech_key, region=service_region)

        # Sets source and target languages.
        # Replace with the languages of your choice, from list found here: https://aka.ms/speech/sttt-languages
        fromLanguage = 'en-US'
        toLanguage = 'de'
        translation_config.speech_recognition_language = fromLanguage
        translation_config.add_target_language(toLanguage)

        # Sets the synthesis output voice name.
        # Replace with the languages of your choice, from list found here: https://aka.ms/speech/tts-languages
        translation_config.voice_name = "de-DE-Hedda"

        # Creates a translation recognizer using and audio file as input.
        recognizer = speechsdk.translation.TranslationRecognizer(translation_config=translation_config)

        # Prepare to handle the synthesized audio data.
        def synthesis_callback(evt):
            size = len(evt.result.audio)
            print('AUDIO SYNTHESIZED: {} byte(s) {}'.format(size, '(COMPLETED)' if size == 0 else ''))

        recognizer.synthesizing.connect(synthesis_callback)

        # Starts translation, and returns after a single utterance is recognized. The end of a
        # single utterance is determined by listening for silence at the end or until a maximum of 15
        # seconds of audio is processed. It returns the recognized text as well as the translation.
        # Note: Since recognize_once() returns only a single utterance, it is suitable only for single
        # shot recognition like command or query.
        # For long-running multi-utterance recognition, use start_continuous_recognition() instead.
        print("Say something...")
        result = recognizer.recognize_once()

        # Check the result
        if result.reason == speechsdk.ResultReason.TranslatedSpeech:
            print("RECOGNIZED '{}': {}".format(fromLanguage, result.text))
            print("TRANSLATED into {}: {}".format(toLanguage, result.translations['de']))
        elif result.reason == speechsdk.ResultReason.RecognizedSpeech:
            print("RECOGNIZED: {} (text could not be translated)".format(result.text))
        elif result.reason == speechsdk.ResultReason.NoMatch:
            print("NOMATCH: Speech could not be recognized: {}".format(result.no_match_details))
        elif result.reason == speechsdk.ResultReason.Canceled:
            print("CANCELED: Reason={}".format(result.cancellation_details.reason))
            if result.cancellation_details.reason == speechsdk.CancellationReason.Error:
                print("CANCELED: ErrorDetails={}".format(result.cancellation_details.error_details))

    translate_speech_to_speech()
    ````

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](../../../../regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a `quickstart.py`végzett módosításokat.

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

1. Futtassa a mintát a konzolról vagy az IDE-ben:

   ```
   python quickstart.py
   ```

1. Mondjon ki egy angol nyelvű kifejezést vagy mondatot. Az alkalmazás elküldi a beszédét a Speech szolgáltatásnak, amely szöveget (ebben az esetben a németre) fordítja le és írja át. A beszédfelismerési szolgáltatás ezután a szintetizált hangot és a szöveget visszaküldi az alkalmazásnak a megjelenítéshez.

   ```
   Say something...
   AUDIO SYNTHESIZED: 76784 byte(s)
   AUDIO SYNTHESIZED: 0 byte(s) (COMPLETE)
   RECOGNIZED 'en-US': What's the weather in Seattle?
   TRANSLATED into 'de': Wie ist das Wetter in Seattle?
   ```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]
