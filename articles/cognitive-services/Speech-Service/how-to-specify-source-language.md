---
title: A beszéd-szöveg forrásnyelvének megadása
titleSuffix: Azure Cognitive Services
description: A beszédsdk lehetővé teszi a forrásnyelv megadását a beszéd szöveggé alakításakor. Ez a cikk azt ismerteti, hogy miként használhatja a FromConfig és a SourceLanguageConfig metódusokat, hogy a beszédfelismerési szolgáltatás ismerje a forrásnyelvet, és egyéni modellcélt biztosítson.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f0723534d9d2187593cb73f058ffea62473b80a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235979"
---
# <a name="specify-source-language-for-speech-to-text"></a>A beszéd-szöveg forrásnyelvének megadása

Ebből a cikkből megtudhatja, hogyan adhatja meg a beszédfelismerési SDK-nak átadott hangbemenet forrásnyelvét. Emellett példakód áll rendelkezésre, hogy adjon meg egy egyéni beszédmodell továbbfejlesztett felismerés.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Forrásnyelv megadása C-ben #

Ebben a példában a forrásnyelv explicit módon `SpeechRecognizer` van megadva a konstruktidust használó paraméterként.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Ebben a példában a forrásnyelv a használatával `SourceLanguageConfig`érhető el. Ezután `sourceLanguageConfig` a kerül átadásra, `SpeechRecognizer` mint egy paraméter tépíteni.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Ebben a példában a forrásnyelv és `SourceLanguageConfig`az egyéni végpont a használatával van megadva. Ezután `sourceLanguageConfig` a kerül átadásra, `SpeechRecognizer` mint egy paraméter tépíteni.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`és `EndpointId` a beállított metódusok elavultak a `SpeechConfig` C#-ban lévő osztályból. Ezeknek a módszereknek a használata nem ajánlott, és `SpeechRecognizer`nem használható a .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Forrásnyelv megadása C++ nyelven

Ebben a példában a forrásnyelv explicit módon `FromConfig` van megadva paraméterként a metódus használatával.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

Ebben a példában a forrásnyelv a használatával `SourceLanguageConfig`érhető el. Ezután `sourceLanguageConfig` a paraméterként kerül `FromConfig` átadásra `recognizer`a létrehozásakor a .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

Ebben a példában a forrásnyelv és `SourceLanguageConfig`az egyéni végpont a használatával van megadva. A `sourceLanguageConfig` paraméter a . `FromConfig` `recognizer`

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`és `SetEndpointId` elavult metódusok a `SpeechConfig` C++ és Java osztályból. Ezeknek a módszereknek a használata nem ajánlott, és `SpeechRecognizer`nem használható a .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Forrásnyelv megadása Java nyelven

Ebben a példában a forrásnyelv explicit módon `SpeechRecognizer`jelenik meg egy új létrehozásakor.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Ebben a példában a forrásnyelv a használatával `SourceLanguageConfig`érhető el. Ezután `sourceLanguageConfig` a paraméterként kerül átadásra `SpeechRecognizer`egy új létrehozásakor.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Ebben a példában a forrásnyelv és `SourceLanguageConfig`az egyéni végpont a használatával van megadva. Ezután `sourceLanguageConfig` a paraméterként kerül átadásra `SpeechRecognizer`egy új létrehozásakor.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`és `setEndpointId` elavult metódusok a `SpeechConfig` C++ és Java osztályból. Ezeknek a módszereknek a használata nem ajánlott, és `SpeechRecognizer`nem használható a .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Forrásnyelv megadása pythonban

Ebben a példában a forrásnyelv explicit módon `SpeechRecognizer` van megadva a konstruktidust használó paraméterként.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

Ebben a példában a forrásnyelv a használatával `SourceLanguageConfig`érhető el. Ezután `SourceLanguageConfig` a kerül átadásra, `SpeechRecognizer` mint egy paraméter tépíteni.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

Ebben a példában a forrásnyelv és `SourceLanguageConfig`az egyéni végpont a használatával van megadva. Ezután `SourceLanguageConfig` a kerül átadásra, `SpeechRecognizer` mint egy paraméter tépíteni.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`és `endpoint_id` a tulajdonságok elavultak `SpeechConfig` az osztály python.and properties are deprecated from the class in Python. Ezeknek a tulajdonságoknak a használata nem ajánlott, és `SpeechRecognizer`nem használható a .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Forrásnyelv megadása Javascript ben

Az első lépés az, hogy hozzon létre egy: `SpeechConfig`

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Ezután adja meg a hang `speechRecognitionLanguage`forrásnyelvét a következővel:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Ha egyéni modellt használ a felismeréshez, megadhatja `endpointId`a végpontot a következővel:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Forrásnyelv megadása a C célkitűzésben

Az első lépés az, hogy hozzon létre egy: `speechConfig`

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Ezután adja meg a hang `speechRecognitionLanguage`forrásnyelvét a következővel:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Ha egyéni modellt használ a felismeréshez, megadhatja `endpointId`a végpontot a következővel:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Lásd még

* A beszéd-szöveghez szükséges támogatott nyelvek és területi beállítások listáját a [Nyelvi támogatás című témakörben tetszése idáig című témakörben található.](language-support.md)

## <a name="next-steps"></a>További lépések

* [BeszédSDK referenciadokumentáció](speech-sdk.md)
