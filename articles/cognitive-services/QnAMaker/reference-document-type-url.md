---
title: Importálásra támogatott URL-típusok - QnA Maker
description: Ismerje meg, hogyan használják az URL-típusokat a QnA-párok importálásához és létrehozásához.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: c6c6c7ba19fe7be54733f45997134fddb56a6ba0
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804316"
---
# <a name="urls-supported-for-importing-documents"></a>Dokumentumok importálásához támogatott URL-címek

Ismerje meg, hogyan használják az URL-típusokat a QnA-párok importálásához és létrehozásához.

## <a name="faq-urls"></a>GYIK URL-ek

A QnA Maker 3 különböző formában támogatja a GYIK-weboldalakat:

* Egyszerű gyakori kérdések oldalak
* Gyakori kérdések oldalak linkekkel
* Gyakran feltett kérdések a Témakörök kezdőlapjával

### <a name="plain-faq-pages"></a>Egyszerű gyakori kérdések oldalak

Ez a leggyakoribb típusú GYIK oldal, amelyben a válaszok azonnal követik a kérdéseket ugyanazon az oldalon.

Az alábbiakban egy példa egy egyszerű GYIK oldal:

![Egyszerű GYIK lap példa egy tudásbázishoz](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Gyakori kérdések oldalak linkekkel

Az ilyen típusú GYIK oldalon a kérdések összesítve vannak, és olyan válaszokhoz kapcsolódnak, amelyek ugyanazon oldal különböző szakaszaiban vagy különböző oldalakon találhatók.

Az alábbi példa egy GYIK oldalt mutat be, amely az ugyanazon az oldalon található szakaszokban található hivatkozásokat tartalmazza:

 ![Példa egy tudásbázis szakaszhivatkozása – gyakori kérdések lap](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Szülőtémakörök lap hivatkozásai a gyermekválaszok lapjaira

Az ilyen típusú GYIK-nek van egy Témakörei oldala, ahol minden témakör egy másik oldalon található megfelelő kérdés- és válaszkészlethez kapcsolódik. A QnA Maker feltérképezi az összes hivatkozott oldalt, hogy kibontsa a megfelelő kérdéseket & válaszokat.

Az alábbiakban egy példa a Témák lap linkeket GYIK szakaszok különböző oldalakon.

 ![Deep link GYIK oldal példa egy tudásbázishoz](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Támogatási URL-ek

A QnA Maker fel tudja dolgozni a félig strukturált támogatási weboldalakat, például azokat a webes cikkeket, amelyek leírják egy adott feladat elvégzésének módját, hogyan diagnosztizálhatják és oldhatják meg az adott problémát, és milyen gyakorlati tanácsok egy adott folyamathoz. A kibontás olyan tartalomesetén működik a legjobban, amelynek világos szerkezete hierarchikus fejlécekkel rendelkezik.

> [!NOTE]
> A támogatási cikkek kinyerése egy új funkció, és korai szakaszában van. Ez szerkezet legjobb részére egyszerű oldalak, amit van jól strukturált, és nem tartalmaz összetett fejes/ lábléc.

![A QnA Maker támogatja a félig strukturált weboldalak kinyerését, ahol a hierarchikus fejlécek világos struktúrát mutatnak be](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)