---
title: "Árképzési & számlázási - Azure Logic Apps |} Microsoft Docs"
description: "Ismerje meg, az Azure Logic Apps árak és számlázás működése."
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2017
ms.author: LADocs; klam
ms.openlocfilehash: 49a66c826a98f7160b97b516e6fd541795ae3b0e
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="logic-apps-pricing-model"></a>Logic Apps díjszabási modell
Hozzon létre, és a felhőben az Azure Logic Apps automatizált méretezhető integrációs munkafolyamatok futtatására. Az alábbiakban a Logic Apps a számlázással és a díjszabással működése részleteit.
## <a name="consumption-pricing-model"></a>Felhasználás árképzési modellt
A újonnan létrehozott logikai alkalmazások kell fizetnie csak akkor használja. Új logic apps fogyasztás terv és árképzési modellt használnak, ami azt jelenti, hogy a logic app-példány által végrehajtott összes végrehajtások mérése.
### <a name="what-are-action-executions"></a>Mik azok a műveleti végrehajtások?
A logic app-definíciót minden lépése művelet, beleértve eseményindítók, ellenőrzési folyamat lépéseit, beépített műveletek hívások és összekötők hívásainak.
### <a name="triggers"></a>Eseményindítók
Eseményindítók olyan speciális művelet, hozzon létre egy logic app-példány, ha egy adott esemény történik. Eseményindítók van több különböző beállításokat, amelyek befolyásolják, hogyan forgalmi díjas a logikai alkalmazást.
* **Lekérdezési eseményindító** – ehhez az eseményindítóhoz folyamatosan ellenőrzi egy végpontot, amíg nem kap egy üzenetet, amely eleget tesz a munkafolyamat szeretne elindítani a logic app-példány létrehozásához. A lekérdezési időköz az eseményindító a Logic App Designer használatával állíthat be. Egyes lekérdezési kérelmek egy végrehajtását számít, akkor is, ha nincs logic app-példány jön létre.
* **Webhook eseményindító** – ehhez az eseményindítóhoz megvárja-e az ügyfél kérelmet küld egy adott végpont számára. Minden a webhook végpontnak küldött kérelemben egy művelet végrehajtási számít. A kérelem és a HTTP Webhook eseményindító például mindkét webhook eseményindítók.
* **Ismétlődés eseményindító** – ehhez az eseményindítóhoz hoz létre egy logic app-példány az ismétlődési az eseményindító beállított alapján. Például három naponként futó ismétlődési trigger mentése vagy összetett ütemezés szerint állíthatja be.

A Logic Apps alkalmazást áttekintés ablaktábláján az eseményindító előzmények szakaszban található trigger végrehajtások.

### <a name="actions"></a>Műveletek
Beépített műveletek, például HTTP, az Azure Functions, vagy az API Management, valamint ellenőrzési folyamat lépéseit, hívó műveletek natív műveletként mérése és a megfelelő típusúak. Műveletek hívó [összekötők](https://docs.microsoft.com/connectors) "ApiConnection" típusú. Összekötők standard vagy enterprise összekötők minősülnek, és, valamint saját mérése [árképzési][pricing].
Sikeres és sikertelen futtatási műveletek számítanak és művelet végrehajtások, díjköteles. Azonban végrehajtandó műveleteket a rendszer kihagyja, miatt unmet feltételek vagy a műveletek, amelyek nem futnak, mert a logikai alkalmazás befejezése előtt megszakadt a művelet végrehajtások nem számít. Letiltott logic Apps alkalmazások nem hozható létre új példányok, így azok nem számítjuk fel, amíg le vannak tiltva.

> [!NOTE]
> A logikai alkalmazás letiltása után a jelenleg futó példányát tekintve előfordulhat, hogy egy kis időre teljesen leállítása előtt.

Műveletek hurkokon belül futó minden ciklusonként a hurkon bájtjai számítanak. "Az egyes" ismétlődő, amely feldolgozza a 10-cikk lista egyetlen művelettel számít például a listaelemek (10) száma megszorozzuk a hurok (1) a műveletek száma plusz egy, a hurok indításához. Igen, ebben a példában a számítási esetén (10 * 1) + 1, 11 művelet végrehajtások eredményez.

### <a name="integration-account-usage"></a>Integráció fiók használata
Használati fogyasztás alapján tartalmaz egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) ahol megismerheti, fejlesztése és tesztelése a [B2B/EDI](logic-apps-enterprise-integration-b2b.md) és [XML-feldolgozás](logic-apps-enterprise-integration-xml.md) nem a Logic Apps szolgáltatásokat További költség nélkül. Ezen integrációs fiókok, valamint régió és legfeljebb 10 szerződések és 25 maps tároló egyike lehet. Rendelkezik, és töltse fel a korlátlan partnereivel, sémákat és tanúsítványokat.

A Logic Apps is biztosít, egyszerű és szabványos integrációs fiókok támogatott Logic Apps SLA-t. Az alapszintű integrációt fiókokat is használhatja, ha csak a kezelési üzenet használni szeretne, vagy működjön, és egy kis üzleti partner, amely nagyobb üzleti entitás kereskedelmi partner kapcsolattal rendelkezik. Standard integrációs fiókok összetettebb B2B kapcsolatokat támogatja, és az entitások felügyelhető számának növeléséhez. További információkért lásd: [Azure-beli árakról](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="pricing"></a>Díjszabás
További információkért lásd: [Logic Apps árképzési](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>További lépések
* [A Logic Apps áttekintése][whatis]
* [Az első logikai alkalmazás létrehozása][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

