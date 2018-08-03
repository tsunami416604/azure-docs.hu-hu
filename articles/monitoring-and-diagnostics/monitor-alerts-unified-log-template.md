---
title: Naplóriasztás létrehozása Resource Manager-sablonnal
description: Megtudhatja, hogyan hozhat létre a riasztás egy Azure Resource Manager-sablon és az API használatával.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 588a0686eda1966582b82a4673a8b6805453c94c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441442"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Naplóriasztás létrehozása Resource Manager-sablonnal
Ez a cikk bemutatja, hogyan kezelheti [naplóriasztások](monitor-alerts-unified-log.md) programozott módon ipari méretekben, az Azure-ban [Azure Resource Manager-sablon](..//azure-resource-manager/resource-group-authoring-templates.md) keresztül [Azure PowerShell-lel](../azure-resource-manager/resource-group-template-deploy.md) és [Az azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md). Jelenleg az Azure-riasztások, támogatja a naplóriasztások a lekérdezések [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) és [Azure Application Insights](../application-insights/app-insights-analytics-tour.md).

## <a name="managing-log-alert-on-log-analytics"></a>A Log Analytics riasztás kezelése
A riasztás [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) integrálva van a [új Azure-riasztások élmény](monitoring-overview-unified-alerts.md); közben továbbra is ki a Log Analytics API-k fut, és továbbra is a korábban kezeléséhezhasználtsémávalkompatibilitási[az OMS-portálon riasztásokat](..//log-analytics/log-analytics-alerts-creating.md).

> [!NOTE]
> 2018. május 14., kezdve egy adott munkaterület minden riasztás automatikusan megkezdődik kiterjesztése az Azure-bA. A felhasználó is önkéntesen riasztások kiterjesztésének kezdeményezése az Azure-ra 2018. május 14. előtt. További információkért lásd: [riasztások kiterjesztése az OMS-ből Azure-ba való](monitoring-alerts-extend.md). 

### <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon használatával
Riasztások a Log Analytics, amely a rendszeres időközönkénti mentett keresést futtat riasztási szabályok hozzák létre. Ha a lekérdezés egyeztetés eredményei adott feltételeknek, létrejön egy riasztásbejegyzés, és a egy vagy több műveletek futnak. 

Erőforrás-sablon a Log analytics-beli mentett keresés és a Log analytics alertsare elérhető dokumentáció a Log Analytics szakaszban. További tudnivalókért tekintse meg, [hozzáadása a Log Analytics mentett keresések és a riasztások](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md); mely szemléltető példák, valamint a séma részleteit tartalmazza.

### <a name="using-resource-template-via-apipowershell"></a>API/PowerShell erőforrás-sablon használatával
A Log Analytics Alert REST API RESTful és az Azure Resource Manager REST API-n keresztül érhető el. Az API-t az így elérhető lesz egy PowerShell-parancssorból, és kimenete JSON formátumban, hogy a keresési eredmények az eredmények használatával számos különböző módon programozott módon.

Tudjon meg többet [létrehozása és kezelése a REST API-val a Log Analytics riasztási szabályai](../log-analytics/log-analytics-api-alerts.md)köztük az API-t a Powershell elérése példái.

## <a name="managing-log-alert-on-application-insights"></a>Az Application Insights riasztás kezelése
Naplóriasztások az Azure Application Insights része alatt az Azure Monitor az új Azure-riasztások jelentek meg. Ezért futása alatt az Azure Monitor API-t szolgáltatásként [ütemezett lekérdezési szabály](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) REST művelet csoport.

### <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon használatával
Az Application Insights-erőforrások riasztás van valamilyen `Microsoft.Insights/scheduledQueryRules/`. Az erőforrástípus további információkért lásd: [Azure Monitor - ütemezett lekérdezési szabály API-referencia](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/).

Az alábbiakban található az struktúráját [ütemezett lekérdezési szabály létrehozása](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/createorupdate) erőforrás-sablont változókként minta adatkészlet alapján.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> És rejtett hivatkozás a célként megadott erőforrás címke mezőt kötelező kitölteni, igénybe veszik a [ütemezett lekérdezési szabály ](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) API-hívás vagy az erőforrás-sablon. 

A fenti json-mintaadatok menthető, ez a bemutató céljából (például:) sampleScheduledQueryRule.json és telepíthetők [Azure Resource Manager az Azure Portalon](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

### <a name="using-resource-template-via-clipowershell"></a>Erőforrás-sablon CLI és PowerShell használatával
Az Azure Monitor - ütemezett lekérdezési szabály API, a REST API-t és a teljes mértékben kompatibilisek az Azure Resource Manager REST API-val. Ezért azt is használható a Powershell használatával a Resource Manager egy parancsmagjához, valamint az Azure CLI-n keresztül.

Az alábbi ábra szemlélteti a használati minta erőforrás sablon korábban bemutatott (sampleScheduledQueryRule.json) Azure Resource Manager PowerShell-parancsmaggal keresztül:
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Az alábbi ábra szemlélteti a használati minta erőforrás sablon korábban bemutatott (sampleScheduledQueryRule.json) az Azure CLI Azure Resource Manager parancs-n keresztül:

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Sikeres művelet esetén 201-es állapot új riasztási szabály létrehozása ad vissza, vagy a 200-as vissza kell adni, ha egy meglévő riasztási szabályt módosítva lett.


## <a name="next-steps"></a>További lépések
* Megismerheti [naplóriasztásokra vonatkozó Webhook-műveletek](monitor-alerts-unified-log-webhook.md)
* Ismerje meg az új [Azure-riasztások](monitoring-overview-unified-alerts.md)
* Tudjon meg többet [Application Insights](../application-insights/app-insights-analytics.md)
* Tudjon meg többet [Log Analytics](../log-analytics/log-analytics-overview.md).   
