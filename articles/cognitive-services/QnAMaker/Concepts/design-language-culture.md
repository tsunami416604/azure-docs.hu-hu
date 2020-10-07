---
title: Nyelvi tervezés – QnA Maker
description: A QnA Maker erőforrás és az erőforráson belüli összes Tudásbázis egyetlen nyelvet támogat. Az egyetlen nyelv szükséges ahhoz, hogy a lekérdezéshez a legjobb eredményt adja.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: e09e15f71b0574a5612e7f9bacd8aaa4739a441c
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777003"
---
# <a name="design-knowledge-base-for-content-language"></a>A tartalom nyelvének tervezési tudásbázisa

A QnA Maker erőforrás és az erőforráson belüli összes Tudásbázis egyetlen nyelvet támogat. Az egyetlen nyelv szükséges ahhoz, hogy a lekérdezéshez a legjobb eredményt adja.

## <a name="single-language-per-resource"></a>Egy nyelv/erőforrás

A nyelvi támogatás QnA Maker szempontjai:

* A QnA Maker szolgáltatás és annak összes tudásbázisa csak egy nyelvet támogat.
* A nyelv explicit módon be van állítva a szolgáltatás első tudásbázisának létrehozásakor
* A nyelv meghatározása a Tudásbázis létrehozásakor hozzáadott fájlokból és URL-címekből történik
* A nyelv nem módosítható a szolgáltatás bármely más tudásbázisa esetében.
* A nyelvet a Cognitive Search szolgáltatás (ranker #1) és a QnA Maker szolgáltatás (ranker #2) használja a legjobb válasz lekérdezésre való létrehozásához.

## <a name="supporting-multiple-languages"></a>Több nyelv támogatása

Ha egy Tudásbázis-rendszer támogatására van szüksége, amely több nyelvet is tartalmaz, az alábbi módszerek közül választhat:

* A [fordítási szöveges szolgáltatással](../../translator/translator-info-overview.md) a kérdés a tudásbázisba való elküldése előtt egyetlen nyelvre fordítható le. Ez lehetővé teszi, hogy a minőségre koncentráljon, valamint a alternatív kérdések és válaszok minőségére.
* Minden nyelvhez hozzon létre egy QnA Maker erőforrást és egy tudásbázist az adott erőforráson belül. Ez lehetővé teszi, hogy külön alternatív kérdéseket kezeljen, és az egyes nyelvekhez árnyaltabb szövegeket adjon. Ez sokkal nagyobb rugalmasságot biztosít, de sokkal nagyobb karbantartási költségeket igényel, ha a kérdések és válaszok minden nyelven megváltoznak.

QnA Maker [által támogatott nyelvek](../overview/language-support.md) áttekintése.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Minden nyelv támogatása QnA Maker erőforrással

* QnA Maker-erőforrás létrehozása minden nyelvhez
* Csak az adott nyelvhez tartozó fájlok és URL-címek hozzáadása
* A nyelv azonosításához használjon egy elnevezési konvenciót az erőforráshoz. Ilyen például `qna-maker-fr` a francia dokumentumok összes tudásbázisa

## <a name="next-steps"></a>Következő lépések

Megtudhatja, [Hogyan kérdezheti](query-knowledge-base.md) le egy adott válasz tudásbázisát.