---
title: Mentett keresések és a riasztások felügyeleti megoldások |} A Microsoft Docs
description: Felügyeleti megoldások általában tartalmaznak a mentett keresések, a Log Analytics megoldás által összegyűjtött adatok elemzéséhez.  Ezeket is definiálhat a riasztás értesíti a felhasználót, vagy automatikusan hajtsa végre a műveletet egy kritikus problémát adott válaszként.  Ez a cikk ismerteti, hogyan adhat meg a Log Analytics-beli mentett keresések és a riasztások a Resource Manager-sablonnal, azok felvehetők az eszközkezelési megoldások.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/18/2018
ms.author: bwren, vinagara
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd8ba47e8fb0d591fab7717117329357b74f907a
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585968"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Log Analytics hozzáadása mentett keresések és a riasztások felügyeleti megoldásra (előzetes verzió)

> [!NOTE]
> Ez az előzetes dokumentum, jelenleg előzetes verzióban elérhető kezelési megoldások létrehozásához. Semmilyen sémát, az alábbiakban a változhat.   


[Felügyeleti megoldások](monitoring-solutions.md) rendszerint tartalmazza a [mentett keresések](../log-analytics/log-analytics-log-searches.md) a Log Analytics megoldás által összegyűjtött adatok elemzéséhez.  Ezen felül is meghatározhatnak [riasztások](../log-analytics/log-analytics-alerts.md) értesíti a felhasználót, vagy automatikusan hajtsa végre a műveletet egy kritikus problémát adott válaszként.  Ez a cikk azt ismerteti, hogyan adhat meg a Log Analytics-beli mentett keresések, és a riasztást egy [Resource Management-sablonnal](../resource-manager-template-walkthrough.md) így is szerepelni a [felügyeleti megoldások](monitoring-solutions-creating.md).

> [!NOTE]
> Ebben a cikkben a minták használata, paraméterek és változók, kötelező vagy közös felügyeleti megoldások és az itt ismertetett [tervezés és felépítés felügyeleti megoldás az Azure-ban](monitoring-solutions-creating.md)  

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már megismerkedett az [felügyeleti megoldás létrehozása](monitoring-solutions-creating.md) és szerkezete egy [Resource Manager-sablon](../resource-group-authoring-templates.md) és a megoldásfájlt.


## <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
Összes erőforrás a Log Analyticsben tárolják a [munkaterület](../log-analytics/log-analytics-manage-access.md).  Leírtak szerint [Log Analytics-munkaterületet és Automation-fiók](monitoring-solutions.md#log-analytics-workspace-and-automation-account), a munkaterület nem található meg a felügyeleti megoldás, de a megoldás telepítése előtt léteznie kell.  Ha nem érhető el, akkor a megoldás telepítése sikertelen lesz.

A munkaterület neve van be minden egyes Log Analytics-erőforrás nevét.  Ezt a megoldást a **munkaterület** paraméter SavedSearch erőforrás a következő példához hasonlóan.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics API-verzió
Egy Resource Manager-sablonban definiált összes Log Analytics erőforrás rendelkezik egy tulajdonság **apiVersion** , amely meghatározza, hogy használja az erőforrás API-verzió.   

Az alábbi táblázat az ebben a példában használt erőforrás API-verzió.

| Erőforrás típusa | API-verzió | Lekérdezés |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Esemény &#124; ahol Error == "Error"  |


## <a name="saved-searches"></a>Mentett keresések
Például [mentett keresések](../log-analytics/log-analytics-log-searches.md) a megoldás a felhasználók a megoldás által összegyűjtött adatokat lekérdezni.  Mentett keresések meg fog jelenni **Kedvencek** az OMS-portálon, és **mentett keresések** az Azure Portalon.  Mentett keresés is az egyes riasztások szükség.   

[A log Analytics-beli mentett keresés](../log-analytics/log-analytics-log-searches.md) erőforrás rendelkezik egy típusú `Microsoft.OperationalInsights/workspaces/savedSearches` és az alábbi struktúrával rendelkeznek.  Ez magában foglalja közös változók és paraméterek, így másolhatja, és ez a kódrészlet illessze be a megoldásfájlt, és módosítsa a paraméterek nevei. 

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



Mentett keresés minden egyes tulajdonsága a következő táblázatban leírt. 

| Tulajdonság | Leírás |
|:--- |:--- |
| category | A kategória a mentett keresésnek.  Minden mentett keresések megoldásban gyakran oszt ki egyetlen kategória, így együtt vannak csoportosítva a konzolon. |
| DisplayName | A mentett keresésnek, a portálon megjelenítendő neve. |
| lekérdezés | A lekérdezés futtatásához. |

> [!NOTE]
> Szükség lehet escape-karaktereket használja a lekérdezésben, ha JSON-fájlként is értelmezhető karaktereket tartalmaz.  Például, ha a lekérdezés a(z) **típusa: AzureActivity OperationName:"Microsoft.Compute/virtualMachines/write"**, ez a megoldás fájlt kell megírni **típusa: AzureActivity OperationName:\\" Microsoft.Compute/virtualMachines/write\\"**.

## <a name="alerts"></a>Riasztások
[Az Azure naplóriasztások](../monitoring-and-diagnostics/monitor-alerts-unified-log.md) megadott naplózási lekérdezések futtatása rendszeres időközönként Azure riasztási szabályok alapján jönnek létre.  Ha a lekérdezés eredményeit a megadott feltételeknek, létrejön egy riasztásbejegyzés, és egy vagy több művelet futtatása használatával [Műveletcsoportok](../monitoring-and-diagnostics/monitoring-action-groups.md).  

> [!NOTE]
> 2018. május 14., kezdve az összeset együtt a Log Analytics-munkaterületet az Azure nyilvános felhő példányát automatikusan megkezdődik kiterjesztése az Azure-bA. A felhasználó is önkéntesen riasztások kiterjesztésének kezdeményezése az Azure-ra 2018. május 14. előtt. További információkért lásd: [riasztások kiterjesztése az OMS-ből Azure-ba való](../monitoring-and-diagnostics/monitoring-alerts-extend.md). A felhasználók számára, hogy a riasztások kiterjesztése az Azure-bA műveletek most már az Azure action groups általi szabályozza. Egy munkaterületet, és a riasztások ki vannak bővítve az Azure-ba, amikor beolvasni, vagy a műveletek hozzáadása a [műveletcsoport – Azure Resource Manager-sablon](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

A felügyeleti megoldás riasztási szabályai a következő három különböző erőforrások épülnek fel.

- **Mentett keresés.**  A Naplókeresés, amely definiálja.  Riasztási szabályok több megoszthat egy mentett keresés.
- **Ütemezés.**  Határozza meg, milyen gyakran fut a naplóbeli keresés.  Minden riasztási szabály pontosan egy ütemezés rendelkezik.
- **Riasztási művelet.**  Minden riasztási szabály van egy művelet csoporterőforrás vagy művelet erőforrást (örökölt) típusú **riasztás** , amely meghatározza, hogy riasztásbejegyzést létrehozásakor és a riasztás súlyossága kritériumait például a riasztás részleteit. [Műveletcsoport](../monitoring-and-diagnostics/monitoring-action-groups.md) erőforrás is rendelkezhet, ha a riasztás akkor aktiválódik,-voice-hívás, SMS, például konfigurált műveleteknek a listája, e-mailt, webhookot, ITSM-eszközhöz, automation-runbookot, a logikai alkalmazás, stb.
 
A művelet erőforrás (örökölt) igény szerint határozza meg az e-mailben és a runbook választ.
- **Webhook művelettel (örökölt).**  Ha a riasztási szabály egy webhookot hív, akkor van szükség egy további műveletet erőforrás típusú **Webhook**.    

Mentett keresés a fentebbiekben leírt erőforrások.  A többi erőforrást az alábbiakban tekintheti át.


### <a name="schedule-resource"></a>Ütemezés erőforrás

Mentett keresés minden ütemezés egy külön riasztási szabály jelölő egy vagy több ütemezés is rendelkezhet. Az ütemezés határozza meg, hogy milyen gyakran a keresés Futtatás és az az időintervallum, amelyben az adatok lekérésére.  Ütemezés erőforrás rendelkezik egy típusú `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` és az alábbi struktúrával rendelkeznek. Ez magában foglalja közös változók és paraméterek, így másolhatja, és ez a kódrészlet illessze be a megoldásfájlt, és módosítsa a paraméterek nevei. 


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



Az alábbi táblázatban ismertetett ütemezés erőforrások tulajdonságait.

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| engedélyezve       | Igen | Adja meg a riasztás engedélyezve van-e a létrehozásakor. |
| interval      | Igen | Milyen gyakran a lekérdezés fut, percek alatt. |
| QueryTimeSpan | Igen | Eltelt idő percben, amelyen végre szeretné kiértékelni az eredményeket. |

Az ütemezés erőforrás a mentett keresés függ, hogy azt az ütemezést előtt hozza létre.

> [!NOTE]
> Ütemezés nevének egyedinek kell lennie az egy adott munkaterület; két ütemezések nem rendelkezhet ugyanazzal az Azonosítóval, még akkor is, ha azok a különböző mentett keresések. Minden mentett keresést, ütemezéseihez és a Log Analytics API-val létrehozott nevet is kisbetűs kell lennie.


### <a name="actions"></a>Műveletek
Egy több művelet is lehet. Művelet egy vagy több folyamat végrehajtásához, például egy levelet küld, vagy a runbook indítása adhat meg, vagy azt adhat meg egy küszöbértéket, amely azt határozza meg, ha a keresési eredmények bizonyos feltételeknek megfelelő-e.  Bizonyos műveleteket meghatározzuk is, hogy a folyamatok hajtja végre, hogy a küszöbértéket.

Műveletek használatával [műveletcsoport] erőforrás vagy a művelet erőforrás lehet definiálni.

> [!NOTE]
> 2018. május 14., kezdve az összeset együtt a Log Analytics-munkaterületet az Azure nyilvános felhő példányát automatikusan megkezdődik kiterjesztése az Azure-bA. A felhasználó is önkéntesen riasztások kiterjesztésének kezdeményezése az Azure-ra 2018. május 14. előtt. További információkért lásd: [riasztások kiterjesztése az OMS-ből Azure-ba való](../monitoring-and-diagnostics/monitoring-alerts-extend.md). A felhasználók számára, hogy a riasztások kiterjesztése az Azure-bA műveletek most már az Azure action groups általi szabályozza. Egy munkaterületet, és a riasztások ki vannak bővítve az Azure-ba, amikor beolvasni, vagy a műveletek hozzáadása a [műveletcsoport – Azure Resource Manager-sablon](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).


A művelet által megadott erőforrás két típusa van a **típus** tulajdonság.  Egy ütemezés csak egy **riasztás** művelet, amely meghatározza a részleteit, valamint a riasztási szabály milyen műveleteket hajtja végre, ha riasztás jön létre. A művelet erőforrás rendelkezik egy típusú `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.  

Riasztási műveletek az alábbi struktúrával rendelkeznek.  Ez magában foglalja közös változók és paraméterek, így másolhatja, és ez a kódrészlet illessze be a megoldásfájlt, és módosítsa a paraméterek nevei. 


```
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

A riasztási művelet erőforrás tulajdonságait az alábbi táblázatok ismertetik.

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| Típus | Igen | A művelet típusa.  Ez a **riasztás** riasztási műveletek esetében. |
| Name (Név) | Igen | A riasztás megjelenítendő neve.  Ez a riasztási szabályt a konzolon megjelenített nevet. |
| Leírás | Nem | A riasztás leírását. |
| Severity | Igen | A következő értékek közül a riasztásbejegyzést súlyossága:<br><br> **Kritikus**<br>**Figyelmeztetés**<br>**Tájékoztató**


#### <a name="threshold"></a>Küszöbérték
Ez a szakasz megadása kötelező.  Azt határozza meg a küszöbérték tulajdonságait.

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| Művelet | Igen | A következő értékek közül az összehasonlító operátort:<br><br>**gt = nagyobb, mint<br>lt = kisebb, mint** |
| Érték | Igen | Az eredmények összehasonlítandó érték. |

##### <a name="metricstrigger"></a>MetricsTrigger
Ez a szakasz nem kötelező.  Adja meg egy metrikamérési riasztás.

> [!NOTE]
> Metrikus egység riasztások jelenleg nyilvános előzetes verzióban érhető el. 

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| TriggerCondition | Igen | Annak megadása, hogy a küszöbértéket az adatszivárgás vagy a következő értékek közül egymás utáni incidensek száma összesen:<br><br>**Teljes<br>egymást követő** |
| Művelet | Igen | A következő értékek közül az összehasonlító operátort:<br><br>**gt = nagyobb, mint<br>lt = kisebb, mint** |
| Érték | Igen | A feltételeknek teljesülniük kell a riasztást kiváltó idők számát. |


#### <a name="throttling"></a>Throttling
Ez a szakasz nem kötelező.  Ez a szakasz tartalmazza, abban az esetben, ha kívánja; ugyanaz a szabály riasztásokat bizonyos idő után létrejön egy riasztás mennyisége.

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| DurationInMinutes | Igen, ha szabályozás elem tartalmazza | Riasztások mellőzésének időtartama után a rendszer létrehoz egyet a ugyanaz a riasztási szabály a percek számát. |


#### <a name="azure-action-group"></a>Az Azure műveletcsoportot
Az Azure-ban, az összeset műveletcsoport használja az alapértelmezett mechanizmusként műveletek kezelésére. A műveletcsoport adja meg a műveletet egyszer, és társíthatja a műveletcsoport több riasztás – az Azure-ban. Nem szükséges, ismételten deklarálja és újra ugyanazokat a műveleteket. Műveletcsoportok támogatja a több műveletek – például az e-mailben, SMS, hanghívás, az ITSM-kapcsolatot, Automation-Runbook, Webhook URI. 

A felhasználó számára ki van bővítve a riasztások az Azure-bA – ütemezés most rendelkezik küszöbértéket, riasztást létrehozni a együtt átadott műveletcsoport részletei. E-mail adatai, a Webhook URL-címek, Runbook-automatizálási részleteit és más műveletek, kell lennie; riasztás létrehozása előtt műveletcsoport ügyféloldali meghatározott létrehozhat egy [műveleti csoport az Azure Monitor](../monitoring-and-diagnostics/monitoring-action-groups.md) a portálon vagy [műveletcsoport - Resource-sablon](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| AzNsNotification | Igen | A riasztás a szükséges műveletek végrehajtása a riasztási feltételek teljesülése esetén társítani kell az Azure műveletcsoport erőforrás-Azonosítóját. |
| CustomEmailSubject | Nem | Egyéni Tárgy sorában az összes, a társított műveletcsoportban megadott címekre küldött e-mailt. |
| CustomWebhookPayload | Nem | Testre szabott tartalom használatával kell küldeni a kapcsolódó műveleteket csoportban meghatározott összes webhook végpontot. Mi a webhook, a várt, és kell lennie egy érvényes szerializált JSON formátumban függ. |


#### <a name="actions-for-oms-legacy"></a>Műveletek OMS (örökölt)

Minden ütemezve van egy **riasztási** művelet.  Ez meghatározza, hogy a riasztást, és igény szerint értesítési és javítási műveletek részleteit.  Értesítés e-mailt küld egy vagy több címet.  A szervizelés runbook elindítja az Azure Automation és próbálja meg elhárítani a probléma.

> [!NOTE]
> 2018. május 14., kezdve az összeset együtt a Log Analytics-munkaterületet az Azure nyilvános felhő példányát automatikusan megkezdődik kiterjesztése az Azure-bA. A felhasználó is önkéntesen riasztások kiterjesztésének kezdeményezése az Azure-ra 2018. május 14. előtt. További információkért lásd: [riasztások kiterjesztése az OMS-ből Azure-ba való](../monitoring-and-diagnostics/monitoring-alerts-extend.md). A felhasználók számára, hogy a riasztások kiterjesztése az Azure-bA műveletek most már az Azure action groups általi szabályozza. Egy munkaterületet, és a riasztások ki vannak bővítve az Azure-ba, amikor beolvasni, vagy a műveletek hozzáadása a [műveletcsoport – Azure Resource Manager-sablon](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

##### <a name="emailnotification"></a>EmailNotification
 Ez a szakasz nem kötelező adja meg, ha azt szeretné, hogy egy vagy több címzett e-mailt küldjön egy riasztást.

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| Címzettek | Igen | Értesítés küldése, ha riasztás jön létre például a következő példában az e-mail-címeket vesszővel tagolt listája.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Tárgy | Igen | Az e-mail tárgysora. |
| Melléklet | Nem | Jelenleg nem támogatja a mellékleteket.  Ha ez az elem megtalálható, kell lennie **None**. |


##### <a name="remediation"></a>Szervizelés
Ez a szakasz nem kötelező adja meg, ha azt szeretné, hogy egy runbookot, hogy a riasztásra adott válaszként indítandók. |

| Elem neve | Szükséges | Leírás |
|:--|:--|:--|
| RunbookName | Igen | A runbook elindításához neve. |
| WebhookUri | Igen | URI-ját a webhookot a runbookhoz. |
| Lejárat | Nem | Dátum és idő, amely a szervizelés lejár. |

##### <a name="webhook-actions"></a>Webhook-műveletek

Webhook-műveletek egy folyamat meghívása egy URL-címet és szükség esetén elküldendő hasznos megkezdéséhez. Azok javítási műveletek hasonló azzal a különbséggel, nem tér ki rájuk, amelyek aktiválják előfordulhat, hogy az Azure Automation-runbookok eltérő folyamatok webhookok. Ezenkívül tartalmaznak további lehetőséget kínál a hasznos kell továbbítani a távoli folyamat.

Ha a riasztás egy webhookot hív, akkor egy művelet erőforrás típusú kell **Webhook** mellett a **riasztás** művelet erőforrás.  

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
| type | Igen | A művelet típusa.  Ez a **Webhook** webhook-műveletek esetében. |
| név | Igen | A művelet megjelenítendő nevét.  Ez nem jelenik meg a konzolon. |
| wehookUri | Igen | A webhook URI azonosítója. |
| customPayload | Nem | A webhook küldendő egyéni adattartalom. Mi a webhookot, a várt formátumban függ. |


## <a name="sample"></a>Sample

Következő látható egy minta olyan megoldás, amely tartalmazza, amely a következő forrásokat tartalmazza:

- Mentett keresés
- Ütemezés
- Műveletcsoport

A példa [standard megoldás paraméterek]( monitoring-solutions-solution-file.md#parameters) változókat gyakran használni kívánt figyelésekor az erőforrás-definíciókban hardcoding értékek-megoldásban.

```
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
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
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
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/',  variables('MyAlert').Schedule.Name, '/',  variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
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
            "AzNsNotification": {
              "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
              "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
            }             
            }
          }
        ]
    }
```

A következő paraméterfájl minták értékeket biztosítja a megoldás.
```
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
* [Nézetek hozzáadása](monitoring-solutions-resources-views.md) a felügyeleti megoldáshoz.
* [Adja hozzá az Automation-runbookok és egyéb erőforrások](monitoring-solutions-resources-automation.md) a felügyeleti megoldáshoz.

