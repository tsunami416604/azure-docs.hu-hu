---
title: Azure Event Hubs – Resource Manager-kivételek | Microsoft Docs
description: A Azure Resource Manager és a javasolt műveletek által felszínben lévő Azure Event Hubs-kivételek listája.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: aa1a3ca647bbf9e6590446549455a9853411fd7d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281038"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure Event Hubs – Resource Manager-kivételek
Ez a cikk azokat a kivételeket sorolja fel, amelyeket az Azure Event Hubs a Azure Resource Manager-sablonokkal vagy közvetlen hívásokkal való interakció során generáltak.

> [!IMPORTANT]
> Ez a dokumentum gyakran frissül. Tekintse át a frissítéseket.

A következő szakaszokban különböző kivételek/hibák jelennek meg Azure Resource Manageron.

## <a name="error-code-conflict"></a>Hibakód: ütközés

| Hibakód | Hiba alkódja | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Ütközés | 40300 | Elérte vagy túllépte a EventHub típusú erőforrások maximális számát. Tényleges: #, Max megengedett: # | A névtér elérte a [kvótáját](event-hubs-quotas.md) a benne foglalt Event Hubs számára. | Törölje a fel nem használt vagy idegen típusú esemény-hubokat a névtérből, vagy vegye fontolóra a [dedikált fürtre](event-hubs-dedicated-overview.md)való frissítését. |
| Ütközés | nincs | Nem lehet törölni a vész-helyreállítási (DR) konfigurációt, mert a replikáció folyamatban van. A DR-konfiguráció törlésének megkísérlése előtt hajtsa végre a feladatátvételt, vagy szakítsa meg a párosítást. | A [GeoDR replikációja](event-hubs-geo-dr.md) folyamatban van, így a konfiguráció jelenleg nem törölhető. | A konfiguráció törlésének feloldásához várjon, amíg a replikáció befejeződik, indítson el egy feladatátvételt, vagy szüntesse meg a GeoDR párosítását. |
| Ütközés | nincs | A névtér frissítése a háttérbeli ütközéssel meghiúsult. | Jelenleg folyamatban van egy másik művelet végrehajtása ezen a névtéren. | Várjon, amíg a jelenlegi művelet befejeződik, majd próbálkozzon újra. |

## <a name="error-code-429"></a>Hibakód: 429

| Hibakód | Hiba alkódja | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | nincs | Névtér kiépítés átmenet alatt | Jelenleg folyamatban van egy másik művelet végrehajtása ezen a névtéren. | Várjon, amíg a jelenlegi művelet befejeződik, majd próbálkozzon újra. |
| 429 | nincs | A vész-helyreállítási művelet folyamatban van. | Folyamatban van egy [GeoDR](event-hubs-geo-dr.md) művelet végrehajtása ezen a névtéren vagy párosításon. | Várjon, amíg a jelenlegi GeoDR művelet befejeződik, majd próbálkozzon újra. |

## <a name="error-code-badrequest"></a>Hibakód: BadRequest

| Hibakód | Hiba alkódja | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | A PartitionCount nem módosítható az Event hub esetében. | Az Azure Event Hubs alapszintű vagy standard szintje nem támogatja a partíciók módosítását. | Hozzon létre egy új Event hub-t a kívánt számú partícióval az alapszintű vagy standard szintű névtérben. A particionálási felskálázás a [dedikált fürtök](event-hubs-dedicated-overview.md)esetében támogatott. |
| BadRequest | 40000 | A MessageRetentionInDays "#" értéke nem érvényes az alapszintű csomag esetében. az érték nem lehet nagyobb, mint 1 nap. | Az alapszintű Event Hubs névterek csak legfeljebb 1 napig támogatják az üzenetek megőrzését. | Ha a rendszer több mint egy nappal az üzenetek megőrzését szeretné megtartani, [hozzon létre egy standard Event Hubs névteret](event-hubs-create.md). | 
| BadRequest | nincs | A megadott név nem érhető el. | A névtér nevének egyedinek kell lennie, és a megadott név már használatban van. | Ha a meglévő névtér tulajdonosa a megadott névvel rendelkezik, törölheti is, ami adatvesztést okoz. Ezután próbálkozzon újra ugyanazzal a névvel. Ha a névtér nem törölhető (vagy nem a tulajdonos), válasszon egy másik névtér-nevet. |
| BadRequest | nincs | A megadott előfizetés elérte a névterek kvótáját. | Az előfizetése elérte az általa tárolható névterek számára vonatkozó [kvótát](event-hubs-quotas.md) . | Érdemes lehet törölni a nem használt névtereket ebben az előfizetésben, egy másik előfizetést létrehozni vagy egy [dedikált fürtre](event-hubs-dedicated-overview.md)frissíteni. |
| BadRequest | nincs | Másodlagos névtér nem frissíthető. | A névtér nem frissíthető, mert ez a másodlagos névtér egy [GeoDR-párosításban](event-hubs-geo-dr.md). | Ha szükséges, végezze el a módosítást az elsődleges névtérben a párosítás helyett. Ellenkező esetben szüntesse meg a GeoDR párosítását a módosítás elvégzéséhez. |
| BadRequest | nincs | Az alapszintű SKU-ban nem állítható be az automatikus kiemelés | Az automatikus feltöltés nem engedélyezhető az alapszintű Event Hubs névterek esetében. | Ha [engedélyezni](event-hubs-auto-inflate.md) szeretné az automatikus kikapcsolást a névtérben, győződjön meg arról, hogy az a standard szintű csomag. |
| BadRequest | nincs | Nincs elegendő kapacitás a névtér létrehozásához. Forduljon a Event Hubs rendszergazdájához. | A kiválasztott régió kapacitása és további névterek nem hozhatók létre. | Válasszon másik régiót a névtér kiválasztásához. |
| BadRequest | nincs | A művelet nem hajtható végre a (z) "ConsumerGroup" típusú entitáson, mert a névtér neve "Basic" szintet használ.  | Az alapszintű Event Hubs névterek egy fogyasztói csoport [kvótája](event-hubs-quotas.md#event-hubs-basic-and-standard---quotas-and-limits) (alapértelmezett). További fogyasztói csoportok létrehozása nem támogatott. | Folytassa az alapértelmezett fogyasztói csoport ($Default) használatát, vagy ha többre van szükség, érdemes lehet egy standard szintű Event Hubs névteret használni. | 
| BadRequest | nincs | A névtér neve nem létezik. | A megadott névtér nem található. | Ellenőrizze, hogy helyes-e a névtér neve, és megtalálható-e az előfizetésében. Ha nem, [hozzon létre egy Event Hubs névteret](event-hubs-create.md). | 
| BadRequest | nincs | Az erőforrás Location tulajdonsága nem felel meg a benne található névtérnek. | Az Event hub egy adott régióban való létrehozása nem sikerült, mert nem felelt meg a névtér régiójának. | Próbálja meg létrehozni az Event hub-t ugyanabban a régióban, mint a névteret. | 

## <a name="error-code-internal-server-error"></a>Hibakód: belső kiszolgálóhiba

| Hibakód | Hiba alkódja | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Belső kiszolgálóhiba | nincs | Belső kiszolgálóhiba. | A Event Hubs szolgáltatás belső hibával rendelkezett. | Próbálja megismételni a sikertelen műveletet. Ha a művelet továbbra is sikertelen, forduljon az ügyfélszolgálathoz. |