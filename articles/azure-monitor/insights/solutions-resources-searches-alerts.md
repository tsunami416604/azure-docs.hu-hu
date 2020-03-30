---
title: Mentett keresések a felügyeleti megoldásokban | Microsoft dokumentumok
description: A felügyeleti megoldások általában tartalmaznak mentett naplólekérdezéseket a megoldás által gyűjtött adatok elemzéséhez. Ez a cikk bemutatja, hogyan definiálhatja a Log Analytics mentett kereséseit egy Erőforrás-kezelő sablonban.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61fc64e140af091b5ff3f631398daf901557791b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663028"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>A Log Analytics mentett kereséseinek és riasztásainak hozzáadása felügyeleti megoldáshoz (előzetes verzió)

> [!IMPORTANT]
> Amint [azt korábban bejelentették](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), a [Azure Resource Manager Template](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) *2019.* **only** [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) [PowerShell cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell) Az ügyfelek könnyedén válthatnak a régebbi munkaterületek [riasztási szabálykezelésének előnyben részesített eszközeiközött,](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) hogy az Azure Monitor scheduledQueryRules alapértelmezettként használjanak, és számos [új előnyt szerezzenek,](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) például a natív PowerShell-parancsmagok használatát, a szabályokban megnövelt visszatekintési időszakot, a szabályok létrehozását külön erőforráscsoportban vagy előfizetésben és még sok másban.

> [!NOTE]
> Ez az előzetes dokumentáció felügyeleti megoldások létrehozásához, amelyek jelenleg előzetes verzióban. Az alábbiakban ismertetett sémák változhatnak.

[A felügyeleti megoldások](solutions.md) általában a Log Analytics [mentett kereséseit](../../azure-monitor/log-query/log-query-overview.md) tartalmazzák a megoldás által gyűjtött adatok elemzéséhez. Riasztásokat is definiálhatnak, hogy [értesítsék](../../azure-monitor/platform/alerts-overview.md) a felhasználót, vagy automatikusan végrehajtanak egy kritikus probléma miatt. Ez a cikk azt ismerteti, hogyan definiálhatók a Log Analytics mentett keresései és riasztásai egy [Erőforrás-kezelési sablonban,](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) hogy azok bekerüljenek a [felügyeleti megoldásokba.](solutions-creating.md)

> [!NOTE]
> A jelen cikkben szereplő minták olyan paramétereket és változókat használnak, amelyek a felügyeleti megoldásokhoz szükségesek vagy közösek, és amelyeket a Tervezés és [felügyeleti megoldás létrehozása az Azure-ban ismertetett.](solutions-creating.md)

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már ismeri a [felügyeleti megoldás létrehozását](solutions-creating.md) és az [Erőforrás-kezelő sablon](../../azure-resource-manager/templates/template-syntax.md) és megoldásfájl szerkezetét.


## <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
A Log Analytics minden erőforrása egy [munkaterületben](../../azure-monitor/platform/manage-access.md)található. A [Log Analytics-munkaterület és az Automation-fiók](solutions.md#log-analytics-workspace-and-automation-account)ban leírtak szerint a munkaterület nem szerepel a felügyeleti megoldásban, de a megoldás telepítése előtt léteznie kell. Ha nem érhető el, akkor a megoldás telepítése sikertelen lesz.

A munkaterület neve az egyes Log Analytics-erőforrások nevében. Ez a megoldásban a **munkaterületi** paraméterrel történik, mint a SavedSearch erőforrás alábbi példájában.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics API-verziója
A Resource Manager-sablonban definiált összes Log Analytics-erőforrás rendelkezik egy **apiVersion** tulajdonsággal, amely meghatározza az erőforrás által használandó API-verziót.

Az alábbi táblázat a példában használt erőforrás API-verzióját sorolja fel.

| Erőforrás típusa | API-verzió | Lekérdezés |
|:---|:---|:---|
| mentett Keresések | 2017-03-15-előzetes | Esemény &#124; ahol EventLevelName == "Hiba"  |


## <a name="saved-searches"></a>Mentett keresések
A [mentett keresések belefoglalása](../../azure-monitor/log-query/log-query-overview.md) egy megoldásba, amely lehetővé teszi a felhasználók számára a megoldás által gyűjtött adatok lekérdezését. A mentett keresések a **Mentett keresések** az Azure Portalon jelennek meg. Minden riasztáshoz mentett keresésre is szükség van.

[A Log Analytics mentett](../../azure-monitor/log-query/log-query-overview.md) keresési `Microsoft.OperationalInsights/workspaces/savedSearches` erőforrásainak típusa és a következő struktúrája van. Ez magában foglalja a gyakori változókat és paramétereket, így ezt a kódrészletet átmásolhatja és beillesztheti a megoldásfájlba, és módosíthatja a paraméterneveket.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

A mentett keresés minden tulajdonságát az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| category | A mentett keresés kategóriája.  Az ugyanabban a megoldásban mentett keresések gyakran egyetlen kategóriát osztanak meg, így azok a konzolon vannak csoportosítva. |
| Displayname | A portálon a mentett kereséshez megjelenítendő név. |
| lekérdezés | Futtatni a lekérdezést. |

> [!NOTE]
> Előfordulhat, hogy escape karaktereket kell használnia a lekérdezésben, ha az olyan karaktereket tartalmaz, amelyek JSON-ként értelmezhetők. Ha például a lekérdezés **az AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"**, meg kell írni a megoldásfájlban **AzureActivity | OperationName:/\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Riasztások
[Az Azure Log-riasztásokat](../../azure-monitor/platform/alerts-unified-log.md) az Azure Alert-szabályok hozták létre, amelyek meghatározott naplólekérdezéseket rendszeres időközönként futtatnak. Ha a lekérdezés eredménye megfelel a megadott feltételeknek, riasztási rekord jön létre, és egy vagy több művelet fut a [műveletcsoportok](../../azure-monitor/platform/action-groups.md)használatával.

A riasztásokat az Azure-ra kiterjesztő felhasználók számára a műveletek mostantól az Azure-műveletcsoportokban vannak vezérelve. Ha egy munkaterületet és annak riasztásait kiterjeszti az Azure-ra, a [Műveletcsoport – Azure Resource Manager-sablon](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)használatával lekérheti vagy hozzáadhatműveleteket.
Az örökölt felügyeleti megoldás riasztási szabályai a következő három különböző erőforrásból állnak.

- **Mentett keresés.** A futtatott naplókeresést határozza meg. Több riasztási szabály is megoszthatja egyetlen mentett keresést.
- **Ütemezése.** Azt határozza meg, hogy milyen gyakran fusson a naplókeresés. Minden riasztási szabály nak egy és csak egy ütemezése van.
- **Riasztási művelet.** Minden riasztási szabály rendelkezik egy műveletcsoport erőforrás vagy művelet erőforrás (örökölt) egy **riasztási** típus, amely meghatározza a riasztás részleteit, például a feltételeket, amikor egy riasztási rekord jön létre, és a riasztás súlyosságát. [A műveletcsoport-erőforrás](../../azure-monitor/platform/action-groups.md) rendelkezhet a riasztás kilövésekénél végrehajtandó konfigurált műveletek listájával – például hanghívás, SMS, e-mail, webhook, ITSM eszköz, automatizálási runbook, logikai alkalmazás stb.

A mentett keresési erőforrásokat a fentiekben ismerteti. A többi forrást az alábbiakban ismertetjük.

### <a name="schedule-resource"></a>Erőforrás ütemezése

A mentett kereséshez egy vagy több ütemezés is tartozik, amelyek mindegyike külön riasztási szabályt képvisel. Az ütemezés határozza meg, hogy milyen gyakran fusson a keresés, és milyen időintervallumon keresztül olvassa be az adatokat. Az ütemezési erőforrások `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` típusa és szerkezete a következő. Ez magában foglalja a gyakori változókat és paramétereket, így ezt a kódrészletet átmásolhatja és beillesztheti a megoldásfájlba, és módosíthatja a paraméterneveket.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }
Az ütemezési erőforrások tulajdonságait az alábbi táblázat ismerteti.

| Elem neve | Kötelező | Leírás |
|:--|:--|:--|
| engedélyezve       | Igen | Itt adható meg, hogy a riasztás engedélyezve legyen-e létrehozáskor. |
| interval      | Igen | A lekérdezés által percekben futó idő. |
| queryTimeSpan | Igen | Az eredmények értékelésére eltelt idő időtartama percben. |

Az ütemezési erőforrásnak a mentett kereséstől kell függenie, hogy az az ütemezés előtt létrejön.
> [!NOTE]
> Az ütemezés nevének egyedinek kell lennie egy adott munkaterületen; két ütemezés nem rendelkezhet ugyanazzal az azonosítóval, még akkor sem, ha különböző mentett keresésekhez vannak társítva. A Log Analytics API-val létrehozott összes mentett keresés, ütemezés és művelet nevének is kisbetűsnek kell lennie.

### <a name="actions"></a>Műveletek
Egy ütemezés több műveletet is végrehajthat. Egy művelet meghatározhat egy vagy több végrehajtandó folyamatot, például egy e-mail küldését vagy egy runbook indítását, vagy meghatározhat egy küszöbértéket, amely meghatározza, hogy a keresés eredménye mikor felel meg bizonyos feltételeknek. Egyes műveletek határozzák meg mindkettőt, hogy a folyamatok végrehajtása a küszöbérték elérésekor.
A műveletek [műveletcsoport] erőforrás sal vagy műveleterőforrással definiálhatók.

A **Típus** tulajdonság kétféle műveleterőforrást határoz meg. Az ütemezés hez egy **riasztási** művelet szükséges, amely meghatározza a riasztási szabály részleteit, és milyen műveleteket hajtanak végre a riasztás létrehozásakor. A műveleterőforrások típusa `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.

A riasztási műveletek a következő struktúrával rendelkeznek. Ez magában foglalja a gyakori változókat és paramétereket, így ezt a kódrészletet átmásolhatja és beillesztheti a megoldásfájlba, és módosíthatja a paraméterneveket.

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

A riasztási művelet erőforrások tulajdonságait az alábbi táblázatok ismertetik.

| Elem neve | Kötelező | Leírás |
|:--|:--|:--|
| `type` | Igen | A művelet típusa.  Ez a riasztási műveletek **riasztási** riasztási. |
| `name` | Igen | A riasztás megjelenítendő neve.  Ez az a név, amely megjelenik a konzolon a riasztási szabály. |
| `description` | Nem | A riasztás nem kötelező leírása. |
| `severity` | Igen | A riasztási rekord súlyossága a következő értékekből:<br><br> **Kritikus**<br>**Figyelmeztetés**<br>**Információs**

#### <a name="threshold"></a>Küszöbérték
Ez a szakasz kötelező. Meghatározza a riasztási küszöbérték tulajdonságait.

| Elem neve | Kötelező | Leírás |
|:--|:--|:--|
| `Operator` | Igen | Operátor a következő értékek összehasonlításához:<br><br>**gt =<br>nagyobb, mint lt = kisebb, mint** |
| `Value` | Igen | Az eredmények összehasonlításához. |

##### <a name="metricstrigger"></a>MetricsTrigger
Ez a szakasz nem kötelező. Adja meg a metrikamérési riasztáshoz.

| Elem neve | Kötelező | Leírás |
|:--|:--|:--|
| `TriggerCondition` | Igen | Itt adható meg, hogy a küszöbérték a következő értékekből származó jogsértések vagy egymást követő szabálysértések teljes számára szolgál-e:<br><br>**Összes<br>egymást követő** |
| `Operator` | Igen | Operátor a következő értékek összehasonlításához:<br><br>**gt =<br>nagyobb, mint lt = kisebb, mint** |
| `Value` | Igen | Azon alkalmak száma, amikor a feltételeknek meg kell felelni ük a riasztás aktiválásához. |


#### <a name="throttling"></a>Throttling
Ez a szakasz nem kötelező. Ha a riasztás létrehozása után bizonyos ideig le szeretné jelennek az ugyanabból a szabályból érkező riasztásokat, adja meg ezt a szakaszt.

| Elem neve | Kötelező | Leírás |
|:--|:--|:--|
| Időtartampercben | Igen, ha a fojtási elemet is tartalmazza | A riasztások letiltására szükséges percek száma, miután egy ugyanabból a riasztási szabályból létrejön egy. |

#### <a name="azure-action-group"></a>Azure műveletcsoport
Az Azure-ban minden riasztást, használja a műveletcsoport, mint a műveletek kezelésére szolgáló alapértelmezett mechanizmus. A műveletcsoporttal egyszer megadhatja a műveleteket, majd a műveletcsoportot több riasztáshoz társíthatja – az Azure-ban. Anélkül, hogy szükség lenne, hogy újra és újra kijelentsd ugyanazokat a tetteket. A műveletcsoportok több műveletet is támogatnak – többek között e-maileket, SMS-t, hanghívást, ITSM-kapcsolatot, Automation Runbookot, Webhook URI-t stb.

A felhasználók számára, akik kiterjesztették a riasztásokat az Azure-ba – az ütemezés most már a műveletcsoport adatait át kell adni a küszöbértéket, hogy egy riasztást hozzon létre. E-mail részleteket, Webhook URL-címeket, Runbook Automation részleteket, és egyéb műveletek, meg kell határozni az oldalon egy műveletcsoport először a riasztás létrehozása előtt; létrehozhat [műveletcsoportot az Azure Monitorból](../../azure-monitor/platform/action-groups.md) a portálon, vagy használhatja [a Műveletcsoportot – Erőforrássablont.](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)

| Elem neve | Kötelező | Leírás |
|:--|:--|:--|
| AzNsÉrtesítés | Igen | Az Azure műveletcsoport erőforrás-azonosítója, amelyet a szükséges műveletek teljesítéséhez szükséges műveletekhez kell társítani, az Azure-műveletcsoport erőforrás-azonosítója. |
| CustomEmailSubject | Nem | A társított műveletcsoportban megadott összes címre küldött levél egyéni tárgysora. |
| CustomWebhookPayload | Nem | A társított műveletcsoportban definiált összes webhook-végpontra küldendő testreszabott hasznos adat. A formátum attól függ, hogy mit vár a webhook, és érvényes szerializált JSON-nak kell lennie. |

## <a name="sample"></a>Sample

Az alábbiakban egy megoldásminta látható, amely a következő erőforrásokat tartalmazza:

- Mentett keresés
- Ütemezés
- Műveletcsoport

A minta [szabványos megoldás paraméterváltozókat]( solutions-solution-file.md#parameters) használ, amelyeket általában egy megoldásban használnak, szemben az erőforrás-definíciók ban szereplő hardcoding értékekkel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

A következő paraméterfájl mintaértékeket biztosít ehhez a megoldáshoz.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>További lépések
* [Nézetek hozzáadása](solutions-resources-views.md) a felügyeleti megoldáshoz.
* [Adja hozzá az Automation runbookokat és egyéb erőforrásokat](solutions-resources-automation.md) a felügyeleti megoldáshoz.
