---
title: Új javaslatok Azure Advisor riasztások létrehozása
description: Új javaslat Azure Advisor riasztások létrehozása
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 07cbc57ef718b6cac104d2b5238ff4e3196f197a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75443156"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>Új javaslatokra vonatkozó Azure Advisor riasztások létrehozása 

Ebből a cikkből megtudhatja, hogyan állíthatja be a Azure Advisor új javaslataira vonatkozó riasztásokat a Azure Portal és a Azure Resource Manager sablonok használatával. 

Ha Azure Advisor észleli az egyik erőforrásra vonatkozó új javaslatot, egy eseményt az [Azure-tevékenység naplójában](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview)tárol a rendszer. Az eseményekre vonatkozó riasztásokat a Azure Advisor-specifikus riasztások létrehozási felületének használatával állíthatja be. Kiválaszthat egy előfizetést, és opcionálisan egy erőforráscsoportot is megadhatja azokat az erőforrásokat, amelyekről riasztást szeretne kapni. 

A következő tulajdonságokkal is meghatározhatja a javaslatok típusait:

* Kategória
* Hatás szintje
* Javaslat típusa

Azt is beállíthatja, hogy milyen műveletet hajtson végre a rendszer a riasztás indításakor:  

* Meglévő műveleti csoport kiválasztása
* Új műveleti csoport létrehozása

A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/platform/action-groups.md) szóló cikkben talál.

> [!NOTE] 
> Az Advisor-riasztások jelenleg csak a magas rendelkezésre állás, a teljesítmény és a Cost-javaslatok esetében érhetők el. A biztonsági javaslatok nem támogatottak. 

## <a name="in-the-azure-portal"></a>Az Azure Portalon
1. A **portálon**válassza a **Azure Advisor**lehetőséget.

    ![Azure Advisor a portálon](./media/advisor-alerts/create1.png)

2. A bal oldali menü **figyelés** területén válassza a **riasztások**lehetőséget. 

    ![Riasztások az Advisorban](./media/advisor-alerts/create2.png)

3. Válassza az **új Advisor-riasztás**lehetőséget.

    ![Új Advisor-riasztás](./media/advisor-alerts/create3.png)

4. A **hatókör** szakaszban válassza ki az előfizetést és opcionálisan azt az erőforráscsoportot, amelyet a riasztásra szeretne kapni. 

    ![Advisor riasztási hatóköre](./media/advisor-alerts/create4.png)

5. A **feltétel** szakaszban válassza ki a riasztás konfigurálásához használni kívánt módszert. Ha szeretne riasztást kapni egy adott kategória és/vagy hatás szintjére vonatkozó javaslatokról, válassza a **Kategória és a hatás szintjét**. Ha egy bizonyos típusra vonatkozó javaslatra vonatkozóan szeretne riasztást kapni, válassza a **javaslat típusa**lehetőséget.

    ![Azure Advisor riasztási feltétel](./media/advisor-alerts/create5.png)

6. A kiválasztott beállítástól függően megadhatja a feltételt. Ha minden javaslatot szeretne, hagyja üresen a fennmaradó mezőket. 

    ![Advisor riasztási műveleti csoport](./media/advisor-alerts/create6.png)

7. A **műveleti csoportok** szakaszban válassza a **meglévő hozzáadása** elemet a már létrehozott műveleti csoport használatához, vagy válassza az **új létrehozása** lehetőséget egy új [műveleti csoport](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)beállításához. 

    ![Advisor-riasztás meglévő hozzáadása](./media/advisor-alerts/create7.png)

8. A riasztás részletei szakaszban adja meg a riasztás nevét és rövid leírását. Ha azt szeretné, hogy a riasztás engedélyezve legyen, hagyja a **szabály engedélyezése** beállítást az **Igen**értékre a létrehozás kiválasztása után. Ezután válassza ki azt az erőforráscsoportot, amelybe menteni szeretné a riasztást. Ez nem befolyásolja a javaslat célcsoport-hatókörét. 

    ![Azure Advisor banner](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>Azure Resource Manager sablonnal

Ez a Resource Manager-sablon egy javaslat-riasztást és egy új műveleti csoportot hoz létre.

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

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Ajánlási riasztások konfigurálása webhook használatára
Ebből a szakaszból megtudhatja, hogyan konfigurálhat Azure Advisor riasztásokat arra, hogy a meglévő rendszereken webhookokon keresztül küldje el a javaslati adatait. 

Beállíthat riasztásokat, amelyekről értesítést kaphat, ha új tanácsadói javaslattal rendelkezik az egyik erőforráson. Ezek a riasztások e-mailben vagy SMS-ben értesítik Önt, de a meglévő rendszerekkel való integrálására is használhatók webhook használatával. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Az Advisor javaslat riasztási hasznos adatai
Ha az Advisor-riasztásokat webhook használatával szeretné integrálni a saját rendszereibe, meg kell elemezni az értesítésből küldött JSON-adattartalmat. 

A riasztáshoz tartozó műveleti csoport beállításakor kiválaszthatja, hogy szeretné-e használni a Common Alert sémát. Ha a gyakori riasztási sémát választja, az adattartalom a következőképpen fog kinézni: 

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

Ha nem használja a közös sémát, az adattartalom a következőhöz hasonlóan néz ki: 

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

Bármelyik sémában azonosíthatja az Advisor-javaslatok eseményeit, ha **eventSource** keres `Recommendation` , **operationName** és a `Microsoft.Advisor/recommendations/available/action`operationName.

Néhány további fontos mező, amelyet érdemes használni: 

* *alertTargetIDs* (a közös sémában) vagy *resourceId* (örökölt séma)
* *recommendationType*
* *recommendationName*
* *recommendationCategory*
* *recommendationImpact*
* *recommendationResourceLink*


## <a name="manage-your-alerts"></a>A riasztások kezelése 

A Azure Advisorban szerkesztheti, törölheti vagy letilthatja és engedélyezheti a javaslatok riasztásait. 

1. A **portálon**válassza a **Azure Advisor**lehetőséget.

    ![Azure Advisor banner](./media/advisor-alerts/create1.png)

2. A bal oldali menü **figyelés** területén válassza a **riasztások**lehetőséget.

    ![Azure Advisor banner](./media/advisor-alerts/create2.png)

3. A riasztások szerkesztéséhez kattintson a riasztás nevére a riasztás megnyitásához, és szerkessze a szerkeszteni kívánt mezőket.

4. A riasztások törléséhez, engedélyezéséhez vagy letiltásához kattintson a sor végén található ellipszisre, majd válassza ki a végrehajtani kívánt műveletet.
 

