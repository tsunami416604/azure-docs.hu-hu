---
title: A mentett keresések riasztások kezelési megoldásokban |} Microsoft Docs
description: Megoldások mentett keresések rendszerint tartalmazza a Log Analytics-megoldás által gyűjtött adatok elemzésére.  Akkor is határozza meg a figyelmeztetéseket, hogy értesítse a felhasználót, vagy automatikusan hajtsa végre a műveletet egy kritikus problémát válaszul.  A cikkből megtudhatja, hogyan adhat meg a mentett keresések és a riasztások a Resource Manager-sablon, azok megoldások tartalmazhat Naplóelemzési.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb787de23022cd7a48ec476968e05dec6560b419
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Naplóelemzési hozzáadása a mentett keresések riasztások kezelési megoldással (előzetes verzió)

> [!NOTE]
> Ez az előzetes dokumentációjában létrehozása kezelési megoldást, amely jelenleg előzetes verziójúak. Az alábbiakban a séma van változhat.   


[Megoldások](operations-management-suite-solutions.md) rendszerint tartalmazza [mentett keresések](../log-analytics/log-analytics-log-searches.md) a Log Analytics-megoldás által gyűjtött adatok elemzésére.  Előfordulhat, hogy is definiálhat [riasztások](../log-analytics/log-analytics-alerts.md) értesítse a felhasználót, vagy automatikusan hajtsa végre a műveletet egy kritikus problémát adott válaszként.  Ez a cikk ismerteti, hogyan határozza meg a mentett keresések Naplóelemzési és riasztások egy [erőforrás-kezelés sablon](../resource-manager-template-walkthrough.md) , azok tartalmazhat [megoldások](operations-management-suite-solutions-creating.md).

> [!NOTE]
> Ebben a cikkben a minták használható paramétereket és változókat, amelyek a szükséges vagy közös felügyeleti megoldás és a [tervezési és -buildek olyan felügyeleti megoldást az Azure-ban](operations-management-suite-solutions-creating.md)  

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy most már tudja, hogyan [felügyeleti megoldás létrehozása](operations-management-suite-solutions-creating.md) és felépítése egy [Resource Manager-sablon](../resource-group-authoring-templates.md) és megoldásfájlt.


## <a name="log-analytics-workspace"></a>Log Analytics Workspace
Összes erőforrása Naplóelemzési találhatók egy [munkaterület](../log-analytics/log-analytics-manage-access.md).  A [Naplóelemzési munkaterületet, és az Automation-fiók](operations-management-suite-solutions.md#log-analytics-workspace-and-automation-account), a munkaterület nem található meg a felügyeleti megoldás, de már léteznie kell a megoldás telepítve van.  Ha nem érhető el, akkor a megoldás telepítése sikertelen lesz.

A munkaterület neve nem minden Naplóelemzési erőforrás nevében.  Ezt a megoldást a **munkaterület** paraméter savedsearch erőforrás az alábbi példában látható módon.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Napló Analytics API-verzió
A Resource Manager-sablon definiált összes Naplóelemzési erőforrás rendelkezik egy tulajdonság **apiVersion** , amely meghatározza, hogy az erőforrást kell használnia az API verzióját.  Ebben a verzióban nem azonos az erőforrások, amelyek használják a [örökölt és a frissített lekérdezési nyelv](../log-analytics/log-analytics-log-search-upgrade.md).  

 Az alábbi táblázat felsorolja a mentett keresések napló Analytics API-verziók a hagyományos és a frissített munkaterületek: 

| Munkaterület-verzió | API-verzió | Lekérdezés |
|:---|:---|:---|
| 1-es verzió (örökölt)   | 2015 11-01. dátumú előnézeti | A hagyományos formátumú.<br> Példa: Írja be az esemény EventLevelName = hiba =  |
| v2 (frissítése) | 2015 11-01. dátumú előnézeti | A hagyományos formátumú.  Telepítse a frissített formátumra alakítja át.<br> Példa: Írja be az esemény EventLevelName = hiba =<br>Konvertálva: esemény &#124; ahol EventLevelName == "Error"  |
| v2 (frissítése) | 2017-03-03 – előzetes | Frissítési formátumban. <br>Példa: Az esemény &#124; ahol EventLevelName == "Error"  |



## <a name="saved-searches"></a>Mentett keresések
Tartalmaznak [mentett keresések](../log-analytics/log-analytics-log-searches.md) a megoldás a felhasználók a megoldás által gyűjtött adatait.  Mentett keresések megjelennek a **Kedvencek** az OMS-portálon és **mentett keresések** az Azure portálon.  A mentett kereséseket is meg kell adni egy riasztás.   

[A Naplóelemzési mentett keresés](../log-analytics/log-analytics-log-searches.md) erőforrások típusa lehet `Microsoft.OperationalInsights/workspaces/savedSearches` és az alábbi szerkezettel rendelkezik.  Ez magában foglalja közös változók és paramétereket, így másolhatja és illessze be a következő kódrészletet a megoldásfájlt a, és módosítsa a paraméterek nevei. 

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



A mentett kereséseket minden egyes tulajdonsága a következő táblázat ismerteti. 

| Tulajdonság | Leírás |
|:--- |:--- |
| category | A kategória a mentett keresés.  A mentett keresések megoldáskezelőben gyakran fogja osztani egyetlen kategória, így azok vannak elhelyezve, a konzol. |
| DisplayName | A mentett keresés a portálon megjelenítendő nevét. |
| query | A lekérdezés futtatásához. |

> [!NOTE]
> Előfordulhat, hogy szeretné használni a lekérdezésben szereplő escape-karakter, ha JSON-ként értelmezhetők karaktereket tartalmaz.  Például, ha a lekérdezés **típusa: AzureActivity OperationName:"Microsoft.Compute/virtualMachines/write"**, azt kell megírni, a fájl **típusa: AzureActivity OperationName:\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Riasztások
[Naplófájl Analytics riasztások](../log-analytics/log-analytics-alerts.md) mentett keresést futtat rendszeres időközönkénti riasztási szabályok jönnek létre.  Ha a lekérdezés megfelelő eredmények megadott feltételeknek, egy riasztás rekord jön létre, és egy vagy több műveletek futnak.  

A riasztási szabályok megoldásra a következő három különböző erőforrások épülnek fel.

- **Mentett keresés.**  Határozza meg a futtatni kívánt napló keresés.  Több riasztási szabályok megoszthat egy mentett keresés.
- **Ütemezés.**  Határozza meg, milyen gyakran fut a naplófájl-keresési.  Minden riasztási szabályt egy ütemezés rendelkezik.
- **A műveletet.**  Minden riasztási szabály van egy művelet erőforrás típussal rendelkező **riasztás** , amely definiálja, például amikor létrejön egy riasztás rekord és a riasztás súlyossága a riasztás részleteit.  A művelet erőforrás opcionálisan határozza meg az e-mail és runbook választ.
- **Webhook művelet (nem kötelező).**  Ha a riasztási szabály meghívja a webhook, akkor azt igényli, hogy egy további művelet erőforrás típussal rendelkező **Webhook**.    

Erőforrások fent ismertetett Keresés mentése.  A további erőforrások az alábbiakban található.


### <a name="schedule-resource"></a>Ütemezés erőforrás

A mentett kereséseket rendelkezhet minden ütemezéssel jelző külön riasztási szabály egy vagy több ütemezés. Az ütemezés határozza meg, hogy milyen gyakran a keresés futtatása és a időtartam, amelyen az adatok lekérésére.  Ütemezés erőforrások típusa lehet `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` és az alábbi szerkezettel rendelkezik. Ez magában foglalja közös változók és paramétereket, így másolhatja és illessze be a következő kódrészletet a megoldásfájlt a, és módosítsa a paraméterek nevei. 


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



Az ütemezés erőforrás tulajdonságait az alábbi táblázat ismerteti.

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| engedélyezve       | Igen | Adja meg a riasztás engedélyezve van-e létrehozásakor. |
| tartam      | Igen | A lekérdezés gyakoriságát percben futtatja. |
| queryTimeSpan | Igen | Idő (percben) keresztül, amelynél az eredmények hosszát. |

Az ütemezés erőforrás a mentett keresés függ, hogy az ütemezés előtt létrehozták.


### <a name="actions"></a>Műveletek
A művelet által megadott erőforrás két típusa van a **típus** tulajdonság.  Ütemezés szükséges **riasztás** művelet, amely meghatározza a riasztási szabály, és milyen műveleteket hajtja végre, amikor létrejön egy riasztás részletes adatait.  Az is előfordulhat, hogy tartalmazza a **Webhook** művelet, ha a riasztás olyan webhook kell hívható meg.  

Művelet erőforrások típusa lehet `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.  

#### <a name="alert-actions"></a>Riasztási műveletek

Minden ütemezve van, egy **riasztási** művelet.  Ez határozza meg a riasztást, és opcionálisan értesítési, a javítási műveletek részleteit.  Értesítést e-mailt küld egy vagy több címet.  A szervizelés elindít egy Azure Automation kísérli meg a probléma megoldásáról.

Értesítési műveletek az alábbi szerkezettel rendelkezik.  Ez magában foglalja közös változók és paramétereket, így másolhatja és illessze be a következő kódrészletet a megoldásfájlt a, és módosítsa a paraméterek nevei. 



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
            "emailNotification": {
                "recipients": [
                    "[variables('Alert').Recipients]"
                ],
                "subject": "[variables('Alert').Subject]"
            },
            "remediation": {
                "runbookName": "[variables('Alert').Remedition.RunbookName]",
                "webhookUri": "[variables('Alert').Remedition.WebhookUri]"
            }
        }
    }

A riasztási művelet erőforrás tulajdonságait az alábbi táblázatok ismertetik.

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| Típus | Igen | A művelet típusát.  Ez az **riasztás** riasztási műveletekhez. |
| Név | Igen | Megjelenítési nevet a riasztáshoz.  Ez az a riasztási szabály a konzolon megjelenített neve. |
| Leírás | Nem | A riasztás nem kötelező leírása. |
| Súlyosság | Igen | A riasztási rekord a következő értékek közül súlyossága:<br><br> **Critical**<br>**Warning**<br>**Tájékoztató** |


##### <a name="threshold"></a>Küszöbérték
Ez a szakasz meg kell adni.  Meghatározza a riasztási küszöbérték tulajdonságait.

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| Operátor | Igen | A következő értékek összehasonlítására operátor:<br><br>**gt = nagyobb, mint<br>lt = kisebb, mint** |
| Érték | Igen | Az eredmények összehasonlítandó értéket. |


##### <a name="metricstrigger"></a>MetricsTrigger
Ez a szakasz nem kötelező megadni.  Adja hozzá a metrika mérési riasztás.

> [!NOTE]
> Metrika mérési riasztások jelenleg nyilvános előzetes verziójához. 

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| TriggerCondition | Igen | Megadja, hogy a küszöbérték megszegése vagy a következő értékek közül egymást követő megszegése teljes száma:<br><br>**Total<br>Consecutive** |
| Operátor | Igen | A következő értékek összehasonlítására operátor:<br><br>**gt = nagyobb, mint<br>lt = kisebb, mint** |
| Érték | Igen | Ennyiszer a aktiválni a riasztást a feltételeknek kell megfelelnie. |

##### <a name="throttling"></a>Szabályozás
Ez a szakasz nem kötelező megadni.  Ebben a szakaszban tartalmazza, ha azt szeretné, hogy ugyanaz a szabály a riasztások letiltásához néhány időtartamig riasztás létrehozása után.

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| DurationInMinutes | Igen, ha a sávszélesség-szabályozás elemet tartalmazza | Ne jelenjen meg többé riasztások közül ugyanaz a riasztási szabály létrehozása után percek számát. |

##### <a name="emailnotification"></a>EmailNotification
 Ez a szakasz nem kötelező adja hozzá, ha azt szeretné, hogy egy vagy több címzett e-mailt küldjön a riasztást.

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| Címzettek | Igen | Értesítést küldeni, ha riasztás jön létre, mint az alábbi példában az e-mail címeket vesszővel tagolt listája.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Tulajdonos | Igen | Az e-mail tárgysora. |
| Melléklet | Nem | Jelenleg nem támogatja a mellékleteket.  Ha ez az elem megtalálható, meg kell **nincs**. |


##### <a name="remediation"></a>Szervizelés
Ez a szakasz nem kötelező adja hozzá, ha azt szeretné, hogy egy runbook elindul a riasztásra adott válaszként. |

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| RunbookName | Igen | Indítsa el a runbook neve. |
| WebhookUri | Igen | A runbook a webhook URI azonosítóját. |
| Lejárat | Nem | Dátum és idő, amely a szervizelési lejár. |

#### <a name="webhook-actions"></a>Webhookműveletek

Webhookműveletek egy folyamat megkezdéséhez hívja az egy URL-cím és a nem kötelezően kell küldeni a hasznos adatok között. Ezek hasonlóak szervizelési műveletek kivételével ezek webhookokkal, amely az Azure Automation-runbook eltérő folyamatok indít el a célja. A további lehetőséget, hogy a hasznos adatok között a távoli folyamat küldendő is biztosítanak.

Ha a riasztás fel fogja hívni a webhook, akkor el kell egy művelet erőforrás típussal rendelkező **Webhook** kívül a **riasztás** művelet erőforrás.  

    {
      "name": "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Webhook').Name)]",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions/",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
      ],
      "properties": {
        "etag": "*",
        "type": "[variables('Alert').Webhook.Type]",
        "name": "[variables('Alert').Webhook.Name]",
        "webhookUri": "[variables('Alert').Webhook.webhookUri]",
        "customPayload": "[variables('Alert').Webhook.CustomPayLoad]"
      }
    }

A Webhook művelet erőforrás tulajdonságait az alábbi táblázatok ismertetik.

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| típus | Igen | A művelet típusát.  Ez az **Webhook** webhook műveletekhez. |
| név | Igen | A művelet megjelenítendő nevét.  Ez a konzol nem jelenik meg. |
| wehookUri | Igen | A webhook URI. |
| customPayload | Nem | A webhook küldendő egyéni hasznos. A formátum a tartalma a webhook megfelelő függ. |




## <a name="sample"></a>Minta

Az alábbiakban látható egy minta a megoldás, amely tartalmazza, amely a következőket tartalmazza:

- Mentett keresés
- Ütemezés
- Riasztási művelet
- Webhook művelet

A mintánkban [szokásos megoldást paraméterek](operations-management-suite-solutions-solution-file.md#parameters) változókat, amelyek gyakran használni a megoldás az erőforrás-definíciókban hardcoding értékek szemben.

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
          "accountName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Automation account"
            }
          },
          "workspaceregionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Log Analytics workspace"
            }
          },
          "regionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Automation account"
            }
          },
          "pricingTier": {
            "type": "string",
            "metadata": {
              "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
          },
          "recipients": {
            "type": "string",
            "metadata": {
              "Description": "List of recipients for the email alert separated by semicolon"
            }
          }
        },
        "variables": {
          "SolutionName": "MySolution",
          "SolutionVersion": "1.0",
          "SolutionPublisher": "Contoso",
          "ProductName": "SampleSolution",
    
          "LogAnalyticsApiVersion": "2015-11-01-preview",
    
          "MySearch": {
            "displayName": "Error records by hour",
            "query": "Type=MyRecord_CL | measure avg(Rating_d) by Instance_s interval 60minutes",
            "category": "Samples",
            "name": "Samples-Count of data"
          },
          "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
            "Severity": "Critical",
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
            "Notification": {
              "Recipients": [
                "[parameters('recipients')]"
              ],
              "Subject": "Sample alert"
            },
            "Remediation": {
              "RunbookName": "MyRemediationRunbook",
              "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=TluBFH3GpX4IEAnFoImoAWLTULkjD%2bTS0yscyrr7ogw%3d"
            },
            "Webhook": {
              "Name": "MyWebhook",
              "Uri": "https://MyService.com/webhook",
              "Payload": "{\"field1\":\"value1\",\"field2\":\"value2\"}"
            }
          }
        },
        "resources": [
          {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
            ],
            "properties": {
              "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
              "referencedResources": [
              ],
              "containedResources": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
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
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/',  variables('MyAlert').Schedule.Name, '/',  variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/',  variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
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
              "EmailNotification": {
                "Recipients": "[variables('MyAlert').Notification.Recipients]",
                "Subject": "[variables('MyAlert').Notification.Subject]",
                "Attachment": "None"
              },
              "Remediation": {
                "RunbookName": "[variables('MyAlert').Remediation.RunbookName]",
                "WebhookUri": "[variables('MyAlert').Remediation.WebhookUri]"
              }
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Webhook.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]",
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name, '/actions/',variables('MyAlert').Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Webhook",
              "Name": "[variables('MyAlert').Webhook.Name]",
              "WebhookUri": "[variables('MyAlert').Webhook.Uri]",
              "CustomPayload": "[variables('MyAlert').Webhook.Payload]"
            }
          }
        ]
    }


A következő paraméterfájl minták értékeket biztosít ebben a megoldásban.

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
            "recipients": {
                "value": "recipient1@contoso.com;recipient2@contoso.com"
            }
        }
    }


## <a name="next-steps"></a>További lépések
* [Nézetek hozzáadása](operations-management-suite-solutions-resources-views.md) a kezelési megoldással.
* [Adja hozzá az Automation-forgatókönyveket és egyéb erőforrások](operations-management-suite-solutions-resources-automation.md) a kezelési megoldással.

