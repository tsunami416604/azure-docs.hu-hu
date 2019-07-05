---
title: Tudásbázis – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker Tudásbázis kérdés-válasz (QnA) párok, és nem kötelező, a kérdések és válaszok párjaihoz társított metaadatokat tartalmaz.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e40af9b2362ee52a1d00f29cdc112d3c2b9a842
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565855"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Mit jelent a QnA Maker Knowledge base?

A QnA Maker Tudásbázis kérdés-válasz (QnA) párok, és nem kötelező, a kérdések és válaszok párjaihoz társított metaadatokat tartalmaz.

## <a name="key-knowledge-base-concepts"></a>Tudásbázis fő fogalmak

* **Kérdések** -kérdés tartalmaz szöveg, amely a legjobban jelképezi a felhasználói lekérdezés. 
* **Válaszok** -választ a eredményül, amikor egy felhasználó lekérdezése egyezik a társított kérdés-válasz.  
* **Metaadatok** -metaadatait egy kérdés-válasz párt társított címkék és jelentésekként jelennek meg a kulcs-érték párokat. Metaadat-címkéket segítségével szűrheti a kérdés-válasz párt, és korlátozza a készlet, melyik lekérdezésben keresztül megfelelő történik.

Egy egyetlen QnA, jelöli QnA numerikus azonosító, több változatának (alternatív kérdések) kérdést, hogy az összes leképezése egy egyetlen választ tartalmaz. Emellett minden egyes ilyen pár rendelkezhet több metaadatmezőket társítva: egy kulcs, és a egy értéket.

![A QnA Maker tudásbázisok](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Tudásbázis tartalom formátuma

Részletes tartalom, például egy Tudásbázis képes feldolgozni, QnA Maker megkísérli a tartalom átalakításához a markdown formátumhoz. Olvasási [ez](https://aka.ms/qnamaker-docs-markdown-support) blog tudni, hogy a markdown-formátumok jelentésséma a legtöbb csevegési ügyfelek.

Metaadatokat tartalmazó mezőket állnak, amelyek egy pontosvesszővel elválasztott kulcs-érték párok **(termék: aprító)** . A kulcs és az értéket csak szöveg kell lennie. A metaadat-kulcs nem tartalmazhat szóközöket. Metaadatok kulcsonként csak egy értéket támogatja.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Hogyan dolgozza fel a QnA Maker válassza ki a legjobb választ a felhasználó lekérdezése

A betanított és [közzétett](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker Tudásbázis kap egy felhasználó, egy robot vagy más ügyfélalkalmazás a [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). A következő ábra szemlélteti a folyamatot, amikor a felhasználó lekérdezése érkezik.

![Felhasználói lekérdezés rangsorolási folyamata](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

A folyamat az alábbi táblázat magyarázza:

|Lépés|Cél|
|--|--|
|1|Az ügyfélalkalmazás elküldi a felhasználói lekérdezés a [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|A QnA Maker alkalmazásával feldolgozza azokat a felhasználói lekérdezési nyelv észlelése, spellers és szóhatároló.|
|3|Alter felhasználó lekérdezése a keresési eredmények elérése érdekében ajánlott az előfeldolgozási venni.|
|4|Azure Search-Index, a módosított lekérdezés küldődik fogadása a `top` eredmények száma. Ha a helyes válasz nem a ezekkel az eredményekkel, értékének növelése `top` némileg. Általában 10 értéket `top` 90 %-a lekérdezések működik.|
|5|A QnA Maker helyességét a lehívott Azure keresési eredmények között, a felhasználó lekérdezése meghatározásához speciális featurization vonatkozik. |
|6|A rangsorolás betanított modell a szolgáltatás pontszám, az 5. lépést, az Azure Search eredmények rangsor használ.|
|7|Az új eredmények visszakerülnek az ügyfélalkalmazásnak rangsorolt sorrendben.|
|||

Használt funkciók közé tartozik, de nem érvényesülnek a word-szintű szemantikát, kifejezés szintű egy korpusz, és meghatározhatja a hasonlóság és a relevancia alapján végzett között két szöveges karakterlánc részletes megismert szemantikai modellek fontossággal bír.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Tudásbázis fejlesztési életciklus](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Lásd még

[A QnA Maker áttekintése](../Overview/overview.md)
