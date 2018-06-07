---
title: A napló riasztásokhoz Azure riasztások webhookműveletek |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan napló riasztási szabály a napló analytics vagy az application insights segítségével fogja leküldeni a adatok HTTP webhook és a különböző testreszabásokat részleteit lehetséges.
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
ms.openlocfilehash: 981b6b65675550fd1403064ad3113c2dca0c3f6e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638670"
---
# <a name="webhook-actions-for-log-alert-rules"></a>A napló riasztási szabályok webhookműveletek
Ha egy [riasztást hoz létre az Azure-ban ](monitor-alerts-unified-usage.md), lehetősége van a [konfigurálása művelet csoportok használatával](monitoring-action-groups.md) egy vagy több műveletek elvégzéséhez.  Ez a cikk ismerteti a rendelkezésre álló különböző webhookműveletek és a részletek a egyéni JSON-alapú webhook konfigurálásával.


## <a name="webhook-actions"></a>Webhookműveletek

Webhookműveletek lehetővé teszi egy külső folyamatban egy HTTP POST kérelemben keresztül.  A meghívott szolgáltatás kell webhookok támogatja, és határozza meg, hogyan használja a tartalom kap.   Példák a webhook riasztást válaszul egy üzenetet küldi [Slackhez](http://slack.com) , vagy hozzon létre egy incidenst a [PagerDuty](http://pagerduty.com/).  

Webhookműveletek megkövetelése a tulajdonságok a következő táblázatban:

| Tulajdonság | Leírás |
|:--- |:--- |
| Webhook URL-címe |A webhook URL-CÍMÉT. |
| Egyéni JSON-adattartalmat |Riasztás létrehozása során ezt a lehetőséget választja, a webhook küldött egyéni hasznos. Rendelkezésre álló részletek [használata Azure riasztások-riasztások kezelése ](monitor-alerts-unified-usage.md) |

> [!NOTE]
> Tesztelje a Webhook gomb mellett *Include egyéni JSON-adattartalmat webhook* napló riasztási választás, akkor indul el, üres hívása teszteli a webhook URL-CÍMÉT. Tényleges adatokat és a jellemző a napló riasztások használt JSON-séma nem tartalmaz. 

Webhook URL-címet és a hasznos adatok között, amely a külső szolgáltatásnak továbbított adatok JSON formátumú, tartalmazza.  Alapértelmezés szerint a tartalom magában foglalja az értékeket a következő táblázatban: esetén dönthet úgy, hogy ezek a hasznos adatok kicseréli a saját egyéni egy.  Ebben az esetben használhatja a változók a táblázatban az egyes paraméterek egyéni adattartalmat értékük felvenni.


| Paraméter | Változó | Leírás |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |A riasztási szabály neve. |
| Severity |#severity |Állítsa be a égetett napló riasztás súlyossága. |
| AlertThresholdOperator |#thresholdoperator |A riasztási szabály operátor küszöbértéket.  *Nagyobb, mint* vagy *kisebb, mint*. |
| AlertThresholdValue |#thresholdvalue |A riasztási szabály tartozó küszöbérték. |
| LinkToSearchResults |#linktosearchresults |Csatolja a rekordot ad vissza a lekérdezést, amely a riasztás létrehozása Analytics-portálról. |
| Attribútumhoz resultcount számlálót. |#searchresultcount |A keresési eredmények rekordok száma. |
| Keresési intervallum befejezési időpontja |#searchintervalendtimeutc |A Befejezés időpontja UTC szerint, a lekérdezés format - hh/nn/éééé óó: pp: ss du. |
| Keresési intervallum |#searchinterval |A riasztás időkerete szabály, - formátum ÓÓ: pp:. |
| Keresési intervallum kezdő időpont |#searchintervalstarttimeutc |Kezdő időpontja a lekérdezést UTC formátumban, format - hh/nn/éééé óó: pp: ss du.... 
| SearchQuery |#searchquery |Naplófájl-keresési lekérdezés a riasztási szabály által használt. |
| SearchResults |"IncludeSearchResults": igaz|Táblaként JSON, csak az első 1000 rekord; a lekérdezés által visszaadott rekordok Ha "IncludeSearchResults": true egyéni JSON webhook definition legfelső szintű tulajdonságként fel van véve. |
| WorkspaceID |#workspaceid |A Naplóelemzési munkaterület azonosítója. |
| Alkalmazásazonosító |#applicationid |Az Application Insights azonosítója alkalmazást. |
| Előfizetés azonosítója |#subscriptionid |Az Application insights szolgáltatással használt Azure-előfizetése Azonosítóját. 


Például megadhatja a következő egyéni payload nevű egyetlen paramétert tartalmazó *szöveg*.  A szolgáltatás, amely behívja a webhook a ennek a paraméternek, akkor rendszer.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Ez a példa hasznos volna oldja fel a következőhöz, ha a webhook.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Mivel minden változóját egy egyéni webhook JSON ház, például a "#searchinterval" értéket, a eredő webhook is változó adatok belül ház például "00: 05:00".

Keresési eredmények belefoglalása az egyéni adattartalom, ügyeljen arra, hogy **IncudeSearchResults** a json-adattartalmat legfelső szintű tulajdonság be van állítva. 

## <a name="sample-payloads"></a>A minta hasznos adat található
Ez a szakasz bemutatja a webhook minta hasznos napló riasztások, beleértve a szabványos hasznos esetén, és ha az egyéni.

> [!NOTE]
> Előző verziókkal való kompatibilitás érdekében Azure Naplóelemzés riasztásokról szabványos webhook hasznos legyen, mint [Naplóelemzési kezelési riasztási](../log-analytics/log-analytics-alerts-creating.md). De napló riasztásokhoz [Application Insights](../application-insights/app-insights-analytics.md), a standard webhook forgalma művelet csoport sémán alapul.

### <a name="standard-webhook-for-log-alerts"></a>Standard Webhook napló riasztások 
Mindkét ezekben a példákban csak két oszlopok és sorok két üres adattartalmat jeleztük.

#### <a name="log-alert-for-azure-log-analytics"></a>Az Azure Naplóelemzés napló riasztás
Az alábbiakban látható egy minta hasznos a szabványos webhook művelet *egyéni Json-beállítás nélkül* napló analytics-alapú értesítések használja.

```json
{
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
 }
 ```   

#### <a name="log-alert-for-azure-application-insights"></a>Napló figyelmeztetés a következő Azure Application insights szolgáltatással
Az alábbiakban látható egy minta hasznos szabványos webhook *egyéni Json-beállítás nélkül* a application insights-alapú napló-riasztások használatakor.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Error",
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Egyéni JSON-adattartalmat napló riasztás
Például egy egyéni adattartalom, amely tartalmazza a riasztás neve és a keresési eredmények létrehozásához használhatja a következő: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Az alábbiakban látható egy minta hasznos bármely napló riasztás egyéni webhook művelethez.
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>További lépések
- További tudnivalók [riasztások jelentkezzen be Azure riasztások ](monitor-alerts-unified-log.md)
- Létrehozásához és kezeléséhez [művelet csoportok az Azure-ban](monitoring-action-groups.md)
- További információ [Application insights szolgáltatással](../application-insights/app-insights-analytics.md)
- További információ [Naplóelemzési](../log-analytics/log-analytics-overview.md). 