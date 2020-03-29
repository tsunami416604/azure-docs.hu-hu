---
title: Azure Event Hubs – Erőforrás-kezelő kivételek | Microsoft dokumentumok
description: Az Azure Event Hubs-kivételek listája az Azure Resource Manager által felszínre, és a javasolt műveletek.
services: service-bus-messaging
documentationcenter: na
author: spelluru
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2019
ms.author: spelluru
ms.openlocfilehash: e6ee1137fce97cbe5a64aa5287223f6ba09dcf47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74936086"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure Event Hubs – Erőforrás-kezelő isztikányi kivételek
Ez a cikk az Azure Event Hubs azure-beli erőforrás-kezelővel való interakció során létrehozott kivételeket sorolja fel – sablonok vagy közvetlen hívások révén.

> [!IMPORTANT]
> Ez a dokumentum gyakran frissül. Kérjük, látogasson vissza a frissítéseket.

A következő szakaszok különböző kivételeket/hibákat tartalmaznak, amelyek az Azure Resource Manageren keresztül jelennek meg.

## <a name="error-code-conflict"></a>Hibakód: Ütközés

| Hibakód | Hiba alkód | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Ütközés | 40300 | Az EventHub típusú erőforrások maximális száma elérte vagy túllépte. Tényleges: #, Max megengedett: # | A névtér elérte a [kvótáját](event-hubs-quotas.md) a tartalmazhat ónevű eseményközpontok száma miatt. | Törölje a nem használt vagy idegen eseményközpontokat a névtérből, vagy fontolja meg a [dedikált fürtre](event-hubs-dedicated-overview.md)való frissítést. |
| Ütközés | Nincs | A vész-helyreállítási (DR) konfiguráció nem törölhető, mert a replikáció folyamatban van. A DR Config törlése előtt feladatátvételt vagy megszakítást. | [GeoDR replikáció](event-hubs-geo-dr.md) folyamatban van, így a konfiguráció nem törölhető ebben az időben. | A konfiguráció törlésének feloldásához várjon, amíg a replikáció befejeződik, indítsa el a feladatátvételt, vagy szakítsa meg a GeoDR-párosítást. |
| Ütközés | Nincs | A névtér frissítése nem sikerült, mert ütközött a háttérrendszerben. | Ezen a névtéren jelenleg egy másik művelet van folyamatban. | Várjon, amíg az aktuális művelet befejeződik, majd próbálkozzon újra. |

## <a name="error-code-429"></a>Hibakód: 429

| Hibakód | Hiba alkód | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | Nincs | Áttűnésben lévő névtér-kiépítés | Ezen a névtéren jelenleg egy másik művelet van folyamatban. | Várjon, amíg az aktuális művelet befejeződik, majd próbálkozzon újra. |
| 429 | Nincs | Vész-helyreállítási művelet folyamatban van. | Jelenleg [geoDR-művelet](event-hubs-geo-dr.md) van folyamatban ezen a névtéren vagy párosításon. | Várjon, amíg az aktuális GeoDR-művelet befejeződik, majd próbálkozzon újra. |

## <a name="error-code-badrequest"></a>Hibakód: BadRequest

| Hibakód | Hiba alkód | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest (Hibás kérés) | 40000 | A PartitionCount nem módosítható egy eseményközpont esetében. | Az Azure Event Hubs alapszintű vagy szabványos szintje nem támogatja a partíciók módosítását. | Hozzon létre egy új eseményközpontot az alapszintű vagy szabványos szintű névtérben lévő partíciók kívánt számával. A partíciós horizontális felskálázás [dedikált fürtök](event-hubs-dedicated-overview.md)számára támogatott. |
| BadRequest (Hibás kérés) | 40000 | A MessageRetentionInDays '#' értéke nem érvényes az alapszintű rétegre. az érték nem haladhatja meg az "1" napot. | Az alapszintű event hubs névterek csak legfeljebb 1 napig támogatják az üzenetek megőrzését. | Ha egynél több üzenetmegőrzésre van szükség, [hozzon létre egy szabványos Event Hubs névteret.](event-hubs-create.md) | 
| BadRequest (Hibás kérés) | Nincs | A megadott név nem érhető el. | A névtérneveknek egyedinek kell lenniük, és a megadott név már foglalt. | Ha ön a megadott nevű meglévő névtér tulajdonosa, törölheti azt, ami adatvesztést okoz. Ezután próbálkozzon újra ugyanazzal a névvel. Ha a névtér törlése nem biztonságos (vagy nem Ön a tulajdonos), válasszon másik névtérnevet. |
| BadRequest (Hibás kérés) | Nincs | A megadott előfizetés elérte a névterek kvótáját. | Az előfizetés elérte a [megtartható](event-hubs-quotas.md) névterek számát. | Fontolja meg a nem használt névterek törlését ebben az előfizetésben, hozzon létre egy másik előfizetést vagy frissítsen egy [dedikált fürtre.](event-hubs-dedicated-overview.md) |
| BadRequest (Hibás kérés) | Nincs | Másodlagos névtér nem frissíthető | A névtér nem frissíthető, mert ez a [GeoDR-párosítás](event-hubs-geo-dr.md)másodlagos névtere. | Ha szükséges, módosítsa az elsődleges névteret ebben a párosításban. Ellenkező esetben szakítsa meg a GeoDR párosítást a módosításhoz. |
| BadRequest (Hibás kérés) | Nincs | Az automatikus felfújás nem állítható be az alaptermékváltozatban | Az automatikus felfújás nem engedélyezhető az alapvető szintű Eseményközpontok névterein. | Ha [engedélyezni szeretné az automatikus felfújni](event-hubs-auto-inflate.md) egy névtérben, győződjön meg arról, hogy a standard szintű. |
| BadRequest (Hibás kérés) | Nincs | Nincs elég kapacitás a névtér létrehozásához. Forduljon az Eseményközpontok rendszergazdájához. | A kijelölt terület kapacitása van, és több névterek nem hozhatók létre. | Válasszon másik területet a névtér elhelyezésére. |
| BadRequest (Hibás kérés) | Nincs | A művelet nem végezhető el a "ConsumerGroup" entitástípuson, mert a "névtér neve" "Alapszintű" réteget használ.  | Az alapszintű Event Hubs névterek [quot]((event-hubs-quotas.md#event-hubs-basic-and-standard---quots-and-limits) egy fogyasztói csoportból (az alapértelmezett). További fogyasztói csoportok létrehozása nem támogatott. | Folytassa az alapértelmezett fogyasztói csoport ($Default) használatát, vagy ha többre van szükség, fontolja meg egy szabványos szintű Event Hubs névtér használatát. | 
| BadRequest (Hibás kérés) | Nincs | A "névtér neve" névtér nem létezik. | A megadott névtér nem található. | Ellenőrizze, hogy a névtér neve helyes-e, és megtalálható-e az előfizetésben. Ha nem, [hozzon létre egy Event Hubs névteret.](event-hubs-create.md) | 
| BadRequest (Hibás kérés) | Nincs | Az erőforrás helytulajdonsága nem egyezik meg a névteret tartalmazó tulajdonságával. | Egy eseményközpont létrehozása egy adott régióban nem sikerült, mert nem felel meg a névtér régiójának. | Próbálja meg létrehozni az eseményközpontot ugyanabban a régióban, mint a névtér. | 

## <a name="error-code-internal-server-error"></a>Hibakód: Belső kiszolgálóhiba

| Hibakód | Hiba alkód | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Belső kiszolgálóhiba | Nincs | Belső kiszolgálóhiba. | Az Event Hubs szolgáltatás belső hibával rendelkezett. | Próbálkozzon újra a sikertelen művelettel. Ha a művelet továbbra is sikertelen, forduljon az ügyfélszolgálathoz. |