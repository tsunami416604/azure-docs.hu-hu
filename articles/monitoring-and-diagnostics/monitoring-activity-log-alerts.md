---
title: Az Azure Monitor tevékenységnapló-riasztások
description: SMS, webhookot, SMS, e-mailben és több, amikor bizonyos események történnek a tevékenységnaplóban értesítést kap.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 6f007ca3aacb338c14bf481ee58407596c8290ad
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091623"
---
# <a name="alerts-on-activity-log"></a>Riasztások a tevékenységnaplóban 

## <a name="overview"></a>Áttekintés
A tevékenységnapló-riasztások aktiválása, ha egy új tevékenységnapló eseményéhez akkor fordul elő a riasztást a megadott feltételeknek megfelelő riasztásokat is. Azure-erőforrások, így azok az Azure Resource Manager-sablon segítségével hozható létre. Akkor is képes lehet létrehozott, frissített vagy törölt az Azure Portalon. Ez a cikk bemutatja a tevékenységnapló-riasztások mögött. Majd bemutatja, hogyan állítsa be a riasztást a tevékenységnapló-események az Azure portal használatával. Használati további információkért lásd: [létrehozása és kezelése a tevékenységnapló-riasztások](alert-activity-log.md).

> [!NOTE]
> Riasztások **nem** események hozhatók létre a tevékenységnapló-riasztási kategória

Általában akkor tevékenységnapló-riasztások létrehozása az értesítések fogadása során:

* Konkrét műveletek az erőforrásokat az Azure-előfizetésben, az adott erőforrás-csoportok vagy az erőforrások gyakran hatóköre fordulhat elő. Például érdemes értesíti, ha bármelyik virtuális gépre myProductionResourceGroup törlődik. Vagy előfordulhat, hogy szeretne értesítést kapjon, ha egyetlen új szerepkör hozzá van rendelve egy felhasználó az előfizetésében.
* A service health esemény következik be. A Szolgáltatásállapot-események értesítési incidensek és a karbantartási események, amelyek érvényesek az előfizetése tartalmazza.

Egy egyszerű analógia ismertetése feltételeket, amelyen riasztási szabályok hozható létre a tevékenységnapló-, és felfedezni vagy szűrheti az eseményeket a rendszer [tevékenységnapló az Azure Portalon](monitoring-overview-activity-logs.md#query-the-activity-log-in-the-azure-portal). Az Azure Monitor - tevékenységnapló-, egy is szűrheti vagy keresse meg szükséges esemény és majd a használatával hozzon létre egy riasztást a **tevékenységnapló-riasztás hozzáadása** gombra.

Mindkét esetben a tevékenységnapló-riasztás csak az az előfizetés, amelyben a riasztás létrejött eseményeket figyeli.

Egy tevékenységnapló-riasztás alapján a JSON-objektumokat egy tevékenységnapló eseményéhez szereplő összes legfelső szintű tulajdonsággal is beállíthatja. További információkért lásd: [áttekintése az Azure-tevékenységnapló](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). A Szolgáltatásállapot-események kapcsolatos további információkért lásd: [szolgáltatási értesítésekhez tevékenységnapló-riasztások fogadása](./monitoring-activity-log-alerts-on-service-notifications.md). 

A tevékenységnapló-riasztások néhány gyakori közül választhat:

- **Kategória**: felügyeleti, a Service Health, automatikus méretezés, biztonsági, a házirend és javaslat. 
- **Hatókör**: az egyes erőforrások vagy parancsával, amelynek a riasztás tevékenységnapló van definiálva. Egy tevékenységnapló-riasztás hatókör különböző szinteken lehet meghatározni:
    - Erőforrás szintje: például egy adott virtuális gép
    - Erőforráscsoport szintje: például összes virtuális gép egy adott erőforráscsoportban
    - Előfizetés szintje: például található összes virtuális gép egy előfizetést (vagy) egy adott előfizetés összes erőforrás
- **Erőforráscsoport**: alapértelmezés szerint a riasztási szabály menti a rendszer ugyanazt az erőforráscsoportot, a cél meghatározott hatókörben. A felhasználó is meghatározhat az erőforráscsoport, ahol a riasztási szabályt kell tárolni.
- **Erőforrástípus**: Resource Manager definiált névtér a cél a riasztás.

- **Název operace**: hozzáférés-vezérlés The Resource Manager Role-Based művelet neve.
- **Szint**: A súlyossági szintet az esemény (részletes, tájékoztatás, figyelmeztetés, hiba vagy kritikus).
- **Állapot**: az esemény, általában elindult, sikertelen vagy sikeres állapotát.
- **Esemény kezdeményezője**: más néven a "hívó." Az e-mail címet vagy az Azure Active Directory a műveletet végrehajtó felhasználó azonosítója.

> [!NOTE]
> Az előfizetéshez legfeljebb 100 riasztási szabályok hozható létre bármelyik hatókör tevékenység: egy erőforrást, minden erőforrás erőforrás csoport (vagy) teljes előfizetési szinten.

Tevékenységnapló-riasztás aktiválódik, ha a műveletek vagy értesítések műveletcsoportot használ. Műveletcsoport egy újrafelhasználható értesítési címzett e-mail-címeket, például a webhook URL-címek vagy SMS ügyfélszolgálatának telefonszámai. A fogadók központosítása és az értesítési csatornák csoportban több riasztásokból lehet hivatkozni. A tevékenységnapló-riasztás határozza meg, amikor két lehetősége van. A következőket teheti:

* Használja meglévő műveletcsoport a tevékenységnapló-riasztás.
* Hozzon létre egy új műveletcsoportot.

További információ a műveletcsoportokról, lásd: [létrehozása és kezelése az Azure Portalon Műveletcsoportok](monitoring-action-groups.md).


## <a name="next-steps"></a>További lépések
- Get- [riasztások áttekintése](monitoring-overview-alerts.md).
- Ismerje meg [létrehozása és módosítása a tevékenységnapló-riasztások](alert-activity-log.md).
- Tekintse át a [tevékenység log riasztási webhookséma](monitoring-activity-log-alerts-webhook.md).
- Ismerje meg [szolgáltatás állapotára vonatkozó értesítések](monitoring-service-notifications.md).

