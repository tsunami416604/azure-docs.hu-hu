---
title: Kifejezések listája – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan biztosíthatja a beszédfelismerési szolgáltatást egy kifejezési listával az objektum használatával a `PhraseListGrammar` beszédfelismerési eredmények szövegének növeléséhez.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: dapine
zone_pivot_groups: programming-languages-speech-services-one-nomore-no-go
ms.openlocfilehash: d6418a863e8af75e4f2f063d9e6151e19a0e7e6e
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977583"
---
# <a name="phrase-lists-for-speech-to-text"></a>A beszédfelismerés szövegéhez tartozó kifejezések listája

Ha a beszédfelismerési szolgáltatást kifejezésekkel látja el, akkor javíthatja a beszédfelismerés pontosságát. A kifejezések listája a hangadatokban található ismert kifejezések azonosítására szolgál, például egy személy nevéhez vagy egy adott helyhez.

Ha például a "Move to" parancs és a "Ward" lehetséges célpontja látható, akkor hozzáadhat egy "Áthelyezés a kórterembe" bejegyzést. Egy kifejezés hozzáadásával nő a valószínűsége annak, hogy ha a Hangfelismerés felismerte, hogy az "Áthelyezés a kórterembe" lehetőség fel lesz Ismerve az "áthelyezés felé" helyett.

Az egyes szavak vagy teljes kifejezések hozzáadhatók egy kifejezési listához. Az elismerés során a rendszer egy kifejezési listán szereplő bejegyzést használ, ha a teljes kifejezés pontos egyezése szerepel a hangban, külön kifejezésként. Ha a kifejezés pontos egyezése nem található, az elismerés nem támogatott.

>[!Note]
> A kifejezések listája jelenleg csak az angol nyelvet támogatja a beszédfelismerés és a szöveg között.

## <a name="how-to-use-phrase-lists"></a>Kifejezések Listájainak használata

Az alábbi példák bemutatják, hogyan hozhat létre egy kifejezés- `PhraseListGrammar` listát az objektum használatával.

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> A beszédfelismerési szolgáltatás által az 1024 mondatok egyeztetéséhez használt kifejezések maximális száma.

A `PhraseListGrammar` Clear () metódus meghívásával törölheti is a hozzájuk társított kifejezéseket.

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> Az `PhraseListGrammar` objektum módosításai érvénybe lépnek a következő felismeréskor, vagy a beszédfelismerési szolgáltatáshoz való Újrakapcsolódás után.

## <a name="next-steps"></a>További lépések

* [A Speech SDK dokumentációja](speech-sdk.md)
