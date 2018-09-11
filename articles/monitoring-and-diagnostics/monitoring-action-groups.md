---
title: Az Azure Portalon a műveleti csoportok létrehozása és kezelése
description: Ismerje meg, hogyan hozhat létre és kezelheti az Azure Portalon Műveletcsoportok.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/1/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 441469e24de5324fb5bed40c75f9a6b26f85bcc5
ms.sourcegitcommit: 465ae78cc22eeafb5dfafe4da4b8b2138daf5082
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44325056"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Az Azure Portalon a műveleti csoportok létrehozása és kezelése
## <a name="overview"></a>Áttekintés ##
Műveletcsoport gyűjteménye, felhasználó által definiált értesítési beállításokat. Azure Monitor és a Service Health-riasztások a riasztás akkor aktiválódik, amikor egy adott műveletcsoport használandó vannak konfigurálva. Az azonos műveletcsoport vagy a felhasználó követelményeitől függően különböző Műveletcsoportok különböző riasztások használhatja.

Ez a cikk bemutatja, hogyan hozhat létre és kezelheti az Azure Portalon Műveletcsoportok.

Minden művelet a következő tulajdonságok tevődik össze:

* **Név**: a művelet csoporton belül egyedi azonosítója.  
* **Művelet típusa**: hanghívások vagy SMS-küldenek, e-mail küldése, webhook meghívása, adatokat küld egy ITSM-eszközhöz, logikai alkalmazások hívása, leküldéses értesítés küldése az Azure-alkalmazáshoz vagy lefuttatni egy Automation-runbookot.
* **Részletek**: A megfelelő telefonszám, számot, az e-mail címet, a webhook URI-t vagy a ITSM-kapcsolat adatai.

Műveletcsoportok konfigurálása Azure Resource Manager-sablonok használatáról további információért lásd: [műveleti csoport Resource Manager-sablonok](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Műveletcsoport létrehozása az Azure portal használatával ##
1. Az a [portál](https://portal.azure.com)válassza **figyelő**. A **figyelő** panel összesíti az összes figyelési beállítást és adatokat egyetlen nézetben.

    ![A "Figyelés" szolgáltatás](./media/monitoring-action-groups/home-monitor.png)
1. Az a **beállítások** szakaszban jelölje be **Műveletcsoportok**.

    ![Az "Action groups" lap](./media/monitoring-action-groups/action-groups-blade.png)
1. Válassza ki **műveleti csoport hozzáadása**, és töltse ki a mezőket.

    ![A "Csoport hozzáadása művelet" parancs](./media/monitoring-action-groups/add-action-group.png)
1. Adjon meg egy nevet a a **műveletcsoport neve** mezőbe, majd adjon meg egy nevet a a **rövid, nevet** mezőbe. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

      ![A műveleti csoport hozzáadása"párbeszédpanel](./media/monitoring-action-groups/action-group-define.png)

1. A **előfizetés** az aktuális előfizetéshez autofills mezőbe. Ebben az előfizetésben a műveletcsoport elmentett.

1. Válassza ki a **erőforráscsoport** a a műveletcsoport mentve.

1. Adja meg a műveleteknek a listája, azáltal, hogy minden egyes művelethez:

    a. **Név**: Adjon meg egy egyedi azonosítót ehhez a művelethez.

    b. **Művelet típusa**: válassza ki az e-mailek és SMS és leküldéses/Hangvétel, a logikai alkalmazás, Webhookot, ITSM vagy Automation-Runbook.

    c. **Részletek**: a művelet típusa alapján, adja meg, egy telefonszám, e-mail címét, webhook URI-t, az Azure app, ITSM-kapcsolat vagy Automation-runbook. ITSM-művelet, továbbá adja meg **munkaelem** és a többi mező az ITSM-eszközhöz van szükség.

1. Válassza ki **OK** a műveletcsoport létrehozásához.

## <a name="action-specific-information"></a>A művelet adott információk
<dl>
<dt>Az Azure app-leküldés</dt>
<dd>Műveletcsoport az Azure app-műveletek legfeljebb 10 lehet.</dd>
<dd>Jelenleg az Azure-alkalmazás a művelet csak ServiceHealth riasztások támogatja. Más riasztási bármikor figyelmen kívül lesz hagyva. Lásd: [riasztások konfigurálása, ha a szolgáltatás állapotával kapcsolatos értesítés közzétételekor](monitoring-activity-log-alerts-on-service-notifications.md).</dd>

<dt>e-mailben</dt>
<dd>E-mailt küld a következő e-mail-címekről. Győződjön meg arról, hogy az e-mailek szűrése megfelelően vannak konfigurálva
<ul>
    <li>azure-noreply@microsoft.com</li>
    <li>azureemail-noreply@microsoft.com</li>
    <li>alerts-noreply@mail.windowsazure.com</li>
</ul>
</dd>
<dd>Előfordulhat, hogy e-mailes műveletek legfeljebb 1000 tartalmaz műveletcsoportot</dd>
<dd>Tekintse meg a [sebessége korlátozza az információk](./monitoring-alerts-rate-limiting.md) cikk</dd>

<dt>ITSM</dt>
<dd>Előfordulhat, hogy legfeljebb 10 ITSM-műveletek tartalmaz műveletcsoportot</dd>
<dd>ITSM-művelethez egy ITSM-kapcsolat szükséges. Ismerje meg, hogyan hozhat létre egy [ITSM-kapcsolat](../log-analytics/log-analytics-itsmc-overview.md).</dd>

<dt>Logikai alkalmazás</dt>
<dd>Előfordulhat, hogy legfeljebb 10 Logic App-műveletek tartalmaz műveletcsoportot</dd>

<dt>Runbook</dt>
<dd>Előfordulhat, hogy legfeljebb 10 Runbook műveleteket tartalmaz műveletcsoportot</dd>
<dd>Tekintse meg a [az Azure-előfizetési szolgáltatási korlátok](../azure-subscription-service-limits.md) a korlátok a Runbook is észleltünk adattartalmakat.</dd>

<dt>SMS</dt>
<dd>Előfordulhat, hogy legfeljebb 10 SMS műveleteket tartalmaz műveletcsoportot</dd>
<dd>Tekintse meg a [sebessége korlátozza az információk](./monitoring-alerts-rate-limiting.md) cikk</dd>
<dd>Tekintse meg a [SMS-riasztás viselkedése](monitoring-sms-alert-behavior.md) cikk</dd>

<dt>Hang</dt>
<dd>Előfordulhat, hogy legfeljebb 10 hangalapú műveleteket tartalmaz műveletcsoportot</dd>
<dd>Tekintse meg a [sebessége korlátozza az információk](./monitoring-alerts-rate-limiting.md) cikk</dd>

<dt>Webhook</dt>
<dd>Előfordulhat, hogy legfeljebb 10 Webhook-műveletek tartalmaz műveletcsoportot
<dd>Újrapróbálkozási logika – az időkorlát választ érték 10 másodperc. A webhook hívása lesz újból legfeljebb 2 idők esetén a következő HTTP-állapotkódok adja vissza: 408, 429-es, 503-as, 504 vagy a HTTP-végpont nem válaszol. Az első újrapróbálkozás 10 másodperc után történik. A második és az utolsó újrapróbálkozási 100 másodperc múlva történik.</dd>
</dl>

## <a name="manage-your-action-groups"></a>A műveleti csoportok kezelése ##
Miután létrehozta a műveletcsoport, is látható, az a **Műveletcsoportok** szakaszában a **figyelő** panelen. Válassza ki a kezelni kívánt műveletcsoport:

* Adja hozzá, szerkeszthet és eltávolíthat műveleteket.
* A műveletcsoport törlése.

## <a name="next-steps"></a>További lépések ##
* Tudjon meg többet [SMS-riasztás viselkedése](monitoring-sms-alert-behavior.md).  
* Szerezzen egy [megértése a tevékenység log riasztási webhookséma](monitoring-activity-log-alerts-webhook.md).  
* Tudjon meg többet [ITSM-összekötő](../log-analytics/log-analytics-itsmc-overview.md)
* Tudjon meg többet [sebességkorlátozással](monitoring-alerts-rate-limiting.md) a riasztásokat.
* Get- [tevékenységnapló-riasztások áttekintése](monitoring-overview-alerts.md), és a riasztások fogadása.  
* Ismerje meg, hogyan [riasztások konfigurálása, ha a szolgáltatás állapotával kapcsolatos értesítés közzétételekor](monitoring-activity-log-alerts-on-service-notifications.md).
