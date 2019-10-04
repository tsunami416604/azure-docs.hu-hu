---
title: Műveletnapló riasztásai Azure Monitor
description: Értesítés küldése SMS-ben, webhookon, SMS-ben, e-mailben és más üzenetekben, ha bizonyos események történnek a tevékenység naplójában.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: b24f24edf2a3a0df8cb8ef9687f205a4a8868537
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71675236"
---
# <a name="alerts-on-activity-log"></a>Riasztások a tevékenység naplójában 

## <a name="overview"></a>Áttekintés
A műveletnapló riasztásai olyan riasztások, amelyek akkor jelentkeznek be, amikor a riasztásban megadott feltételeknek megfelelő új [műveletnapló-esemény](activity-log-schema.md) következik be. Az Azure-beli [tevékenység naplójában](activity-logs-overview.md)rögzített események sorrendje és mennyisége alapján a riasztási szabály tüzet fog eredményezni. A műveletnapló riasztási szabályai Azure-erőforrások, így a Azure Resource Manager sablon segítségével hozhatók létre. Emellett a Azure Portal is létrehozhatók, frissíthetők és törölhetők. Ez a cikk a tevékenység naplójának riasztásai mögötti fogalmakat ismerteti. A műveletnapló riasztási szabályainak létrehozásával és használatával kapcsolatos további információkért lásd: [műveletnapló riasztások létrehozása és kezelése](alerts-activity-log.md).

> [!NOTE]
> **Nem** hozhatók létre riasztások a tevékenység naplójának riasztási kategóriájában lévő eseményekhez.

Az értesítések fogadásához általában a következő esetekben kell létrehoznia a tevékenység naplójának riasztásait:

* Az Azure-előfizetésében lévő erőforrásokra adott műveletek vonatkoznak, gyakran adott erőforrás-csoportokra vagy erőforrásokra is kiterjednek. Előfordulhat például, hogy értesítést szeretne kapni, ha a myProductionResourceGroup valamelyik virtuális gépe törölve van. Vagy előfordulhat, hogy értesítést szeretne kapni, ha az előfizetésben lévő felhasználóhoz új szerepkörök vannak rendelve.
* A szolgáltatás állapotára vonatkozó esemény történik. A szolgáltatás állapotával kapcsolatos események az előfizetésben lévő erőforrásokra vonatkozó incidensek és karbantartási események értesítését tartalmazzák.

A tevékenység naplójában a riasztási szabályok létrehozására szolgáló feltételek értelmezése egyszerű hasonlóságot jelent, ha az eseményeket a [Azure Portal tevékenység naplóján](activity-log-view.md#azure-portal)keresztül vizsgálja vagy szűri. A Azure Monitor-Activity naplóban egy szűrő vagy megkeresheti a szükséges eseményt, majd létrehozhat egy riasztást a **tevékenység naplójának hozzáadása riasztás** gomb használatával.

Mindkét esetben a műveletnapló riasztása csak az előfizetésben lévő azon eseményekhez figyeli a riasztást, amelyekben a riasztás létrejött.

Egy műveletnapló-eseményen a JSON-objektum minden legfelső szintű tulajdonsága alapján beállíthat egy tevékenység naplójának riasztását. További információ: [Az Azure-tevékenység naplójának áttekintése](./activity-logs-overview.md#categories-in-the-activity-log). További információ a szolgáltatás állapotával kapcsolatos eseményekről: [fogadási tevékenység naplójának riasztásai a szolgáltatási értesítéseken](./alerts-activity-log-service-notifications.md). 

A műveletnapló riasztásai néhány gyakori lehetőséggel rendelkeznek:

- **Kategória**: Adminisztráció, Service Health, autoskálázás, biztonság, házirend és javaslat. 
- **Hatókör**: Az adott erőforrás vagy erőforrás (ok), amelyhez a riasztás a tevékenység naplójában meg van határozva. A tevékenység naplójának riasztási hatóköre különböző szinteken határozható meg:
    - Erőforrás szintje: Egy adott virtuális gép esetében például
    - Erőforráscsoport szintje: Egy adott erőforráscsoport összes virtuális gépe például
    - Előfizetés szintje: Például az előfizetésben lévő összes virtuális gép (vagy) az előfizetés összes erőforrása
- **Erőforráscsoport**: Alapértelmezés szerint a rendszer a riasztási szabályt ugyanabban az erőforráscsoporthoz menti, mint a hatókörben definiált cél. A felhasználó megadhatja azt az erőforráscsoportot is, amelyben a riasztási szabályt tárolni szeretné.
- **Erőforrás típusa**: Erőforrás-kezelő által definiált névtér a riasztás céljához.
- **Művelet neve**: A [Azure Resource Manager művelet](../../role-based-access-control/resource-provider-operations.md) neve szerepköralapú Access Control számára lett kihasználva. A Azure Resource Managerban nem regisztrált műveletek nem használhatók a műveletnapló riasztási szabályaiban.
- **Szint**: Az esemény súlyossági szintje (részletes, tájékoztató, figyelmeztetés, hiba vagy kritikus).
- **Állapot**: Az esemény állapota, általában elindítva, sikertelen, vagy sikeres.
- **Esemény kezdeményezője**: Más néven "hívó". A műveletet végrehajtó felhasználó e-mail-címe vagy Azure Active Directory azonosítója.

> [!NOTE]
> Egy előfizetésben akár 100 riasztási szabály is létrehozható egy hatóköri tevékenységhez: egyetlen erőforrás, az erőforráscsoport összes erőforrása (vagy) a teljes előfizetési szint.

Ha egy műveletnapló-riasztás aktiválva van, a műveletek vagy értesítések létrehozásához egy műveleti csoportot használ. A műveleti csoport az értesítési fogadók újrafelhasználható készlete, például e-mail-címek, webhook URL-címek vagy SMS-telefonszámok. A fogadók több riasztásból is hivatkozhatnak az értesítési csatornák központosítására és csoportosítására. A műveletnapló riasztásának meghatározásakor két lehetőség közül választhat. A következőket teheti:

* Meglévő műveleti csoport használata a tevékenység naplójának riasztásában.
* Hozzon létre egy új műveleti csoportot.

A műveleti csoportokkal kapcsolatos további információkért lásd: [műveleti csoportok létrehozása és kezelése a Azure Portalban](action-groups.md).


## <a name="next-steps"></a>További lépések

- [A riasztások áttekintése](alerts-overview.md).
- Tudnivalók a [műveletnapló-riasztások létrehozásáról és módosításáról](alerts-activity-log.md).
- Tekintse át a [tevékenység naplójának riasztása webhook sémáját](activity-log-alerts-webhook.md).
- Tudnivalók a [szolgáltatás állapotával](service-notifications.md)kapcsolatos értesítésekről.