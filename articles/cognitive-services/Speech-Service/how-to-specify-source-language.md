---
title: Beszéd nyelvének megadása szöveghez
titleSuffix: Azure Cognitive Services
description: A Speech SDK lehetővé teszi, hogy a beszédfelismerés szövegre konvertálásakor megadja a forrás nyelvét. Ez a cikk azt ismerteti, hogyan használható a FromConfig és a SourceLanguageConfig metódus, hogy a beszédfelismerési szolgáltatás Ismerje a forrás nyelvét, és adjon meg egy egyéni modell célját.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 07ca4c519a04910e7e126d3481afb17d2b5dc350
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977719"
---
# <a name="specify-source-language-for-speech-to-text"></a>Forrás nyelvének megadása beszédhez szövegként

Ebből a cikkből megtudhatja, hogyan határozhatja meg a beszédfelismeréshez készült Speech SDK által átadott hangbemenet forrásának nyelvét. Emellett például a kód megadásával egyéni beszédfelismerési modellt is megadhat a jobb felismeréshez.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>A forrás nyelvének meghatározása a C-ban #

Ebben a példában a forrás nyelve explicit módon paraméterként van megadva a konstrukció `SpeechRecognizer` használatával.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Ebben a példában a forrás nyelvét kell megadnia a következővel: `SourceLanguageConfig`. Ezt követően a `sourceLanguageConfig` rendszer paraméterként adja át a `SpeechRecognizer` értékét a létrehozáshoz.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Ebben a példában a forrás nyelvét és az egyéni végpontot a `SourceLanguageConfig`használatával biztosítjuk. Ezt követően a `sourceLanguageConfig` rendszer paraméterként adja át a `SpeechRecognizer` értékét a létrehozáshoz.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`a `EndpointId` és a set metódusok elavultak `SpeechConfig` a C# osztályból. A módszerek használata nem ajánlott, és nem használható az a `SpeechRecognizer`összeállításakor.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Forrás nyelvének meghatározása C++ nyelven

Ebben a példában a forrás nyelvét explicit módon kell megadni paraméterként a `FromConfig` metódus használatával.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

Ebben a példában a forrás nyelvét kell megadnia a következővel: `SourceLanguageConfig`. Ezt követően a `sourceLanguageConfig` rendszer paraméterként adja át a `FromConfig` értékét a létrehozásakor `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

Ebben a példában a forrás nyelvét és az egyéni végpontot a `SourceLanguageConfig`használatával biztosítjuk. A `sourceLanguageConfig` a (z) paraméterként `FromConfig` lesz átadva `recognizer`a következő létrehozásakor:.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`és `SetEndpointId` elavult metódusok a C++ és `SpeechConfig` a Java osztályból. A módszerek használata nem ajánlott, és nem használható az a `SpeechRecognizer`összeállításakor.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Forrás nyelvének meghatározása Java-ban

Ebben a példában a forrás nyelvét explicit módon kell megadnia új `SpeechRecognizer`létrehozásakor.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Ebben a példában a forrás nyelvét kell megadnia a következővel: `SourceLanguageConfig`. Ezt követően a `sourceLanguageConfig` rendszer paraméterként adja át a függvényt új `SpeechRecognizer`létrehozásakor.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Ebben a példában a forrás nyelvét és az egyéni végpontot a `SourceLanguageConfig`használatával biztosítjuk. Ezt követően a `sourceLanguageConfig` rendszer paraméterként adja át a függvényt új `SpeechRecognizer`létrehozásakor.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`és `setEndpointId` elavult metódusok a C++ és `SpeechConfig` a Java osztályból. A módszerek használata nem ajánlott, és nem használható az a `SpeechRecognizer`összeállításakor.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>A forrás nyelvének meghatározása a Pythonban

Ebben a példában a forrás nyelve explicit módon paraméterként van megadva a konstrukció `SpeechRecognizer` használatával.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

Ebben a példában a forrás nyelvét kell megadnia a következővel: `SourceLanguageConfig`. Ezt követően a `SourceLanguageConfig` rendszer paraméterként adja át a `SpeechRecognizer` értékét a létrehozáshoz.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

Ebben a példában a forrás nyelvét és az egyéni végpontot a `SourceLanguageConfig`használatával biztosítjuk. Ezt követően a `SourceLanguageConfig` rendszer paraméterként adja át a `SpeechRecognizer` értékét a létrehozáshoz.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`a `endpoint_id` és a tulajdonságok elavultak a `SpeechConfig` Python osztályból. Ezeknek a tulajdonságoknak a használata nem ajánlott, és nem használható az a `SpeechRecognizer`összeállításakor.

::: zone-end

::: zone pivot="programming-language-javascript"

## <a name="how-to-specify-source-language-in-javascript"></a>Forrás nyelvének meghatározása a JavaScriptben

Első lépésként hozzon létre egy `SpeechConfig`:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Ezután adja meg a hang forrásának nyelvét a `speechRecognitionLanguage`következővel:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Ha egyéni modellt használ az elismeréshez, megadhatja a végpontot a következővel `endpointId`:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```
::: zone-end

::: zone pivot="programming-language-objectivec"

## <a name="how-to-specify-source-language-in-objective-c"></a>A forrás nyelvének meghatározása a Objective-C-ben

Első lépésként hozzon létre egy `speechConfig`:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Ezután adja meg a hang forrásának nyelvét a `speechRecognitionLanguage`következővel:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Ha egyéni modellt használ az elismeréshez, megadhatja a végpontot a következővel `endpointId`:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Lásd még

* A támogatott nyelvek és területi beállítások listájáért lásd: [nyelvi támogatás](language-support.md).

## <a name="next-steps"></a>További lépések

* [A Speech SDK dokumentációja](speech-sdk.md)
