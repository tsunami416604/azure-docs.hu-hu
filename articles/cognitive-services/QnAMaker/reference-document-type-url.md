---
title: Az import-QnA Maker által támogatott URL-típusok
description: Ismerje meg, hogy az URL-típusok hogyan használhatók az QnA-készletek importálásához és létrehozásához.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: cf2a02f55d7c95ff9b3f0231ea07a783d18b73cf
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651827"
---
# <a name="urls-supported-for-importing-documents"></a>Dokumentumok importálásához támogatott URL-címek

Ismerje meg, hogy az URL-típusok hogyan használhatók az QnA-készletek importálásához és létrehozásához.

## <a name="faq-urls"></a>Gyakori kérdések URL-címek

A QnA Maker 3 különböző formában támogathatja a gyakori kérdések weblapjait:

* Egyszerű gyakori kérdéseket tartalmazó oldalak
* Mutató hivatkozásokat tartalmaz a gyakori kérdéseket tartalmazó oldalak
* GYIK-lapok a témakörök kezdőlapján

### <a name="plain-faq-pages"></a>Egyszerű gyakori kérdéseket tartalmazó oldalak

Ez a gyakori kérdéseket tartalmazó oldal, ahol a válaszok azonnal kövesse ugyanazon az oldalon szereplő kérdések leggyakoribb típusát.

Az alábbi, egy egyszerű gyakori kérdéseket tartalmazó oldal egy példát:

![Egyszerű – gyakori kérdések lapján példa a Tudásbázis](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Mutató hivatkozásokat tartalmaz a gyakori kérdéseket tartalmazó oldalak

A gyakori kérdéseket tartalmazó oldal az ilyen típusú kérdéseket együtt vannak összesítve, és választ, amelyek különböző szakaszaiban azonos vagy eltérő lapjain van csatolva.

Alább egyik példája egy gyakori kérdéseket tartalmazó oldal mutató hivatkozásokat tartalmaz, amelyek ugyanazon az oldalon szakaszokban:

 ![A szakasz kapcsolat – gyakori kérdések lapján példa a Tudásbázis](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Fölérendelt témakörök lap a gyermek válaszokat tartalmazó lapokra mutató hivatkozások

Az ilyen típusú GYIK olyan témaköröket tartalmaz, ahol az egyes témakörök egy adott kérdésekkel és válaszokkal vannak összekapcsolva egy másik oldalon. QnA Maker feltérképezi az összes csatolt oldalt, hogy kinyerje a megfelelő kérdéseket & választ.

Az alábbi példa egy olyan témakört mutat be, amely a különböző lapokon található gyakori kérdések fejezeteire mutató hivatkozásokat tartalmaz.

 ![Tudásbázis lapon példa áruházra mutató mélyhivatkozás – gyakori kérdések](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Támogatási URL-címek

A QnA Maker képes feldolgozni a részben strukturált támogatási weblapokat, például a webcikkeket, amelyek leírják, hogyan hajthat végre egy adott feladatot, hogyan diagnosztizálhatja és elháríthatja az adott problémát, és hogy mi az ajánlott eljárás az adott folyamathoz. A kinyerés a legjobb olyan tartalmaknál működik, amelyek hierarchikus fejlécekkel rendelkező tiszta struktúrával rendelkeznek.

> [!NOTE]
> A támogatási cikkek kinyerése egy új szolgáltatás, amely korai fázisban van. A legmegfelelőbb az egyszerű lapokhoz, amelyek jól strukturáltak, és nem tartalmaznak összetett fejléceket/élőlábakat.

![QnA Maker támogatja a félig strukturált weblapok kinyerését, ahol a világos struktúra hierarchikus fejlécekkel van megjelenítve](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)