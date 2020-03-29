---
title: Összetett műveletek aktiválása az Azure Monitor-riasztások segítségével
description: Ismerje meg, hogyan hozhat létre egy logikai alkalmazás művelet et az Azure Monitor-riasztások feldolgozásához.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 07/18/2018
ms.subservice: alerts
ms.openlocfilehash: 655a3acc44a1418778b37fbef85e5df75d042317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206236"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Összetett műveletek aktiválása az Azure Monitor-riasztások segítségével

Ez a cikk bemutatja, hogyan állíthat be és indíthat el egy logikai alkalmazást, amely beszélgetést hoz létre a Microsoft Teamsben, amikor riasztás aktiválódik.

## <a name="overview"></a>Áttekintés

Amikor egy Azure Monitor riasztási eseményindítók, meghívja a [műveletcsoport.](../../azure-monitor/platform/action-groups.md) A műveletcsoportok lehetővé teszik egy vagy több művelet indítását, hogy értesítsen másokat egy riasztásról, és kiis orvosolja azt.

Az általános folyamat a következő:

-   Hozza létre a logikai alkalmazást a megfelelő riasztástípushoz.

-   A megfelelő riasztástípus mintahasznos adatimportálása a logikai alkalmazásba.

-   Adja meg a logikai alkalmazás viselkedését.

-   Másolja a logikai alkalmazás HTTP-végpontját egy Azure-műveletcsoportba.

A folyamat hasonló, ha azt szeretné, hogy a logikai alkalmazás egy másik műveletet hajtson végre.

## <a name="create-an-activity-log-alert-administrative"></a>Tevékenységnapló-riasztás létrehozása: Felügyeleti

1.  Az Azure Portalon válassza **az erőforrás létrehozása** a bal felső sarokban.

2.  Keresse meg a **Logikai alkalmazás**kifejezést, és válassza a **Létrehozás lehetőséget.**

3.  Adjon a logikai **alkalmazásnak nevet,** válasszon **erőforráscsoportot**és így tovább.

    ![Logikai alkalmazás létrehozása](media/action-groups-logic-app/create-logic-app-dialog.png "Logikai alkalmazás létrehozása")

4.  A logikai alkalmazás létrehozásához válassza a **Létrehozás** gombot. Az előugró üzenet azt jelzi, hogy a logikai alkalmazás létrejött. Válassza **az Erőforrás indítása** lehetőséget a **Logic Apps Designer**megnyitásához.

5.  Válassza ki az eseményindítót: **HTTP-kérelem érkezésekor**.

    ![A logikai alkalmazások eseményindítói](media/action-groups-logic-app/logic-app-triggers.png "A logikai alkalmazások eseményindítói")

6.  A **HTTP-kérelem** eseményindítójának módosításához válassza a Szerkesztés lehetőséget.

    ![HTTP-kérelem eseményindítói](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-kérelem eseményindítói")

7.  Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget.

    ![Mintahasznos adat használata](media/action-groups-logic-app/use-sample-payload-button.png "Mintahasznos adat használata")

8.  Másolja és illessze be a következő mintahasznos tavást a párbeszédpanelre:

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

9. A **Logic App Designer** egy előugró ablakot jelenít meg, amely emlékezteti, hogy a logikai alkalmazásnak küldött kérelemnek a **Content-Type** fejlécet **alkalmazásra/jsonra kell állítania.** Zárja be az előugró ablakot. Az Azure Monitor riasztási beállítja a fejlécet.

    ![A Tartalomtípus fejlécének beállítása](media/action-groups-logic-app/content-type-header.png "A Tartalomtípus fejlécének beállítása")

10. Válassza **+** **az Új lépés,** majd a Művelet **hozzáadása**lehetőséget.

    ![Művelet hozzáadása](media/action-groups-logic-app/add-action.png "Művelet hozzáadása")

11. Keresse meg és jelölje ki a Microsoft Teams összekötőt. Válassza a **Microsoft Teams – Üzenet közzélépése** műveletet.

    ![Microsoft Teams-műveletek](media/action-groups-logic-app/microsoft-teams-actions.png "Microsoft Teams-műveletek")

12. Konfigurálja a Microsoft Teams műveletet. A **Logic Apps Designer** arra kéri, hogy hitelesítse magát az Office 365-fiókjában. Válassza ki azt a **csapatazonosítót** és **csatornaazonosítót,** amelynek el szeretné küldeni az üzenetet.

13. Konfigurálja az üzenetet statikus szöveg és a \<dinamikus\> tartalom mezőire mutató hivatkozások kombinációjával. Másolja és illessze be a következő szöveget az **Üzenet** mezőbe:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Ezután keresse meg \<\> és cserélje le a mezőket az azonos nevű dinamikus tartalomcímkékre.

    > [!NOTE]
    > Két dinamikus mező van, amelynek **neve állapot.** Adja hozzá mindkét mezőt az üzenethez. Használja az **activityLog** tulajdonságtáskában lévő mezőt, és törölje a másik mezőt. Vigye a kurzort az **állapotmező** fölé a teljesen minősített mezőhivatkozás megtekintéséhez, ahogy az a következő képernyőképen látható:

    ![Microsoft Teams-művelet: Üzenet feladása](media/action-groups-logic-app/teams-action-post-message.png "Microsoft Teams-művelet: Üzenet feladása")

14. A **Logic Apps Designer**tetején válassza a **Mentés** lehetőséget a logikai alkalmazás mentéséhez.

15. Nyissa meg a meglévő műveletcsoportot, és adjon hozzá egy műveletet a logikai alkalmazásra való hivatkozáshoz. Ha nem rendelkezik meglévő műveletcsoporttal, olvassa [el a Műveletcsoportok létrehozása és kezelése az Azure Portalon](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) hozzon létre egyet. Ne felejtsd el menteni a módosításokat.

    ![A műveletcsoport frissítése](media/action-groups-logic-app/update-action-group.png "A műveletcsoport frissítése")

A következő alkalommal, amikor egy riasztás hívja meg a műveletcsoportot, a logikai alkalmazás neve.

## <a name="create-a-service-health-alert"></a>Szolgáltatásállapot-riasztás létrehozása

Az Azure Service Health-bejegyzések a tevékenységnapló részét képezik. A riasztás létrehozásának folyamata hasonló a [tevékenységnapló-riasztás létrehozásához,](#create-an-activity-log-alert-administrative)de néhány módosítással:

- Az 1-7.
- A 8.

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
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  A 9.
-  A 11–14.

   1. Válassza **+** **az Új lépés,** majd a Feltétel **hozzáadása**lehetőséget. Állítsa be a következő feltételeket, hogy a logikai alkalmazás csak akkor hajtson végre, ha a bemeneti adatok megegyeznek az alábbi értékekkel.  Amikor beírja a verzióértéket a szövegmezőbe, tegyen idézőjeleket köré ("0.1.1"),, hogy a program karakterláncként értékelje, ne pedig numerikus típusként.  A rendszer nem jeleníti meg az ajánlatokat, ha visszatér az oldalra, de az alapul szolgáló kód megtartja a karakterlánc típusát.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Szolgáltatásállapot hasznos adatának feltétele"](media/action-groups-logic-app/service-health-payload-condition.png "Szolgáltatás állapotának hasznos adata")

   1. Ha **igaz** állapotban kövesse a 11–13. [Create an activity log alert](#create-an-activity-log-alert-administrative)

   1. Az üzenetet HTML és dinamikus tartalom kombinációjával határozhatja meg. Másolja és illessze be a következő tartalmat az **Üzenet** mezőbe. Cserélje `[incidentType]`le `[trackingID]` `[title]`a `[communication]` , , , és mezőket azonos nevű dinamikus tartalomcímkékre:

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

       !["A szolgáltatás állapota valódi állapot a beavatkozás után"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Szolgáltatás állapota valódi állapot utáni művelet")

   1. Az **If false** feltételhez adjon meg egy hasznos üzenetet:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["A szolgáltatás állapota hamis állapot a beavatkozás után"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Szolgáltatás állapota hamis állapot utáni művelet")

- A 15. Kövesse az utasításokat a logikai alkalmazás mentéséhez és a műveletcsoport frissítéséhez.

## <a name="create-a-metric-alert"></a>Metrikariasztás létrehozása

A metrikariasztás létrehozásának folyamata hasonló a [tevékenységnapló-riasztás létrehozásához,](#create-an-activity-log-alert-administrative)de néhány módosítással:

- Az 1-7.
- A 8.

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
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
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- A 9.
- A 11–14.

  1. Válassza **+** **az Új lépés,** majd a Feltétel **hozzáadása**lehetőséget. Állítsa be a következő feltételeket, hogy a logikai alkalmazás csak akkor hajtson végre, ha a bemeneti adatok megfelelnek az alábbi értékeknek. Amikor beírja a verzióértéket a szövegmezőbe, helyezzen idézőjeleket köré ("2.0"), hogy megbizonyosodjon arról, hogy a program karakterláncként, és nem numerikus típusként értékeli ki.  A rendszer nem jeleníti meg az ajánlatokat, ha visszatér az oldalra, de az alapul szolgáló kód megtartja a karakterlánc típusát. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Metrikariasztás hasznos adatfeltétele"](media/action-groups-logic-app/metric-alert-payload-condition.png "Metrikariasztás hasznos adatfeltétele")

  1. A **ha igaz** feltétel, adjunk hozzá egy **minden** ciklus és a Microsoft Teams művelet. Az üzenetet HTML és dinamikus tartalom kombinációjával határozhatja meg.

      !["Metrika riasztás valódi feltétel utáni művelet"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Metrikariasztás valódi feltétel utáni művelet")

  1. Az **Ha hamis** feltétel, adjon meg egy Microsoft Teams-műveletet annak közléséhez, hogy a metrikariasztás nem felel meg a logikai alkalmazás elvárásainak. A JSON-tartalom belefoglalása. Figyelje meg, `triggerBody` hogyan hivatkozik a `json()` kifejezés dinamikus tartalmára.

      !["Mérőszám riasztás a művelet után hamis állapot"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Metrikariasztás hamis feltétel utáni művelet")

- A 15. Kövesse az utasításokat a logikai alkalmazás mentéséhez és a műveletcsoport frissítéséhez.

## <a name="calling-other-applications-besides-microsoft-teams"></a>A Microsoft Teamsen kívüli egyéb alkalmazások hívása
A Logic Apps számos különböző összekötővel rendelkezik, amelyek lehetővé teszik, hogy az alkalmazások és adatbázisok széles körében műveleteket indítson el. A Slack, az SQL Server, az Oracle, a Salesforce csak néhány példa. Az összekötőkről további információt a [Logic App-összekötők](../../connectors/apis-list.md)című témakörben talál.  

## <a name="next-steps"></a>További lépések
* Áttekintést [kaphat az Azure-tevékenységnapló-riasztásokról,](../../azure-monitor/platform/alerts-overview.md) és megtudhatja, hogyan kaphat riasztásokat.  
* Ismerje meg, hogyan konfigurálhatja a [riasztásokat az Azure Service Health-értesítés közzétételéről.](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)
* További információ a [műveletcsoportokról](../../azure-monitor/platform/action-groups.md).

