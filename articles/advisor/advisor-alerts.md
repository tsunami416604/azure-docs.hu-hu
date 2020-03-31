---
title: Azure Advisor-riasztások létrehozása új javaslatokhoz
description: Azure Advisor-riasztások létrehozása új javaslathoz
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 07cbc57ef718b6cac104d2b5238ff4e3196f197a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443156"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Azure Advisor-riasztások létrehozása új javaslatokhoz 

Ez a cikk bemutatja, hogyan állíthat be egy riasztást az Azure Advisor új javaslataihoz az Azure Portal és az Azure Resource Manager-sablonok használatával. 

Amikor az Azure Advisor új javaslatot észlel az egyik erőforrásához, egy esemény az [Azure-tevékenységnaplóban](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview)tárolódik. Az Azure Advisor által az Azure Advisor-tól riasztásokat állíthat be egy javaslatspecifikus riasztások létrehozása ként. Kiválaszthat egy előfizetést, és szükség esetén egy erőforráscsoportot, amely megadja azokat az erőforrásokat, amelyekről értesítéseket szeretne kapni. 

A javaslatok típusait az alábbi tulajdonságok használatával is meghatározhatja:

* Kategória
* Ütközési szint
* Javaslat típusa

Azt a műveletet is beállíthatja, amely akkor történik, amikor a riasztást a következő váltja ki:  

* Meglévő műveletcsoport kijelölése
* Új műveletcsoport létrehozása

A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/platform/action-groups.md) szóló cikkben talál.

> [!NOTE] 
> Az Advisor-riasztások jelenleg csak a magas rendelkezésre állású, a teljesítmény és a költségjavaslatok esetén érhetők el. A biztonsági javaslatok nem támogatottak. 

## <a name="in-the-azure-portal"></a>Az Azure Portalon
1. A **portálon**válassza az **Azure Advisor lehetőséget.**

    ![Azure Advisor a portálon](./media/advisor-alerts/create1.png)

2. A bal oldali menü **Figyelés** szakaszában válassza a **Riasztások**lehetőséget. 

    ![Figyelmeztetések az Advisorban](./media/advisor-alerts/create2.png)

3. Válassza az **Új tanácsadóriasztás lehetőséget.**

    ![Új tanácsadó riasztás](./media/advisor-alerts/create3.png)

4. A **Hatókör szakaszban** válassza ki azt az előfizetést és szükség esetén azt az erőforráscsoportot, amelyről értesítést szeretne kapni. 

    ![Tanácsadó riasztási hatóköre](./media/advisor-alerts/create4.png)

5. A **Feltétel csoportban** válassza ki a riasztás konfigurálásához használni kívánt módszert. Ha egy adott kategóriára és/vagy hatásszintre vonatkozó összes javaslatról szeretne értesítést kapni, válassza **a Kategória és hatásszint**lehetőséget. Ha egy adott típusú összes javaslatra vonatkozóan szeretne értesítést kapni, válassza a **Javaslat típust.**

    ![Az Azure Advisor riasztási feltétele](./media/advisor-alerts/create5.png)

6. A kiválasztott Beállítás beállítástól függően megadhatja a feltételeket. Ha az összes javaslatot szeretné, hagyja üresen a fennmaradó mezőket. 

    ![Tanácsadó riasztási műveletcsoportja](./media/advisor-alerts/create6.png)

7. A **műveletcsoportok** csoportban válassza a **Meglévő hozzáadása lehetőséget** a már létrehozott műveletcsoport használatához, vagy válassza az Új **létrehozása** lehetőséget új [műveletcsoport](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)beállításához. 

    ![Az Advisor riasztása meglévőt ad hozzá](./media/advisor-alerts/create7.png)

8. A Riasztás részletei szakaszban adja meg a riasztás nevét és rövid leírását. Ha engedélyezni szeretné a riasztást, hagyja a **Szabály engedélyezése a létrehozási** kijelölés i **értékén**beállítást. Ezután válassza ki azt az erőforráscsoportot, amelybe menteni szeretné a riasztást. Ez nem befolyásolja az ajánlás célzási hatókörét. 

    ![Azure-tanácsadó bannere](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>Azure Resource Manager-sablonnal

Ez az Erőforrás-kezelő sablon létrehoz egy javaslatriasztást és egy új műveletcsoportot.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest2",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      },
      "dependsOn": []
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "name": "[parameters('activityLogAlerts_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
  ```

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Javaslatriasztások konfigurálása webhook használatára
Ez a szakasz bemutatja, hogyan konfigurálhatja az Azure Advisor-riasztásokat, hogy a webhookokon keresztül javaslatokat küldjenek a meglévő rendszereknek. 

Beállíthatja, hogy a riasztások értesítést kapjanak, ha új tanácsadói javaslata van az egyik erőforrásához. Ezek a riasztások e-mailben vagy szöveges üzenetben értesíthetik Önt, de a meglévő rendszerekkel való integrációra is használhatók egy webhookon keresztül. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Az Advisor-ajánlás riasztási hasznos teher használata
Ha azt szeretné, hogy integrálja az Advisor-riasztásokat a saját rendszerébe egy webhook használatával, elemeznie kell a JSON hasznos adat, amely az értesítésből küldött. 

Amikor beállítja a műveletcsoportot ehhez a riasztáshoz, kiválaszthatja, hogy szeretné-e használni a közös riasztási sémát. Ha kiválasztja a közös riasztási sémát, a hasznos adat a következőkre fog kinézni: 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

Ha nem használja a közös sémát, a hasznos adat a következőképpen néz ki: 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

Mindkét sémában azonosíthatja az Advisor ajánlási eseményeit **az eventSource** is `Recommendation` és **operationName** is. `Microsoft.Advisor/recommendations/available/action`

Néhány más fontos mező, amelyet érdemes lehet használni: 

* *alertTargetIds* (a közös séma) vagy *resourceId* (örökölt séma)
* *ajánlástípusa*
* *ajánlásNeve*
* *ajánlásKategória*
* *ajánlásHatás*
* *ajánlásResourceLink*


## <a name="manage-your-alerts"></a>A riasztások kezelése 

Az Azure Advisorban szerkesztheti, törölheti vagy letilthatja és engedélyezheti a javaslatok riasztásait. 

1. A **portálon**válassza az **Azure Advisor lehetőséget.**

    ![Azure-tanácsadó bannere](./media/advisor-alerts/create1.png)

2. A bal oldali menü **Figyelés** szakaszában válassza a **Riasztások**lehetőséget.

    ![Azure-tanácsadó bannere](./media/advisor-alerts/create2.png)

3. A riasztás szerkesztéséhez kattintson a Riasztás nevére a riasztás megnyitásához és a szerkesztendő mezők szerkesztéséhez.

4. A riasztás törléséhez, engedélyezéséhez vagy letiltásához kattintson a sor végén lévő ellipszisre, majd válassza ki a végrehajtani kívánt műveletet.
 

