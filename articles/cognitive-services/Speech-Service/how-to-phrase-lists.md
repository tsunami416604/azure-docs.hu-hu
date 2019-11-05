---
title: Kifejezések listája – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan biztosíthatja a beszédfelismerési szolgáltatásokat egy kifejezési listával a `PhraseListGrammar` objektum használatával a beszédfelismerési eredmények javítása érdekében.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 61d3e4d2de6b8707ee7433815f8002e5d5e3e3d6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464543"
---
# <a name="phrase-lists-for-speech-to-text"></a>A beszédfelismerés szövegéhez tartozó kifejezések listája

Ha a beszédfelismerési szolgáltatásokat a mondatok listájával biztosítja, akkor javíthatja a beszédfelismerés pontosságát. A kifejezések listája a hangadatokban található ismert kifejezések azonosítására szolgál, például egy személy nevéhez vagy egy adott helyhez.

Ha például a "Move to" parancs és a "Ward" lehetséges célpontja látható, akkor hozzáadhat egy "Áthelyezés a kórterembe" bejegyzést. Egy kifejezés hozzáadásával nő a valószínűsége annak, hogy ha a Hangfelismerés felismerte, hogy az "Áthelyezés a kórterembe" lehetőség fel lesz Ismerve az "áthelyezés felé" helyett.

Az egyes szavak vagy teljes kifejezések hozzáadhatók egy kifejezési listához. Az elismerés során a rendszer egy kifejezési listán szereplő bejegyzést használ, ha pontos egyezést tartalmaz a hang. Az előző példára építve, ha a kifejezések listája a "Move to Ward" szöveget tartalmazza, a rögzített hang pedig a "költözés iránya" és az "Áthelyezés a kórterembe" értékre hasonlít

>[!Note]
> A kifejezések listája jelenleg csak az angol nyelvet támogatja a beszédfelismerés és a szöveg között.

## <a name="how-to-use-phrase-lists"></a>Kifejezések Listájainak használata

Az alábbi példák bemutatják, hogyan hozhat létre egy kifejezés-listát a `PhraseListGrammar` objektum használatával.

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

Törölheti a `PhraseListGrammar` társított kifejezéseket is a Clear () metódus meghívásával.

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
> Egy `PhraseListGrammar` objektum módosításai a következő felismerés után lépnek érvénybe, vagy a beszédfelismerési szolgáltatásokhoz való Újrakapcsolódás után következnek be.

## <a name="next-steps"></a>További lépések

* [A Speech SDK dokumentációja](speech-sdk.md)
