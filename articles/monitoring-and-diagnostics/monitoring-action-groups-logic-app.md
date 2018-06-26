---
title: Riasztások figyelése Azure és a művelet csoportok összetett műveletek események
description: Megtudhatja, hogyan hozzon létre egy logic app művelet Azure riasztások feldolgozni.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 14e562234152d2f1f2f2d2b57b34cd5724d3dd14
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753093"
---
# <a name="create-a-logic-app-action"></a>Hozzon létre egy logic app művelet

Ez a cikk bemutatja, hogyan állíthat be, és a beszélgetés létrehozása a Microsoft Teams, egy riasztás aktiválódásakor logikai alkalmazás indítás.

## <a name="overview"></a>Áttekintés
Egy Azure Monitor-riasztás váltja ki, ha meghívja az [művelet csoport](monitoring-action-groups.md). Művelet csoportok lehetővé teszik egy vagy több műveletek értesíteni másokat a riasztást, és megoldásáról is elindítható.

Az általános folyamat a következő:

-   A megfelelő riasztási típus a logikai alkalmazás létrehozása.

-   A megfelelő riasztási sémáját importálnia kell a logikai alkalmazást.

-   Adja meg a logic app viselkedését.

-   Másolja a HTTP-végpont a logikai alkalmazás egy Azure művelet csoportjához.

A folyamat hasonlít, ha azt szeretné, hogy a logikai alkalmazás egy másik művelet végrehajtásához.

## <a name="create-an-activity-log-alert-administrative"></a>Hozzon létre egy tevékenység napló riasztást: rendszergazda

1.  Válassza ki az Azure-portálon **hozzon létre egy erőforrást** bal felső sarokban.

2.  Keresse meg és jelölje ki **logikai alkalmazás**, majd jelölje be **létrehozása**.

3.  A Logic Apps alkalmazást ad egy **neve**, válassza ki a **erőforráscsoport**, és így tovább.

    ![Logikai alkalmazás létrehozása](media/monitoring-action-groups/create-logic-app-dialog.png "logikai alkalmazás létrehozása")

4.  Válassza ki **létrehozása** a logikai alkalmazás létrehozása. Felbukkanó üzenet azt jelzi, hogy létrejött-e a logikai alkalmazást. Válassza ki **indítása erőforrás** megnyitásához a **Logic Apps Designer**.

5.  Válassza ki az eseményindító: **érkezik, amikor egy HTTP-kérelem**.

    ![Logic app eseményindítók](media/monitoring-action-groups/logic-app-triggers.png "Logic app eseményindítók")

6.  Válassza ki **szerkesztése** módosítása a HTTP-eseményindítóval.

    ![HTTP-kérelem eseményindítók](media/monitoring-action-groups/http-request-trigger-shape.png "HTTP-kérelem eseményindítók")

7.  Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget.

    ![Használjon egy minta hasznos](media/monitoring-action-groups/use-sample-payload-button.png "használja a minta hasznos adatok között")

8.  Másolja és illessze be a következő minta séma párbeszédpanel:

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

9. A **Logic App Designer** megjelenik egy előugró ablak, jelezve, hogy be kell-e a logikai alkalmazás küldött kérelem az **Content-Type** fejlécének **az application/json**. Az előugró ablak bezárásához. Az Azure Monitor-riasztás beállítja a fejléc.

    ![Állítsa be a Content-Type fejléc](media/monitoring-action-groups/content-type-header.png "a Content-Type fejléc beállítása")

10. Válassza ki **+** **új lépés** majd **művelet hozzáadása**.

    ![Új művelet](media/monitoring-action-groups/add-action.png "művelet hozzáadása")

11. Keresse meg és jelölje ki a Microsoft Teams összekötőt. Válassza ki a **Microsoft Teams – feladás egy vagy több üzenetet** művelet.

    ![Microsoft Teams műveletek](media/monitoring-action-groups/microsoft-teams-actions.png "Microsoft Teams műveletek")

12. A Microsoft Teams művelet konfigurálásához. A **Logic Apps Designer** kéri, hogy az Office 365-fiókkal hitelesítést. Válassza ki a **csoportazonosító** és **csatorna azonosítója** elküldeni az üzenetet.

13. Az üzenet konfigurálása statikus szöveget és a hivatkozások használatával a \<mezők\> a dinamikus tartalom. Másolja és illessze be a következő szöveget a **üzenet** mező:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Majd keresse meg, és cserélje le a \<mezők\> azonos nevű dinamikus tartalom címkékkel.

    > [!NOTE]
    > Két dinamikus mező nevű **állapot**. Adja hozzá a következő mezők az üzenethez. A mezőben, amely a **activityLog** tulajdonságcsomag, törölje a másik mező. A kurzorral rámutat a **állapot** mezőben a teljesen minősített hivatkozást, az alábbi képernyőfelvételen látható módon:

    ![Microsoft Teams művelet: üzenetet](media/monitoring-action-groups/teams-action-post-message.png "Microsoft Teams művelet: üzenet")

14. Felső részén a **Logic Apps Designer**, jelölje be **mentése** menteni a Logic Apps alkalmazást.

15. Nyissa meg a meglévő művelet csoportot, és adja hozzá a logikai alkalmazás hivatkozni művelet. Ha egy meglévő művelet csoport nem rendelkezik, tekintse meg [létrehozása és kezelése az Azure portálon művelet csoportok](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups) kattintva létrehozhat egyet. Ne feledje, a módosítások mentéséhez.

    ![A művelet csoport](media/monitoring-action-groups/update-action-group.png "a művelet csoport frissítése")

A következő alkalommal riasztást hív meg, a művelet csoport a Logic Apps alkalmazást nevezik.

## <a name="create-a-service-health-alert"></a>Riasztás létrehozása, a szolgáltatás állapota

Az Azure szolgáltatás állapotát a rendszer a műveletnapló részét. A riasztás létrehozása a folyamat hasonlít [létre tevékenység napló riasztást](#create-an-activity-log-alert-administrative), azonban néhány változás:

- 1 és 7 közötti lépései megegyeznek.
- 8. lépést használja az alábbi minta-séma a HTTP-eseményindítóval:

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

-  9 és 10 lépései megegyeznek.
-  A 11-14. lépéseket tegye a következőket:

   1. Válassza ki **+** **új lépés** majd **feltétel hozzáadása**. Állítsa be a következő feltételek annak érdekében, hogy a logikai alkalmazás végrehajtja, csak ha a bemeneti adatok megegyezik ezeket az értékeket:
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == 0.1.1`

      !["A szolgáltatás állapotát hasznos feltétel"](media/monitoring-action-groups/service-health-payload-condition.png "hasznos feltétel szolgáltatásának állapota")

   1. Az a **igaz értéke esetén** feltétel, kövesse az utasításokat a lépéseket 11 – 13 [tevékenység napló riasztás létrehozása](#create-an-activity-log-alert-administrative) hozzáadása a Microsoft Teams művelet.

   1. HTML és a dinamikus tartalom együttes használatával határozza meg az üzenetet. Másolja és illessze be a következő tartalmat a **üzenet** mező. Cserélje le a `[incidentType]`, `[trackingID]`, `[title]`, és `[communication]` azonos nevű dinamikus tartalom címkékkel mezők:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Szolgáltatás állapota true feltétel post művelet"](media/monitoring-action-groups/service-health-true-condition-post-action.png "szolgáltatásának állapota true feltétel post művelet")

   1. Az a **hamis** feltétel, adja meg a hasznos üzenetet:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Szolgáltatás állapotát a feltétel hamis post action"](media/monitoring-action-groups/service-health-false-condition-post-action.png "szolgáltatásának állapota hamis feltétel post művelet")

- 15. lépés megegyezik. Kövesse az utasításokat, mentse a Logic Apps alkalmazást, és a művelet csoport frissítése.

## <a name="create-a-metric-alert"></a>Riasztás létrehozása, a metrika

A metrika-riasztások létrehozásának folyamata hasonlít [egy figyelmeztetés a napló létrehozása](#create-an-activity-log-alert-administrative), azonban néhány változás:

- 1 és 7 közötti lépései megegyeznek.
- 8. lépést használja az alábbi minta-séma a HTTP-eseményindítóval:

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

- 9 és 10 lépései megegyeznek.
- A 11-14. lépéseket tegye a következőket:

   1. Válassza ki **+** **új lépés** majd **feltétel hozzáadása**. Állítsa be a következő feltételek annak érdekében, hogy a logikai alkalmazás végrehajtja, csak ha a bemeneti adatok megegyezik ezeket az értékeket:
       - `schemaId == AzureMonitorMetricAlert`
       - `version == 2.0`

       !["Metrika riasztási hasznos feltétel"](media/monitoring-action-groups/metric-alert-payload-condition.png "metrika riasztási hasznos feltétel")

   1. Az a **igaz értéke esetén** állapotról, vegye fel a **minden** hurok és a Microsoft Teams művelet. HTML és a dinamikus tartalom együttes használatával határozza meg az üzenetet.

       !["Metrika riasztási igaz post action"](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "metrika riasztási true feltétel post művelet")

   1. Az a **hamis** feltétel, a Microsoft Teams műveletet való kommunikációhoz, hogy a mérték riasztás nem felel meg a logikai alkalmazást az elvárásainak. A JSON-adattartalmat tartalmazza. Figyelje meg, hogyan hivatkozható a `triggerBody` a dinamikus tartalmat a `json()` kifejezés.

       !["Metrika riasztási hamis feltétel post művelet"](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "metrika riasztási hamis feltétel post művelet")

- 15. lépés megegyezik. Kövesse az utasításokat, mentse a Logic Apps alkalmazást, és a művelet csoport frissítése.

## <a name="next-steps"></a>További lépések
* Első egy [Azure tevékenység napló riasztások áttekintése](monitoring-overview-alerts.md) és megtudhatja, hogyan szeretné megkapni a riasztásokat.  
* Megtudhatja, hogyan [riasztások konfigurálása, amikor a rendszer visszaküldi az Azure szolgáltatás állapota értesítések](monitoring-activity-log-alerts-on-service-notifications.md).
* További információ [művelet csoportok](monitoring-action-groups.md).