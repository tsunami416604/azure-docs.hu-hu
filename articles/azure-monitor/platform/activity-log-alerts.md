---
title: Tevékenységnapló-riasztások az Azure Monitorban
description: Értesítést kell küldeni SMS-ben, webhook, SMS, e-mail és több, ha bizonyos események fordulnak elő a tevékenységnaplóban.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 26ecfdb33b92c91010af63ec14089dd148d6bad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669013"
---
# <a name="alerts-on-activity-log"></a>Riasztások a tevékenységnaplóban

## <a name="overview"></a>Áttekintés

A tevékenységnapló-riasztások olyan riasztások, amelyek akkor aktiválódnak, ha olyan új [tevékenységnapló-esemény](activity-log-schema.md) következik be, amely megfelel a riasztásban megadott feltételeknek. Az [Azure tevékenységnaplójában](platform-logs-overview.md)rögzített események sorrendje és mennyisége alapján a riasztási szabály kigyullad. A tevékenységnapló-riasztási szabályok Azure-erőforrások, így egy Azure Resource Manager-sablon használatával hozhatók létre. Ők is létrehozható, frissíthető vagy törölhetők az Azure Portalon. Ez a cikk bemutatja a tevékenységnapló-riasztások mögötti fogalmakat. A tevékenységnapló-riasztási szabályok létrehozásával és használatával kapcsolatos további tudnivalókért olvassa el a [Tevékenységnapló-riasztások létrehozása és kezelése című témakört.](alerts-activity-log.md)

> [!NOTE]
> A tevékenységnapló riasztási kategóriájában lévő eseményekhez **nem** hozhatók létre riasztások.

Általában tevékenységnapló-riasztásokat hoz létre, hogy értesítéseket kapjon, ha:

* Adott műveletek az Azure-előfizetésben lévő erőforrásokon fordulnak elő, amelyek gyakran adott erőforráscsoportokra vagy erőforrásokra vonatkoznak. Előfordulhat például, hogy értesítést szeretne kapni, ha a myProductionResourceGroup bármely virtuális gépe törlődik. Vagy érdemes lehet értesítést kapnia, ha új szerepkörök vannak hozzárendelve egy felhasználóhoz az előfizetésben.
* Szolgáltatásállapot-esemény következik be. A szolgáltatás állapoteseményei közé tartozik az előfizetésben lévő erőforrásokra vonatkozó incidensek és karbantartási események értesítése.

Egy egyszerű analógia a feltételek megértéséhez, amelyek riasztási szabályok hozhatók létre a tevékenységnaplóban, hogy feltárja vagy szűrje az eseményeket [az Azure Portal tevékenységnaplóján](activity-log-view.md#azure-portal)keresztül. Az Azure Monitor – Tevékenységnaplóban szűrheti vagy megtalálhatja a szükséges eseményt, majd riasztást hozhat létre a **Tevékenységnapló riasztáshozzáadása** gombbal.

Mindkét esetben egy tevékenységnapló-riasztás csak az okat az előfizetésben lévő eseményeket figyeli, amelyben a riasztás t.

A JSON-objektum bármely legfelső szintű tulajdonsága alapján konfigurálhat tevékenységnapló-riasztást egy tevékenységnapló-eseményhez. További információ: [Kategóriák a tevékenységnaplóban.](activity-log-view.md#categories-in-the-activity-log) Ha többet szeretne megtudni a szolgáltatásállapot-eseményekről, olvassa el [a Tevékenységnapló-értesítések fogadása a szolgáltatásértesítésekről.](alerts-activity-log-service-notifications.md) 

A tevékenységnapló-riasztások hoz néhány gyakori lehetőség:

- **Kategória**: Adminisztrációs, Szolgáltatás állapot, Automatikus skálázás, Biztonság, Házirend és Ajánlás. 
- **Hatókör**: Az az erőforrás vagy erőforráskészlet, amelyhez a tevékenységnaplóban lévő riasztás definiálva van. A tevékenységnapló-riasztás hatóköre különböző szinteken határozható meg:
    - Erőforrásszint: Például egy adott virtuális gépesetében
    - Erőforráscsoport szintje: Például egy adott erőforráscsoport összes virtuális gépe
    - Előfizetési szint: Például az előfizetésben (vagy) az előfizetésösszes erőforrásában lévő összes virtuális gép
- **Erőforráscsoport**: Alapértelmezés szerint a riasztási szabály ugyanabba az erőforráscsoportba kerül, mint a Hatókörben definiált cél. A felhasználó is megadhatja az erőforráscsoport, ahol a riasztási szabályt kell tárolni.
- **Erőforrástípusa**: Az erőforrás-kezelő névteret adott meg a riasztás céljához.
- **Művelet neve**: Az [Azure Resource Manager szerepköralapú](../../role-based-access-control/resource-provider-operations.md) hozzáférés-vezérléshez használt műveletneve. Az Azure Resource Manager rel nem regisztrált műveletek nem használhatók tevékenységnapló-riasztási szabályban.
- **Szint**: Az esemény súlyossági szintje (részletes, tájékoztató, figyelmeztetés, hiba vagy kritikus).
- **Állapot**: Az esemény állapota, általában Elindítva, Sikertelen vagy Sikeres.
- **Az eseményt a következő kezdeményezte:**"hívó". A műveletet végző felhasználó e-mail-címe vagy Azure Active Directory-azonosítója.

> [!NOTE]
> Egy előfizetésben legfeljebb 100 riasztási szabályok hozhatók létre egy hatókör-tevékenységhez: egyetlen erőforrás, az erőforráscsoport (vagy) teljes előfizetési szintjének összes erőforrása.

Ha egy tevékenységnapló-riasztás aktiválva van, egy műveletcsoportot használ műveletek vagy értesítések létrehozásához. A műveletcsoport az értesítési fogadók újrafelhasználható készlete, például e-mail címek, webhook URL-címek vagy SMS-telefonszámok. A fogadók több riasztásból is hivatkozhatók az értesítési csatornák központosítása és csoportosítása érdekében. A tevékenységnapló-riasztás definiálásakor két lehetősége van. A következőket teheti:

* Használjon egy meglévő műveletcsoportot a tevékenységnapló-riasztásban.
* Hozzon létre egy új műveletcsoportot.

Ha többet szeretne megtudni a műveletcsoportokról, olvassa [el a Műveletcsoportok létrehozása és kezelése az Azure Portalon című témakört.](action-groups.md)


## <a name="next-steps"></a>További lépések

- Áttekintés [a riasztásokról.](alerts-overview.md)
- További információ a [tevékenységnapló-riasztások létrehozásáról és módosításáról.](alerts-activity-log.md)
- Tekintse át a [tevékenységnapló-riasztási webhook-sémáját.](activity-log-alerts-webhook.md)
- További információ a [szolgáltatásállapot-értesítésekről.](service-notifications.md)
