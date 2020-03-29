---
title: Az automatikus nyelvfelismerés használata beszéd-szövegként
titleSuffix: Azure Cognitive Services
description: A beszédsdk támogatja az automatikus nyelvfelismerést a beszéd-szöveg hez. A szolgáltatás használatakor a rendszer összehasonlítja a megadott hanganyagokat a megadott nyelvek listájával, és a legvalószínűbb egyezést határozza meg. A visszaadott érték segítségével kiválaszthatja a beszéd-szöveg nyelvmodellt.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5592fc3e50db892c6abb09fc2516b8e1c03f0f03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239603"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Automatikus nyelvfelismerés a beszéd-szöveg hez

Az automatikus nyelvfelismerés segítségével határozható meg a beszédfelismerési SDK-nak átadott hang legvalószínűbb egyezése a megadott nyelvek listájával összehasonlítva. Az automatikus nyelvfelismerés által visszaadott érték ezután a beszéd-szöveg nyelvmodelljének kiválasztására szolgál, pontosabb átírást biztosítva. Az elérhető nyelvek megtekintéséről a Nyelvi támogatás ( Nyelvi támogatás ) [(Nyelvi támogatás) (Nyelvi támogatás) (Nyelvi támogatás) (Nyelvi támogatás) (Nyelvi támogatás) (Nyelvi támogatás](language-support.md)

Ebben a cikkben megtudhatja, `AutoDetectSourceLanguageConfig` hogyan hozhat `SpeechRecognizer` létre egy objektumot, és hogyan kérheti be az észlelt nyelvet.

> [!IMPORTANT]
> Ez a funkció csak a Beszéd sDK C#, C++, Java és Python esetén érhető el.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatikus nyelvfelismerés a beszédfelismerési SDK-val

Az automatikus nyelvfelismerés jelenleg észlelésenként két nyelvre van korlátozva. Tartsa szem előtt ezt `AudoDetectSourceLanguageConfig` a korlátozást, amikor az objektum építése. Az alábbi mintákban hozzon `AutoDetectSourceLanguageConfig`létre egy t, `SpeechRecognizer`majd készítse el azt.

> [!TIP]
> Megadhat egy egyéni modellt is, amelyet a beszéd-szöveg végrehajtásakor kell használni. További információ: [Egyéni modell használata automatikus nyelvfelismeréshez.](#use-a-custom-model-for-automatic-language-detection)

Az alábbi kódrészletek bemutatják, hogyan használhatja az automatikus nyelvfelismerést az alkalmazásokban:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Egyéni modell használata az automatikus nyelvfelismeréshez

A beszédfelismerési szolgáltatások modelljeinek használatával történő nyelvfelismerés mellett egyéni modellt is megadhat a fokozott felismeréshez. Ha egy egyéni modell nem biztosított, a szolgáltatás az alapértelmezett nyelvi modellt fogja használni.

Az alábbi kódrészletek bemutatják, hogyan adhat meg egyéni modellt a beszédfelismerési szolgáltatás hívásában. Ha az észlelt `en-US`nyelv a , akkor az alapértelmezett modell t használja. Ha az észlelt `fr-FR`nyelv a , majd az egyéni modell végpontja használatos:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

## <a name="next-steps"></a>További lépések

- [BeszédSDK referenciadokumentáció](speech-sdk.md)
