---
title: Kifejezések listája – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan biztosíthatja a beszédfelismerési szolgáltatásokat egy kifejezési listával az objektum használatával a `PhraseListGrammar` beszédfelismerési eredmények szövegének felismeréséhez.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 0e552d502184d1b537263c2c1f6b2a8562cdf791
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562787"
---
# <a name="phrase-lists-for-speech-to-text"></a>A beszédfelismerés szövegéhez tartozó kifejezések listája

Ha a beszédfelismerési szolgáltatásokat egy kifejezési listával látja el, akkor javíthatja a beszédfelismerés pontosságát. A kifejezések listája a hangadatokban található ismert kifejezések azonosítására szolgál, például egy személy nevéhez vagy egy adott helyhez.

Ha például a "Move to" parancs és a "Ward" lehetséges célpontja látható, akkor hozzáadhat egy "Áthelyezés a kórterembe" bejegyzést. Egy kifejezés hozzáadásával nő a valószínűsége annak, hogy ha a Hangfelismerés felismerte, hogy az "Áthelyezés a kórterembe" lehetőség fel lesz Ismerve az "áthelyezés felé" helyett.

Az egyes szavak vagy teljes kifejezések hozzáadhatók egy kifejezési listához. Az elismerés során a rendszer egy kifejezési listán szereplő bejegyzést használ, ha pontos egyezést tartalmaz a hang. Az előző példára építve, ha a kifejezés lista "Áthelyezés a kórterembe" értékre mutat, és a rögzített kifejezés "lassan halad a középpontban", akkor a felismerési eredmény "Move to lassan" lesz.

>[!Note]
> A kifejezések listája jelenleg csak az angol nyelvet támogatja a beszédfelismerés és a szöveg között.

## <a name="how-to-use-phrase-lists"></a>Kifejezések Listájainak használata

Az alábbi példák bemutatják, hogyan hozhat létre egy kifejezés- `PhraseListGrammar` listát az objektum használatával.

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> A beszédfelismerési szolgáltatás által az 1024 mondatok egyeztetéséhez használt kifejezések maximális száma.

A `PhraseListGrammar` Clear () metódus meghívásával törölheti is a hozzájuk társított kifejezéseket.

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> `PhraseListGrammar` Az objektum módosításai hatással lesznek a következő felismerésre, vagy a beszédfelismerési szolgáltatásokhoz való Újrakapcsolódás után.

## <a name="next-steps"></a>További lépések

* [A Speech SDK dokumentációja](speech-sdk.md)
