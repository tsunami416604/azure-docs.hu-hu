---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: 040ffea69f76255dcb1bfc6787cad45a95baa904
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305859"
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
> Az itt látható példa a helyi kulcsszavas felismerést használja, mert nincs szükség `SpeechConfig` objektumra a hitelesítési környezethez, és nem lép kapcsolatba a háttérrel. A kulcsszó-felismerést és az ellenőrzést azonban [folyamatos háttérbeli kapcsolatok](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword)használatával is futtathatja.