---
title: Tudásbázis – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker Tudásbázis a kérdés-válasz típusú (QnA) és az egyes QnA-párokhoz tartozó opcionális metaadatokból áll.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 78fc9fe34eb3463021dae69990fe1d30668d453f
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300511"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Mi az a QnA Maker Tudásbázis?

A QnA Maker Tudásbázis a kérdés-válasz típusú (QnA) és az egyes QnA-párokhoz tartozó opcionális metaadatokból áll.

## <a name="key-knowledge-base-concepts"></a>Tudásbázis fő fogalmak

* **Kérdések**: A kérdés olyan szöveget tartalmaz, amely a legjobban egy felhasználói lekérdezést jelöl. 
* **Válaszok**: A válasz az a válasz, amelyet a rendszer akkor ad vissza, amikor egy felhasználói lekérdezés megfelel a társított kérdésnek.  
* **Metaadatok**: A metaadatok egy QnA-párral társított címkék, amelyek kulcs-érték párokként jelennek meg. A metaadatok címkéi a QnA párok szűrésére és a lekérdezési egyeztetést végző készlet korlátozására használhatók.

Egy egyetlen QnA, jelöli QnA numerikus azonosító, több változatának (alternatív kérdések) kérdést, hogy az összes leképezése egy egyetlen választ tartalmaz. Emellett minden egyes pár több metaadat-mezővel is rendelkezhet: egy kulcs és egy érték.

![A QnA Maker tudásbázisok](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Tudásbázis tartalom formátuma

Részletes tartalom, például egy Tudásbázis képes feldolgozni, QnA Maker megkísérli a tartalom átalakításához a markdown formátumhoz. Olvassa el [ezt a blogot](https://aka.ms/qnamaker-docs-markdown-support) , és ismerkedjen meg a legtöbb csevegési ügyfél által érthető Markdown-formátumokkal.

A metaadatok mezői egy kettősponttal elválasztott kulcs-érték párokból, például a termék: Iratmegsemmisítőből állnak. A kulcsnak és az értéknek csak szövegesnek kell lennie. A metaadat-kulcs nem tartalmazhat szóközöket. A metaadatok csak egy értéket támogatnak a kulcsok esetében.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Hogyan dolgozza fel QnA Maker a felhasználói lekérdezéseket a legjobb válasz kiválasztásához

A betanított és [közzétett](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker Tudásbázis felhasználói lekérdezést kap egy robottól vagy más ügyfélalkalmazástól a [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)-ban. A következő ábra a felhasználói lekérdezés fogadásának folyamatát szemlélteti.

![Felhasználói lekérdezés rangsorolási folyamata](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Rangsorolási folyamat

A folyamatot az alábbi táblázat ismerteti.

|Lépés|Cél|
|--|--|
|1|Az ügyfélalkalmazás elküldi a felhasználói lekérdezést a [GENERATEANSWER API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)-nak.|
|2|QnA Maker elődolgozza a felhasználói lekérdezést a nyelvfelismerés, a helyesírás-ellenőrző és a Word-megszakítók használatával.|
|3|Ez az előfeldolgozás a legjobb keresési eredmények felhasználói lekérdezésének megváltoztatásához szükséges.|
|4|Ez a módosított lekérdezés Azure Search indexbe lesz küldve, amely megkapja az `top` eredmények számát. Ha a helyes válasz nem szerepel ezekben az eredményekben, növelje a `top` kis-és nagymértékű értéket. Általában a 10-es `top` érték a lekérdezések 90%-ában működik.|
|5|A QnA Maker speciális featurization alkalmaz, hogy meghatározza a felhasználói lekérdezés beolvasott Azure Search eredményeinek helyességét. |
|6|A betanított Ranger-modell az 5. lépésben a szolgáltatás pontszámát használja a Azure Search eredményeinek rangsorolásához.|
|7|Az új eredményeket rangsorolt sorrendben adja vissza az ügyfélalkalmazás.|
|||

A használatban lévő funkciók közé tartozik például a Word-szintű szemantika, a kifejezés szintű fontosság a corpusban, és a mélyebben megtanult szemantikai modellek határozzák meg a hasonlóságot és a megfelelést két szöveges karakterlánc között.

## <a name="http-request-and-response-with-endpoint"></a>HTTP-kérelem és-válasz végponttal
A Tudásbázis közzétételekor a szolgáltatás egy REST-alapú HTTP-végpontot hoz létre, amely integrálható az alkalmazásba, és általában egy csevegési robot. 

### <a name="the-user-query-request-to-generate-an-answer"></a>A felhasználó lekérdezési kérelme a válasz létrehozásához

A felhasználó lekérdezése az a kérdés, hogy a végfelhasználó kéri a tudásbázist, például `How do I add a collaborator to my app?`:. A lekérdezés gyakran természetes nyelvi formátumban van, vagy néhány kulcsszó, amely a kérdést jelképezi, például `help with collaborators`:. A rendszer elküldi a lekérdezést az ügyfélalkalmazás HTTP-kérelme alapján.

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

A beszélgetési [kontextusban](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) [többfordulatos funkciókkal](../how-to/multiturn-conversation.md) megtarthatja a beszélgetést a kérdések és válaszok pontosításával, hogy megtalálja a megfelelő és a végső választ.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Válasz meghívása a válasz létrehozásához

A HTTP-válasz a Tudásbázisból beolvasott válasz, amely egy adott felhasználói lekérdezés legjobb egyezése alapján történik. A válasz tartalmazza a választ és az előrejelzési pontszámot. Ha a `top` tulajdonsággal egynél több legfelső szintű választ kér, egynél több legjobb választ kap, amelyek mindegyike egy pontszámmal rendelkezik. 

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
A Tudásbázis a QnA Maker használatával létrehozott, karbantartott és felhasználható kérdések és válaszok tárháza. Minden QnA Maker szint több tudásbázishoz is használható.

A Tudásbázis két állapottal rendelkezik: *tesztelés* és *Közzététel*.

A *teszt Tudásbázis* az a verzió, amelyet a rendszer szerkeszt, ment és tesztelt a válaszok pontossága és teljessége érdekében. A teszt Tudásbázisban végrehajtott módosítások nem érintik az alkalmazás vagy a csevegési robot végfelhasználóját. A teszt Tudásbázis a http `test` -kérelemben is ismert. 

A *közzétett Tudásbázis* a csevegési robotban vagy alkalmazásban használt verzió. A Tudásbázis közzétételének művelete a Tudásbázis közzétett verziójában a teszt Tudásbázis tartalmát helyezi el. Mivel a közzétett Tudásbázis az alkalmazás által a végponton keresztül használt verzió, győződjön meg arról, hogy a tartalom helyes és jól tesztelt. A közzétett Tudásbázis a http `prod` -kérelemben is ismert.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Tudásbázis fejlesztési életciklus](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Lásd még

[A QnA Maker áttekintése](../Overview/overview.md)

Tudásbázis létrehozása és szerkesztése az alábbiakkal: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Válasz létrehozása az alábbiakkal: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
