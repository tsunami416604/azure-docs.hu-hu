---
title: A riasztásokra az Azure Naplóelemzés válaszok |} Microsoft Docs
description: Naplóelemzési riasztások határozza meg az Azure munkaterületen fontos adatokat és is proaktív értesítést küldenek, problémák vagy meghívása műveletek kijavításának őket.  Ez a cikk ismerteti a riasztási szabály és a részletek a különböző általuk végezhető műveletek létrehozása.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 717adf1b19b9de8542ec507df3a01b187d0df8a5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="add-actions-to-alert-rules-in-log-analytics"></a>Műveletek hozzáadni a Naplóelemzési riasztási szabályok

> [!NOTE]
> A Naplóelemzési riasztások [az Azure kiterjesztendő](../monitoring-and-diagnostics/monitoring-alerts-extend.md).  Riasztások az Azure használatát [művelet csoportok](../monitoring-and-diagnostics/monitoring-action-groups.md) ebben a cikkben az információk helyett a lépések meghatározásához.


Ha egy [riasztást hoz létre a Naplóelemzési](log-analytics-alerts.md), lehetősége van a [a riasztási szabály konfigurálása](log-analytics-alerts.md) egy vagy több műveletek elvégzéséhez.  Ez a cikk ismerteti a rendelkezésre álló különféle műveletek és a részletek konfigurálásával összes típusához.

| Műveletek | Leírás |
|:--|:--|
| [E-mail](#email-actions) | A riasztás részleteit e-mailt küldeni egy vagy több címzett. |
| [Webhook](#webhook-actions) | Egy külső folyamatban egy HTTP POST kérelemben keresztül meghívni. |
| [a runbook](#runbook-actions) | Elindít egy forgatókönyvet az Azure Automation. |


## <a name="email-actions"></a>E-mailek műveletek
E-mailek műveletek a riasztás részleteit e-mail küldése egy vagy több címzett.  Az e-mail tárgyát megadhatja, a tartalma azonban szabványos, és a Log Analytics hozza létre.  Ez magában foglalja például a nevét a riasztás részleteit a napló keresés által visszaadott legfeljebb tíz rekordok mellett összegző információkat.  A Naplóelemzési, hogy a rekordok teljes készletet ad vissza, hogy a lekérdezés egy napló keresés hivatkozást is tartalmaz.   Az üzenet küldője *a Microsoft Operations Management Suite Team &lt; noreply@oms.microsoft.com &gt;* . 

E-mailek műveletek igényelnek a tulajdonságok az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| Tárgy |A tulajdonos e-mailben.  Az üzenet törzse nem módosítható. |
| Címzettek |Címzett e-mail címét.  Ha több címet ad meg, pontosvesszővel (;) válassza el őket. |


## <a name="webhook-actions"></a>Webhookműveletek

Webhookműveletek lehetővé teszi egy külső folyamatban egy HTTP POST kérelemben keresztül.  A meghívott szolgáltatás kell webhookok támogatja, és határozza meg, hogyan használja a tartalom kap.  Sikerült is meghívhatja a REST API-t nem támogató kifejezetten webhookokkal, amíg a kérelmet, amely együttműködik a API formátumban.  Példák a webhook riasztást válaszul egy üzenetet küldi [Slackhez](http://slack.com) , vagy hozzon létre egy incidenst a [PagerDuty](http://pagerduty.com/).  Riasztási szabály létrehozása a Slackhez hívása olyan webhook részletes útmutatást érhető el: [Naplóelemzési riasztásokban Webhookok](log-analytics-alerts-webhooks.md).

Webhookműveletek igényelnek a tulajdonságok az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| Webhook URL-CÍMÉT |A webhook URL-CÍMÉT. |
| Egyéni JSON-adattartalmat |A webhook küldendő egyéni hasznos.  További információ alább olvasható. |


Webhook URL-címet és a hasznos adatok között, amely a külső szolgáltatásnak továbbított adatok JSON formátumú, tartalmazza.  Alapértelmezés szerint a tartalom a következő táblázat a értékeket tartalmaz.  Ha szeretné, ezek a hasznos adatok kicseréli a saját egyéni egy.  Ebben az esetben használhatja a változók a táblázatban az egyes paraméterek egyéni adattartalmat értékük felvenni.


| Paraméter | Változó | Leírás |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |A riasztási szabály neve. |
| AlertThresholdOperator |#thresholdoperator |A riasztási szabály operátor küszöbértéket.  *Nagyobb, mint* vagy *kisebb, mint*. |
| AlertThresholdValue |#thresholdvalue |A riasztási szabály tartozó küszöbérték. |
| LinkToSearchResults |#linktosearchresults |A lekérdezésből, amely a riasztás létrehozása a rekordok visszaadó Naplóelemzési napló keresési kapcsolódik. |
| Attribútumhoz resultcount számlálót. |#searchresultcount |A keresési eredmények rekordok száma. |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |Befejezési ideje UTC formátumban a lekérdezést. |
| SearchIntervalInSeconds |#searchinterval |A riasztási szabály időszak. |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |Indítsa el a lekérdezések ideje UTC formátumban. |
| SearchQuery |#searchquery |Naplófájl-keresési lekérdezés a riasztási szabály által használt. |
| SearchResults |Lásd alább |A lekérdezés JSON formátumban rögzíti.  Az első 5000 rekordok korlátozva. |
| WorkspaceID |#workspaceid |A Naplóelemzési munkaterület azonosítója. |

Például megadhatja a következő egyéni payload nevű egyetlen paramétert tartalmazó *szöveg*.  A szolgáltatás, amely behívja a webhook a ennek a paraméternek, akkor rendszer.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Ez a példa hasznos volna oldja fel a következőhöz, ha a webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

A keresési eredmények belefoglalása az egyéni adattartalom, adja hozzá a következő sort a json-adattartalmat legfelső szintű tulajdonság.  

    "IncludeSearchResults":true

Például egy egyéni adattartalom, amely tartalmazza a riasztás neve és a keresési eredmények létrehozásához használhatja a következő. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Riasztási szabály létrehozása a webhook: egy külső szolgáltatás indítására átfogó példát keresztül végigvezetheti [riasztási webhook művelet létrehozása a Log Analyticshez üzenetet küldeni a Slackhez](log-analytics-alerts-webhooks.md).


## <a name="runbook-actions"></a>Runbook-műveletek
Runbook műveletek az Azure Automationben runbook indítása.  Ahhoz, hogy a művelet típusát, rendelkeznie kell a [Folyamatautomatizálási megoldása](log-analytics-add-solutions.md) telepített és konfigurált Naplóelemzési munkaterületet.  Az automation-fiókban az Automation-megoldás beállított runbookok közül választhat.

Runbook műveleteket igényelnek a tulajdonságok az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:---|
| Forgatókönyv | Ez a Runbook elindítja a riasztás létrehozásakor. |
| Futtassa a | Adja meg **Azure** a runbook futtatásához a felhőben.  Adja meg **hibridfeldolgozó** a runbook futhat az ügynök [hibrid forgatókönyv-feldolgozó](../automation/automation-hybrid-runbook-worker.md ) telepítve.  |

Runbook műveletek indíthatja a runbook egy [webhook](../automation/automation-webhooks.md).  A riasztási szabály létrehozásakor automatikusan létrehoz egy új webhook a runbook nevű **OMS riasztás szervizelési** GUID követ.  

Nem közvetlenül tölthető fel a runbook paramétereket, de a [$WebhookData paraméter](../automation/automation-webhooks.md) a a riasztást, többek között az azt létrehozó napló keresési eredmények tartalmazza.  A runbook kell határoznia, hogy **$WebhookData** úgy, hogy a riasztás tulajdonságai érhetők el a paramétert.  A riasztási adatokat érhető el egyetlen tulajdonságot, json formátumban **SearchResult** (a runbook-műveletek és a szabványos tartalom webhookműveletek) vagy **SearchResults** (webhookműveletek együtt egyéni hasznos beleértve **IncludeSearchResults ": true**) az a **RequestBody** tulajdonsága **$WebhookData**.  Az alábbi táblázatban a tulajdonságokkal azt.

>[!NOTE]
> Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), akkor a runbook tartalom módosult.  A formátum részletei: [Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse).  A példa látható [minták](#sample-payload) alatt.  

| Csomópont | Leírás |
|:--- |:--- |
| id |Elérési út és a keresés GUID. |
| __metadata |A riasztás rekordok száma és a keresési eredmények állapotát kapcsolatos információkat. |
| érték |A keresési eredmények rekordokban külön bejegyzést.  A bejegyzés a részleteket a tulajdonságok és értékek a rekord felel meg. |

Például a következő forgatókönyvek Ehhez bontsa ki a napló keresés által visszaadott rekordok, és rendelje hozzá minden egyes bejegyzés típusától függően különböző tulajdonságokat.  Vegye figyelembe, hogy a runbook elindítja átalakításával **RequestBody** JSON úgy, hogy az erőforrások az PowerShell objektumként.

>[!NOTE]
> Ez a forgatókönyv használ **SearchResult** Ez a tulajdonság, amely tartalmazza a runbook-műveletek és a szabványos tartalom webhookműveletek eredményeit.  Ha a runbook volt meghívva egy egyéni adattartalom használatával webhook választ, meg kell módosítani ezt a tulajdonságot **SearchResults**.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody

    # Get all metadata properties    
    $AlertRuleName = $RequestBody.AlertRuleName
    $AlertThresholdOperator = $RequestBody.AlertThresholdOperator
    $AlertThresholdValue = $RequestBody.AlertThresholdValue
    $AlertDescription = $RequestBody.Description
    $LinktoSearchResults =$RequestBody.LinkToSearchResults
    $ResultCount =$RequestBody.ResultCount
    $Severity = $RequestBody.Severity
    $SearchQuery = $RequestBody.SearchQuery
    $WorkspaceID = $RequestBody.WorkspaceId
    $SearchWindowStartTime = $RequestBody.SearchIntervalStartTimeUtc
    $SearchWindowEndTime = $RequestBody.SearchIntervalEndtimeUtc
    $SearchWindowInterval = $RequestBody.SearchIntervalInSeconds

    # Get detailed search results
    if($RequestBody.SearchResult -ne $null)
    {
        $SearchResultRows    = $RequestBody.SearchResult.tables[0].rows 
        $SearchResultColumns = $RequestBody.SearchResult.tables[0].columns;

        foreach ($SearchResultRow in $SearchResultRows)
        {   
            $Column = 0
            $Record = New-Object –TypeName PSObject 
        
            foreach ($SearchResultColumn in $SearchResultColumns)
            {
                $Name = $SearchResultColumn.name
                $ColumnValue = $SearchResultRow[$Column]
                $Record | Add-Member –MemberType NoteProperty –Name $name –Value $ColumnValue -Force
                        
                $Column++
            }

            # Include code to work with the record. 
            # For example $Record.Computer to get the computer property from the record.
            
        }
    }



## <a name="sample-payload"></a>A minta forgalma
Ez a szakasz bemutatja a webhook és runbook műveletek minta hasznos.

### <a name="webhook-actions"></a>Webhookműveletek
Ez a példa **SearchResult** Ez a tulajdonság, amely tartalmazza a szabványos tartalom webhookműveletek eredményeit.  Ha a webhook egy egyéni adattartalom, amely tartalmazza a keresési eredmények között, ez a tulajdonság lenne **SearchResults**.

Az alábbiakban látható egy minta hasznos webhook művelethez.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "workspaceID",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
                "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }


### <a name="runbooks"></a>Runbookok

Az alábbiakban látható egy minta hasznos a runbook műveletet.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "AutomationAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "861bd466-5400-44be-9552-5ba40823c3aa",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
            "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Critical"
    }



## <a name="next-steps"></a>További lépések
- A forgatókönyv a [konfigurálása egy webook](log-analytics-alerts-webhooks.md) a riasztási szabályt.  
- Ismerje meg, hogyan írhat [az Azure Automation runbookjai](https://azure.microsoft.com/documentation/services/automation) riasztások által azonosított problémák megoldásáról.
