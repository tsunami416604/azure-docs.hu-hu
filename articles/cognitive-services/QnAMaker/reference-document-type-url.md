---
title: Az import-QnA Maker által támogatott URL-típusok
description: Ismerje meg, hogy az URL-típusok hogyan használhatók az QnA-párok importálásához és létrehozásához.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: c6c6c7ba19fe7be54733f45997134fddb56a6ba0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80804316"
---
# <a name="urls-supported-for-importing-documents"></a>Dokumentumok importálásához támogatott URL-címek

Ismerje meg, hogy az URL-típusok hogyan használhatók az QnA-párok importálásához és létrehozásához.

## <a name="faq-urls"></a>Gyakori kérdések URL-címei

A QnA Maker 3 különböző formában támogathatja a gyakori kérdések weblapjait:

* Egyszerű gyakori kérdések oldalai
* GYIK-lapok hivatkozásokkal
* GYIK-lapok a témakörök kezdőlapján

### <a name="plain-faq-pages"></a>Egyszerű gyakori kérdések oldalai

Ez a leggyakoribb GYIK-oldal, amelyben a válaszok azonnal követik egyazon oldal kérdéseit.

Az alábbi példa egy egyszerű GYIK-oldalra mutat:

![A Tudásbázis egyszerű gyakori kérdések oldalának példája](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>GYIK-lapok hivatkozásokkal

Ebben a gyakori kérdések oldalon a kérdések össze vannak összesítve, és az azonos oldal különböző szakaszaiban vagy különböző lapokon található válaszokhoz vannak társítva.

Az alábbi példa egy olyan GYIK-oldalt mutat be, amely az ugyanazon a lapon található hivatkozásokat tartalmaz:

 ![Szakasz hivatkozás – GYIK oldal, példa tudásbázisra](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Fölérendelt témakörök lap a gyermek válaszokat tartalmazó lapokra mutató hivatkozások

Az ilyen típusú GYIK olyan témaköröket tartalmaz, ahol az egyes témakörök egy adott kérdésekkel és válaszokkal vannak összekapcsolva egy másik oldalon. QnA Maker feltérképezi az összes csatolt oldalt, hogy kinyerje a megfelelő kérdéseket & választ.

Az alábbi példa egy olyan témakört mutat be, amely a különböző lapokon található gyakori kérdések fejezeteire mutató hivatkozásokat tartalmaz.

 ![Részletes hivatkozás – GYIK oldal, példa a tudásbázisra](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Támogatási URL-címek

A QnA Maker képes feldolgozni a részben strukturált támogatási weblapokat, például a webcikkeket, amelyek leírják, hogyan hajthat végre egy adott feladatot, hogyan diagnosztizálhatja és elháríthatja az adott problémát, és hogy mi az ajánlott eljárás az adott folyamathoz. A kinyerés a legjobb olyan tartalmaknál működik, amelyek hierarchikus fejlécekkel rendelkező tiszta struktúrával rendelkeznek.

> [!NOTE]
> A támogatási cikkek kinyerése egy új szolgáltatás, amely korai fázisban van. A legmegfelelőbb az egyszerű lapokhoz, amelyek jól strukturáltak, és nem tartalmaznak összetett fejléceket/élőlábakat.

![QnA Maker támogatja a félig strukturált weblapok kinyerését, ahol a világos struktúra hierarchikus fejlécekkel van megjelenítve](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)