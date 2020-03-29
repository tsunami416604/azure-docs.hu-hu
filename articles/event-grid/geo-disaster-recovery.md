---
title: Geovész-helyreállítás az Azure Event Gridben | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy az Azure Event Grid hogyan támogatja automatikusan a geovész-helyreállítást (GeoDR).
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66307317"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Kiszolgálóoldali geovész-helyreállítás az Azure Event Gridben
Event Grid most már rendelkezik egy automatikus geovész-helyreállítási (GeoDR) metaadatok nem csak az új, de az összes meglévő tartományok, témakörök és esemény-előfizetések. Ha egy teljes Azure-régió leáll, az Event Grid már az összes eseménykapcsolatos infrastruktúra-metaadatokat szinkronizálja egy párosított régióval. Az új események újra elkezdenek folyni, beavatkozás nélkül. 

A vészhelyreállítást két mérőszámral mérik:

- [Helyreállítási pont célkitűzés (RPO):](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective)az elveszhet adatok percevagy órája.
- [Helyreállítási idő célkitűzés (RTO):](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective)a szolgáltatás leállása.

Az Event Grid automatikus feladatátvétele különböző RMO-kat és RSO-kat (esemény-előfizetések stb.) és adatokat (eseményeket) hoz. Ha a következő specifikációktól eltérő specifikációra van szüksége, akkor is megvalósíthatja a saját [ügyféloldali feladatátvételt a témakör állapoti api-jának használatával.](custom-disaster-recovery.md)

## <a name="recovery-point-objective-rpo"></a>Helyreállítási időkorlát (RPO)
- **Metaadat-rpo**: nulla perc. Amikor egy erőforrás létrejön az Event Gridben, azonnal replikálódik a régiók között. Feladatátvétel esetén nem vesznek el metaadatok.
- **Adatok RPO:** Ha a rendszer kifogástalan állapotú, és a regionális feladatátvétel időpontjában a meglévő forgalom, az RPO események körülbelül 5 perc.

## <a name="recovery-time-objective-rto"></a>Helyreállítási időre vonatkozó célkitűzés (RTO)
- **Metaadat-rto:** Bár általában sokkal gyorsabban történik, 60 percen belül az Event Grid elkezdi elfogadni a témakörök és előfizetések létrehozási/frissítési/törlési hívásait.
- **Adatok RTO:** A metaadatokhoz hasonlóan általában sokkal gyorsabban történik, de 60 percen belül az Event Grid megkezdi az új forgalom fogadását egy regionális feladatátvétel után.

> [!NOTE]
> A metaadat-geodr költsége az Event Gridben: $0.


## <a name="next-steps"></a>További lépések
Ha saját ügyféloldali feladatátvételi logikát szeretne megvalósítani, olvassa [el a # Saját vész-helyreállítási rendszer létrehozása az Egyéni témakörökhöz az Event Gridben](custom-disaster-recovery.md)
