---
title: A kifejezés listákat – beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan a beszédszolgáltatások egy kifejezést lista használatával adja meg a `PhraseListGrammar` objektum hang-szöveg transzformációs felismerési eredményeket javítása érdekében.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 5/02/2019
ms.author: rhurey
ms.openlocfilehash: 576d3c4a70c8870a31bc352b9f7723d2c2e69854
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025805"
---
# <a name="phrase-lists-for-speech-to-text"></a>Hang-szöveg transzformációs listák kifejezés

Azáltal, hogy a beszédszolgáltatások kifejezések listájával, beszédfelismerés pontosságának javítása érdekében. A kifejezés listák hang adatok, például egy személy neve vagy egy adott helyen az ismert kifejezések azonosítására szolgál.

Például, ha egy parancs "-re", "Ward", előfordulhat, hogy beszélt, lehetséges célja a "Ward való áthelyezése" bejegyzést adhat hozzá. Kifejezés hozzáadása növeli a valószínűsége annak, amikor a hangot felismer, hogy "Ward való áthelyezése" helyett "Áthelyezési felé" ismerhető fel.

Önálló szavak vagy kifejezések teljes listáját a kifejezés lehet hozzáadni. Során recognition található meg egy kifejezést használja, ha pontosan egyezik a hanganyag szerepel. Ha a kifejezés a lista tartalmazza a "Ward való áthelyezése" és a rögzített kifejezést az előző példa készítése "Áthelyezése felé lassan", majd a felismerés eredményét lesz az "Ugrás Ward lassan".

## <a name="how-to-use-phrase-lists"></a>Felsorolja a kifejezés használata

Az alábbi példák bemutatják, hogyan hozhat létre egy kifejezést lista használatával a `PhraseListGrammar` objektum.

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
> A kifejezés tartalmazza a beszédfelismerési szolgáltatás által használt megfelelő beszédfelismerési maximális száma: 1024.

A társított kifejezéseket is törölheti a `PhraseListGrammar` hívó clear() szerint.

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
> Módosítja egy `PhraseListGrammar` végezze el a hatása a következő felismerése vagy a visszacsatlakozást a Speech Services a következő objektum.

## <a name="next-steps"></a>További lépések

* [Beszéd SDK dokumentációja](speech-sdk.md)
