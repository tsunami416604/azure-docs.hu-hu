---
title: Design nyelvhez - QnA Maker
description: A QnA Maker erőforrás és az erőforráson belüli összes tudásbázis egyetlen nyelvet támogat. Az egyetlen nyelv szükséges, hogy a legjobb válaszeredményeket a lekérdezést.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843421"
---
# <a name="design-knowledge-base-for-content-language"></a>Tudásbázis tervezése a tartalomnyelvhez

A QnA Maker erőforrás és az erőforráson belüli összes tudásbázis egyetlen nyelvet támogat. Az egyetlen nyelv szükséges, hogy a legjobb válaszeredményeket a lekérdezést.

## <a name="single-language-per-resource"></a>Egyetlen nyelv erőforrásonként

A QnA Maker nyelvi támogatással kapcsolatos szempontjai:

* A QnA Maker szolgáltatás és annak összes tudásbázisa csak egy nyelvet támogat.
* A nyelv explicit módon van beállítva, amikor a szolgáltatás első tudásbázisa létrejön
* A nyelv meghatározása a tudásbázis létrehozásakor hozzáadott fájlokból és URL-ekből történik
* A szolgáltatás más tudásbázisai esetében nem módosítható a nyelv
* A nyelvet a Cognitive Search szolgáltatás (#1 rangsoroló) és a QnA Maker szolgáltatás (#2. rangesztő) használja a lekérdezésre adott legjobb válasz létrehozásához.

## <a name="supporting-multiple-languages"></a>Több nyelv támogatása

Ha több nyelvet tartalmazó tudásbázisrendszert kell támogatnia, az alábbi módszerek közül választhat:

* A [Fordítási szöveg szolgáltatás sal](../../translator/translator-info-overview.md) lefordíthatja a kérdést egyetlen nyelvre, mielőtt elküldi a kérdést a tudásbázisába. Ez lehetővé teszi, hogy összpontosítson a minősége egy egységes nyelv és a minősége az alternatív kérdések és válaszok.
* Hozzon létre egy QnA Maker erőforrást és egy tudásbázist az erőforráson belül, minden nyelven. Ez lehetővé teszi, hogy különböző alternatív kérdéseket kezeljen, és olyan szöveget válaszoljon, amely árnyaltabb az egyes nyelvekhez. Ez sokkal nagyobb rugalmasságot biztosít, de sokkal magasabb karbantartási költséget igényel, ha a kérdések vagy válaszok minden nyelven változnak.

A QnA Maker [által támogatott nyelvek](../overview/language-support.md) áttekintése.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Minden nyelv támogatása QnA Maker erőforrással

* QnA Maker erőforrás létrehozása minden nyelvhez
* Csak fájlok és URL-címek hozzáadása az adott nyelvhez
* A nyelv azonosításához használjon elnevezési konvenciót az erőforráshoz. Példa erre `qna-maker-fr` a francia dokumentumok összes tudásbázisa

## <a name="next-steps"></a>További lépések

Ismerje [meg,](query-knowledge-base.md) hogyan lehet a tudásbázist választ kérni.