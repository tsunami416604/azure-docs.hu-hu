---
title: Az Azure portálon művelet csoportok létrehozása és kezelése
description: Útmutató az Azure portálon művelet csoportok létrehozásához és kezeléséhez.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/1/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 63216d56fb3acbb954086fbf026441e69073621e
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263065"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Az Azure portálon művelet csoportok létrehozása és kezelése
## <a name="overview"></a>Áttekintés ##
Egy művelet csoport, a felhasználó által megadott értesítési beállítások gyűjteménye. Egy bizonyos művelet csoport használatára, a riasztás aktiválása Azure figyelő és a szolgáltatás állapotát riasztások vannak konfigurálva. Különféle riasztások is megjelenhetnek a azonos művelet csoport(ok) különböző művelet attól függően, hogy a felhasználói követelmények használhat.

Ez a cikk bemutatja, hogyan az Azure portálon művelet csoportok létrehozásához és kezeléséhez.

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
4. Adjon meg egy nevet a a **művelet csoportnév** mezőbe, majd írjon be egy nevet a **rövid név** mezőbe. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

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
<dd>A következő e-mail címek a kapnak e-mailt. Győződjön meg arról, hogy az e-mailek szűrése megfelelően konfigurálta

    - azure-noreply@microsoft.com
    - azureemail-noreply@microsoft.com
    - alerts-noreply@mail.windowsazure.com
    
</dd>
<dd>Előfordulhat, hogy legfeljebb 1000 e-mail műveletek művelet-csoportban</dd>
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
<dd>Újrapróbálkozási logika - időkorlát választ érték 10 másodperc. A webhook hívás lesz a rendszer megpróbálja újból végrehajtani a maximális 2 alkalommal létrehozásakor a következő HTTP-állapotkódok vannak okozott: 408, 429, 503-as, 504 vagy a HTTP-végpont nem válaszol. Az első újrapróbálkozásnál 10 másodperc után történik. A második és az utolsó újrapróbálkozási 100 másodpercen belül történik.</dd>
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
