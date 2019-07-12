---
title: Az Azure alerts szolgáltatásban naplóriasztásokra vonatkozó Webhook-műveletek
description: Ez a cikk bemutatja, hogyan hozhat létre egy riasztási szabály a Log Analytics-munkaterületet vagy az Application Insights használatával hogyan a riasztás adatokat leküldi egy HTTP-webhook, valamint a különböző testreszabási beállításokat, amelyek lehetséges részleteit.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 6aa007c621e76cb0c188a7dab6279fd9e387b2b3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705185"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook-műveletek a riasztási szabály
Ha egy [riasztás jön létre az Azure-ban](alerts-log.md), lehetősége van a [beállításával a Műveletcsoportok használatával](action-groups.md) egy vagy több művelet végrehajtásához. Ez a cikk ismerteti a rendelkezésre álló különböző webhook-műveletek, és bemutatja egy egyéni JSON-alapú webhook konfigurálásához.

> [!NOTE]
> Használhatja még a [gyakori riasztási séma](https://aka.ms/commonAlertSchemaDocs) a webhook Integrációk. A gyakori riasztási séma lehetővé teszi az előnye, hogy egyetlen bővíthető és egységes riasztási hasznos az Azure Monitor riasztási szolgáltatásban. [Ismerje meg a gyakori riasztási sémadefiníciók.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhook-műveletek

Webhook-műveletek külső folyamat egyetlen HTTP POST-kérés hívhatók meg. A szolgáltatás, amelynek a neve kell webhookok támogatása, és határozza meg, hogyan használni minden olyan hasznos kap.

Webhook-műveletek az alábbi táblázatban szereplő tulajdonságok van szükség.

| Tulajdonság | Leírás |
|:--- |:--- |
| **Webhook URL-címe** |A webhook URL-címe |
| **Egyéni JSON-adattartalmat** |A webhook küldése, ha a riasztás létrehozásakor ezt a lehetőséget választja, az egyéni adattartalom. További információkért lásd: [kezelés naplóriasztások](alerts-log.md).|

> [!NOTE]
> A **nézet Webhook** gomb problémamentesen a **Belefoglalás egyéni JSON-adattartalom-webhook** a riasztás a testreszabás, a megadott minta-webhook hasznos adatai megjelenítésére vonatkozó beállítást. Azt azonban nem tartalmazza a tényleges adatok képviselője naplóriasztások használt JSON-sémája. 

Webhookok tartalmaznak egy URL-CÍMÉT és a egy hasznos, amelyek a külső szolgáltatásnak küldött adatok JSON-formátumú. Alapértelmezés szerint a hasznos adatokat az alábbi táblázatban az értékeket tartalmazza. Ha szeretné, cserélje le a saját egyéni egy hasznos adatot. Ebben az esetben használhatnak a változókat a táblázatban az egyes paraméterek azok értékeit az egyéni hasznos adat található.


| Paraméter | Változó | Leírás |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |A riasztási szabály neve. |
| *Súlyosság* |#severity |Állítsa be az aktivált riasztás súlyossága. |
| *AlertThresholdOperator* |#thresholdoperator |Küszöboperátor a riasztási szabály, amely nagyobb vagy kisebb használja, mint. |
| *AlertThresholdValue* |#thresholdvalue |A riasztási szabályhoz tartozó küszöbérték. |
| *LinkToSearchResults* |#linktosearchresults |Hivatkozás az analitika portálra, amelyek a rekordokat adja vissza a lekérdezést, amely létrehozta a riasztást. |
| *ResultCount* |#searchresultcount |A keresési eredmények rekordok száma. |
| *Keresési intervallum befejezési időpontja* |#searchintervalendtimeutc |Befejezési idő (UTC), a lekérdezés és a formátuma HH/nn/éééé óó: pp: ss de. / du. |
| *Keresési intervallum* |#searchinterval |A riasztási szabályt, és a formátum ÓÓ időtartományából. |
| *Keresési intervallum kezdő időpont* |#searchintervalstarttimeutc |Kezdési idő a lekérdezés (UTC), és a formátuma HH/nn/éééé óó: pp: ss de. / du. 
| *SearchQuery* |#searchquery |Naplóbeli keresési lekérdezés a riasztási szabály által használt. |
| *SearchResults* |"IncludeSearchResults": true|Ha a JSON táblázat, csak az első 1000 rekord, a lekérdezés által visszaadott rekordok "IncludeSearchResults": true adnak hozzá egy egyéni JSON webhook definíciója adható meg a legfelső szintű tulajdonsággal a. |
| *Riasztás típusa*| #alerttype | Riasztási szabály konfigurálva típusú [metrikamérési](alerts-unified-log.md#metric-measurement-alert-rules) vagy [eredmények száma](alerts-unified-log.md#number-of-results-alert-rules).|
| *WorkspaceID* |#workspaceid |A Log Analytics-munkaterület azonosítója. |
| *Alkalmazás azonosítója* |#applicationid |Az Application Insights azonosítója alkalmazást. |
| *előfizetés-azonosító* |#subscriptionid |A használt Azure-előfizetés azonosítója. 

> [!NOTE]
> *LinkToSearchResults* adja át a paraméterek *SearchQuery*, *keresési intervallum StartTime*, és *keresési intervallum befejezésének* az URL-cím az Azure-ban portál és az Analytics szakaszban megtekinthető. Az Azure Portalon méretkorlátja URI megközelítőleg 2000 karakternél. A portál fog *nem* nyissa meg a riasztások hivatkozásokra, ha a paraméterértékek meghaladja a korlátot. Manuálisan megadhatja a részletes eredmények megtekintése az Analytics-portálon. Vagy használhatja a [Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) vagy a [Log Analytics REST API](/rest/api/loganalytics/) eredmények programozott módon lekéréséhez. 

Például megadhatja a következő nevű egyetlen paramétert tartalmazó egyéni adattartalom *szöveg*. A szolgáltatás, amely meghívja ezt a webhookot ezt a paramétert vár.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Ebben a példában hasznos értékről az alábbihoz hasonlóra oldja fel a webhook történő küldésekor:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Mivel az egyéni webhook minden változót kell megadni egy JSON-lemezház, például a "#searchinterval," eredő webhook is rendelkezik változó adatok belül ház, például "00: 05:00."

Keresési eredmények belefoglalása az egyéni adattartalom, ellenőrizze, hogy **IncludeSearchResults** a JSON-adattartalom legfelső szintű tulajdonság be van állítva. 

## <a name="sample-payloads"></a>Minta is észleltünk adattartalmakat.
Ez a szakasz bemutatja a naplóriasztásokra vonatkozó webhook minta is észleltünk adattartalmakat. A minta hasznos példákat is tartalmaznak, amikor a hasznos a standard és egyéni.

### <a name="standard-webhook-for-log-alerts"></a>Standard szintű, naplóriasztásokra vonatkozó webhook 
Mindkét példa kell, hogy egy helyőrző adattartalom csak két oszlop, és két sort.

#### <a name="log-alert-for-log-analytics"></a>A Log Analytics riasztás
A következő hasznosadat-minta egy szabványos webhook művelet van *egy egyéni JSON-beállítás nélkül* használt riasztások a Log Analyticsre épül:

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
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
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> Változhat, hogy a "Súlyossági" mező értékét, ha [váltott, az API szabályozó](alerts-log-api-switch.md) riasztások a Log Analytics számára.


#### <a name="log-alert-for-application-insights"></a>Riasztás az Application Insights
A következő hasznosadat-minta egy szabványos webhook van *egy egyéni JSON-beállítás nélkül* amikor naplóriasztások használható az Application Insights alapján:
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
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
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Egyéni JSON-adattartalom-riasztás
Például hozzon létre egy egyéni hasznos adat, amely tartalmazza a riasztás neve és a keresési eredmények között, használhatja a következőket: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

A következő hasznosadat-minta bármely riasztás egyéni webhook művelet van:
    
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
- Ismerje meg [naplóriasztások az Azure-riasztások](alerts-unified-log.md).
- Megismerheti, hogyan [kezelése az Azure-ban riasztások](alerts-log.md).
- Létrehozásához és kezeléséhez [Műveletcsoportok az Azure-ban](action-groups.md).
- Tudjon meg többet [Application Insights](../../azure-monitor/app/analytics.md).
- Tudjon meg többet [lekérdezések naplózását](../log-query/log-query-overview.md). 

