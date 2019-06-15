---
title: Georedundáns vészhelyreállítás az Azure Event Grid |} A Microsoft Docs
description: Ismerteti, hogyan Azure Event Grid támogatja a földrajzi vészhelyreállítási (GeoDR) automatikusan.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66307317"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Az Azure Event Grid kiszolgálóoldali georedundáns vészhelyreállítás
Event Grid a metaadatok nem csupán, az új, azonban az összes meglévő tartományt, témaköröket és eseményfeliratkozásokat, most már rendelkezik egy automatikus georedundáns vészhelyreállítás (GeoDR). Ha egy teljes Azure-régiót leáll, Event Grid már rendelkezik az összes szinkronizált párosított régióval esemény a biztonsággal kapcsolatos infrastruktúra metaadatait. Az új események áramlását újra beavatkozás nélküli, megkezdődik. 

Vész-helyreállítási mérik a két mérőszám:

- [A helyreállítási időkorlátot (RPO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective): a percek vagy órák, amelyek elveszhetnek.
- [A helyreállítási időre vonatkozó célkitűzés (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective): lehet, hogy a szolgáltatás óra perc le.

Event Grid automatikus feladatátvétellel rendelkezik másik Rpo és RTO-k a metaadatok (esemény-előfizetések stb.) és adatokat (események). Ha másik specifikációnak a következő állók közül, továbbra is megvalósítható a saját [ügyféloldali átadja a feladatokat a témakör segítségével health API-k](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Helyreállítási időkorlát (RPO)
- **Metaadatok RPO**: nulla perc. Visszaállít egy erőforrás létrehozása az Event Gridben azonnal replikálja régiók között elosztva. A feladatátvétel esetén sem a metaadatok nem elveszett.
- **Adatok RPO**: Ha a rendszer állapota kifogástalan, és a regionális feladatátvétel időpontjáig feldolgozta a meglévő forgalmat, akkor az események RPO-ja körülbelül 5 perc.

## <a name="recovery-time-objective-rto"></a>Helyreállítási időre vonatkozó célkitűzés (RTO)
- **Metaadatok RTO**: Bár általában akkor történik, sokkal gyorsabban 60 percen belül megkezdődik Event Grid, témakörök és előfizetések létrehozása/frissítése/törlése hívásokat fogadni.
- **Adatok RTO**: Metaadatokat, például azt általában akkor fordul elő, sokkal gyorsabban azonban 60 percen belül Event Grid elkezdi az új forgalmat fogad egy regionális feladatátvétel után.

> [!NOTE]
> A metaadatok az Event Grid GeoDR költség: 0 USD.


## <a name="next-steps"></a>További lépések
Ha szeretné, saját ügyféloldali feladatátvételi a logikát alkalmazzák, [# hozhat létre a saját vész-helyreállítási egyéni témakört az Event Gridben](custom-disaster-recovery.md)
