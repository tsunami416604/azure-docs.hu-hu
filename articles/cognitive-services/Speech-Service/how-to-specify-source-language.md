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
ms.date: 10/26/2019
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 94b8fb026b61b52e8096cf54e1db30a6c260c04b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109956"
---
# <a name="specify-source-language-for-speech-to-text"></a>Forrás nyelvének megadása beszédhez szövegként

Ebből a cikkből megtudhatja, hogyan határozhatja meg a beszédfelismeréshez készült Speech SDK által átadott hangbemenet forrásának nyelvét. Emellett például a kód megadásával egyéni beszédfelismerési modellt is megadhat a jobb felismeréshez.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Forrás nyelvének meghatározása a következőben:C#

Első lépésként hozzon létre egy `SpeechConfig`:

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Ezután adja meg a hang forrásának nyelvét a `SpeechRecognitionLanguage`:

```csharp
speechConfig.SpeechRecognitionLanguage = "de-DE";
```

Ha egyéni modellt használ az elismeréshez, megadhatja a végpontot `EndpointId`:

```csharp
speechConfig.EndpointId = "The Endpoint ID for your custom model.";
```

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Forrás nyelvének meghatározása a következőben:C++

Ebben a példában a forrás nyelve explicit módon paraméterként van megadva a `FromConfig` metódus használatával.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

Ebben a példában a forrás nyelvét `SourceLanguageConfig`használatával kell megadnia. Ezt követően a `sourceLanguageConfig` a `recognizer`létrehozásakor `FromConfig` paraméterként lesz átadva.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

Ebben a példában a forrás nyelvét és az egyéni végpontot a `SourceLanguageConfig`használatával biztosítjuk. A `sourceLanguageConfig` a `recognizer`létrehozásakor `FromConfig` paraméterként lesz átadva.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> a `SetSpeechRecognitionLanguage` és a `SetEndpointId` elavult metódusok a `SpeechConfig` osztály C++ és a javában. A módszerek használata nem ajánlott, és nem használható `SpeechRecognizer`összeállításakor.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Forrás nyelvének meghatározása Java-ban

Ebben a példában a forrás nyelvét explicit módon kell megadnia új `SpeechRecognizer`létrehozásakor.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Ebben a példában a forrás nyelvét `SourceLanguageConfig`használatával kell megadnia. Ezt követően a `sourceLanguageConfig` új `SpeechRecognizer`létrehozásakor paraméterként lesz átadva.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Ebben a példában a forrás nyelvét és az egyéni végpontot a `SourceLanguageConfig`használatával biztosítjuk. Ezt követően a `sourceLanguageConfig` új `SpeechRecognizer`létrehozásakor paraméterként lesz átadva.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> a `setSpeechRecognitionLanguage` és a `setEndpointId` elavult metódusok a `SpeechConfig` osztály C++ és a javában. A módszerek használata nem ajánlott, és nem használható `SpeechRecognizer`összeállításakor.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>A forrás nyelvének meghatározása a Pythonban

Első lépésként hozzon létre egy `speech_config`:

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

Ezután adja meg a hang forrásának nyelvét a `speech_recognition_language`:

```Python
speech_config.speech_recognition_language="de-DE"
```

Ha egyéni modellt használ az elismeréshez, megadhatja a végpontot `endpoint_id`:

```Python
speech_config.endpoint_id = "The Endpoint ID for your custom model."
```

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Forrás nyelvének meghatározása a JavaScriptben

Első lépésként hozzon létre egy `SpeechConfig`:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Ezután adja meg a hang forrásának nyelvét a `speechRecognitionLanguage`:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Ha egyéni modellt használ az elismeréshez, megadhatja a végpontot `endpointId`:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>A forrás nyelvének meghatározása a Objective-C-ben

Első lépésként hozzon létre egy `speechConfig`:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Ezután adja meg a hang forrásának nyelvét a `speechRecognitionLanguage`:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Ha egyéni modellt használ az elismeréshez, megadhatja a végpontot `endpointId`:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Lásd még

* A támogatott nyelvek és területi beállítások listájáért lásd: [nyelvi támogatás](language-support.md).

## <a name="next-steps"></a>További lépések

* [A Speech SDK dokumentációja](speech-sdk.md)
