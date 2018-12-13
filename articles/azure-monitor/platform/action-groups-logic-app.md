---
title: Hogyan összetett műveletek indítása az Azure Monitor riasztások
description: Ismerje meg, hogyan hozhat létre egy logikaialkalmazás-művelet feldolgozásához Azure Monitor-riasztásokat.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 4b8547435293f26c7f22ed6d5181c64fbabcd574
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285038"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Hogyan összetett műveletek indítása az Azure Monitor riasztások

Ez a cikk bemutatja, hogyan beállítását, és aktiválja a Logic Apps-alkalmazás, a beszélgetés létrehozása a Microsoft Teams, riasztások aktiválódásakor.

## <a name="overview"></a>Áttekintés
Az Azure Monitor riasztás aktiválásakor meghívja egy [műveletcsoport](../../azure-monitor/platform/action-groups.md). Műveletcsoportok lehetővé teszik egy vagy több műveletet is megoldásáról, és értesíteni másokat a riasztásokkal kapcsolatos indít.

Általános folyamata a következő:

-   Hozzon létre a logikai alkalmazás a megfelelő típusú.

-   A logikai alkalmazás importálja a megfelelő riasztás sémáját.

-   Logikai alkalmazás viselkedésének megadása.

-   Másolja a HTTP-végpontot a logikai alkalmazás egy Azure műveletcsoport.

A folyamat hasonlít, ha azt szeretné, hogy a logikai alkalmazás egy másik művelet végrehajtására.

## <a name="create-an-activity-log-alert-administrative"></a>Tevékenységnapló-riasztás létrehozása: Adminisztratív

1.  Az Azure Portalon válassza ki a **erőforrás létrehozása** a bal felső sarokban.

2.  Keresse meg és válassza **logikai alkalmazás**, majd **létrehozás**.

3.  Adja meg a logikai alkalmazás egy **neve**, válasszon egy **erőforráscsoport**, és így tovább.

    ![Hozzon létre egy logikai alkalmazást](media/action-groups-logic-app/create-logic-app-dialog.png "logikai alkalmazás létrehozása")

4.  Válassza ki **létrehozás** a logikai alkalmazás létrehozásához. Egy előugró üzenet azt jelzi, hogy a logikai alkalmazás létrejött. Válassza ki **indítsa el a erőforrás** megnyitásához a **Logic Apps Designerben**.

5.  Az eseményindító kiválasztása: **HTTP-kérés fogadásakor**.

    ![A logikai alkalmazások eseményindítói](media/action-groups-logic-app/logic-app-triggers.png "Logikaialkalmazás-triggerek")

6.  Válassza ki **szerkesztése** módosíthatja a HTTP-kérés eseményindító.

    ![HTTP-kérelem eseményindítók](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-kérelem eseményindítók")

7.  Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget.

    ![Hasznosadat-minta használata](media/action-groups-logic-app/use-sample-payload-button.png "hasznosadat-minta használata")

8.  Másolja és illessze be az alábbi minta-sémát a párbeszédpanelen:

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

9. A **Logikaialkalmazás-Tervező** egy előugró ablak, amely emlékezteti, hogy be kell-e a logikai alkalmazás küldött kérelem jeleníti meg a **Content-Type** fejlécet **application/json**. Az előugró ablak bezárásához. Az Azure Monitor alert beállítja a fejlécet.

    ![A Content-Type fejléc beállítása](media/action-groups-logic-app/content-type-header.png "a Content-Type fejléc beállítása")

10. Válassza ki **+** **új lépés** majd **művelet hozzáadása**.

    ![Művelet hozzáadása](media/action-groups-logic-app/add-action.png "művelet hozzáadása")

11. Keresse meg és jelölje ki a Microsoft Teams-összekötőt. Válassza ki a **Microsoft Teams-üzenet közzététele** művelet.

    ![Microsoft Teams-műveletek](media/action-groups-logic-app/microsoft-teams-actions.png "Microsoft Teams-műveletek")

12. Konfigurálja a Microsoft Teams-műveletet. A **Logic Apps Designerben** arra kéri, hogy az Office 365-fiókja hitelesítéséhez. Válassza ki a **Csapatazonosító** és **csatorna azonosítója** , az üzenet elküldéséhez.

13. Az üzenet konfigurálása az állandó szövegrészek és mutató hivatkozások együttes használatával a \<mezők\> a dinamikus tartalom. Másolja és illessze be a következő szöveget a **üzenet** mező:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Keressen rá, és cserélje le a \<mezők\> ugyanazzal a névvel, a dinamikus tartalom címkékkel.

    > [!NOTE]
    > Két dinamikus mező nevű **állapot**. Adja hozzá a következő mezők mind az üzenetet. Használja a mezőt, amely szerepel a **activityLog** tulajdonságcsomagot és a többi mező törlése. Vigye a kurzort a **állapot** mezőt, teljesen minősített mezőhivatkozásnak lásd az alábbi képernyőképen látható módon:

    ![Microsoft Teams-művelet: Üzenet küldése](media/action-groups-logic-app/teams-action-post-message.png "Microsoft Teams-művelet: Üzenet küldése")

14. Felső részén a **Logic Apps Designerben**válassza **mentése** a logikai alkalmazás mentéséhez.

15. Nyissa meg a meglévő műveletcsoport, és adja hozzá a logikai alkalmazás hivatkozni egy műveletet. Ha nem rendelkezik meglévő műveletcsoport, [létrehozása és kezelése az Azure Portalon Műveletcsoportok](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) hozhat létre egyet. Ne felejtse el menteni a módosításokat.

    ![A műveletcsoport frissítéséhez](media/action-groups-logic-app/update-action-group.png "a műveletcsoport frissítéséhez")

A következő alkalommal, amikor egy riasztást a műveletcsoport meghívja a logikai alkalmazás neve.

## <a name="create-a-service-health-alert"></a>Hozzon létre egy szolgáltatásállapot-riasztás

Az Azure Service Health-bejegyzések a tevékenységnapló részét képezik. A riasztás létrehozása a folyamat hasonlít a [tevékenységnapló-riasztás létrehozása](#create-an-activity-log-alert-administrative), de néhány módosítással:

- 1 – 7. lépései megegyeznek.
- 8. lépés használja az alábbi minta-sémát a HTTP-kérés eseményindító:

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
-  14 – 11. lépéseket a következő folyamat használja:

   1. Válassza ki **+** **új lépés** majd **feltétel hozzáadása**. Állítsa be a következő feltételek, így a logikai alkalmazás hajt végre, ha a bemeneti adatok megfelel az alábbi értékeket.  Amikor beírja a verzió értéket a szövegmezőbe, helyezze el az idézőjeleket ("0.1.1"), győződjön meg arról, hogy egy karakterláncot és egy numerikus típus nem értékeli, hogy.  A rendszer nem jeleníti meg az idézőjelek között, ha visszatérhet erre az oldalra, de a mögöttes kód továbbra is fenntartja a string típussá.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["A Service Health hasznos feltétel"](media/action-groups-logic-app/service-health-payload-condition.png "Service Health hasznos feltétel")

   1. Az a **ha igaz** feltételt, kövesse a lépéseket 11 – 13 [tevékenységnapló-riasztás létrehozása](#create-an-activity-log-alert-administrative) a Microsoft Teams-művelet hozzáadása.

   1. Adja meg az üzenet HTML és a dinamikus tartalom együttes használatával. Másolja és illessze be a következő tartalmat a **üzenet** mező. Cserélje le a `[incidentType]`, `[trackingID]`, `[title]`, és `[communication]` ugyanazzal a névvel, a dinamikus tartalom címkékkel rendelkező mezők:

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

       !["A Service Health igaz post művelet"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Service Health igaz post művelet")

   1. Az a **ha hamis** feltételt, adja meg a hasznos üzenetet:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["A Service Health hamis feltétel post művelet"](media/action-groups-logic-app/service-health-false-condition-post-action.png "szolgáltatás állapotát a feltétel hamis post művelet")

- 15. lépés megegyezik. Kövesse az utasításokat a logikai alkalmazás mentéséhez és a műveletcsoport frissítéséhez.

## <a name="create-a-metric-alert"></a>Metrikariasztás létrehozása

A metrikariasztás létrehozása a folyamat hasonlít a [tevékenységnapló-riasztás létrehozása](#create-an-activity-log-alert-administrative), de néhány módosítással:

- 1 – 7. lépései megegyeznek.
- 8. lépés használja az alábbi minta-sémát a HTTP-kérés eseményindító:

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
- 14 – 11. lépéseket a következő folyamat használja:

   1. Válassza ki **+** **új lépés** majd **feltétel hozzáadása**. Állítsa be a következő feltételek, így a logikai alkalmazás hajt végre, ha a bemeneti adatok megfelel az alábbi ezeket az értékeket. Írja be a verzió értékét a szövegmezőbe, helyezze idézőjelek ("2.0"), körülötte való gondoskodik róla, hogy ha egy karakterláncot és egy numerikus típus nem értékeli.  A rendszer nem jeleníti meg az idézőjelek között, ha visszatérhet erre az oldalra, de a mögöttes kód továbbra is fenntartja a string típussá. 
       - `schemaId == AzureMonitorMetricAlert`
       - `version == "2.0"`
       
       !["A metrikaalapú riasztási hasznos feltétel"](media/action-groups-logic-app/metric-alert-payload-condition.png "Metrikaalapú riasztási hasznos feltétel")

   1. Az a **ha igaz** feltételt, adjon hozzá egy **minden** hurok és a Microsoft Teams-művelet. Adja meg az üzenet HTML és a dinamikus tartalom együttes használatával.

       !["A metrikaalapú riasztási igaz post művelet"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Metrikaalapú riasztási igaz post művelet")

   1. Az a **ha hamis** feltételt, való kommunikációhoz, hogy a metrikaalapú riasztás nem felel meg az elvárásainak, a logikai alkalmazás egy Microsoft Teams műveletet definiálhat. Például a JSON-adattartalmat. Figyelje meg, hogy miként lehet hivatkozni a `triggerBody` a dinamikus tartalom a `json()` kifejezés.

       !["Riasztás false (hamis) metrikai feltétel post művelet"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Metrikaalapú riasztási hamis feltétel post művelet")

- 15. lépés megegyezik. Kövesse az utasításokat a logikai alkalmazás mentéséhez és a műveletcsoport frissítéséhez.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Más alkalmazások mellett a Microsoft Teams hívása
A Logic Apps rendelkezik, amelyekkel az eseményindító műveletek számos az alkalmazások és adatbázisok különböző összekötőket. A Slack, az SQL Server, Oracle, a Salesforce, néhány példa azt szemlélteti. Összekötők kapcsolatos további információkért lásd: [logikai alkalmazás-összekötők](../../connectors/apis-list.md).  

## <a name="next-steps"></a>További lépések
* Get- [Azure tevékenységnapló-riasztások áttekintése](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) és riasztások fogadása.  
* Ismerje meg, hogyan [riasztások konfigurálása az Azure Service Health értesítési közzétételekor](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
* Tudjon meg többet [Műveletcsoportok](../../azure-monitor/platform/action-groups.md).
