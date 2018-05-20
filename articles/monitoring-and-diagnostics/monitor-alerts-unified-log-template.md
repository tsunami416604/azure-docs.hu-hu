---
title: Riasztás létrehozása, a napló egy Resource Manager-sablonnal |} Microsoft Docs
description: Megtudhatja, hogyan napló riasztás létrehozása az Azure Resource Manager-sablon és a API használatával.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: vinagara
ms.openlocfilehash: 14a2560d91fd0f8dcc729b32c7155c4b74aa8aa1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Naplóriasztás létrehozása Resource Manager-sablonnal
Ez a cikk bemutatja, hogyan kezelheti [figyelmeztetések naplózása](monitor-alerts-unified-log.md) programozott módon léptékű Azure használatával [Azure Resource Manager sablon](..//azure-resource-manager/resource-group-authoring-templates.md) keresztül [Azure Powershell](../azure-resource-manager/resource-group-template-deploy.md) és [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md). Jelenleg Azure riasztások támogatja jelentkezzen ki riasztást a lekérdezések [Azure Naplóelemzés](../log-analytics/log-analytics-tutorial-viewdata.md) és [Azure Application Insights](../application-insights/app-insights-analytics-tour.md).

## <a name="managing-log-alert-on-log-analytics"></a>A Naplóelemzési napló riasztás kezelése
Napló-riasztás [Azure Naplóelemzés](../log-analytics/log-analytics-tutorial-viewdata.md) integrálva van a [új Azure riasztások élmény](monitoring-overview-unified-alerts.md); közben továbbra is ki Analytics API-k fut, és továbbra is a séma kezeléséreszolgálókorábbikompatibilitási[az OMS-portálon riasztásokat](..//log-analytics/log-analytics-alerts-creating.md).

> [!NOTE]
> 2018. május 14., kezdve egy munkaterület minden riasztás automatikusan megkezdődik, Azure kiterjeszti. Egy felhasználó önkéntesen kezdeményezhet kibővítése értesítések az Azure-bA 2018 május 14 előtt. További információkért lásd: [kiterjesztése értesítések az Azure az OMS Szolgáltatáshoz](monitoring-alerts-extend.md). 

### <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon használatával
Riasztási szabályok, amelyek rendszeres időközönkénti mentett keresést futtat Naplóelemzési napló riasztások jönnek létre. Ha a lekérdezés megfelelő eredmények megadott feltételeknek, egy riasztás rekord jön létre, és egy vagy több műveletek futnak. 

Az erőforrás-sablon [napló analytics mentett keresés](../log-analytics/log-analytics-log-searches.md) és [analytics riasztások jelentkezzen](../log-analytics/log-analytics-alerts.md) találhatók dokumentáció Naplóelemzési része. További információ, [Naplóelemzési hozzáadása a mentett keresések riasztások](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md); tartalmazók szemléltető mintákat, valamint a sémaadatokat.

### <a name="using-resource-template-via-apipowershell"></a>Erőforrás-sablonnal API/Powershell használatával
A napló Analytics riasztási REST API RESTful, és az Azure Resource Manager REST API-n keresztül érhető el. Az API-t fog kimeneti keresési eredmények Önnek JSON formátumban, lehetővé téve az eredmények segítségével számos különböző módon programozott módon, és a PowerShell parancssori így elérhető.

További információ [létrehozása és kezelése a REST API-val Naplóelemzési riasztási szabályok](../log-analytics/log-analytics-api-alerts.md); beleértve az API eléréséhez Powershell példát.

## <a name="managing-log-alert-on-application-insights"></a>Az Application Insights napló riasztás kezelése
Az új Azure riasztások az Azure-figyelő részeként Azure Application Insights napló riasztások jelentek meg. Ezért futása alatt Azure figyelő API, [ütemezett lekérdezési szabályok](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) REST művelet csoport.

### <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon használatával
Az Application Insights-erőforrások napló riasztáshoz tartozik egy típusú `Microsoft.Insights/scheduledQueryRules/`. Az erőforrástípus további információkért lásd: [Azure figyelője – ütemezett lekérdezési szabályok API-referencia](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/).

Az alábbiakban található a szerkezet [ütemezett lekérdezési szabályok létrehozásának](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/createorupdate) erőforrás sablon minta adatkészlet változók alapján.

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
> Rejtett-hivatkozás célerőforrás címke mező kitöltése kötelező igénybe veszik a [ütemezett lekérdezési szabályok ](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) API-hívás vagy az erőforrás-sablon. 

A fenti példa json (azaz) sampleScheduledQueryRule.json Ez a bemutató céljából menthetők, és segítségével telepíthető [az Azure portál Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

### <a name="using-resource-template-via-clipowershell"></a>Erőforrás-sablonnal CLI/Powershell használatával
A figyelő az Azure - ütemezett lekérdezési szabályok API van a REST API-t és a teljes mértékben kompatibilis a Azure Resource Manager REST API-t. Ezért használható PowerShell-parancsmag erőforrás-kezelő, valamint az Azure parancssori felület használatával.

Az alábbi ábra szemlélteti használati minta erőforrás sablon korábban bemutatott (sampleScheduledQueryRule.json) az Azure Resource Manager PowerShell parancsmag segítségével:
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Az alábbi ábra szemlélteti használati minta erőforrás sablon korábban bemutatott (sampleScheduledQueryRule.json) az Azure CLI Azure Resource Manager-parancs segítségével:

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
A sikeres művelet 201 visszatér állapot új riasztási szabály létrehozása vagy 200 adható vissza, ha egy meglévő szabályt módosítva lett.


## <a name="next-steps"></a>További lépések
* Megértéséhez [webhookműveletek napló riasztások](monitor-alerts-unified-log-webhook.md)
* További tudnivalók az új [Azure riasztások](monitoring-overview-unified-alerts.md)
* További információ [Application insights szolgáltatással](../application-insights/app-insights-analytics.md)
* További információ [Naplóelemzési](../log-analytics/log-analytics-overview.md).   
