---
title: A beszédfelismerés automatikus nyelvfelismerés használata szövegre
titleSuffix: Azure Cognitive Services
description: A Speech SDK támogatja a beszédfelismerés automatikus nyelvi észlelését a szöveghez. A szolgáltatás használatakor a rendszer összehasonlítja a megadott hangot a nyelvek egy adott listájával, és a legvalószínűbb egyezést határozza meg. A visszaadott érték Ezután kiválaszthatja a szövegre való beszédfelismeréshez használt nyelvi modellt.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-seven
ms.openlocfilehash: bc438c3e606fefc10e9ffbb64c79f7167d9af062
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122049"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Beszédfelismerés automatikus nyelvfelismerés a szöveghez

Az automatikus nyelvfelismerés segítségével megállapítható, hogy a beszédfelismerési SDK milyen nagy valószínűséggel egyezik meg a megadott nyelvek listájának összehasonlításával. Ekkor a rendszer az automatikus nyelvfelismerés által visszaadott értéket használja a beszédfelismeréshez használt nyelvi modell kiválasztásához, amely pontosabb átírást biztosít. Ha szeretné megtekinteni, hogy mely nyelvek érhetők el, tekintse meg a [nyelvi támogatást](language-support.md).

Ebből a cikkből megtudhatja, hogyan használható a `AutoDetectSourceLanguageConfig` egy `SpeechRecognizer` objektum létrehozásához és az észlelt nyelv lekéréséhez.

> [!IMPORTANT]
> Ez a funkció csak a Speech SDK for C# C++ és a Java esetében érhető el.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatikus nyelvfelismerés a Speech SDK-val

Az automatikus nyelvfelismerés jelenleg csak két nyelvet tartalmaz. Tartsa szem előtt ezt a korlátozást a `AudoDetectSourceLanguageConfig` objektum építésekor. Az alábbi mintákban létre kell hoznia egy `AutoDetectSourceLanguageConfig`, majd felhasználhatja `SpeechRecognizer`létrehozásához.

> [!TIP]
> Megadhat egy egyéni modellt is, amelyet a beszéd szövegre való elvégzéséhez használhat. További információ: [Egyéni modell használata automatikus nyelvfelismerés esetén](#use-a-custom-model-for-automatic-language-detection).

Az alábbi kódrészletek azt szemléltetik, hogyan használható az automatikus nyelvfelismerés az alkalmazásokban:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromLanguages(new string[] { "en-US", "de-DE" });
using (var recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Egyéni modell használata az automatikus nyelvfelismerés

A Speech Service-modelleket használó nyelvfelismerés mellett egyéni modellt is megadhat a továbbfejlesztett felismeréshez. Ha nincs megadva egyéni modell, a szolgáltatás az alapértelmezett nyelvi modellt fogja használni.

Az alábbi kódrészletek azt szemléltetik, hogyan lehet egyéni modellt megadni a beszédfelismerési szolgáltatás hívását. Ha az észlelt nyelv `en-US`, akkor a rendszer az alapértelmezett modellt használja. Ha az észlelt nyelv `fr-FR`, akkor a rendszer az egyéni modell végpontját használja:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

## <a name="next-steps"></a>Következő lépések

- [A Speech SDK dokumentációja](speech-sdk.md)
