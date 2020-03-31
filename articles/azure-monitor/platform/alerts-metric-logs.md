---
title: Metrikariasztások létrehozása naplókhoz az Azure Monitorban
description: Oktatóanyag a népszerű naplóelemzési adatok közel valós idejű metrikariasztáseinek létrehozásáról.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/17/2018
ms.subservice: alerts
ms.openlocfilehash: 6b21f228858954292e7a3bc5561d5e86fcfaaf41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055175"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Metrikariasztások létrehozása naplókhoz az Azure Monitorban

## <a name="overview"></a>Áttekintés

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure Monitor támogatja a [metrikariasztás típusát,](../../azure-monitor/platform/alerts-metric-near-real-time.md) amely a klasszikus riasztások előnyeit [élvezi.](../../azure-monitor/platform/alerts-classic-portal.md) Metrikák érhetők el [az Azure-szolgáltatások nagy listájához.](../../azure-monitor/platform/metrics-supported.md) Ez a cikk egy részhalmaz (azaz) `Microsoft.OperationalInsights/workspaces`erőforráshoz való használatát ismerteti .

Metrikariasztásokat használhat a népszerű Log Analytics-naplókmetrika metrikákként kinyert metrikák a naplók, beleértve az Azure-ban vagy a helyszíni erőforrásokat is. A támogatott Log Analytics-megoldások az alábbiak:

- [Teljesítményszámlálók](../../azure-monitor/platform/data-sources-performance-counters.md) Windows & Linux rendszerű gépekhez
- [Az ügynök állapotának szívverési rekordjai](../../azure-monitor/insights/solution-agenthealth.md)
- [Felügyeleti](../../automation/automation-update-management.md) rekordok frissítése
- [Eseményadatnaplók](../../azure-monitor/platform/data-sources-windows-events.md)

Számos előnye van a **metrikariasztások** használatával naplók lekérdezésalapú [naplóriasztások](../../azure-monitor/platform/alerts-log.md) az Azure-ban; némelyikük az alábbiakban felsorolva:

- Metrikariasztások közel valós idejű figyelési képesség és metrikariasztások a naplóforrásból származó naplók elágazások adatai, hogy biztosítsák az azonos.
- Metrikariasztások állapotalapúak – csak egyszer értesíti, ha riasztás aktiválódik, és egyszer, ha a riasztás feloldódik; a naplóriasztásokkal ellentétben, amelyek állapotnélküliek, és a riasztási feltétel teljesülése esetén minden időközönként tüzelnek.
- Metrikariasztások a naplóhoz több dimenziót biztosítanak, így a szűrés bizonyos értékekre, például számítógépekre, operációs rendszer típusára stb. anélkül, hogy penning lekérdezés analytics.

> [!NOTE]
> Adott metrika és/vagy dimenzió csak akkor jelenik meg, ha a kiválasztott időszakban léteznek adatok. Ezek a metrikák az Azure Log Analytics-munkaterületekkel rendelkező ügyfelek számára érhetők el.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Naplókhoz támogatott metrikák és dimenziók

 Metrikariasztások támogatják a dimenziókat használó metrikák riasztását. A dimenziók segítségével a megfelelő szintre szűrheti a metrikát. A [Log Analytics-munkaterületeknaplóihoz](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) támogatott mérőszámok teljes listája megjelenik; a támogatott megoldások között.

> [!NOTE]
> A Log Analytics-munkaterületről az [Azure Monitor – Metrikák](../../azure-monitor/platform/metrics-charts.md)keresztül kinyert támogatott metrika megtekintéséhez az adott metrika-riasztást kell létrehozni az adott metrikában. A metrikariasztásban a naplók kiválasztott dimenziók – csak az Azure Monitor – Metrikák keresztül idomítva jelennek meg.

## <a name="creating-metric-alert-for-log-analytics"></a>Metrikariasztás létrehozása a Log Analytics szolgáltatáshoz

A népszerű naplók metrikaadatait a Log Analytics az Azure Monitor – Metrikák dolgozza fel a Log Analytics feldolgozása előtt. Ez lehetővé teszi a felhasználók számára, hogy kihasználják a metrika platform képességeit, valamint a metrikariasztást , beleértve az 1 perces gyakorisággal rendelkező riasztásokat is.
Az alábbiakban felsorolt eszközök a metrika riasztás naplók.

## <a name="prerequisites-for-metric-alert-for-logs"></a>A naplók metrikariasztásának előfeltételei

A Log Analytics-adatokon összegyűjtött naplók metrikája előtt a következőket kell beállítani és elérhetővé tenni:

1. **Aktív loganalytics-munkaterület:** Érvényes és aktív Log Analytics-munkaterületnek kell lennie. További információt a [Log Analytics-munkaterület létrehozása az Azure Portalon című témakörben talál.](../../azure-monitor/learn/quick-create-workspace.md)
2. **Ügynök konfigurálva van a Log Analytics-munkaterület:** Ügynök konfigurálni kell az Azure virtuális gépek (és/vagy) helyszíni virtuális gépek adatok küldése a Log Analytics-munkaterület használt korábbi lépésben. További információt a [Log Analytics – Ügynök – – áttekintés című témakörben talál.](../../azure-monitor/platform/agents-overview.md)
3. **A támogatott Log Analytics-megoldások telepítve vannak**: A Log Analytics-megoldást konfigurálni kell, és adatokat kell küldeni a Log Analytics munkaterületére – a támogatott megoldások [a Windows & Linux teljesítményszámlálói](../../azure-monitor/platform/data-sources-performance-counters.md), [az Agent Health szívverési rekordjai](../../azure-monitor/insights/solution-agenthealth.md), [az Update management](../../automation/automation-update-management.md)és az Event [data](../../azure-monitor/platform/data-sources-windows-events.md).
4. **Naplók küldésére konfigurált Log Analytics-megoldások:** A Log Analytics-megoldásnak engedélyeznie kell a szükséges naplókat/adatokat a [Log Analytics-munkaterületek hez támogatott metrikáknak megfelelően.](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) Például a *%Available Memory* számláló t először a [Teljesítményszámlálók](../../azure-monitor/platform/data-sources-performance-counters.md) megoldásban kell konfigurálni.

## <a name="configuring-metric-alert-for-logs"></a>Metrikariasztás konfigurálása naplókhoz

 Metrikariasztások az Azure Portal, a Resource Manager-sablonok, a REST API, a PowerShell és az Azure CLI használatával hozhatók létre és kezelhetők. Mivel a naplók metrikariasztások, egy változata metrika riasztások – miután az előfeltételek elkészültek, metrika riasztás naplók hozhat létre a megadott Log Analytics-munkaterület. A [metrikariasztások](../../azure-monitor/platform/alerts-metric-near-real-time.md) minden jellemzője és funkciója a naplók metrikariasztásaira is vonatkozik; beleértve a hasznos sémát, a vonatkozó kvótakorlátokat és a számlázott árat.

Részletes részletek és minták – [metrikariasztások létrehozása és kezelése.](https://aka.ms/createmetricalert) Pontosabban a metrikariasztások naplók - kövesse a metrikariasztások kezelésére vonatkozó utasításokat, és biztosítsa a következőket:

- A metrikariasztás célterülete egy érvényes *Log Analytics-munkaterület*
- A metrikariasztáshoz kiválasztott *Log Analytics-munkaterülethez* kiválasztott jel **metrika** típusú
- Szűrő adott feltételekre vagy erőforrásokra dimenziószűrők használatával; a naplók metrikái többdimenziósak
- A *Signal Logic konfigurálásakor*egyetlen riasztás hozható létre, amely több dimenzióértékre is kiterjed (például számítógép)
- Ha **nem** használja az Azure Portalt metrikariasztás létrehozásához a kiválasztott *Log Analytics-munkaterülethez;* ezután a felhasználónak először létre kell hoznia egy explicit szabályt a naplóadatok metrikává alakításához az [Azure Monitor – Ütemezett lekérdezési szabályok](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)használatával.

> [!NOTE]
> Metrikariasztás létrehozásakor a Log Analytics-munkaterület az Azure Portalon keresztül - megfelelő szabály átalakítására naplóadatok metrika az [Azure Monitor - Ütemezett lekérdezési szabályok](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) automatikusan létre jön a háttérben, *anélkül, hogy bármilyen felhasználói beavatkozás vagy művelet.* Metrikariasztás a naplók létrehozása az Azure Portalon kívül más eszközökkel, [lásd: Erőforrás sablon metrikariasztások naplók](#resource-template-for-metric-alerts-for-logs) naplók minta segítségével hozzon létre egy ScheduledQueryRule alapú napló metrika konverziós szabály előtt metrika riasztás létrehozása - egyébként nem lesz adat a metrika riasztás t létrehozott naplók.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Erőforrássablon a naplók metrikariasztásaihoz

Ahogy azt korábban említették, a naplóból származó metrikariasztások létrehozásának folyamata két ágú:

1. Szabály létrehozása metrikák kibontására a támogatott naplókból az scheduledQueryRule API használatával
2. Metrikariasztás létrehozása a naplóból (1. lépés) és a Log Analytics-munkaterületcél-erőforrásként kinyert metrikához

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Metrikariasztások statikus küszöbértékkel rendelkező naplókhoz

Ennek elérése érdekében használhatja a minta Azure Resource Manager sablon alatt , ahol egy statikus küszöbérték metrika riasztás létrehozása attól függ, hogy a szabály sikeres létrehozása a naplókból az ütemezettQueryRule keresztül metrikák kinyerése.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
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
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
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

Mondja a fenti JSON menti metricfromLogsAlertStatic.json - akkor lehet csatolni egy paraméter JSON fájlt erőforrássablon alapú létrehozása. A Minta paraméter JSON fájl az alábbiakban látható:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan"
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Feltéve, hogy a fenti paraméterfájl metrikusként kerül mentésreLogsAlertStatic.parameters.json néven; ezután metrikariasztást hozhat létre a naplókhoz az Erőforrássablon használatával [az Azure Portalon történő létrehozáshoz.](../../azure-resource-manager/templates/deploy-portal.md)

Másik lehetőségként használhatja az alábbi Azure Powershell parancsot is:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Vagy használja az erőforrássablon t az Azure CLI használatával:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Metrikariasztások dinamikus küszöbértékekkel rendelkező naplókhoz

Ennek elérése érdekében használhatja a minta Azure Resource Manager sablon alatt - ahol egy dinamikus küszöbértékek metrika riasztás létrehozása attól függ, hogy sikeres létrehozása a szabály kibontása metrikák naplók keresztül scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
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
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
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
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
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

Mondja a fenti JSON menti metricfromLogsAlertDynamic.json - akkor lehet csatolni egy paraméter JSON fájlt erőforrássablon alapú létrehozása. A Minta paraméter JSON fájl az alábbiakban látható:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule"
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterOrLessThan"
          },
          "alertSensitivity": {
              "value": "Medium"
          },
          "numberOfEvaluationPeriods": {
              "value": "4"
          },
          "minFailingPeriodsToAlert": {
              "value": "3"
          },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Feltéve, hogy a fenti paraméterfájl metmetrikaként kerül mentésreLogsAlertDynamic.parameters.json néven; ezután metrikariasztást hozhat létre a naplókhoz az Erőforrássablon használatával [az Azure Portalon történő létrehozáshoz.](../../azure-resource-manager/templates/deploy-portal.md)

Másik lehetőségként használhatja az alábbi Azure Powershell parancsot is:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Vagy használja az erőforrássablon t az Azure CLI használatával:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>További lépések

- További információ a [metrikariasztásokról.](alerts-metric.md)
- Ismerje meg [a naplóriasztásokat az Azure-ban.](../../azure-monitor/platform/alerts-unified-log.md)
- További információ [az Azure-beli riasztásokról.](alerts-overview.md)
