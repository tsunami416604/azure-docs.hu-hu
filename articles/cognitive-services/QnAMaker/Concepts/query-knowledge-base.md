---
title: A tudásbázis lekérdezése - QnA Maker
description: A tudásbázist közzé kell tenni. A közzététel után a tudásbázis lekérdezése a futásidejű előrejelzési végpont a generateAnswer API használatával.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220720"
---
# <a name="query-the-knowledge-base-for-answers"></a>A tudásbázis lekérdezése válaszokért

A tudásbázist közzé kell tenni. A közzététel után a tudásbázis lekérdezése a futásidejű előrejelzési végpont a generateAnswer API használatával. A lekérdezés tartalmazza a kérdés szövegét és egyéb beállításokat, hogy a QnA Maker a válasznak a lehető legjobb egyezést válassza ki.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Hogyan dolgozza fel a QnA Maker a felhasználói lekérdezést a legjobb válasz kiválasztásához?

A betanított és [közzétett](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker tudásbázis egy bottól vagy más ügyfélalkalmazástól felhasználói lekérdezést kap a [GenerateAnswer API-n.](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage) Az alábbi ábra a felhasználói lekérdezés fogadásának folyamatát mutatja be.

![A felhasználói lekérdezés rangsorolási modellfolyamata](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Ranker folyamat

A folyamatot az alábbi táblázat ismerteti.

|Lépés|Cél|
|--|--|
|1|Az ügyfélalkalmazás elküldi a felhasználói lekérdezést a [GenerateAnswer API-nak.](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)|
|2|A QnA Maker nyelvfelismeréssel, helyesírás-ellenőrzőkkel és szómegszakítókkal dolgozza fel a felhasználói lekérdezést.|
|3|Ez az előfeldolgozás a felhasználói lekérdezés módosításához történik a legjobb keresési eredmények érdekében.|
|4|Ezt a módosított lekérdezést a rendszer elküldi egy `top` Azure Cognitive Search Index, amely megkapja az eredmények számát. Ha a helyes válasz nem ezekben az `top` eredményekben, növelje az értékét kissé. Általában a lekérdezések 90%-ában a művek értéke `top` 10.|
|5|A QnA Maker szintaktikai és szemantikai alapú featurizációval határozza meg a felhasználói lekérdezés és a lekért QnA-eredmények közötti hasonlóságot.|
|6|A gép megtanult ranker modell a különböző funkciók, az 5.|
|7|Az új eredmények rangsorolt sorrendben kerülnek vissza az ügyfélalkalmazásba.|
|||

A használt funkciók közé tartozik, de nem korlátozódik a szószintű szemantikára, a corpus kifejezésszintű fontosságára és a mélytanult szemantikai modellekre a két szöveges karakterlánc hasonlóságának és relevanciájának meghatározásához.

## <a name="http-request-and-response-with-endpoint"></a>HTTP-kérelem és válasz a végponttal
A tudásbázis közzétételekor a szolgáltatás létrehoz egy REST-alapú HTTP-végpontot, amely integrálható az alkalmazásba, általában egy csevegőrobotba.

### <a name="the-user-query-request-to-generate-an-answer"></a>Válasz létrehozására irányuló felhasználói lekérdezési kérelem

A felhasználói lekérdezés az a kérdés, amelyet a `How do I add a collaborator to my app?`végfelhasználó a tudásbázisról kérdez, például . A lekérdezés gyakran természetes nyelvű formátumban vagy néhány olyan kulcsszóban `help with collaborators`van, amely a kérdést képviseli, például . A lekérdezést a rendszer az ügyfélalkalmazásban http-kérelemből küldi el a tudásbázisnak.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

A választ olyan tulajdonságok beállításával szabályozhatja, mint a [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [a top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)és a [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Használja [beszélgetési környezet](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) [ben multi-turn funkcióval](../how-to/multiturn-conversation.md) tartani a beszélgetést fog finomítani a kérdéseket és válaszokat, hogy megtalálják a helyes és végső választ.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>A válasz egy hívásból érkező válasz, hogy

A HTTP-válasz a tudásbázisból beolvasott válasz, amely egy adott felhasználói lekérdezés legjobb egyezésén alapul. A válasz tartalmazza a választ és az előrejelzési pontszámot. Ha egynél több felső választ `top` kért az ingatlannal, akkor egynél több felső választ kap, mindegyik nek egy pontszámot.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megbízhatósági pontszám](./confidence-score.md)
