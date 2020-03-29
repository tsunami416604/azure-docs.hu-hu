---
title: Kifejezéslisták – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy miként biztosíthat a `PhraseListGrammar` beszédfelismerési szolgáltatást kifejezéslistával az objektum használatával a beszéd-szöveg felismerés imázsának javítása érdekében.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f84ea6b2b0f1e8246e1d765e54f663cd01f29b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77560847"
---
# <a name="phrase-lists-for-speech-to-text"></a>Kifejezéslisták a szöveggé bírjaként

Ha a beszédfelismerési szolgáltatást a kifejezések listájával együtt adja meg, javíthatja a beszédfelismerés pontosságát. A Kifejezéslisták kifejezéssel azonosítják a hangadatokban szereplő ismert kifejezéseket, például egy személy nevét vagy egy adott helyet.

Például, ha van egy parancs "Move to", és egy lehetséges cél a "Ward", hogy lehet beszélni, akkor adjunk hozzá egy bejegyzést a "Move to Ward". Egy kifejezés hozzáadása növeli annak valószínűségét, hogy amikor a hang felismeri, hogy a "Move to Ward" lesz felismerhető helyett "Move towards".

A kifejezéslistához egyetlen szó vagy teljes kifejezés adható. Az elismerés során a kifejezéslista egy bejegyzését használja, ha a teljes kifejezés pontos egyezése külön kifejezésként szerepel a hangban. Ha nem talál pontos egyezést a kifejezéssel, az elismerés nem segít.

>[!Note]
> A Kifejezéslisták kifejezéslisták jelenleg csak az angol nyelvet támogatja a szöveggé való beszédfelismeréshez.

## <a name="how-to-use-phrase-lists"></a>A kifejezéslisták használata

Az alábbi minták bemutatják, hogyan `PhraseListGrammar` lehet egy kifejezéslistát készíteni az objektum használatával.

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

::: zone pivot="programming-language-more"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> A beszédfelismerési szolgáltatás által a beszédfelismeréshez használt kifejezéslisták maximális száma 1024 kifejezés.

A kifejezéshez társított kifejezéseket `PhraseListGrammar` a clear() hívással is törölheti.

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

::: zone pivot="programming-language-more"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> Az `PhraseListGrammar` objektum módosításai a következő felismeréskor vagy a beszédfelismerési szolgáltatáshoz való újracsatlakozást követően lépnek érvénybe.

## <a name="next-steps"></a>További lépések

* [BeszédSDK referenciadokumentáció](speech-sdk.md)
