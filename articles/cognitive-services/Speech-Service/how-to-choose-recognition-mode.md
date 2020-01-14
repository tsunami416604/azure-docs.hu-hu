---
title: Beszédfelismerési mód kiválasztása a Speech SDK-val
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan választhatja ki a legjobb felismerési módot a Speech SDK használatakor.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 51bf005bdad4197120fed96894ac1cdd150738ee
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75935226"
---
# <a name="choose-a-speech-recognition-mode"></a>Beszédfelismerési mód kiválasztása

A beszéd-szöveg felismerési műveletekre való tekintettel a [SPEECH SDK](speech-sdk.md) több módot biztosít a beszéd feldolgozására. Elméletileg, más néven *felismerési mód*. Ez a cikk a különböző felismerési módokat hasonlítja össze.

## <a name="recognize-once"></a>Egyszeri felismerés

Ha egyszerre egy "mondatot" szeretne feldolgozni, használja a "felismerés egyszer" függvényt. Ez a metódus az észlelt beszéd elejétől kezdődően észlelt, a következő szüneteltetésig elvégezhető felismerést észleli. A Szüneteltetés általában egy mondat végét jelöli, vagy a gondolatot.

Az egyik felismertség végén a szolgáltatás leállítja a hang feldolgozását a kérelemből. Az elismerésre vonatkozó maximális korlát 20 másodperces időtartam.

::: zone pivot="programming-language-csharp"

További információ a `RecognizeOnceAsync` függvény használatáról: [.net SPEECH SDK docs](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync().ConfigureAwait(false);
```

::: zone-end
::: zone pivot="programming-language-cpp"

Az `RecognizeOnceAsync` függvény használatáról további információt a [ C++ Speech SDK dokumentációjában](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync)talál.

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

További információ az `recognizeOnceAsync` függvény használatáról: [Java SPEECH SDK docs](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable).

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Az `recognize_once` függvény használatáról további információt a [Python SPEECH SDK dokumentációjában](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult)talál.

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

További nyelvek: [beszédfelismerési SDK-dokumentáció](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="continuous"></a>Folyamatos

Ha hosszú ideig futó felismerésre van szüksége, használja a Start és a megfelelő leállítás függvényt a folyamatos felismeréshez. A Start függvény elindítja és folytatja az összes hosszúságú kimondott szöveg feldolgozását, amíg meg nem hívja a leállítási függvényt, vagy amíg a csendben túl sok idő el nem telik. A folyamatos mód használatakor ügyeljen arra, hogy regisztrálja azokat a különböző eseményeket, amelyek az előforduláskor tüzet fognak. Például a "felismert" esemény akkor következik be, amikor beszédfelismerés történik. Az elismerés kezeléséhez egy eseménykezelőre van szükség. A beszédfelismerési szolgáltatás az összes beszédfelismerési idő 10 perces korlátját kikényszeríti.

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
await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
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

További nyelvek: [beszédfelismerési SDK-dokumentáció](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="dictation"></a>Diktálás

Folyamatos felismerés használatakor engedélyezheti a diktálás feldolgozását a megfelelő "diktálás engedélyezése" funkció használatával. Ez a mód azt eredményezi, hogy a beszédfelismerési konfigurációs példány értelmezi a mondatok (például a központozás) szövegének leírását. A "Do You Live in Town kérdőjel" kifejezés például "a városban él?" szöveget fogja értelmezni.

::: zone pivot="programming-language-csharp"

További információ a `EnableDictation` függvény használatáról: [.net SPEECH SDK docs](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Az `EnableDictation` függvény használatáról további információt a [ C++ Speech SDK dokumentációjában](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation)talál.

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

További információ az `enableDictation` függvény használatáról: [Java SPEECH SDK docs](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable).

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Az `enable_dictation` függvény használatáról további információt a [Python SPEECH SDK dokumentációjában](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--)talál.

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

További nyelvek: [beszédfelismerési SDK-dokumentáció](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További beszédfelismerési SDK-minták megismerése a GitHubon](https://aka.ms/csspeech/samples)
