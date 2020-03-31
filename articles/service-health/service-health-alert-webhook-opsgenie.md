---
title: Az Azure-szolgáltatás állapotriasztásának küldése az OpsGenie szolgáltatással webhookok használatával
description: Személyre szabott értesítéseket kaphat a szolgáltatásállapot-eseményekről az OpsGenie-példányra.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: def12d5e7b1b93b8370cd7be61538fca53531ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654137"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Az Azure-szolgáltatás állapotriasztásának küldése az OpsGenie szolgáltatással webhookok használatával

Ez a cikk bemutatja, hogyan állíthatja be az Azure-szolgáltatás állapotjelző riasztásait az OpsGenie webhook használatával. Az [OpsGenie](https://www.opsgenie.com/)Azure-szolgáltatásállapot-integrációjának használatával továbbíthatja az Azure Service Health-riasztásokat az OpsGenie-nek. OpsGenie tudja meghatározni a megfelelő embereket, hogy értesítse alapján on-call menetrendek, e-mail, szöveges üzenetek (SMS), telefonhívások, iOS & Android push értesítések, és eszkalálódó riasztások, amíg a riasztás nyugtázza vagy le.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Szolgáltatásállapot-integrációs URL-cím létrehozása az OpsGenie-ben
1.  Győződjön meg róla, hogy feliratkozott, és be van jelentkezve [opsgenie](https://www.opsgenie.com/) fiókjába.

1.  Keresse meg az OpsGenie **Integrációk** szakaszát.

    ![Az OpsGenie "Integrációk" című szakasza](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Válassza az **Azure Service Health** integrációs gombot.

    ![Az "Azure Service Health gomb" az OpsGenie-ben](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Nevezze el** a riasztást, és adja meg a **Csapathoz rendelt** mezőt.

1.  Töltse ki a többi mezőt, például **a Címzettek**, **az Engedélyezve**és **az Értesítések letiltása mezőt.**

1.  Másolja és mentse az **integrációs URL-címet,** amelynek már tartalmaznia `apiKey` kell a hozzáfűzve a végén.

    ![Az "Integrációs URL" az OpsGenie-ben](./media/webhook-alerts/opsgenie-integration-url.png)

1.  **Integráció mentése** lehetőséget

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Riasztás létrehozása az OpsGenie használatával az Azure Portalon
### <a name="for-a-new-action-group"></a>Új műveletcsoport esetén:
1. Kövesse az [1–8.](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

1. Definiálás a **műveletek**listájában:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** A korábban mentett **OpsGenie-integrációs URL-cím.**

    c. **Név:** Webhook neve, aliasa vagy azonosítója.

1. A riasztás létrehozásához válassza a **Mentés,** ha kész lehetőséget.

### <a name="for-an-existing-action-group"></a>Meglévő műveletcsoport esetén:
1. Az [Azure Portalon](https://portal.azure.com/)válassza a **Figyelő**lehetőséget.

1. A **Beállítások csoportban** válassza a **Műveletcsoportok lehetőséget.**

1. Keresse meg és jelölje ki a szerkesztni kívánt műveletcsoportot.

1. Hozzáadás a **műveletek listájához:**

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** A korábban mentett **OpsGenie-integrációs URL-cím.**

    c. **Név:** Webhook neve, aliasa vagy azonosítója.

1. A műveletcsoport frissítéséhez válassza a **Mentés** gombra, ha elkészült.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>A webhook-integráció tesztelése HTTP POST-kérelemmel
1. Hozza létre az elküldeni kívánt szolgáltatás állapothasznos adatát. Egy példa szolgáltatás állapotát webhook hasznos adat a [Webhooks az Azure-tevékenységnapló-riasztások.](../azure-monitor/platform/activity-log-alerts-webhook.md)

1. Http POST-kérelem létrehozása az alábbiak szerint:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. A "sikeres" állapot üzenetével `200 OK` választ kell kapnia.

1. Nyissa meg az [OpsGenie](https://www.opsgenie.com/) webhelyet, és ellenőrizze, hogy az integráció sikeresen be lett-e állítva.

## <a name="next-steps"></a>További lépések
- További információ [arról, hogyan állíthat be webhook-értesítéseket a meglévő problémakezelő rendszerekhez.](service-health-alert-webhook-guide.md)
- Tekintse át a [tevékenységnapló-riasztási webhook-sémáját.](../azure-monitor/platform/activity-log-alerts-webhook.md) 
- További információ a [szolgáltatásállapot-értesítésekről.](../azure-monitor/platform/service-notifications.md)
- További információ a [műveletcsoportokról](../azure-monitor/platform/action-groups.md).