---
title: Beszédfelismerési mód kiválasztása a BeszédSDK-val
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan választhatja ki a legjobb felismerési módot a beszédfelismerési SDK használatakor.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5fdca371e9188ef69068ddbcaa416cbb2b44054c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402154"
---
# <a name="choose-a-speech-recognition-mode"></a>Beszédfelismerési mód kiválasztása

A beszéd-szöveg felismerési műveletek mérlegelésekor a [beszédfelismerési SDK](speech-sdk.md) több módot is biztosít a beszédfelismerés feldolgozásához. Fogalmilag, néha a *felismerési mód*. Ez a cikk összehasonlítja a különböző felismerési módokat.

## <a name="recognize-once"></a>Felismerés egyszer

Ha azt szeretné, hogy minden utterance (mondat) egy időben, használja a "recognize once" függvényt. Ez a módszer észleli a felismert utterance (kifejezés) a bemeneti kezdve az észlelt beszéd a következő szünetig. Általában a szünet egy mondat vagy gondolatsor végét jelzi.

Egy felismert utterance (kifejezés) végén a szolgáltatás leállítja a kérelemből származó hang feldolgozását. Az elismerés maximális határa 20 másodperces mondat.

::: zone pivot="programming-language-csharp"

A függvény használatáról `RecognizeOnceAsync` további információt a [.](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync)

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

A függvény használatáról `RecognizeOnceAsync` további információt a [C++ beszédfelismerési SDK-dokumentumokban](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync)talál.

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

A függvény használatáról `recognizeOnceAsync` további információt a [Java Speech SDK-dokumentumokban](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable)talál.

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

A függvény használatáról `recognize_once` további információt a [Python Speech SDK-dokumentumokban](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult)talál.

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

További nyelveket a [BeszédsDK referenciadokumentumai ban láthat.](speech-to-text.md#speech-sdk-reference-docs)

::: zone-end

## <a name="continuous"></a>Folyamatos

Ha hosszú ideig tartó felismerésre van szüksége, használja a start és a megfelelő leállítási függvényeket a folyamatos felismeréshez. A start függvény elindul, és folytatja az összes utterances feldolgozását, amíg meg nem hívja a stop függvényt, vagy amíg túl sok csendben eltelt idő. A folyamatos üzemmód használatakor győződjön meg róla, hogy regisztráljon a különböző eseményekre, amelyek az esemény bekövetkeztekor indulnak el. A "felismert" esemény például a beszédfelismerés bekövetkeztekor következik be. A felismerés kezeléséhez egy eseménykezelőre van szükség.

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end
::: zone pivot="programming-language-more"

További nyelveket a [BeszédsDK referenciadokumentumai ban láthat.](speech-to-text.md#speech-sdk-reference-docs)

::: zone-end

## <a name="dictation"></a>Diktálás

Folyamatos felismerés használataesetén engedélyezheti a diktálás feldolgozását a megfelelő "Diktálás engedélyezése" funkcióval. Ezzel a móddal a beszédfelismerési konfigurációs példány értelmezi a mondatstruktúrák, például az írásjelek szóleírását. Például a "A városban él" kimondott szöveget a "A városban él?" szövegként értelmezi.

::: zone pivot="programming-language-csharp"

A függvény használatáról `EnableDictation` további információt a [.](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation)

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

A függvény használatáról `EnableDictation` további információt a [C++ beszédfelismerési SDK-dokumentumokban](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation)talál.

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

A függvény használatáról `enableDictation` további információt a [Java Speech SDK-dokumentumokban](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable)talál.

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

A függvény használatáról `enable_dictation` további információt a [Python Speech SDK-dokumentumokban](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--)talál.

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

További nyelveket a [BeszédsDK referenciadokumentumai ban láthat.](speech-to-text.md#speech-sdk-reference-docs)

::: zone-end

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További beszédbeszédSDk-minták felfedezése a GitHubon](https://aka.ms/csspeech/samples)
