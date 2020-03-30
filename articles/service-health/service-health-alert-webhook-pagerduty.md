---
title: Az Azure-szolgáltatás állapoti riasztásainak küldése a PagerDuty segítségével
description: Személyre szabott értesítéseket kaphat a szolgáltatás állapoteseményeiről a PagerDuty-példányba.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: bb449a5279f3cea55e6aec2f72edfd11fb26227a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654069"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Az Azure-szolgáltatás állapotriasztásának küldése a PagerDuty szolgáltatással webhookok használatával

Ez a cikk bemutatja, hogyan állíthatja be az Azure-szolgáltatás állapotára vonatkozó értesítéseket a PagerDuty webhook használatával. A [PagerDuty](https://www.pagerduty.com/)egyéni Microsoft Azure-integrációs típusának használatával könnyedén hozzáadhat Service Health-riasztásokat az új vagy meglévő PagerDuty-szolgáltatásokhoz.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Szolgáltatásállapot-integrációs URL-cím létrehozása a PagerDuty szolgáltatásban
1.  Győződjön meg róla, hogy feliratkozott, és be van jelentkezve [a PagerDuty](https://www.pagerduty.com/) fiókjába.

1.  Keresse meg a **PagerDuty Szolgáltatások** szakaszát.

    ![A "Szolgáltatások" szakasz a PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Válassza **az Új szolgáltatás hozzáadása lehetőséget,** vagy nyisson meg egy már beállított szolgáltatást.

1.  Az **Integrációs beállítások csoportban**válassza ki a következőket:

    a. **Integráció típusa**: Microsoft Azure

    b. **Integráció**neve \<: Név\>

    ![Az "Integrációs beállítások" a PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Töltse ki a többi kötelező mezőt, és válassza **a Hozzáadás lehetőséget.**

1.  Nyissa meg ezt az új integrációt, és másolja és mentse az **integrációs URL-t.**

    ![Az "Integrációs URL" a PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Riasztás létrehozása a PagerDuty használatával az Azure Portalon
### <a name="for-a-new-action-group"></a>Új műveletcsoport esetén:
1. Kövesse az [1–8.](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

1. Definiálás a **műveletek**listájában:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** A korábban mentett **PagerDuty-integrációurl-címe.**

    c. **Név:** Webhook neve, aliasa vagy azonosítója.

1. A riasztás létrehozásához válassza a **Mentés,** ha kész lehetőséget.

### <a name="for-an-existing-action-group"></a>Meglévő műveletcsoport esetén:
1. Az [Azure Portalon](https://portal.azure.com/)válassza a **Figyelő**lehetőséget.

1. A **Beállítások csoportban** válassza a **Műveletcsoportok lehetőséget.**

1. Keresse meg és jelölje ki a szerkesztni kívánt műveletcsoportot.

1. Hozzáadás a **műveletek listájához:**

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** A korábban mentett **PagerDuty-integrációurl-címe.**

    c. **Név:** Webhook neve, aliasa vagy azonosítója.

1. A műveletcsoport frissítéséhez válassza a **Mentés** gombra, ha elkészült.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>A webhook-integráció tesztelése HTTP POST-kérelemmel
1. Hozza létre az elküldeni kívánt szolgáltatás állapothasznos adatát. Egy példa szolgáltatás állapotát webhook hasznos adat a [Webhooks az Azure-tevékenységnapló-riasztások.](../azure-monitor/platform/activity-log-alerts-webhook.md)

1. Http POST-kérelem létrehozása az alábbiak szerint:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Az "eseményazonosítóját" tartalmazó üzenetet kell kapnia. `202 Accepted`

1. Nyissa meg a [PagerDuty](https://www.pagerduty.com/) webhelyet, és ellenőrizze, hogy az integráció sikeresen be van-e állítva.

## <a name="next-steps"></a>További lépések
- További információ [arról, hogyan állíthat be webhook-értesítéseket a meglévő problémakezelő rendszerekhez.](service-health-alert-webhook-guide.md)
- Tekintse át a [tevékenységnapló-riasztási webhook-sémáját.](../azure-monitor/platform/activity-log-alerts-webhook.md) 
- További információ a [szolgáltatásállapot-értesítésekről.](../azure-monitor/platform/service-notifications.md)
- További információ a [műveletcsoportokról](../azure-monitor/platform/action-groups.md).