---
title: Tudásbázis – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker Tudásbázis kérdés-válasz (QnA) párok, és nem kötelező, a kérdések és válaszok párjaihoz társított metaadatokat tartalmaz.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 02111ac90fe97ddaddbd41ad42410e7e76f1c405
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311095"
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

Metaadatokat tartalmazó mezőket állnak, amelyek egy pontosvesszővel elválasztott kulcs-érték párok **(termék: aprító)**. A kulcs és az értéket csak szöveg kell lennie. A metaadat-kulcs nem tartalmazhat szóközöket. Metaadatok kulcsonként csak egy értéket támogatja.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Tudásbázis fejlesztési életciklus](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Lásd még

[A QnA Maker áttekintése](../Overview/overview.md)
