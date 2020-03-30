---
title: Webhook-műveletek az Azure-riasztások naplózási riasztásaihoz
description: Ez a cikk ismerteti, hogyan hozhat létre egy naplóriasztási szabályt a Log Analytics munkaterület vagy az Application Insights használatával, hogyan küldi le a riasztás az adatokat HTTP-webhookként, és a lehetséges különböző testreszabások részleteit.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 7b1956ad2bf9bf38ba9edc4c7234078557564071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667703"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook-műveletek a naplóriasztási szabályokhoz
Amikor egy [naplóriasztást hoz létre az Azure-ban,](alerts-log.md)lehetősége van [konfigurálására műveletcsoportok használatával](action-groups.md) egy vagy több művelet végrehajtásához. Ez a cikk ismerteti a különböző webhook-műveletek, amelyek rendelkezésre állnak, és bemutatja, hogyan kell beállítani egy egyéni JSON-alapú webhook.

> [!NOTE]
> A webhook-integrációk hoz is használhatja a [közös riasztási sémát.](https://aka.ms/commonAlertSchemaDocs) A közös riasztási séma biztosítja azt az előnyt, hogy egyetlen bővíthető és egységes riasztási hasznos adat az Azure Monitor összes riasztási szolgáltatásában. Elhalasztja a közös riasztási séma hasznos adatát, ha ez van kiválasztva, függetlenül attól, hogy milyen testreszabást végezhetett a riasztási szabály szintjén. [Ismerje meg a gyakori riasztási sémadefiníciók.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhook-műveletek

A webhook-műveletekkel egyetlen HTTP POST-kérelemen keresztül meghívhat egy külső folyamatot. A hívott szolgáltatásnak támogatnia kell a webhookokat, és meg kell határoznia, hogyan használhatja a kapott hasznos adatokat.

A Webhook-műveletekhez az alábbi táblázatban szereplő tulajdonságokra van szükség.

| Tulajdonság | Leírás |
|:--- |:--- |
| **Webhook URL-címe** |A webhook URL-címe. |
| **Egyéni JSON hasznos adat** |Az egyéni hasznos küldés a webhook, ha ezt a beállítást választják a riasztás létrehozása során. További információt a [Naplóriasztások kezelése című témakörben talál.](alerts-log.md)|

> [!NOTE]
> A **View Webhook** gomb mellett az **egyéni JSON hasznos adat** a webhook beállítás a naplóriasztás megjeleníti a minta webhook hasznos adat a megadott testreszabási. Nem tartalmaz tényleges adatokat, de a naplóriasztásokhoz használt JSON-sémát reprezentálja. 

A webhookok tartalmaznak egy URL-címet és egy JSON-ban formázott hasznos adatot, amelyet a külső szolgáltatásnak küldött adatok tartalmaznak. Alapértelmezés szerint a hasznos adat az alábbi táblázatban szereplő értékeket tartalmazza. Dönthet úgy, hogy ezt a hasznos terhet egy saját egyénire cseréli le. Ebben az esetben használja a változók a táblázatban az egyes paraméterek et az egyéni hasznos adat tartalmazza az értéküket.


| Paraméter | Változó | Leírás |
|:--- |:--- |:--- |
| *AlertRuleName (Figyelmeztetési szabály neve)* |#alertrulename |A riasztási szabály neve. |
| *Súlyosság* |#severity |Az elsült naplóriasztás súlyossága beállítva. |
| *AlertThresholdOperator* |#thresholdoperator |Küszöbérték operátora a riasztási szabály, amely akkora, mint a kisebb, mint. |
| *AlertThresholdValue érték* |#thresholdvalue |A riasztási szabály küszöbértéke. |
| *LinkToSearchResults* |#linktosearchresults |Hivatkozás az Analytics-portálra, amely a riasztást létrehozó lekérdezés rekordjait adja vissza. |
| *Eredményszáma* |#searchresultcount |A keresési eredményekben lévő rekordok száma. |
| *Keresési időköz befejezési ideje* |#searchintervalendtimeutc |A lekérdezés befejezési ideje UTC-ben, mm/dd/éééé HH:mm:sS AM/PM formátummal. |
| *Keresési időköz* |#searchinterval |A figyelmeztetési szabály időablaka HH:mm:ss formátummal. |
| *Keresési időköz kezdési ideje* |#searchintervalstarttimeutc |A lekérdezés kezdési időpontja UTC-ben, mm/dd/éééé HH:mm:sS AM/PM formátummal. 
| *SearchQuery* |#searchquery |A riasztási szabály által használt keresési lekérdezés naplózása. |
| *Keresési eredmények* |"IncludeSearchResults": true|A lekérdezés által JSON-táblaként visszaadott rekordok, az első 1000 rekordra korlátozva, ha az "IncludeSearchResults": true egy egyéni JSON webhook-definícióban kerül hozzáadásra legfelső szintű tulajdonságként. |
| *Riasztás típusa*| #alerttype | A [metrikus mérésként](alerts-unified-log.md#metric-measurement-alert-rules) vagy [az eredmények számaként](alerts-unified-log.md#number-of-results-alert-rules)konfigurált naplóriasztási szabály típusa.|
| *Munkaterületazonosító* |#workspaceid |A Log Analytics-munkaterület azonosítója. |
| *Alkalmazásazonosító* |#applicationid |Az Application Insights-alkalmazás azonosítója. |
| *Előfizetés azonosítója* |#subscriptionid |A használt Azure-előfizetés azonosítója. 

> [!NOTE]
> *A LinkToSearchResults* olyan paramétereket ad át, mint a *SearchQuery*, *a Search Interval StartTime*és a *Search Interval End idő* az Azure Portal URL-címében az Analytics szakaszban való megtekintéshez. Az Azure Portal uri-mérethatára körülbelül 2000 karakter. A portál *nem* nyitja meg a riasztásokban megadott hivatkozásokat, ha a paraméterértékek meghaladják a korlátot. Az eredmények megtekintéséhez manuálisan is beviheti az eredményeket az Analytics-portálon. Vagy használhatja az [Application Insights Analytics REST API-t](https://dev.applicationinsights.io/documentation/Using-the-API) vagy a Log Analytics REST [API-t](/rest/api/loganalytics/) az eredmények programozott lekéréséhez. 

Megadhatja például a következő egyéni hasznos tartalmat, amely egyetlen *szöveg*nevű paramétert tartalmaz. A webhook által meghívni kívánt szolgáltatás ezt a paramétert várja.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Ez a példa a hasznos teher a következőhöz hasonló módon oldható meg, amikor a webhook-ra küldi:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Mivel az egyéni webhook összes változóját meg kell adni egy JSON-házon belül, például a "#searchinterval", az eredő webhook változó adatokkal is rendelkezik a házakon belül, például "00:05:00".

Ha a keresési eredményeket egyéni hasznos adatba szeretné foglalni, győződjön meg arról, hogy az **IncludeSearchResults** a JSON-tartalom legfelső szintű tulajdonságaként van beállítva. 

## <a name="sample-payloads"></a>Minta rakományok
Ez a szakasz a naplóriasztások webhookjainak mintahasznos terhelését jeleníti meg. A minta hasznos adatok példákat tartalmaznak, amikor a hasznos teher szabványos, és ha az egyéni.

### <a name="standard-webhook-for-log-alerts"></a>Szabványos webhook a naplóriasztásokhoz 
Mindkét példa rendelkezik egy dummy hasznos adat csak két oszlop és két sor.

#### <a name="log-alert-for-log-analytics"></a>Naplóriasztás a Log Analytics szolgáltatáshoz
A következő minta hasznos adat egy szabványos webhook-művelethez, amely a Log Analytics szolgáltatáson alapuló riasztásokhoz használható *egyéni JSON-beállítás nélkül* érhető el:

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
> A "Súlyosság" mező értéke változhat, ha a Log Analytics naplóriasztásokra [vonatkozó API-preferenciáját módosította.](alerts-log-api-switch.md)


#### <a name="log-alert-for-application-insights"></a>Naplózási riasztás az Application Insights számára
A következő minta hasznos adat egy szabványos webhook *egyéni JSON-beállítás nélkül,* ha az Application Insights alapján naplóriasztásokhoz használja:
    
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

#### <a name="log-alert-with-custom-json-payload"></a>Naplóriasztás egyéni JSON-tartalommal
Ha például olyan egyéni hasznos tartalmat szeretne létrehozni, amely csak a riasztás nevét és a keresési eredményeket tartalmazza, a következőket használhatja: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

A következő minta hasznos adat egy egyéni webhook-művelet hez minden naplóriasztáshoz:
    
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
- Ismerje meg [a naplóriasztásokat az Azure-riasztásokban.](alerts-unified-log.md)
- Ismerje meg, hogyan kezelheti a [naplóriasztásokat az Azure-ban.](alerts-log.md)
- Műveletcsoportok létrehozása és kezelése [az Azure-ban.](action-groups.md)
- További információ az [Application Insights](../../azure-monitor/app/analytics.md)ról.
- További információ a [naplólekérdezésekről.](../log-query/log-query-overview.md) 

