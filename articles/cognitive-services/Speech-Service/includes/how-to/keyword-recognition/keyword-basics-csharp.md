---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: 786f9587ab223cf87a48cd791f366049b94af59b
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97865968"
---
Először töltse be a kulcsszavas modellt tartalmazó fájlt a `FromFile()` statikus függvénnyel, amely egy értéket ad vissza `KeywordRecognitionModel` . Használja a `.table` Speech studióból letöltött fájl elérési útját. Emellett `AudioConfig` az alapértelmezett mikrofon használatával is létrehoz egy új példányt a `KeywordRecognizer` hangkonfiguráció használatával.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Ezután a kulcsszó-felismerés futtatása egy hívással történik `RecognizeOnceAsync()` a Model objektum átadásával. Ez elindít egy kulcsszó-felismerési munkamenetet, amely addig tart, amíg el nem ismeri a kulcsszót. Ezért ezt a kialakítási mintát általában többszálas alkalmazásokban vagy használati esetekben érdemes használni, ahol előfordulhat, hogy a rendszer határozatlan ideig várakozik.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> Az itt látható példa a helyi kulcsszavas felismerést használja, mert nincs szükség `SpeechConfig` objektumra a hitelesítési környezethez, és nem lép kapcsolatba a háttérrel. A kulcsszavak felismerését és ellenőrzését azonban [közvetlenül háttérbeli kapcsolatok](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword)használatával is futtathatja.

### <a name="continuous-recognition"></a>Folyamatos felismerés

A Speech SDK többi osztálya támogatja a folyamatos felismerést (a beszéd-és a szándék-felismerés esetében is) a kulcsszó-felismeréssel. Ez lehetővé teszi, hogy ugyanazt a kódot használja, amelyet általában a folyamatos felismeréshez használhat, és a `.table` kulcsszavas modellre mutató fájlra hivatkozhat.

Beszéd – szöveg [esetén a folyamatos](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started-speech-to-text?tabs=script%2Cbrowser%2Cwindowsinstall&pivots=programming-language-csharp#continuous-recognition) felismerés beállításához kövesse a rövid útmutatóban látható tervezési mintát. Ezután cserélje le a-t `recognizer.StartContinuousRecognitionAsync()` a `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` -re, és adja át az `KeywordRecognitionModel` objektumnak. A folyamatos felismerés leállításához használja a kulcsszót `recognizer.StopKeywordRecognitionAsync()` a helyett `recognizer.StopContinuousRecognitionAsync()` .

A szándék-felismerés azonos mintát használ a [`StartKeywordRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) és a [`StopKeywordRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) függvényekkel.