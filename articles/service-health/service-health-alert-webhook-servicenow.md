---
title: Azure-szolgáltatások állapotának riasztások konfigurálása a ServiceNow |} Microsoft Docs
description: A ServiceNow példányra szolgáltatás állapotának eseményekről személyre szabott értesítések lekérése.
author: shawntabrizi
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
ms.openlocfilehash: 867a8c0b478df9d2b7690b8b914ded7c42558583
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="configure-service-health-alerts-with-servicenow"></a>A ServiceNow health-riasztások konfigurálása

Ez a cikk bemutatja, hogyan Azure-szolgáltatások állapotának riasztások integrálása a ServiceNow egy webhook használatával. Miután beállította a ServiceNow példányát webhook integrációja, kapott riasztások a meglévő értesítési infrastruktúrán keresztül Azure szolgáltatásokkal kapcsolatos problémákról hatása a felhasználókra. Minden alkalommal, amikor egy Azure szolgáltatás állapota riasztás akkor következik be, meghívja a webhook ServiceNow tartozó parancsprogrammal létrehozva REST API-n keresztül.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>A ServiceNow egy parancsprogram-alapú REST API létrehozása
1.  Ellenőrizze, hogy rendelkezik regisztrált a bejelentkezett a [ServiceNow](https://www.servicenow.com/) fiók.

2.  Keresse meg a **System webszolgáltatások** ServiceNow, és válassza ki a szakasz **parancsprogrammal létrehozva REST API-k**.

    ![A ServiceNow "Parancsprogrammal létrehozva webszolgáltatás" című](./media/webhook-alerts/servicenow-sws-section.png)

3.  Válassza ki **új** parancsprogrammal létrehozva REST új szolgáltatás létrehozása.
 
    ![Az "Új parancsprogrammal létrehozva REST API" gombra, a ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

4.  Adja hozzá a **neve** a REST API-t és a készlet a **API azonosítója** a `azureservicehealth`.

5.  Válassza ki **nyújt**.

    ![A "REST API-beállítások" a ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

6.  Válassza ki a REST API hozott létre, és a **erőforrások** lapon válassza **új**.

    ![A ServiceNow "Erőforrás lap"](./media/webhook-alerts/servicenow-resources-tab.png)

7.  **Név** az új erőforrás `event` , és módosítsa a **HTTP-metódus** való `POST`.

8.  Az a **parancsfájl** területen írja be a következő JavaScript-kódot:

    >[!NOTE]
    >Frissíteni kell a `<secret>`,`<group>`, és `<email>` értékét az alábbi parancsfájlt.
    >* `<secret>` véletlenszerű karakterlánc, például a GUID-nak kell lennie.
    >* `<group>` a ServiceNow csoport ki szeretné osztani az incidenst a következőhöz
    >* `<email>` az adott személy ki szeretné osztani az incidenst a következőhöz (nem kötelező) kell lennie.
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
                } else if (event.data.context.activityLog.properties.incidentType == "Information" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
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

9.  A biztonság lapon törölje a jelet **hitelesítést igényel** válassza **Submit**. A `<secret>` meg set védelmet nyújt az API helyette.

    ![A ServiceNow "Szükséges hitelesítés" jelölőnégyzet](./media/webhook-alerts/servicenow-resource-settings.png)

10.  Eközben a REST API-k parancsprogrammal létrehozva szakaszban keresse meg a **alap API elérési** az új REST API:

     ![A "kiinduló API elérési út" a ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

11.  A teljes integrációs URL-címet a következőhöz hasonló:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>A ServiceNow használata az Azure portálon riasztás létrehozása
### <a name="for-a-new-action-group"></a>Egy új művelet csoporthoz:
1. Végezze el az 1 – 8 [Ez a cikk](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) riasztás létrehozása egy új művelet csoporttal.

2. Adja meg a közül **műveletek**:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** a ServiceNow **integrációs URL-címet** meg a korábban mentett.

    c. **Name:** Webhook tartozó név, az alias vagy a azonosítója.

3. Válassza ki **mentése** végzett a riasztás létrehozása.

### <a name="for-an-existing-action-group"></a>Meglévő művelet csoportok:
1. Az a [Azure-portálon](https://portal.azure.com/), jelölje be **figyelő**.

2. Az a **beállítások** szakaszban jelölje be **művelet csoportok**.

3. Keresse meg és válassza ki a szerkeszteni kívánt művelet csoportot.

4. Vegye fel a **műveletek**:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** a ServiceNow **integrációs URL-címet** meg a korábban mentett.

    c. **Name:** Webhook tartozó név, az alias vagy a azonosítója.

5. Válassza ki **mentése** végzett frissíteni a művelet csoportot.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>A webhook integrációs via HTTP POST-kérelmet tesztelése
1. Hozzon létre a szolgáltatás állapotának forgalma szeretne küldeni. Példa service állapotfigyelő webhook payloadot címen található [Webhookok Azure tevékenység naplózása riasztások](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Hozzon létre egy HTTP POST-kérelmet az alábbiak szerint:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Ekkor egy `200 OK` válasz a következő üzenettel: "Incidenst hozott létre."

4. Ugrás a [ServiceNow](https://www.servicenow.com/) annak ellenőrzéséhez, hogy integráció sikeres volt-e beállítva.

## <a name="next-steps"></a>További lépések
- Megtudhatja, hogyan [meglévő probléma felügyeleti rendszerekhez webhook értesítések konfigurálása](service-health-alert-webhook-guide.md).
- Tekintse át a [műveletnapló riasztási webhook séma](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- További tudnivalók [szolgáltatás állapotával kapcsolatos értesítésekre](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- További információ [művelet csoportok](../monitoring-and-diagnostics/monitoring-action-groups.md).