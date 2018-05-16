---
title: Árképzési & számlázási - Azure Logic Apps |} Microsoft Docs
description: Ismerje meg, az Azure Logic Apps árak és számlázás működése
services: logic-apps
author: kevinlam1
manager: cfowler
editor: ''
documentationcenter: ''
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/11/2018
ms.author: klam
ms.openlocfilehash: 3f01ac12bb3987d564f8d9f70706ae6aabd0a0ee
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="logic-apps-pricing-model"></a>Logic Apps díjszabási modell

Hozzon létre, és a felhőben az Azure Logic Apps automatizált méretezhető integrációs munkafolyamatok futtatására. Az alábbiakban a Logic Apps a számlázással és a díjszabással működése részleteit. 

## <a name="consumption-pricing-model"></a>Felhasználás árképzési modellt

A újonnan létrehozott logikai alkalmazások kell fizetnie csak akkor használja. Új logic apps fogyasztás terv és árképzési modellt, ami azt jelenti, hogy a logic app-példány által végrehajtott művelet végrehajtások mérése használnia. A logic app-definíciót minden lépése művelet, beleértve eseményindítók, ellenőrzési folyamat lépéseit, beépített műveletek hívások és összekötők hívásainak. További információkért lásd: [Logic Apps árképzési](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Triggerek

Eseményindítók olyan speciális művelet, hozzon létre egy logic app-példány, ha egy adott esemény történik. Eseményindítók különböző módokon, amelyek befolyásolják, hogyan a logic app forgalmi díjas jár el.

* **Lekérdezési eseményindító** – ehhez az eseményindítóhoz folyamatosan ellenőrzi az üzenetek, amely megfelel a feltételeknek a logic app-példány létrehozásához, és elindítja a munkafolyamatot a végpont. Egyes lekérdezési kérelmek egy végrehajtását számít, és a forgalmi díjas, akkor is, ha nincs logic app-példány jön létre. Adja meg a lekérdezési időköztől számított, állítsa be az eseményindító a Logic App Designer keresztül.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook eseményindító** – ehhez az eseményindítóhoz megvárja-e az ügyfél kérelmet küld egy adott végpont számára. Minden a webhook végpontnak küldött kérelemben egy művelet végrehajtási számít. A kérelem és a HTTP Webhook eseményindító például mindkét webhook eseményindítók.

* **Ismétlődés eseményindító** – ehhez az eseményindítóhoz hoz létre egy logic app-példány az ismétlődési az eseményindító beállított alapján. Például három naponként futó ismétlődési trigger mentése vagy összetett ütemezés szerint állíthatja be.

A Logic Apps alkalmazást áttekintés ablaktábláján az eseményindító előzmények szakaszban található trigger végrehajtások.

## <a name="actions"></a>Műveletek

Beépített műveletek, például HTTP, az Azure Functions vagy az API Management, amely is szabályozhatja a folyamat lépései műveleteket mérése műveletként natív, amely rendelkezik a megfelelő típusú. Műveletek hívó [összekötők](https://docs.microsoft.com/connectors) "ApiConnection" típusú. Az összekötők standard vagy enterprise összekötők, amelyek mérése a megfelelő alapján besorolt [árképzési][pricing]. 

Sikeres és sikertelen futtatási műveletek számítanak és művelet végrehajtások, díjköteles. Azonban végrehajtandó műveleteket a rendszer kihagyja, miatt unmet feltételek vagy a műveletek, amelyek nem futnak, mert a logikai alkalmazás befejezése előtt megszakadt a művelet végrehajtások nem számít. Letiltott logic Apps alkalmazások nem hozható létre új példányok, így azok nem számítjuk fel, amíg le vannak tiltva.

> [!NOTE]
> Logikai alkalmazás letiltása után a folyamatban levő példányok eltarthat egy ideig, teljesen leállítása előtt.

Műveletek hurkokon belül futó minden ciklusonként a hurkon bájtjai számítanak. "Az egyes" ismétlődő, amely feldolgozza a 10-cikk lista egyetlen művelettel számít például a listaelemek (10) száma megszorozzuk a hurok (1) a műveletek száma plusz egy, a hurok indításához. Igen, ebben a példában a számítási esetén (10 * 1) + 1, 11 művelet végrehajtások eredményez.

## <a name="integration-account-usage"></a>Integráció fiók használata

Használati fogyasztás alapján tartalmaz egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) ahol megismerheti, fejlesztése és tesztelése a [B2B/EDI](logic-apps-enterprise-integration-b2b.md) és [XML-feldolgozás](logic-apps-enterprise-integration-xml.md) nem a Logic Apps szolgáltatásokat További költség nélkül. Valamint régió és tároló specifikus legfeljebb egy integrációs fiók lehet [az összetevők számát](../logic-apps/logic-apps-limits-and-config.md), például EDI kereskedelmi partnereknek és megállapodások maps, sémákat, szerelvényeket, tanúsítványok vagy kötegelt konfigurációk.

A Logic Apps is biztosít, egyszerű és szabványos integrációs fiókok támogatott Logic Apps SLA-t. Az alapszintű integrációt fiókokat is használhatja, ha csak a kezelési üzenet használni szeretne, vagy működjön, és egy kis üzleti partner, amely nagyobb üzleti entitás kereskedelmi partner kapcsolattal rendelkezik. Standard integrációs fiókok összetettebb B2B kapcsolatokat támogatja, és az entitások felügyelhető számának növeléséhez. További információkért lásd: [Azure-beli árakról](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>További lépések

* [További információk a Logic Apps][whatis]
* [Az első logikai alkalmazás létrehozása][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

