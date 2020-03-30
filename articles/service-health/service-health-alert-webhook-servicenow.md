---
title: Az Azure-szolgáltatás állapotriasztásának küldése a ServiceNow szolgáltatással
description: Személyre szabott értesítéseket kaphat a ServiceNow-példány szolgáltatásállapot-eseményeiről.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 3daae05aabff571010d043cf5602847e95ea29f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654103"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Az Azure-szolgáltatás állapotriasztásának küldése a ServiceNow szolgáltatással webhookok használatával

Ez a cikk bemutatja, hogyan integrálhatja az Azure-szolgáltatás állapotjelző riasztásait a ServiceNow webhook használatával. A webhook-integráció beállítása után a ServiceNow-példány, a meglévő értesítési infrastruktúrán keresztül kap riasztásokat, ha az Azure-szolgáltatás problémái érintik Önt. Minden alkalommal, amikor egy Azure Service Health riasztás aktiválódik, webhookot hív meg a ServiceNow scriptelt REST API-n keresztül.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Parancsfájlalapú REST API létrehozása a ServiceNow szolgáltatásban

1.  Győződjön meg arról, hogy feliratkozott, és be van jelentkezve [ServiceNow-fiókjába.](https://www.servicenow.com/)

1.  Nyissa meg a ServiceNow **Rendszer webszolgáltatások** szakaszát, és válassza **a Parancsfájlalapú REST API-k lehetőséget.**

    ![A ServiceNow "Parancsfájlalapú webszolgáltatás" szakasza](./media/webhook-alerts/servicenow-sws-section.png)

1.  Új parancsfájlalapú REST-szolgáltatás létrehozásához válassza az **Új** lehetőséget.
 
    ![Az "Új parancsfájlalapú REST API" gomb a ServiceNow-ban](./media/webhook-alerts/servicenow-new-button.png)

1.  Adjon **hozzá egy nevet** a REST API-hoz, és állítsa az **API-azonosítót** `azureservicehealth`.

1.  Válassza a **Küldés** lehetőséget.

    ![A "REST API-beállítások" a ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Jelölje ki a létrehozott REST API-t, és az **Erőforrások** lapon válassza az **Új lehetőséget.**

    ![Az "Erőforrás lap" a ServiceNow-ban](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Nevezze el** `event` az új erőforrást, és módosítsa a **HTTP metódust** `POST`.

1.  A **Parancsfájl** szakaszban adja hozzá a következő JavaScript-kódot:

    >[!NOTE]
    >Frissítenie kell `<secret>`a`<group>`, `<email>` és az értéket az alábbi parancsfájlban.
    >* `<secret>`kell lennie egy véletlen karakterlánc, mint egy GUID
    >* `<group>`kell lennie annak a ServiceNow csoportnak, amelyhez hozzá kívánja rendelni az incidenst
    >* `<email>`kell lennie az a személynek, akihez hozzá kívánja rendelni az incidenst (nem kötelező)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  A Biztonság lapon törölje a jelet a **Hitelesítés hez,** és válassza a **Küldés**lehetőséget. A `<secret>` beállított védi ezt az API-t helyette.

    ![A ServiceNow "Hitelesítést igényel" jelölőnégyzet](./media/webhook-alerts/servicenow-resource-settings.png)

1.  A Parancsfájlalapú REST API-k szakaszban meg kell találnia az új REST API **alap API-elérési útját:**

     ![A "Base API Path" a ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  A teljes integrációs URL így néz ki:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Riasztás létrehozása a ServiceNow használatával az Azure Portalon
### <a name="for-a-new-action-group"></a>Új műveletcsoport esetén:
1. A [cikk](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 1–8.

1. Definiálás a **műveletek**listájában:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** A korábban mentett **ServiceNow-integráció URL-címe.**

    c. **Név:** Webhook neve, aliasa vagy azonosítója.

1. A riasztás létrehozásához válassza a **Mentés,** ha kész lehetőséget.

### <a name="for-an-existing-action-group"></a>Meglévő műveletcsoport esetén:
1. Az [Azure Portalon](https://portal.azure.com/)válassza a **Figyelő**lehetőséget.

1. A **Beállítások csoportban** válassza a **Műveletcsoportok lehetőséget.**

1. Keresse meg és jelölje ki a szerkesztni kívánt műveletcsoportot.

1. Hozzáadás a **műveletek listájához:**

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** A korábban mentett **ServiceNow-integráció URL-címe.**

    c. **Név:** Webhook neve, aliasa vagy azonosítója.

1. A műveletcsoport frissítéséhez válassza a **Mentés** gombra, ha elkészült.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>A webhook-integráció tesztelése HTTP POST-kérelemmel
1. Hozza létre az elküldeni kívánt szolgáltatás állapothasznos adatát. Egy példa szolgáltatás állapotát webhook hasznos adat a [Webhooks az Azure-tevékenységnapló-riasztások.](../azure-monitor/platform/activity-log-alerts-webhook.md)

1. Http POST-kérelem létrehozása az alábbiak szerint:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Az "Incidens `200 OK` létrehozva" üzenettel kell érkeznie.

1. Nyissa meg a [ServiceNow](https://www.servicenow.com/) webhelyet, és ellenőrizze, hogy az integráció sikeresen be van-e állítva.

## <a name="next-steps"></a>További lépések
- További információ [arról, hogyan állíthat be webhook-értesítéseket a meglévő problémakezelő rendszerekhez.](service-health-alert-webhook-guide.md)
- Tekintse át a [tevékenységnapló-riasztási webhook-sémáját.](../azure-monitor/platform/activity-log-alerts-webhook.md) 
- További információ a [szolgáltatásállapot-értesítésekről.](../azure-monitor/platform/service-notifications.md)
- További információ a [műveletcsoportokról](../azure-monitor/platform/action-groups.md).