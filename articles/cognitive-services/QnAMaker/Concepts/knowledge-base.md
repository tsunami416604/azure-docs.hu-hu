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
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 022b16669791b9b9cce066b3dd17c70b33569cc0
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955238"
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


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Tudásbázis fejlesztési életciklus](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Lásd még

[A QnA Maker áttekintése](../Overview/overview.md)
