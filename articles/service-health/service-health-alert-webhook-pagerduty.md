---
title: Azure-szolgáltatások health-riasztások konfigurálása PagerDuty |} Microsoft Docs
description: Állapotfigyelő szolgáltatás események PagerDuty-példány személyre szabott értesítések lekérése.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 6e9fcf20d368e270f9af4551c539acd873335498
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30178912"
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>PagerDuty állapotfigyelő szolgáltatásriasztások konfigurálása

Ez a cikk bemutatja, hogyan állíthatja be az Azure szolgáltatás állapotával kapcsolatos értesítésekre PagerDuty keresztül egy webhook használatával. Használatával [PagerDuty](https://www.pagerduty.com/)tartozó egyéni Microsoft Azure-integráció típusa, az új vagy meglévő PagerDuty szolgáltatások egyszerűen lehet szolgáltatásának állapota riasztásokat is hozzáadhat.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>A szolgáltatás állapotának integrációs URL-címet PagerDuty létrehozása
1.  Ellenőrizze, hogy rendelkezik regisztrált a bejelentkezett a [PagerDuty](https://www.pagerduty.com/) fiók.

2.  Keresse meg a **szolgáltatások** PagerDuty szakaszában.

    ![A "Szolgáltatások" című PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

3.  Válassza ki **hozzáadása új szolgáltatás** vagy nyisson meg egy meglévő szolgáltatást állította be.

4.  Az a **integrációs beállítások**, jelölje be az alábbiakat:

    a. **Integráció típusa**: Microsoft Azure

    b. **Integráció neve**: \<neve\>

    ![A PagerDuty "integrációs beállításai"](./media/webhook-alerts/pagerduty-integration-settings.png)

5.  A többi kötelező mezőt kitöltötte, és válassza ki **Hozzáadás**.

6.  Nyissa meg az új integrációs, másolja és mentse a **integrációs URL-címet**.

    ![Az "integrációs URL-címet" PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Hozzon létre egy riasztást PagerDuty használata az Azure-portálon
### <a name="for-a-new-action-group"></a>Egy új művelet csoporthoz:
1. Végezze el az 1 – 8 [egy új művelet csoport Állapotfigyelő szolgáltatás értesítést a riasztás létrehozása az Azure portál használatával](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Adja meg a közül **műveletek**:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** a PagerDuty **integrációs URL-címet** meg a korábban mentett.

    c. **Name:** Webhook tartozó név, az alias vagy a azonosítója.

3. Válassza ki **mentése** végzett a riasztás létrehozása.

### <a name="for-an-existing-action-group"></a>Meglévő művelet csoportok:
1. Az a [Azure-portálon](https://portal.azure.com/), jelölje be **figyelő**.

2. Az a **beállítások** szakaszban jelölje be **művelet csoportok**.

3. Keresse meg és válassza ki a szerkeszteni kívánt művelet csoportot.

4. Vegye fel a **műveletek**:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** a PagerDuty **integrációs URL-címet** meg a korábban mentett.

    c. **Name:** Webhook tartozó név, az alias vagy a azonosítója.

5. Válassza ki **mentése** végzett frissíteni a művelet csoportot.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>A webhook integrációs via HTTP POST-kérelmet tesztelése
1. Hozzon létre a szolgáltatás állapotának forgalma szeretne küldeni. Példa service állapotfigyelő webhook payloadot címen található [Webhookok Azure tevékenység naplózása riasztások](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Hozzon létre egy HTTP POST-kérelmet az alábbiak szerint:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Ekkor egy `202 Accepted` egy üzenet, amely tartalmazza a "event ID azonosítójával."

4. Ugrás a [PagerDuty](https://www.pagerduty.com/) annak ellenőrzéséhez, hogy integráció sikeres volt-e beállítva.

## <a name="next-steps"></a>További lépések
- Megtudhatja, hogyan [meglévő probléma felügyeleti rendszerekhez webhook értesítések konfigurálása](service-health-alert-webhook-guide.md).
- Tekintse át a [műveletnapló riasztási webhook séma](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- További tudnivalók [szolgáltatás állapotával kapcsolatos értesítésekre](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- További információ [művelet csoportok](../monitoring-and-diagnostics/monitoring-action-groups.md).