---
title: Az Azure riasztások összetett műveleteket és a műveletek csoportok
description: Útmutató a Logic App művelet folyamat Azure riasztások létrehozásához.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: eafb2bcf0175190748c9dd020051cbebfcaee1fd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263884"
---
# <a name="create-a-logic-app-action"></a>Hozzon létre egy Logic App művelet
## <a name="overview"></a>Áttekintés ##
Egy Azure Monitor-riasztás váltja ki, ha meghívja az [művelet csoport](monitoring-action-groups.md). Művelet csoportok lehetővé teszik egy vagy több műveletek értesítheti a riasztás és megoldásáról még akkor is elindítható.

Ez a cikk bemutatja, hogyan állíthat be, és a beszélgetés létrehozása a Microsoft Teams, egy riasztás aktiválódásakor logikai alkalmazás indítás.

Az általános folyamat a következő:

-   A megfelelő riasztási típus a logikai alkalmazás létrehozása

-   A megfelelő riasztási sémáját importálnia kell a logikai alkalmazás

-   Logikai alkalmazás viselkedésének meghatározása

-   A logikai alkalmazás HTTP-végpont átmásolja egy Azure-művelet csoport

A folyamat hasonlít, ha azt szeretné, hogy a logikai alkalmazás egy másik művelet végrehajtásához.

## <a name="create-an-activity-log-alert--administrative"></a>Felügyeleti tevékenység napló értesítés – létrehozása

1.  Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

2.  Keressen a „logikai alkalmazás” kifejezésre, majd válassza a **Logikai alkalmazás** lehetőséget. Kattintson a **Létrehozás** gombra.

3.  Nevezze el a Logic Apps alkalmazást, válasszon egy erőforráscsoportot, stb.

    ![Hozzon létre logic app párbeszédpanel](media/monitoring-action-groups/create-logic-app-dialog.png "létrehozása logic app párbeszédpanel")

4.  A Létrehozás gombra kattintva a logikai alkalmazás létrehozása. Egy előugró ablak jelenik meg a logikai alkalmazás létrehozása. Kattintson a nyissa meg a Logic Apps designer erőforrás indítása gombra.

5.  Válassza ki az eseményindító **érkezik, amikor egy HTTP-kérelem**.

    ![Logic App eseményindítók](media/monitoring-action-groups/logic-app-triggers.png "Logic App eseményindítók")

6.  Válassza ki **szerkesztése** a HTTP-eseményindítóval módosítása

    ![HTTP-kérelem eseményindító alakzat](media/monitoring-action-groups/http-request-trigger-shape.png "HTTP-kérelem eseményindító alakzat")

7.  Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget.

    ![Használja a minta hasznos gomb](media/monitoring-action-groups/use-sample-payload-button.png "használata minta hasznos gomb")

8.  Másolja és illessze be a következő minta séma a párbeszédpanel bezárásához.

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. A Logic App Designer emlékeztető megjelenítése, hogy a logikai alkalmazás küldött kérelem az application/json a Content-Type fejléc értéke csak megjegyzés jeleníti meg. Lépjen tovább, és a párbeszédpanel bezárásához. Az Azure Monitor-riasztás fog ehhez megfelelően.

    ![Content-Type fejléc](media/monitoring-action-groups/content-type-header.png "Content-Type fejléc")

10. Válassza ki **+ új lépés** majd **művelet hozzáadása**.

    ![Adja hozzá a műveleti](media/monitoring-action-groups/add-action.png "művelet hozzáadása")

11. Keresse meg és jelölje ki a Microsoft Teams összekötőt. Válassza ki a **Microsoft Teams – feladás egy vagy több üzenetet** művelet.

    ![Microsoft Teams műveletek](media/monitoring-action-groups/microsoft-teams-actions.png "Microsoft Teams műveletek")

12. A Microsoft Teams művelet konfigurálásához. A Logic Apps Designer kérni fogja az Office365 fiók felé történő hitelesítésre. Válassza ki a **csoportazonosító** és **csatorna azonosítója** elküldeni az üzenetet.

13. Konfigurálja a **üzenet** statikus szöveget és mutató hivatkozások kombinációját használva a \<mezők\> a dinamikus tartalom. Kivágás, és illessze be a következő szöveget az üzenet mezőbe.

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Majd keresse meg, és cserélje le a \<mezők\> azonos nevű dinamikus tartalom címkékkel.

    **[MEGJEGYZÉS!]**  Nevű két dinamikus mező **állapot**. Mindkét állapota mezőket ad hozzá az üzenetet. Az egyetlen módszer alkalmazása activityLog tulajdonságcsomag, és törölje a másikra. Ha az egérrel megáll fölött a **állapot** látni fogja a teljesen minősített hivatkozást a képernyőfelvételen látható módon mező

    ![Művelet – feladás egy vagy több üzenetet csapatának](media/monitoring-action-groups/teams-action-post-message.png "csapatok művelet - üzenet közzététele")

14. A Tervező tetején a Mentés gombra kattintva a logikai alkalmazás mentése

15. Kattintson a HTTP-kérelem alakzat bontsa ki azt. Másolja a HTTP POST URL-címet.

    ![HTTP POST URL-CÍM](media/monitoring-action-groups/http-post-url.png "HTTP POST URL-CÍME")

16. Nyissa meg a meglévő művelet csoportot, és adja hozzá a logikai alkalmazás hivatkozni művelet. Ha egy meglévő művelet csoport nem rendelkezik, tekintse meg a <https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups> kattintva létrehozhat egyet. Ne feledje, a módosítások mentéséhez.

    ![Frissítési művelet csoport](media/monitoring-action-groups/update-action-group.png "Csoportfrissítési műveletet")

A következő alkalommal riasztást hív meg, a művelet csoport a Logic Apps alkalmazást nevezik.

## <a name="create-a-service-health-alert"></a>Riasztás létrehozása, a szolgáltatás állapota

Szolgáltatás állapotának bejegyzések a műveletnapló részét képezik, így a folyamat hasonlít a következő változások

1.  1 és 7 közötti lépései megegyeznek.
2.  Az alábbi minta-séma használata a HTTP-eseményindítóval, a 8. lépés.

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

3.  9-10 lépései megegyeznek.
4.  11. lépés használja az alábbi folyamatot.
5.  Kattintson a **+ új lépés** gombra, majd válassza a **feltétel hozzáadása**. Annak érdekében, hogy a logikai alkalmazás csak akkor megy végbe a bemeneti adatok megegyezik ezeket az értékeket a következő feltételek beállítása
    - schemaId == Microsoft.Insights/activityLogs
    - eventSource == ServiceHealth
    - verzió == 0.1.1

    !["Szolgáltatás állapotának hasznos feltétel"](media/monitoring-action-groups/service-health-payload-condition.png "szolgáltatás állapotának hasznos feltétel")

6. Az a **igaz értéke esetén** feltétel, a Microsoft Teams művelet 11 – 13. lépést az előző példából adja hozzá.

7. Adja meg a HTML- és [dinamikus tartalom] kombinációjával üzenet. Másolással illessze be a tartalom alatt az üzenet mezőbe. Cserélje le a [incidentType], [trackingID], [cím] és [kommunikáció] mező azonos nevű dinamikus tartalom címkékkel

    ```html
    <p>
    <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
    <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
    <strong>Title:</strong>&nbsp;[title]</p>
    <p>
    <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard</a>
    </p>
    <p>[communication]</p>
    ```

    !["Szolgáltatás állapotának true feltétel post művelet"](media/monitoring-action-groups/service-health-true-condition-post-action.png "szolgáltatás állapotának true feltétel post művelet")

8. Az a **hamis** feltételt, adja meg a hasznos üzenetet

    ```html
    <p><strong>Service Health Alert</strong></p>
    <p><b>Unrecognized alert schema</b></p>
    <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard\</a></p>
    ```

    !["Szolgáltatás állapotát a feltétel hamis post action"](media/monitoring-action-groups/service-health-false-condition-post-action.png "szolgáltatásának állapota hamis feltétel post művelet")

9.  Kövesse az előző példában a logikai alkalmazás mentéséhez, és a művelet csoport 15 – 16 lépéseket

## <a name="metric-alert"></a>Metrikariasztás

1.  1 – 7. lépést ugyanazok, mint az első példában
2.  Az alábbi minta-séma használata a HTTP-eseményindítóval, a 8. lépés.

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

3.  9-10 lépései megegyeznek.
4.  11. lépés használja az alábbi folyamatot.
5.  Kattintson a **+ új lépés** gombra, majd válassza a **feltétel hozzáadása**. Annak érdekében, hogy a logikai alkalmazás csak akkor megy végbe a bemeneti adatok megegyezik ezeket az értékeket a következő feltételek beállítása

    - schemaId == AzureMonitorMetricAlert
    - verzió: 2.0

    !["Metrika riasztási hasznos feltétel"](media/monitoring-action-groups/metric-alert-payload-condition.png "metrika riasztási hasznos feltétel")

6.  Az a **igaz értéke esetén** feltétel adunk hozzá egy **minden** alakzat és a Microsoft Teams művelet, és adja meg az üzenet HTML és a [dinamikus tartalom]

    !["Metrika riasztási igaz post action"](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "metrika riasztási true feltétel post művelet")

7.  Az a **hamis** alakul, amely közli, hogy a metrika riasztás nem felel meg a Logic App elvárásainak és közé tartozik a JSON-adattartalmat Microsoft Teams műveletet. Vegye figyelembe, hogyan jelenleg hivatkozik a triggerBody dinamikus tartalmat a json() kifejezésben.

    !["Metrika riasztási hamis feltétel post művelet"](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "metrika riasztási hamis feltétel post művelet")

8.  Lépések 15 – 16 az első példában mentése a Logic Apps alkalmazást, és a művelet csoport frissítése

## <a name="next-steps"></a>További lépések ##
* Első egy [tevékenység napló riasztások áttekintése](monitoring-overview-alerts.md), és megtudhatja, hogyan szeretné megkapni a riasztásokat.  
* Megtudhatja, hogyan [riasztások konfigurálása, ha az állapotfigyelő szolgáltatáshoz értesítést visszaküldi](monitoring-activity-log-alerts-on-service-notifications.md).
* További információ [művelet csoportok](monitoring-action-groups.md)