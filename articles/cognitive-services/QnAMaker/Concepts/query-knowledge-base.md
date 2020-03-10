---
title: A Tudásbázis lekérdezése – QnA Maker
description: A tudásbázist közzé kell tenni. A közzététel után a tudásbázist a generateAnswer API használatával kérdezi le a futásidejű előrejelzési végponton.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389280"
---
# <a name="query-the-knowledge-base-for-answers"></a>A válaszok tudásbázisának lekérdezése

A tudásbázist közzé kell tenni. A közzététel után a tudásbázist a generateAnswer API használatával kérdezi le a futásidejű előrejelzési végponton. A lekérdezés tartalmazza a kérdés szövegét és az egyéb beállításokat, hogy QnA Maker válassza ki a lehető legjobb egyezést a válaszhoz.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Hogyan dolgozza fel QnA Maker a felhasználói lekérdezéseket a legjobb válasz kiválasztásához

A betanított és [közzétett](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker Tudásbázis felhasználói lekérdezést kap egy robottól vagy más ügyfélalkalmazástól a [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)-ban. A következő ábra a felhasználói lekérdezés fogadásának folyamatát szemlélteti.

![A felhasználói lekérdezés rangsorolási modelljének folyamata](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Rangsorolási folyamat

A folyamatot az alábbi táblázat ismerteti.

|Lépés|Cél|
|--|--|
|1|Az ügyfélalkalmazás elküldi a felhasználói lekérdezést a [GENERATEANSWER API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)-nak.|
|2|QnA Maker elődolgozza a felhasználói lekérdezést a nyelvfelismerés, a helyesírás-ellenőrző és a Word-megszakítók használatával.|
|3|Ez az előfeldolgozás a legjobb keresési eredmények felhasználói lekérdezésének megváltoztatásához szükséges.|
|4|Ezt a módosított lekérdezést egy Azure Cognitive Search indexbe küldik, amely az eredmények `top` számát fogadja. Ha a helyes válasz nem szerepel ezekben az eredményekben, növelje `top` kis értékét. Általában a `top` 10 értéke a lekérdezések 90%-ában működik.|
|5|A QnA Maker szintaktikai és szemantikai alapú featurization használatával határozza meg a felhasználói lekérdezés és a beolvasott QnA eredményei közötti hasonlóságot.|
|6|A géppel megtanult Ranger-modell az 5. lépés különböző funkcióit használja a megbízhatósági pontszámok és az új rangsorolási sorrend meghatározásához.|
|7|Az új eredményeket rangsorolt sorrendben adja vissza az ügyfélalkalmazás.|
|||

A használatban lévő funkciók közé tartozik például a Word-szintű szemantika, a kifejezés szintű fontosság a corpusban, és a mélyebben megtanult szemantikai modellek határozzák meg a hasonlóságot és a megfelelést két szöveges karakterlánc között.

## <a name="http-request-and-response-with-endpoint"></a>HTTP-kérelem és-válasz végponttal
A Tudásbázis közzétételekor a szolgáltatás egy REST-alapú HTTP-végpontot hoz létre, amely integrálható az alkalmazásba, és általában egy csevegési robot.

### <a name="the-user-query-request-to-generate-an-answer"></a>A felhasználó lekérdezési kérelme a válasz létrehozásához

A felhasználó lekérdezése az a kérdés, hogy a végfelhasználó megkérdezi a tudásbázist, például `How do I add a collaborator to my app?`. A lekérdezés gyakran természetes nyelvi formátumban van, vagy néhány kulcsszó, amely a kérdést jelöli, például `help with collaborators`. A rendszer elküldi a lekérdezést az ügyfélalkalmazás HTTP-kérelme alapján.

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

A választ a tulajdonságok, például a [scoreThreshold](./confidence-score.md#choose-a-score-threshold), a [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)és a [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)tulajdonságainak beállításával szabályozhatja.

A beszélgetési [kontextusban](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) [többfordulatos funkciókkal](../how-to/multiturn-conversation.md) megtarthatja a beszélgetést a kérdések és válaszok pontosításával, hogy megtalálja a megfelelő és a végső választ.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Válasz meghívása a válasz létrehozásához

A HTTP-válasz a Tudásbázisból beolvasott válasz, amely egy adott felhasználói lekérdezés legjobb egyezése alapján történik. A válasz tartalmazza a választ és az előrejelzési pontszámot. Ha a `top` tulajdonsággal egynél több legfelső szintű választ kér, több legfelső szintű választ kap, amelyek mindegyike egy pontszámmal rendelkezik.

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


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Megbízhatósági pontszám](./confidence-score.md)
