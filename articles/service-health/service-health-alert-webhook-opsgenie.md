---
title: Azure-szolgáltatások health-riasztások konfigurálása OpsGenie |} Microsoft Docs
description: Állapotfigyelő szolgáltatás események OpsGenie-példány személyre szabott értesítések lekérése.
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
ms.openlocfilehash: 6b8017f62dd895219f1d2cdac40f0efdf2db6c93
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30179344"
---
# <a name="configure-service-health-alerts-with-opsgenie"></a>OpsGenie állapotfigyelő szolgáltatásriasztások konfigurálása

Ez a cikk bemutatja, hogyan állítsa be a Azure-szolgáltatások állapotának riasztások OpsGenie egy webhook használatával. A [OpsGenie](https://www.opsgenie.com/)tartozó Azure szolgáltatás állapota integrálását, akkor is Azure szolgáltatás állapota riasztásokat továbbít OpsGenie. OpsGenie meg tudja állapítani a megfelelő értesítendő személyeket és az alapján a készenléti ütemezések használatával az e-mailek, szöveges üzenetek (SMS), telefonhívások, iOS és Android leküldéses értesítések és riasztások növekvő, amíg a riasztás nem nyugtázott vagy lezárt.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>A szolgáltatás állapotának integrációs URL-címet OpsGenie létrehozása
1.  Ellenőrizze, hogy rendelkezik regisztrált a bejelentkezett a [OpsGenie](https://www.opsgenie.com/) fiók.

2.  Keresse meg a **integrációja** OpsGenie szakaszában.

    ![A "Integrációja" című rész, OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

3.  Válassza ki a **Azure szolgáltatás állapota** integrációs gombra.

    ![Az "Azure szolgáltatás állapota gombjára" OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

4.  **Név** a riasztásra, és adja meg a **csapathoz rendelt** mező.

5.  Például a mezők kitöltése **címzettek**, **engedélyezve**, és **értesítések letiltásához**.

6.  Másolja ki és mentse a **integrációs URL-címet**, amely már tartalmaznia kell a `apiKey` vége lesz hozzáfűzve.

    ![Az "integrációs URL-címet" OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

7.  Válassza ki **integrációs mentése**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Hozzon létre egy riasztást OpsGenie használata az Azure-portálon
### <a name="for-a-new-action-group"></a>Egy új művelet csoporthoz:
1. Végezze el az 1 – 8 [egy új művelet csoport Állapotfigyelő szolgáltatás értesítést a riasztás létrehozása az Azure portál használatával](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Adja meg a közül **műveletek**:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** a OpsGenie **integrációs URL-címet** meg a korábban mentett.

    c. **Name:** Webhook tartozó név, az alias vagy a azonosítója.

3. Válassza ki **mentése** végzett a riasztás létrehozása.

### <a name="for-an-existing-action-group"></a>Meglévő művelet csoportok:
1. Az a [Azure-portálon](https://portal.azure.com/), jelölje be **figyelő**.

2. Az a **beállítások** szakaszban jelölje be **művelet csoportok**.

3. Keresse meg és válassza ki a szerkeszteni kívánt művelet csoportot.

4. Vegye fel a **műveletek**:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** a OpsGenie **integrációs URL-címet** meg a korábban mentett.

    c. **Name:** Webhook tartozó név, az alias vagy a azonosítója.

5. Válassza ki **mentése** végzett frissíteni a művelet csoportot.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>A webhook integrációs via HTTP POST-kérelmet tesztelése
1. Hozzon létre a szolgáltatás állapotának forgalma szeretne küldeni. Példa service állapotfigyelő webhook payloadot címen található [Webhookok Azure tevékenység naplózása riasztások](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Hozzon létre egy HTTP POST-kérelmet az alábbiak szerint:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Ekkor egy `200 OK` állapota "sikeres" üzenet válasz

4. Ugrás a [OpsGenie](https://www.opsgenie.com/) annak ellenőrzéséhez, hogy integráció sikeres volt-e beállítva.

## <a name="next-steps"></a>További lépések
- Megtudhatja, hogyan [meglévő probléma felügyeleti rendszerekhez webhook értesítések konfigurálása](service-health-alert-webhook-guide.md).
- Tekintse át a [műveletnapló riasztási webhook séma](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- További tudnivalók [szolgáltatás állapotával kapcsolatos értesítésekre](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- További információ [művelet csoportok](../monitoring-and-diagnostics/monitoring-action-groups.md).