---
title: A log-riasztások webhook-műveletei az Azure-riasztásokban
description: Ez a cikk azt ismerteti, hogyan lehet napló-riasztási szabályt létrehozni a Log Analytics munkaterületen, vagy Application Insights, hogy a riasztás hogyan küldjön le adatokat HTTP-webhookként, valamint a különböző testreszabási lehetőségek részleteit.
author: yanivlavi
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 3e29bdf41b0421aa4461b11fbf9bc0535179486d
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677767"
---
# <a name="webhook-actions-for-log-alert-rules"></a>A napló riasztási szabályaihoz kapcsolódó webhook-műveletek
Ha egy [naplóbeli riasztást hoz létre az Azure-ban](alerts-log.md), akkor beállíthatja, hogy a [műveleti csoportok használatával](action-groups.md) egy vagy több műveletet végezzen. Ez a cikk az elérhető különböző webhook-műveleteket ismerteti, és bemutatja, hogyan konfigurálhat egyéni JSON-alapú webhookot.

> [!NOTE]
> Használhatja a [gyakori riasztási sémát](https://aka.ms/commonAlertSchemaDocs) is a webhook-integrációhoz. Az általános riasztási séma előnye, hogy egyetlen bővíthető és egységesített riasztási adattartalommal rendelkezik a Azure Monitor összes riasztási szolgáltatásában. [Ismerje meg a riasztási séma általános definícióit.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhook-műveletek

A webhook-műveletekkel egyetlen HTTP POST-kérelemmel hívhat meg egy külső folyamatot. A hívott szolgáltatásnak támogatnia kell a webhookokat, és meg kell határoznia, hogyan használja a kapott adattartalmat.

A webhook-műveletekhez a következő táblázatban szereplő tulajdonságok szükségesek.

| Tulajdonság | Leírás |
|:--- |:--- |
| **Webhook URL-címe** |A webhook URL-címe. |
| **Egyéni JSON-adattartalom** |A webhooktal küldendő egyéni adattartalom, ha ezt a beállítást a riasztás létrehozásakor választja ki a rendszer. További információ: a [naplózási riasztások kezelése](alerts-log.md).|

> [!NOTE]
> A log-riasztáshoz tartozó webhookhoz tartozó **Egyéni JSON** -adattartalom belefoglalása a webhookhoz lehetőség mellett megjelenik a minta webhook adattartalma a megadott testreszabáshoz. Nem tartalmaz tényleges adatokat, de a naplózási riasztásokhoz használt JSON-sémára jellemző. 

A webhookok egy URL-címet és egy JSON-ban formázott hasznos adatot tartalmaznak, amelyet a külső szolgáltatásnak küldenek. Alapértelmezés szerint a hasznos adatok tartalmazzák az alábbi táblázatban szereplő értékeket. Dönthet úgy, hogy lecseréli ezt a hasznos adatot egy saját egyéni felhasználóval. Ebben az esetben használja az egyes paraméterekhez tartozó táblázatban szereplő változókat, hogy az egyéni adattartalomban szereplő értékeket is tartalmazzák.


| Paraméter | Változó | Leírás |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |A riasztási szabály neve. |
| *Súlyosság* |#severity |A kilőtt napló riasztásának súlyossági értéke. |
| *AlertThresholdOperator* |#thresholdoperator |A riasztási szabály küszöbértékének operátora, amely nagyobb vagy kisebb, mint. |
| *AlertThresholdValue* |#thresholdvalue |A riasztási szabály küszöbértéke. |
| *LinkToSearchResults* |#linktosearchresults |Az Analytics-portálra mutató hivatkozás, amely a riasztást létrehozó lekérdezés rekordjait adja vissza. |
| *ResultCount* |#searchresultcount |A keresési eredményekben szereplő rekordok száma. |
| *Keresési időköz befejezési időpontja* |#searchintervalendtimeutc |A lekérdezés befejezési időpontja UTC szerint, hh/nn/éééé óó: PP: SS AM/du formátumban. |
| *Keresési időköz* |#searchinterval |A riasztási szabály időablaka, a HH: PP: mm formátumban. |
| *Keresési időköz kezdő időpont* |#searchintervalstarttimeutc |A lekérdezés kezdési időpontja UTC formátumban, hh/nn/éééé óó: PP: SS AM/du formátumban. 
| *SearchQuery* |#searchquery |A riasztási szabály által használt napló keresési lekérdezése. |
| *SearchResults* |"IncludeSearchResults": igaz|A lekérdezés által visszaadott, JSON-táblázatként visszaadott rekordok, amelyek az első 1 000-rekordra korlátozódnak, ha a "IncludeSearchResults": true (igaz) értéket adja hozzá egy egyéni JSON webhook-definícióban legfelső szintű tulajdonságként. |
| *Riasztás típusa*| #alerttype | A [metrika mértékének](alerts-unified-log.md#metric-measurement-alert-rules) vagy az [eredmények számának](alerts-unified-log.md#number-of-results-alert-rules)megfelelően konfigurált log riasztási szabály típusa.|
| *WorkspaceID* |#workspaceid |A Log Analytics munkaterület azonosítója. |
| *Alkalmazás azonosítója* |#applicationid |A Application Insights alkalmazás azonosítója. |
| *Előfizetés azonosítója* |#subscriptionid |A használt Azure-előfizetés azonosítója. 

> [!NOTE]
> A *LinkToSearchResults* átadja a paramétereket, például a *SearchQuery*, a *keresési időközt*és a *keresési intervallum befejezési idejét* a Azure Portal URL-címében az elemzési szakaszban való megtekintéshez. A Azure Portal egy körülbelül 2 000 karakterből álló URI-mérethatárt tartalmaz. A portál *nem* nyitja meg a riasztásokban megadott hivatkozásokat, ha a paraméterek értéke túllépi a korlátot. Manuálisan is beírhatja a részleteket az eredmények megtekintéséhez az elemzési portálon. A [Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) vagy a [log Analytics REST API](/rest/api/loganalytics/) használatával programozott módon is beolvashatja az eredményeket. 

Például megadhatja a következő egyéni adattartalmat, amely egyetlen, *szöveg*nevű paramétert tartalmaz. A szolgáltatás, amelyhez ez a webhook meghívja ezt a paramétert várja.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Ez a példa a következőhöz hasonló adatokat oldja fel a webhooknak való elküldésekor:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Mivel az egyéni webhookok összes változóját meg kell adni egy JSON-házban, például a "#searchinterval", az eredményül kapott webhook a különböző házakon belüli változó adatkereteket is tartalmaz, például "00:05:00".

A keresési eredmények egyéni adattartalomba való felvételéhez győződjön meg arról, hogy a **IncludeSearchResults** a JSON-adattartalomban legfelső szintű tulajdonságként van beállítva. 

## <a name="sample-payloads"></a>Minta hasznos adatok
Ez a szakasz a naplókhoz tartozó webhookok mintavételi hasznos adatait mutatja be. A minta hasznos adatokat tartalmaz példákat, ha a hasznos adatok standard és egyéni.

### <a name="standard-webhook-for-log-alerts"></a>Szabványos webhook a naplózási riasztásokhoz 
Mindkét példa egy olyan dummy-adattartalommal rendelkezik, amely csak két oszloppal és két sorral rendelkezik.

#### <a name="log-alert-for-log-analytics"></a>Log Analytics naplózási riasztása
A következő minta-adattartalom szabványos webhook-művelethez használható *Egyéni JSON-beállítás nélkül* , amelyet a rendszer a log Analyticson alapuló riasztásokhoz használ:

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
> A "súlyosság" mező értéke változhat, ha átváltotta az [API](alerts-log-api-switch.md) -t a log Analytics naplózási értesítéseire.


#### <a name="log-alert-for-application-insights"></a>Application Insights naplózási riasztása
A következő minta-adattartalom egy *Egyéni JSON-beállítás nélküli* szabványos webhookhoz használható, ha Application Insights alapján naplózási riasztásokat használ:
    
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

#### <a name="log-alert-with-custom-json-payload"></a>Riasztás egyéni JSON-adattartalommal
Ha például olyan egyéni adattartalmat szeretne létrehozni, amely csak a riasztás nevét és a keresési eredményeket tartalmazza, a következőt használhatja: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

A következő minta hasznos adatokat tartalmaz egy egyéni webhook-művelethez a naplózási riasztásokhoz:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
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
- Tudnivalók a [riasztásokról az Azure-riasztásokban](alerts-unified-log.md).
- Ismerje meg, hogyan kezelheti a [naplózási riasztásokat az Azure-ban](alerts-log.md).
- Műveleti csoportok létrehozása és kezelése [Az Azure-ban](action-groups.md).
- További információ a [Application Insightsról](../../azure-monitor/app/analytics.md).
- További információ a [naplók lekérdezéséről](../log-query/log-query-overview.md). 

