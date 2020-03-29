---
title: A beszédsdka memóriahasználatának nyomon követése – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédszolgáltatás SDK számos programozási nyelvet támogat a beszéd-szöveg és a szöveg-beszéd átalakításhoz, valamint a beszédfordítást. Ez a cikk az SDK-ba épített memóriakezelő eszközökről beszél.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: da5103317a2215aca68cec14ba8a0951258c9b89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456431"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>A beszédsdka memóriahasználatának nyomon követése

A beszédstabk egy natív kódbázison alapul, amely több programozási nyelvre vetítve, együttműködési rétegek sorozatán keresztül. Minden nyelvspecifikus vetület idiomatikusan helyes funkciókkal rendelkezik az objektum életciklusának kezeléséhez. Emellett a beszédstab-sdk memóriakezelő eszközökkel nyomon követheti az erőforrás-használatot az objektumnaplózással és az objektumkorlátokkal. 

## <a name="how-to-read-object-logs"></a>Objektumnaplók olvasása

Ha [a beszédbeszéd SDK-naplózása engedélyezve van,](how-to-use-logging.md)a program nyomkövetési címkéket bocsát ki a korábbi objektummegfigyelés engedélyezéséhez. Ezek a címkék a következők: 

* `TrackHandle` vagy `StopTracking` 
* Az objektum típusa
* Az objektum típusát és az aktuálisan nyomon követett objektumok aktuális száma.

Itt egy minta napló: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Figyelmeztetési küszöbérték beállítása

Lehetősége van figyelmeztető küszöbérték létrehozására, és ha túllépi ezt a küszöbértéket (feltéve, hogy a naplózás engedélyezve van), a rendszer figyelmeztető üzenetet naplóz. A figyelmeztető üzenet az összes létező objektum ról és azok számáról tartalmaz egy memóriaképet. Ez az információ felhasználható a problémák jobb megértéséhez. 

A figyelmeztetési küszöbérték engedélyezéséhez azt meg `SpeechConfig` kell adni egy objektumon. Új felismerő létrehozásakor ez az objektum be van jelölve. A következő példákban tegyük fel, hogy létrehozott `SpeechConfig` `config`egy példányt a hívott:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> A tulajdonság alapértelmezett értéke 10 000.

## <a name="set-an-error-threshold"></a>Hibaküszöbérték beállítása 

A beszédsdka használatával beállíthatja az adott időpontban engedélyezett objektumok maximális számát. Ha ez a beállítás engedélyezve van, a maximális szám elérésekor az új felismerő objektumok létrehozására tett kísérletek sikertelenek lesznek. A meglévő objektumok továbbra is működni fognak.

Íme egy mintahiba:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

A hibaküszöbérték engedélyezéséhez meg kell adni `SpeechConfig` egy objektumon. Új felismerő létrehozásakor ez az objektum be van jelölve. A következő példákban tegyük fel, hogy létrehozott `SpeechConfig` `config`egy példányt a hívott:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> A tulajdonság alapértelmezett értéke egy `size_t` adattípus platformspecifikus maximális értéke. Egy tipikus felismerés 7 és 10 belső objektum között fog elhasználódni.

## <a name="next-steps"></a>További lépések

* [A Beszédfelismerési szolgáltatás próba-előfizetésének beolvasása](get-started.md)
* [A beszéd felismerése mikrofonnal](quickstarts/speech-to-text-from-microphone.md)