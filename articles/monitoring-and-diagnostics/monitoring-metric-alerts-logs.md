---
title: Metrikákhoz kapcsolódó riasztások hoz létre az Azure Monitor-naplók
description: A népszerű log analytics-adatok közel valós idejű metrikákhoz kapcsolódó riasztások létrehozásához keres oktatóanyagot.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 55b86e0d23936e47a82aedf213e20eb6dec3d56d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621818"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Metrikákhoz kapcsolódó riasztások létrehozása az Azure Monitor-naplók  

## <a name="overview"></a>Áttekintés
Az Azure Monitor támogatja [metric riasztástípus](monitoring-near-real-time-metric-alerts.md) előnyt amely felett az [klasszikus riasztások](alert-metric-classic.md). Metrikák érhetők el a [az Azure-szolgáltatások nagy lista](monitoring-supported-metrics.md). Ez a cikk azt ismerteti, alhálózatát (az) erőforrás - használat `Microsoft.OperationalInsights/workspaces`. 

Metrikákhoz kapcsolódó riasztások használhatja a népszerű Log Analytics-naplók, metrikák, naplók, beleértve az erőforrásokat az Azure-ban vagy a helyi mérőszámainak részeként ki kell olvasni. A támogatott Log Analytics-megoldások az alábbiak:
- [Teljesítményszámlálók](../log-analytics/log-analytics-data-sources-performance-counters.md) Windows és Linux rendszerű gépek
- [Szívverés rekordok az ügynök állapota](../monitoring/monitoring-solution-agenthealth.md)
- [Frissítéskezelés](../automation/automation-update-management.md) rekordok
- [Eseményadatok](../log-analytics/log-analytics-data-sources-windows-events.md) naplók
 
Nincsenek számos előnnyel jár a **metrika riasztások naplók** keresztül a lekérdezéshez [Naplóriasztások](alert-log.md) az Azure rendszerben; ezek közül az alábbiak:
- Metrikákhoz kapcsolódó riasztások ajánlja fel a közel valós idejű figyelési képességek és metrika riasztások elágazásoknak adataiból annak biztosítása érdekében ugyanazt a napló forrásból
- Metrikákhoz kapcsolódó riasztások állapotalapúak – csak az után, ha a riasztás akkor aktiválódik, és ha riasztás oldódik meg; ha értesítése ellentétben a riasztások, amelyek állapot nélküli és tartsa a riasztási feltétel teljesülése esetén ez minden időközönként
- Metrikákhoz kapcsolódó riasztások a Log több dimenzióban, adja meg például a számítógépeket, az operációs rendszer típusa és az egyéb egyszerűbb; adott értékekre szűrést elemzési lekérdezés penning szükségessége nélkül

> [!NOTE]
> Adott metrika és/vagy a dimenzió csak megjelenik létezik-e adatokat, a választott időszakban. Ezek a metrikák az Azure Log Analytics-munkaterületek rendelkező ügyfelek számára érhetők el.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Metrikák és naplók támogatott méretek
 metrikákhoz kapcsolódó riasztások támogatja a dimenziók használó metrikákhoz riasztásokat. Dimenziók segítségével szűrheti a metrika a megfelelő szintre. Teljes listája megtalálható a naplókhoz a támogatott mérőszámok [Log Analytics-munkaterületek](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces) között támogatott a megoldások; szerepel a listán.

> [!NOTE]
> A Log Analytics-munkaterület-n keresztül való kibontása közben a támogatott mérőszámok megtekintéséhez [Azure Monitor - metrikák](monitoring-metric-charts.md); egy metrikát a napló léteznie kell az említett metrika riasztás. A kiválasztott metrika riasztási a naplók - dimenziók feltárási keresztül az Azure Monitor - metrikák csak jelenik meg.

# <a name="creating-metric-alert-for-log-analytics"></a>Metrikariasztás létrehozása a Log Analytics
A népszerű naplókból metrikaadatok van olyan parancsoknak, a Log Analytics, az Azure Monitor - metrikák feldolgozása előtt. Ez lehetővé teszi a felhasználók számára a metrika platform, valamint a metrikariasztás – beleértve a figyelmeztetések kérelemegységszámot 1 perces gyakorisággal képességeit kihasználva. Az alábbiakban az azt jelenti, hogy a naplók metrikariasztás elvégezte.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Metrikariasztás naplók előfeltételei
A metrika a naplókhoz gyűjtött a Log Analytics data működéséről, mielőtt a következő kell állítani és elérhetők:
1. **Aktív Log Analytics-munkaterület**: érvényes és aktív Log Analytics-munkaterület jelen kell lennie. További információkért lásd: [Log Analytics-munkaterület létrehozása az Azure Portalon](../log-analytics/log-analytics-quick-create-workspace.md).
2. **Ügynök úgy van beállítva, a Log Analytics-munkaterület**: ügynök kell konfigurálni az Azure virtuális gépek (és/vagy) a helyszíni virtuális gépek is küldhet adatokat, a Log Analytics-munkaterületet a korábbi lépésben használt. További információkért lásd: [Log Analytics - ügynök – áttekintés](../monitoring/monitoring-overview-azure-agents.md).
3. **Telepítve van a támogatott Log Analytics Solutions**: Log Analytics megoldás kell lennie állítva és küldő adatok Log Analytics-munkaterület - támogatott megoldások [teljesítményszámlálók a Windows és Linux-környezetekkel](../log-analytics/log-analytics-data-sources-performance-counters.md), [Szívverési rekordok Agent Health](../monitoring/monitoring-solution-agenthealth.md), [Update management, és [eseményadatok](../log-analytics/log-analytics-data-sources-windows-events.md).
4. **Log Analytics megoldások, amely naplókat küld**: Log Analytis megoldásnak rendelkeznie kell a szükséges naplók és adatok megfelelő [Log Analytics-munkaterületek a támogatott mérőszámok](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces) engedélyezve van. Például *rendelkezésre álló memória %* kell konfigurálni, hogy a számláló [teljesítményszámlálók](../log-analytics/log-analytics-data-sources-performance-counters.md) megoldás első.

## <a name="configuring-metric-alert-for-logs"></a>Metrikariasztás naplók konfigurálása
 metrikákhoz kapcsolódó riasztások hozhatók létre és kezeli az Azure portal Resource Manager-sablonok, a REST API-t, a PowerShell és az Azure CLI használatával. Mivel a metrika riasztások a naplókhoz, valamely változatában metrikákhoz kapcsolódó riasztások – Miután végzett az előfeltételeket, metrikariasztás naplókhoz megadott Log Analytics-munkaterület is létrehozható. Az összes jellemzőit és funkciói [ metrikákhoz kapcsolódó riasztások](monitoring-near-real-time-metric-alerts.md) metrikákhoz kapcsolódó riasztások naplókat, valamint; például adattartalom-sémát, a vonatkozó kvótát és a számlázott díj érvényes lesz.

Részletesen és -minták –: [létrehozásába és kezelésébe metrikákhoz kapcsolódó riasztások](https://aka.ms/createmetricalert). Pontosabban a naplók - metrika riasztások kövesse az utasításokat a metrikákhoz kapcsolódó riasztások kezelése és ellenőrizze, a következő:
- Metrikariasztás van egy érvényes cél *Log Analytics-munkaterület*
- A kiválasztott metrikariasztás kiválasztott jel *Log Analytics-munkaterület* típusú **metrika**
- Az egyes esetekben vagy ezekkel a szűrőkkel dimenzió; erőforrás szűrése metrikák, naplók többdimenziós.
- Konfigurálásakor *Jellogika*, egyetlen riasztás hozható létre több több érték dimenzió (például számítógép)
- Ha **nem** metrikariasztás létrehozása a kiválasztott az Azure portal használatával *Log Analytics-munkaterület*; felhasználónak manuálisan kell majd először hozzon létre egy explicit szabály a Teljesítménynapló-adatok átalakítása használatávalmetrika[Az azure Monitor - ütemezett lekérdezési szabály](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules
).

> [!NOTE]
> Metrikariasztás az Azure Portalon – Log Analytics-munkaterület létrehozásakor szabály Teljesítménynapló-adatok átalakítása a metrika-n keresztül a megfelelő [Azure Monitor - ütemezett lekérdezési szabály](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) automatikusan jön létre a háttér-információkért  *művelet vagy felhasználói beavatkozás nélkül*. Metrikariasztás naplók létrehozási azt jelenti, hogy nem az Azure portal használatával, lásd: [erőforrás sablon metrika riasztások naplók](#resource-template-for-metric-alerts-for-logs) szakasz a minta azt jelenti, hogy egy alapú ScheduledQueryRule napló metrika átalakítási szabály előtt metrikariasztás létrehozása létrehozás – más nem lesznek a létrehozott naplók a metrikariasztás nincsenek adatok.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Metrikákhoz kapcsolódó riasztások naplók erőforrás sablonja
Ahogyan korábban hangsúlyoztuk, a folyamat a naplókból metrikákhoz kapcsolódó riasztások létrehozása a következő két pronged:
1. Hozzon létre egy szabályt a metrikák kinyerését scheduledQueryRule API-val támogatott naplók
2. Jelentkezzen be (az 1. lépés) kinyert metrika metrikariasztás létrehozása és a egy cél-erőforrásra, Log Analytics-munkaterület

Ugyanennek, egy használhatja a mintát az Azure Resource Manager-sablon az alábbi – ahol metrikariasztás létrehozása függ, hogy a szabály metrikák kinyerését keresztül scheduledQueryRule naplók sikeres létrehozása.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                "description": "Specifies whether the log convertion rule is enabled"
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
Tegyük fel, hogy a fenti JSON metricfromLogsAlert.json - mentett akkor is kombinálható az egy paraméter JSON-fájlt a sablonalapú erőforrás létrehozásához. Alább egy mintát paraméter JSON-fájlt:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
Feltéve, hogy a fenti paraméterfájl mentett metricfromLogsAlert.parameters.json; egyet hozhat létre metrikariasztásokat használatával a naplókat, majd [Resource-sablon létrehozása az Azure Portalon](../azure-resource-manager/resource-group-template-deploy-portal.md). 

Az egyik is használhatja az alábbi Azure Powershell-parancsot is:
```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlert.json TemplateParameterFile metricfromLogsAlert.parameters.json
```

Vagy használja az Azure CLI-vel erőforrás-sablon üzembe helyezése:
```CLI
az group deployment create --resource-group myRG --template-file metricfromLogsAlert.json --parameters @metricfromLogsAlert.parameters.json
```

## <a name="next-steps"></a>További lépések

* Tudjon meg többet a [ metrikákhoz kapcsolódó riasztások](https://aka.ms/createmetricalert).
* Ismerje meg [naplóriasztások az Azure-ban](monitor-alerts-unified-log.md).
* Ismerje meg [riasztások az Azure-ban](monitoring-overview-alerts.md).
