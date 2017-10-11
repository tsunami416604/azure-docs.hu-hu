---
title: "Tevékenység napló értesítéseket szolgáltatás értesítések |} Microsoft Docs"
description: "Értesítés SMS, e-mailben vagy webhook Azure-szolgáltatás esetén."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.openlocfilehash: bf6a98fd7e7e11764bef174f9efd0635fa7efe9a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>A szolgáltatás értesítések napló riasztások tevékenység létrehozása
## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan tevékenység napló riasztások a szolgáltatás állapotával kapcsolatos értesítésekre beállítása az Azure portál használatával.  

Akkor is figyelmezteti, ha Azure szolgáltatás állapotával kapcsolatos értesítésekre küld az Azure-előfizetéshez. A riasztás alapján konfigurálhatja:

- A-osztály szolgáltatás állapota (incidens, karbantartási, stb.).
- Az érintett szolgáltatásokat.
- Az érintett régió(k) esetében.
- Az értesítés (aktív vagy megoldott) állapotát.
- Az értesítések (információs, figyelmeztetési, hiba) szintjét.

Emellett konfigurálhatja ki a riasztást küldjön el:

- Válasszon egy meglévő művelet csoportot.
- Hozzon létre egy új művelet (riasztást használható).

Művelet csoportokkal kapcsolatos további tudnivalókért lásd: [létrehozása és kezelése a művelet](monitoring-action-groups.md).

Állapotfigyelő értesítési szolgáltatásriasztások konfigurálása Azure Resource Manager-sablonok használatával kapcsolatos információkért lásd: [Resource Manager-sablonok](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Egy új művelet csoport Állapotfigyelő szolgáltatás értesítést a riasztás létrehozása az Azure portál használatával
1. Az a [portal](https://portal.azure.com), jelölje be **figyelő**.

    ![A "Figyelés" szolgáltatás](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)

2. Az a **tevékenységnapló** szakaszban jelölje be **riasztások**.

    ![Az "Értesítések" lapon](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)

3. Válassza ki **Hozzáadás figyelmeztetés a napló**, és töltse ki a mezőket.

    ![A "Hozzáadás napló figyelmeztetés" parancs](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)

4. Írjon be egy nevet a **tevékenység napló riasztás neve** mezőbe, majd adjon meg egy **leírása**.

    ![A "Napló figyelmeztetés hozzáadása" párbeszédpanel](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group.png)

5. A **előfizetés** a jelenlegi előfizetés autofills mezőben. Ez az előfizetés tevékenységgel menthetők a napló figyelmeztetés. A riasztási erőforrás ebbe az előfizetésbe telepítve van, és az események a műveletnaplóban figyeli.

6. Válassza ki a **erőforráscsoport** a riasztási erőforrás létrehozása. Ez az erőforráscsoport, amelyet a riasztás nem található. Ehelyett az erőforráscsoportot, ahol a riasztás erőforrás.

7. Az a **eseménykategória** mezőben válassza **szolgáltatásának állapota**. Bejelölheti a **szolgáltatás**, **régió**, **típus**, **állapot**, és **szint** a szeretne kapni, a szolgáltatás állapotával kapcsolatos értesítésekre.

8. A **keresztül riasztási**, jelölje be a **új** művelet csoport gombra. Adjon meg egy nevet a a **művelet csoportnév** mezőbe, majd írjon be egy nevet a **rövid név** mezőbe. A rövid nevét a riasztás aktiválódásakor közreműködésével küldött értesítő hivatkozik.

9. Adja meg a fogadók listáját a fogadó megadásával:

    a. **Név**: Adja meg a fogadó alias, név vagy azonosító.

    b. **Művelet típusa**: válassza ki az SMS, e-mailek vagy webhook.

    c. **Részletek**: választott művelet típusa alapján, adjon meg egy telefonszámot, az e-mail címet, illetve a webhook URI.

10. Válassza ki **OK** a riasztás létrehozása.

Néhány percen belül a riasztás aktív, és elkezdi való létrehozása során a megadott feltételek alapján.

A napló tevékenységriasztásokat webhook sémáját információkért lásd: [Webhookok Azure tevékenység naplózása riasztások](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>A művelet csoportnak, az alábbi lépéseket az használható fel újra egy meglévő művelet csoportként jövőbeli riasztási-definíciók.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Hozzon létre egy riasztást az állapotfigyelő szolgáltatás értesítést meglévő művelet csoportok az Azure portál használatával

1. Végezze el az 1-7 hozhat létre a szolgáltatás állapotának értesítési az előző szakaszban. 

2. A **keresztül riasztási**, jelölje be a **meglévő** művelet csoport gombra. Válassza ki a megfelelő műveletet.

3. Válassza ki **OK** a riasztás létrehozása.

Néhány percen belül a riasztás aktív, és elkezdi való létrehozása során a megadott feltételek alapján.

## <a name="manage-your-alerts"></a>A riasztások kezelése

Riasztás létrehozása után is látható, az a **riasztások** szakasza a **figyelő** panelen. Jelölje ki a kezelni kívánt riasztást:

* Szerkesztheti.
* Törölje a parancsikont.
* Tiltsa le, vagy engedélyezheti, ha azt szeretné, ideiglenesen leállítani, vagy folytassa a riasztás értesítések fogadásának.

## <a name="next-steps"></a>Következő lépések
- További tudnivalók [szolgáltatás állapotával kapcsolatos értesítésekre](monitoring-service-notifications.md).
- További tudnivalók [értesítési sebessége korlátozza az](monitoring-alerts-rate-limiting.md).
- Tekintse át a [műveletnapló riasztási webhook séma](monitoring-activity-log-alerts-webhook.md).
- Első egy [tevékenység napló riasztások áttekintése](monitoring-overview-alerts.md), és megtudhatja, hogyan szeretné megkapni a riasztásokat. 
- További információ [művelet csoportok](monitoring-action-groups.md).
