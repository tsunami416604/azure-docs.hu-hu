---
title: Az Azure szolgáltatáshoz értesítést tevékenység napló riasztásokat fogadhat.
description: Értesítés SMS, e-mailben vagy webhook Azure-szolgáltatás esetén.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 01dc3a3c6489b694af26c78ae3b4756f3e8f00b7
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263116"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>A szolgáltatás értesítések napló riasztások tevékenység létrehozása
## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan tevékenység napló riasztások a szolgáltatás állapotával kapcsolatos értesítésekre beállítása az Azure portál használatával.  

Akkor is figyelmezteti, ha Azure szolgáltatás állapotával kapcsolatos értesítésekre küld az Azure-előfizetéshez. A riasztás alapján konfigurálhatja:

- A-osztály szolgáltatás állapota (szolgáltatásokkal kapcsolatos problémákról, tervezett karbantartás, egészségügyi tanácsadók).
- Az előfizetés hatással.
- Az érintett szolgáltatásokat.
- Az érintett régió(k) esetében.

Emellett konfigurálhatja ki a riasztást küldjön el:

- Válasszon egy meglévő művelet csoportot.
- Hozzon létre egy új művelet (riasztást használható).

Művelet csoportokkal kapcsolatos további tudnivalókért lásd: [létrehozása és kezelése a művelet](monitoring-action-groups.md).

Állapotfigyelő értesítési szolgáltatásriasztások konfigurálása Azure Resource Manager-sablonok használatával kapcsolatos információkért lásd: [Resource Manager-sablonok](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Egy új művelet csoport Állapotfigyelő szolgáltatás értesítést a riasztás létrehozása az Azure portál használatával
1. Az a [portal](https://portal.azure.com), jelölje be **szolgáltatásának állapota**.

    ![A "Service" állapotszolgáltatás](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

2. Az a **riasztások** szakaszban jelölje be **Health-riasztások**.

    ![Az "Állapotfigyelő értesítések" lapon](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

3. Válassza ki **létrehozás szolgáltatás állapotriasztás** és töltse ki a mezőket.

    ![A "Create service állapotriasztás" parancs](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

4. Válassza ki a **előfizetés**, **szolgáltatások**, és **régiók** a kapni kívánt.

    ![A "Napló figyelmeztetés hozzáadása" párbeszédpanel](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Ez az előfizetés tevékenységgel menthetők a napló figyelmeztetés. A riasztási erőforrás ebbe az előfizetésbe telepítve van, és az események a műveletnaplóban figyeli.

5. Válassza ki a **eseménytípusok** a kapni kívánt: *probléma szolgáltatás*, *a tervezett karbantartások*, és *állapotfigyelő tanácsadók* 

6. Adja meg a riasztás részleteinek megadásával egy **riasztásiszabály-névnek** és **leírás**.

7. Válassza ki a **erőforráscsoport** , amelyben szeretné menteni a riasztást.

8. Hozzon létre egy új művelet csoportot kiválasztásával **új művelet csoport**. Adjon meg egy nevet a a **művelet csoportnév** mezőbe, majd írjon be egy nevet a **rövid nevét** mezőbe. A rövid nevét a riasztás aktiválódásakor közreműködésével küldött értesítő hivatkozik.

    ![A művelet új csoport létrehozása](./media/monitoring-activity-log-alerts-on-service-notifications/action-group-creation.png)

9. Adja meg a fogadók listáját a fogadó megadásával:

    a. **Név**: Adja meg a fogadó alias, név vagy azonosító.

    b. **Művelet típusa**: válassza ki az SMS, e-mailek, webhook, Azure-alkalmazás és több.

    c. **Részletek**: a kiválasztott művelet típusa alapján, adjon meg egy telefonszámot, e-mail címét, webhook URI, stb.

10. Válassza ki **OK** művelet csoport létrehozásához, majd **riasztási szabály létrehozása** a riasztás befejezéséhez.

Néhány percen belül a riasztás aktív, és elkezdi való létrehozása során a megadott feltételek alapján.

Megtudhatja, hogyan [meglévő probléma felügyeleti rendszerekhez webhook értesítések konfigurálása](../service-health/service-health-alert-webhook-guide.md). A napló tevékenységriasztásokat webhook sémáját információkért lásd: [Webhookok Azure tevékenység naplózása riasztások](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>A művelet csoportnak, az alábbi lépéseket az használható fel újra egy meglévő művelet csoportként jövőbeli riasztási-definíciók.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Hozzon létre egy riasztást az állapotfigyelő szolgáltatás értesítést meglévő művelet csoportok az Azure portál használatával

1. Végezze el az 1-7 hozhat létre a szolgáltatás állapotának értesítési az előző szakaszban. 

2. A **Define művelet csoport**, kattintson a **kijelölési művelet csoport** gombra. Válassza ki a megfelelő műveletet.

3. Válassza ki **Hozzáadás** hozzá a műveleti csoportot, majd **riasztási szabály létrehozása** a riasztás befejezéséhez.

Néhány percen belül a riasztás aktív, és elkezdi való létrehozása során a megadott feltételek alapján.

## <a name="manage-your-alerts"></a>A riasztások kezelése

Riasztás létrehozása után is látható, az a **riasztások** szakasza **figyelő**. Jelölje ki a kezelni kívánt riasztást:

* Szerkesztheti.
* Törölje a parancsikont.
* Tiltsa le, vagy engedélyezheti, ha azt szeretné, ideiglenesen leállítani, vagy folytassa a riasztás értesítések fogadásának.

## <a name="next-steps"></a>További lépések
- Megtudhatja, hogyan [meglévő probléma felügyeleti rendszerekhez webhook értesítések konfigurálása](../service-health/service-health-alert-webhook-guide.md).
- További tudnivalók [szolgáltatás állapotával kapcsolatos értesítésekre](monitoring-service-notifications.md).
- További tudnivalók [értesítési sebessége korlátozza az](monitoring-alerts-rate-limiting.md).
- Tekintse át a [műveletnapló riasztási webhook séma](monitoring-activity-log-alerts-webhook.md).
- Első egy [tevékenység napló riasztások áttekintése](monitoring-overview-alerts.md), és megtudhatja, hogyan szeretné megkapni a riasztásokat. 
- További információ [művelet csoportok](monitoring-action-groups.md).
