---
title: Geo vész-helyreállítás a Azure Event Gridban | Microsoft Docs
description: Leírja, hogyan támogatja a Azure Event Grid a Geo vész-helyreállítási (GeoDR) automatikus használatát.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "66307317"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Kiszolgálóoldali földrajzi katasztrófa-helyreállítás Azure Event Grid
Event Grid mostantól automatikus geo vész-helyreállítási (GeoDR) metaadatokat tartalmaz, nem csak az új, hanem az összes meglévő tartományt, témakört és esemény-előfizetést. Ha egy teljes Azure-régió leáll, Event Grid az összes eseménnyel kapcsolatos infrastruktúra-metaadatát szinkronizálja egy párosított régióba. Az új események az Ön beavatkozása nélkül is újra áramlanak. 

A vész-helyreállítás két mérőszámmal mérhető:

- [Helyreállítási pont célkitűzése (RPO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective): az elveszíthető percek vagy órák.
- [Helyreállítási időre vonatkozó célkitűzés (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective): az a perc, ameddig a szolgáltatás leáll.

Event Grid automatikus feladatátvétele különböző RPO és RTOs rendelkezik a metaadatokhoz (esemény-előfizetések stb.) és az adatokhoz (eseményekhez). Ha a következőtől eltérő specifikációra van szüksége, akkor is saját [ügyféloldali feladatátvételt alkalmazhat a témakör Health API-k használatával](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Helyreállítási időkorlát (RPO)
- **Metaadatok RPO**: nulla perc. Amikor az erőforrást Event Grid hozza létre, a rendszer azonnal replikálja a régiók között. Feladatátvétel esetén a metaadatok nem vesznek el.
- **Adatok RPO**: Ha a rendszer kifogástalan állapotú, és a regionális feladatátvétel időpontjában már meglévő forgalomra került, az események RPO körülbelül 5 percet vesz igénybe.

## <a name="recovery-time-objective-rto"></a>Helyreállítási időre vonatkozó célkitűzés (RTO)
- **Metaadatok RTO**: bár általában sokkal gyorsabban történik, 60 percen belül, Event Grid elkezdi elfogadni a témakörök és előfizetések létrehozási/frissítési/törlési felhívásait.
- **Adatok RTO**: a metaadatokhoz hasonlóan általában sokkal gyorsabban történik, de 60 percen belül Event Grid a regionális feladatátvételt követően megkezdi az új forgalom fogadását.

> [!NOTE]
> A Event Grid metaadatok GeoDR díja: $0.


## <a name="next-steps"></a>További lépések
Ha saját ügyféloldali feladatátvételi logikát szeretne megvalósítani, tekintse meg a [# saját vész-helyreállítási felépítése az egyéni témakörökhöz című részt Event Grid](custom-disaster-recovery.md)
