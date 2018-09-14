---
title: Tudásbázis – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker Tudásbázis kérdés-válasz (QnA) párok, és nem kötelező, a kérdések és válaszok párjaihoz társított metaadatokat tartalmaz.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: e1ac7bd3322f613888ca82cbd3c23c726f0c0e15
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45541181"
---
# <a name="knowledge-base"></a>Tudásbázis

A QnA Maker Tudásbázis kérdés-válasz (QnA) párok, és nem kötelező, a kérdések és válaszok párjaihoz társított metaadatokat tartalmaz.

## <a name="key-knowledge-base-concepts"></a>Tudásbázis fő fogalmak

* **Kérdések** -kérdés tartalmaz szöveg, amely a legjobban jelképezi a felhasználói lekérdezés. 
* **Válaszok** -választ a eredményül, amikor egy felhasználó lekérdezése egyezik a társított kérdés-válasz.  
* **Metaadatok** -metaadatait egy kérdés-válasz párt társított címkék és jelentésekként jelennek meg a kulcs-érték párokat. Metaadatok segítségével QnA párok szűrését, és korlátozza a készlet, melyik lekérdezésben keresztül megfelelő történik.

Egy egyetlen QnA, jelöli QnA numerikus azonosító, több változatának (alternatív kérdések) kérdést, hogy az összes leképezése egy egyetlen választ tartalmaz. Emellett minden egyes ilyen pár lehet társítva több metaadatokat tartalmazó mezőket.

![A QnA Maker tudásbázisok](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Tudásbázis tartalom formátuma

Részletes tartalom, például egy Tudásbázis képes feldolgozni, QnA Maker megkísérli a tartalom átalakításához a markdown formátumhoz. Olvasási [ez](https://aka.ms/qnamaker-docs-markdown-support) blog tudni, hogy a markdown-formátumok jelentésséma a legtöbb csevegési ügyfelek.

Metaadatokat tartalmazó mezőket állnak, amelyek egy pontosvesszővel elválasztott kulcs-érték párok **(termék: aprító)**. A kulcs és az értéket csak szöveg kell lennie. A metaadat-kulcs nem tartalmazhat szóközöket.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Tudásbázis fejlesztési életciklus](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Lásd még

[A QnA Maker – áttekintés](../Overview/overview.md)