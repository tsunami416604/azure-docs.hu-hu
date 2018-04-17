---
title: Az Azure portálon művelet csoportok létrehozása és kezelése |} Microsoft Docs
description: Útmutató az Azure portálon művelet csoportok létrehozásához és kezeléséhez.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: dukek
ms.openlocfilehash: e3185b8d8ce97ffd04188b2b49a457bd14d5c6c8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Az Azure portálon művelet csoportok létrehozása és kezelése
## <a name="overview"></a>Áttekintés ##
Ez a cikk bemutatja, hogyan az Azure portálon művelet csoportok létrehozásához és kezeléséhez.

A művelet csoportok konfigurálható azon műveletek listáját. Minden riasztás definiál, biztosítva, hogy ugyanazokat a műveleteket a rendszer minden alkalommal, amikor a figyelmeztetést majd használhatják ezeket a csoportokat.

Egyes műveletek során a következő tulajdonságok tevődik össze:

* **Név**: a művelet csoporton belül egyedi azonosítója.  
* **Művelet típusa**: küldése hang hívás vagy SMS, küldjön egy e-mailt, hívható meg olyan webhook, adatokat küldeni egy ITSM eszköz, logikai alkalmazás hívja, leküldéses értesítést küldeni az Azure app vagy az Automation-forgatókönyv futtatása.
* **Részletek**: A megfelelő telefonszám, számot, e-mail címét, webhook URI vagy ITSM kapcsolódási adatait.

Művelet csoportok konfigurálása Azure Resource Manager-sablonok használatával kapcsolatos információkért lásd: [művelet csoport Resource Manager-sablonok](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Egy művelet csoport létrehozása az Azure-portál használatával ##
1. Az a [portal](https://portal.azure.com), jelölje be **figyelő**. A **figyelő** panel összes figyelési beállítások és adatok az egyik nézetben összesíti.

    ![A "Figyelés" szolgáltatás](./media/monitoring-action-groups/home-monitor.png)
2. Az a **beállítások** szakaszban jelölje be **művelet csoportok**.

    ![A "Művelet csoportok" lap](./media/monitoring-action-groups/action-groups-blade.png)
3. Válassza ki **művelet csoport hozzáadása**, és töltse ki a mezőket.

    ![A "Művelet csoport hozzáadása" parancs](./media/monitoring-action-groups/add-action-group.png)
4. Adjon meg egy nevet a a **művelet csoportnév** mezőbe, majd írjon be egy nevet a **rövid név** mezőbe. A rövid nevét használja a teljes műveletet csoport neve helyett amikor ez a csoport értesítések küldése.

      ![A művelet csoport hozzáadása"párbeszédpanel](./media/monitoring-action-groups/action-group-define.png)

5. A **előfizetés** a jelenlegi előfizetés autofills mezőben. Ez az előfizetés, amelyben a művelet csoport mentett lesz.

6. Válassza ki a **erőforráscsoport** az a művelet csoport mentve.

7. Adja meg azon műveletek listáját, adja meg a minden egyes művelethez:

    a. **Név**: Adjon meg egy egyedi azonosítót ehhez a művelethez.

    b. **Művelet típusa**: e-mailek és SMS/leküldéses/szóbeli, logikai alkalmazás, Webhook, ITSM vagy Automation-forgatókönyv kiválasztása.

    c. **Részletek**: művelet típusa alapján, adjon meg egy telefonszámot, e-mail címét, webhook URI, az Azure app, ITSM kapcsolat, vagy Automation-runbook. A ITSM művelet, továbbá meg **munkaelem** és más mezők a ITSM eszköz szükséges.

8. Válassza ki **OK** a művelet csoport létrehozásához.

## <a name="action-specific-information"></a>A művelet információk
<dl>
<dt>Azure-alkalmazás leküldési</dt>
<dd>Előfordulhat, hogy legfeljebb 10 Azure-alkalmazás műveletek művelet-csoportban.</dd>
<dd>Ekkor az Azure app csak által támogatott műveletet ServiceHealth riasztásokat. Más riasztási bármikor a rendszer figyelmen kívül hagyja. Lásd: [riasztások konfigurálása, ha az állapotfigyelő szolgáltatáshoz értesítést visszaküldi](monitoring-activity-log-alerts-on-service-notifications.md).</dd>

<dt>E-mailek</dt>
<dd>Előfordulhat, hogy legfeljebb 50 e-mail műveletek művelet-csoportban</dd>
<dd>Tekintse meg a [információk sebességével](./monitoring-alerts-rate-limiting.md) cikk</dd>

<dt>ITSM</dt>
<dd>Előfordulhat, hogy legfeljebb 10 ITSM műveletek művelet-csoportban</dd>
<dd>ITSM művelet ITSM kapcsolatot igényel. Megtudhatja, hogyan hozzon létre egy [ITSM kapcsolat](../log-analytics/log-analytics-itsmc-overview.md).</dd>

<dt>Logikai alkalmazás</dt>
<dd>Előfordulhat, hogy legfeljebb 10 logikai alkalmazás műveletek művelet-csoportban</dd>

<dt>a runbook</dt>
<dd>Előfordulhat, hogy legfeljebb 10 Runbook műveletek művelet-csoportban</dd>

<dt>SMS</dt>
<dd>Előfordulhat, hogy legfeljebb 10 SMS műveletek művelet-csoportban</dd>
<dd>Tekintse meg a [információk sebességével](./monitoring-alerts-rate-limiting.md) cikk</dd>
<dd>Tekintse meg a [SMS riasztási viselkedés](monitoring-sms-alert-behavior.md) cikk</dd>

<dt>hangalámondás</dt>
<dd>Előfordulhat, hogy legfeljebb 10 hang műveletek művelet-csoportban</dd>
<dd>Tekintse meg a [információk sebességével](./monitoring-alerts-rate-limiting.md) cikk</dd>

<dt>Webhook</dt>
<dd>Előfordulhat, hogy legfeljebb 10 webhookműveletek művelet-csoportban
<dd>Újrapróbálkozási logika - a webhook hívás lesz a rendszer megpróbálja újból végrehajtani egy legfeljebb 3-szor létrehozásakor a következő HTTP-állapotkódok vannak okozott: 408, 429, 503-as, 504</dd>
</dl>

## <a name="manage-your-action-groups"></a>A művelet csoportok kezelése ##
Egy művelet csoport létrehozása után is látható, az a **művelet csoportok** szakasza a **figyelő** panelen. Válassza ki a kezelni kívánt:

* Adja hozzá, szerkeszthet és eltávolíthat műveletek.
* A művelet csoport törlése.

## <a name="next-steps"></a>További lépések ##
* További információ [SMS riasztási viselkedés](monitoring-sms-alert-behavior.md).  
* Szerezzen egy [megismerni a műveletnapló riasztási webhook séma](monitoring-activity-log-alerts-webhook.md).  
* További információ [ITSM összekötő](../log-analytics/log-analytics-itsmc-overview.md)
* További információ [sebességkorlátozást](monitoring-alerts-rate-limiting.md) értesítésekről.
* Első egy [tevékenység napló riasztások áttekintése](monitoring-overview-alerts.md), és megtudhatja, hogyan szeretné megkapni a riasztásokat.  
* Megtudhatja, hogyan [riasztások konfigurálása, ha az állapotfigyelő szolgáltatáshoz értesítést visszaküldi](monitoring-activity-log-alerts-on-service-notifications.md).
