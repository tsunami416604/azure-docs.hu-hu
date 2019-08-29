---
title: Tudásbázis – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker Tudásbázis kérdés-válasz (QnA) párok, és nem kötelező, a kérdések és válaszok párjaihoz társított metaadatokat tartalmaz.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2b3e74f337cf8f57321c3a8d94f8191fc3ebb530
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093904"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Mit jelent a QnA Maker Knowledge base?

A QnA Maker Tudásbázis kérdés-válasz (QnA) párok, és nem kötelező, a kérdések és válaszok párjaihoz társított metaadatokat tartalmaz.

## <a name="key-knowledge-base-concepts"></a>Tudásbázis fő fogalmak

* **Kérdések** -kérdés tartalmaz szöveg, amely a legjobban jelképezi a felhasználói lekérdezés. 
* **Válaszok** -választ a eredményül, amikor egy felhasználó lekérdezése egyezik a társított kérdés-válasz.  
* **Metaadatok** -metaadatait egy kérdés-válasz párt társított címkék és jelentésekként jelennek meg a kulcs-érték párokat. A metaadatok címkéi a QnA párok szűrésére és a lekérdezési egyeztetést végző készlet korlátozására használhatók.

Egy egyetlen QnA, jelöli QnA numerikus azonosító, több változatának (alternatív kérdések) kérdést, hogy az összes leképezése egy egyetlen választ tartalmaz. Emellett minden egyes pár több metaadat-mezővel is rendelkezhet hozzájuk: egy kulccsal és egy értékkel.

![A QnA Maker tudásbázisok](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Tudásbázis tartalom formátuma

Részletes tartalom, például egy Tudásbázis képes feldolgozni, QnA Maker megkísérli a tartalom átalakításához a markdown formátumhoz. Olvasási [ez](https://aka.ms/qnamaker-docs-markdown-support) blog tudni, hogy a markdown-formátumok jelentésséma a legtöbb csevegési ügyfelek.

Metaadatokat tartalmazó mezőket állnak, amelyek egy pontosvesszővel elválasztott kulcs-érték párok **(termék: aprító)** . A kulcs és az értéket csak szöveg kell lennie. A metaadat-kulcs nem tartalmazhat szóközöket. A metaadatok csak egy értéket támogatnak a kulcsok esetében.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Hogyan dolgozza fel QnA Maker a felhasználói lekérdezéseket a legjobb válasz kiválasztásához

A betanított és [közzétett](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker Tudásbázis felhasználói lekérdezést kap egy robottól vagy más ügyfélalkalmazástól a [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)-ban. A következő ábra a felhasználói lekérdezés fogadásának folyamatát szemlélteti.

![Felhasználói lekérdezés rangsorolási folyamata](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

A folyamatot az alábbi táblázat ismerteti:

|Lépés|Cél|
|--|--|
|1|Az ügyfélalkalmazás elküldi a felhasználói lekérdezést a [GENERATEANSWER API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)-nak.|
|2|A QnA-készítő a felhasználói lekérdezés előfeldolgozását a nyelvfelismerés, a helyesírás-ellenőrző és a Word-megszakítók használatával.|
|3|Ez az előfeldolgozás a legjobb keresési eredmények felhasználói lekérdezésének megváltoztatásához szükséges.|
|4|Ez a módosított lekérdezés Azure Search indexbe lesz küldve, amely az `top` eredmények számát fogadja. Ha a helyes válasz nem szerepel ezekben az eredményekben, növelje a `top` kis-és nagymértékű értéket. Általában a 10-es `top` érték a lekérdezések 90%-ában működik.|
|5|A QnA Maker speciális featurization alkalmaz, hogy meghatározza a felhasználói lekérdezéshez tartozó beolvasott Azure Search eredményeinek helyességét. |
|6|A betanított Ranger-modell az 5. lépésben a szolgáltatás pontszámát használja a Azure Search eredményeinek rangsorolásához.|
|7|Az új eredményeket rangsorolt sorrendben adja vissza az ügyfélalkalmazás.|
|||

A használatban lévő funkciók közé tartozik például a Word-szintű szemantika, a nagybetűs szint fontossága egy corpusban, és a mélyebben megtanult szemantikai modellek határozzák meg a hasonlóságot és a megfelelést két szöveges karakterlánc között.

## <a name="http-request-and-response-with-endpoint"></a>HTTP-kérelem és-válasz végponttal
A Tudásbázis közzétételekor a szolgáltatás egy REST-alapú HTTP-végpontot hoz létre , amely integrálható az alkalmazásba, és általában egy csevegési robot. 

### <a name="the-user-query-request-to-generate-an-answer"></a>A felhasználó lekérdezési kérelme a válasz létrehozásához

A **felhasználó lekérdezése** az a kérdés, hogy a végfelhasználó megkéri a tudásbázist, például `How do I add a collaborator to my app?`:. A lekérdezés gyakran természetes nyelvi formátumban van, vagy néhány kulcsszó, amely a kérdést jelképezi, például `help with collaborators`:. A lekérdezést az ügyfélalkalmazás HTTP- **kérelme** alapján küldi el a rendszer.

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

A választ a tulajdonságok, például a [scoreThreshold](./confidence-score.md#choose-a-score-threshold), a [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)és a [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)tulajdonságainak beállításával szabályozhatja.

A [](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) beszélgetési kontextusban többfordulatos [funkciókkal](../how-to/multiturn-conversation.md) megtarthatja a beszélgetést a kérdések és válaszok pontosításával, hogy megtalálja a megfelelő és a végső választ.

### <a name="the-response-from-a-call-to-generate-answer"></a>A válasz meghívása a válasz létrehozásához

A HTTP- **Válasz** a Tudásbázisból beolvasott válasz, amely egy adott felhasználói lekérdezés legjobb egyezése alapján történik. A válasz tartalmazza a választ és az előrejelzési pontszámot. Ha több legfelső szintű választ kér, a `top` tulajdonsággal egynél több legjobb választ kap, amelyek mindegyike egy pontszámmal rendelkezik. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

### <a name="test-and-production-knowledge-base"></a>Tesztelési és üzemi Tudásbázis
Tudásbázis az adattár kérdések, valamint választ ad a, karbantartani, és a QnA Maker keresztül használt. Az egyes QnA Maker szintek több tudásbázisok használható.

Tudásbázis kétállapotú - teszt rendelkezik, és közzé. 

A **teszt Tudásbázis** az a verzió, amelyet a rendszer szerkeszt, ment és tesztelt a válaszok pontossága és teljessége érdekében. A teszt Tudásbázisban végzett módosítások nem érintik a végfelhasználó számára az alkalmazás-/ csevegőrobot. A teszt Tudásbázis a http `test` -kérelemben is ismert. 

A **közzétett Tudásbázis** a csevegési robotban/alkalmazásban használt verzió. Tudásbázis közzététele művelet a teszt Tudásbázis tartalmát a Tudásbázis közzétett verziójának helyezi. Mivel a közzétett Tudásbázis a verzió, az alkalmazás által használt a végponton keresztül, annak érdekében, hogy a tartalom-e a megfelelő és tesztelt gondot kell fordítani. A közzétett Tudásbázis a http `prod` -kérelemben is ismert. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Tudásbázis fejlesztési életciklus](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Lásd még

[A QnA Maker áttekintése](../Overview/overview.md)

Tudásbázis létrehozása és szerkesztése a rel: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.Net SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Válasz készítése az alábbiakkal: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.Net SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
