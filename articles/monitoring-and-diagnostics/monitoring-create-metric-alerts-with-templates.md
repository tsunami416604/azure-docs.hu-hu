---
title: Metrikariasztás létrehozása Resource Manager-sablonnal
description: Megtudhatja, hogyan metrika riasztás létrehozása egy Resource Manager-sablon használatával.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/26/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 0a4e6c2ebb57aca13a53a8ff12953f0c7a90bc61
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263446"
---
# <a name="create-a-metric-alert-with-a-resource-manager-template"></a>Metrikariasztás létrehozása Resource Manager-sablonnal
Ez a cikk bemutatja, hogyan használható egy [Azure Resource Manager sablon](../azure-resource-manager/resource-group-authoring-templates.md) konfigurálása [újabb metrika riasztások](monitoring-near-real-time-metric-alerts.md) Azure-figyelőben. Resource Manager-sablonok lehetővé teszi a programozott módon hozza létre a riasztások következetes és reprodukálható módon a környezetben. Újabb metrika riasztások jelenleg érhetők el a [e típusú erőforrások csoportja](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).

Az alapvető lépések a következők:

1. A JSON-fájl, amely leírja a riasztás létrehozása, használja az alábbi sablonok közül.
2. Szerkessze és használja a megfelelő paramétereket fájlt egy JSON-ként a riasztás testreszabása
3. A sablon szabályzatsablonokat [bármely olyan telepítési módszerrel](../azure-resource-manager/resource-group-template-deploy.md).


## <a name="resource-manager-template-for-a-simple-metric-alert"></a>Egy egyszerű metrika riasztás Resource Manager-sablon
Hozzon létre egy riasztást, a Resource Manager-sablonnal, hozzon létre egy típusú erőforrást `Microsoft.Insights/metricAlerts` , és töltse ki az összes kapcsolódó tulajdonságok. Alatt van egy minta-sablont, amely egy metrika riasztási szabályt hoz létre.

Az alábbi json elmentse simplemetricalert.json a lépésein végighaladva céljából.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],   
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"                
                    }
                ]
            }
        }
    ]
}
```

Riasztási szabály a séma és a Tulajdonságok magyarázatot [érhető el itt](https://docs.microsoft.com/en-us/rest/api/monitor/metricalerts/createorupdate).

A paraméterek értékeit állíthatja be, a parancssorban vagy egy paraméterfájl keresztül. A minta paraméterfájl lejjebb tekinthetők meg. 

Alább json elmentse simplemetricalert.parameters.json, és szükség szerint módosítsa azt.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Metric Alert"
        },
        "alertDescription": {
            "value": "New metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/Microsoft.Compute/virtualMachines/replace-with-resource-name"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "80"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group"
        }
    }
}
```


A metrika riasztás a sablon és a paraméterek fájl, a PowerShell vagy Azure CLI használatával hozhat létre.

Az Azure PowerShell használata

```powershell
Connect-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile simplemetricalert.json -TemplateParametersFile simplemetricalert.parameters.json
```


Az Azure parancssori felület használata
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file simplemetricalert.json \
    --parameters @simplemetricalert.parameters.json
```

> [!NOTE]
>
> A metrika riasztást egy másik erőforráscsoportban található a célerőforrás sikerült létrehozni, amíg a cél erőforrásként ugyanabban az erőforráscsoportban használatát javasoljuk.

## <a name="resource-manager-template-for-a-more-advanced-metric-alert"></a>A speciális metrika riasztások Resource Manager-sablon
Újabb metrika riasztások támogatási riasztásai többdimenziós metrikák, valamint a támogató több feltételt. Az alábbi sablon használatával dimenzionális mérőszámokat speciális metrika riasztás létrehozása, és adja meg a több feltételt.

Az alábbi json elmentse advancedmetricalert.json a lépésein végighaladva céljából.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the resource emitting the metric that will be used for the comparison."
            }
        },
        "criterion1":{
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "criterion2": {
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": { 
        "criterion1": "[array(parameters('criterion1'))]",
        "criterion2": "[array(parameters('criterion2'))]",
        "criteria": "[concat(variables('criterion1'),variables('criterion2'))]"
     },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": "[variables('criteria')]"
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"                
                    }
                ]
            }
        }
    ]
}
```

A fenti sablon alább paraméterfájl együtt használható. 

Mentse és advancedmetricalert.parameters.json a lépésein végighaladva céljából, az alábbi json módosítása.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Multi-dimensional Metric Alert (Replace with your alert name)"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template (Replace with your alert description)"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourcegroup-name/providers/Microsoft.Storage/storageAccounts/replace-with-storage-account"
        },
        "criterion1": {
            "value": {
                    "name": "1st criterion",
                    "metricName": "Transactions",
                    "dimensions": [
                        {
                            "name":"ResponseType",
                            "operator": "Includes",
                            "values": ["Success"]
                        },
                        {
                            "name":"ApiName",
                            "operator": "Includes",
                            "values": ["GetBlob"]
                        }
                    ],
                    "operator": "GreaterThan",
                    "threshold": "5",
                    "timeAggregation": "Total"
                }
        },
        "criterion2": {
            "value":{
                "name": "2nd criterion",
                "metricName": "SuccessE2ELatency",
                "dimensions": [
                    {
                        "name":"ApiName",
                        "operator": "Includes",
                        "values": ["GetBlob"]
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "250",
                "timeAggregation": "Average"
            }      
        },          
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/Default-ActivityLogAlerts/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }    
}
```


A metrika a PowerShell vagy Azure CLI használata az aktuális munkakönyvtárban a sablon és a paraméterek fájllal riasztást hozhat létre

Az Azure PowerShell használata
```powershell
Connect-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile advancedmetricalert.json -TemplateParametersFile advancedmetricalert.parameters.json
```



Az Azure parancssori felület használata
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file advancedmetricalert.json \
    --parameters @advancedmetricalert.parameters.json
```

>[!NOTE]
>
> A metrika riasztást egy másik erőforráscsoportban található a célerőforrás sikerült létrehozni, amíg a cél erőforrásként ugyanabban az erőforráscsoportban használatát javasoljuk.

## <a name="next-steps"></a>További lépések
* Tudjon meg többet az [értesítések az Azure-ban](monitoring-overview-unified-alerts.md)
* Megtudhatja, hogyan [művelet csoport Resource Manager-sablonok létrehozása](monitoring-create-action-group-with-resource-manager-template.md)
