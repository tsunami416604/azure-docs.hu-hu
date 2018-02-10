---
title: "A napló riasztásokhoz (előzetes verzió) Azure riasztások webhookműveletek |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan napló analytics vagy az application insights használó napló riasztási szabály, küldi az adatokat HTTP webhook és a különböző testreszabásokat részleteit lehetséges."
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 49905638-f9f2-427b-8489-a0bcc7d8b9fe
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: vinagara
ms.openlocfilehash: 4af1bb61888810011ce64fde7931cabfefe76ab6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="webhook-actions-for-log-alert-rules"></a>A napló riasztási szabályok webhookműveletek
Ha egy [riasztást hoz létre az Azure (előzetes verzió)](monitor-alerts-unified-usage.md), lehetősége van a [konfigurálása művelet csoportok használatával](monitoring-action-groups.md) egy vagy több műveletek elvégzéséhez.  Ez a cikk ismerteti a rendelkezésre álló különböző webhookműveletek és a részletek a egyéni JSON-alapú webhook konfigurálásával.


## <a name="webhook-actions"></a>Webhookműveletek

Webhookműveletek lehetővé teszi egy külső folyamatban egy HTTP POST kérelemben keresztül.  A meghívott szolgáltatás kell webhookok támogatja, és határozza meg, hogyan fogja használni a tartalom kap.  Sikerült is meghívhatja a REST API-t nem támogató kifejezetten webhookokkal, amíg a kérelmet, amely együttműködik a API formátumban.  Példák a webhook riasztást válaszul egy üzenetet küldi [Slackhez](http://slack.com) , vagy hozzon létre egy incidenst a [PagerDuty](http://pagerduty.com/).  Riasztási szabály létrehozása a Slackhez hívása olyan webhook részletes útmutatást érhető el: [Naplóelemzési riasztásokban Webhookok](../log-analytics/log-analytics-alerts-webhooks.md).

Webhookműveletek igényelnek a tulajdonságok az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| Webhook URL-CÍMÉT |A webhook URL-CÍMÉT. |
| Egyéni JSON-adattartalmat |A webhook küldendő egyéni hasznos.  Ha a riasztás létrehozásakor lehetőséget választja. Rendelkezésre álló részletek [Azure figyelmeztetések (előzetes verzió) használatával riasztások kezelése](monitor-alerts-unified-usage.md) |

> [!NOTE]
> Tesztelje a Webhook gomb mellett *Include egyéni JSON-adattartalmat webhook* napló riasztási választás, akkor indul el, üres hívása teszteli a webhook URL-CÍMÉT. Azoknak a Riasztásoknak napló JSON-séma képviselője vagy az adat nem tartalmaz. Amíg bármely reprezentatív egyéni JSON egy webhook teszteléséhez művelet csoportban konfigurált összes webhookok az egyéni JSON-adattartalmat kapnak.

Webhook URL-címet és a hasznos adatok között, amely a külső szolgáltatásnak továbbított adatok JSON formátumú, tartalmazza.  Alapértelmezés szerint a tartalom a következő táblázat tartalmazza az értékeket.  Ha szeretné, ezek a hasznos adatok kicseréli a saját egyéni egy.  Ebben az esetben használhatja a változók a táblázatban az egyes paraméterek egyéni adattartalmat értékük felvenni.


| Paraméter | Változó | Leírás |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |A riasztási szabály neve. |
| AlertThresholdOperator |#thresholdoperator |A riasztási szabály operátor küszöbértéket.  *Nagyobb, mint* vagy *kisebb, mint*. |
| AlertThresholdValue |#thresholdvalue |A riasztási szabály tartozó küszöbérték. |
| LinkToSearchResults |#linktosearchresults |A lekérdezésből, amely a riasztás létrehozása a rekordok visszaadó Naplóelemzési napló keresési kapcsolódik. |
| ResultCount |#searchresultcount |A keresési eredmények rekordok száma. |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |Befejezési ideje UTC formátumban a lekérdezést. |
| SearchIntervalInSeconds |#searchinterval |A riasztási szabály időszak. |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |Indítsa el a lekérdezések ideje UTC formátumban. |
| SearchQuery |#searchquery |Naplófájl-keresési lekérdezés a riasztási szabály által használt. |
| SearchResults |"IncludeSearchResults":true|Táblaként JSON, csak az első 1000 rekord/sorok; a lekérdezés által visszaadott rekordok Ha "IncludeSearchResults": true egyéni JSON webhook definition legfelső szintű tulajdonságként fel van véve |
| WorkspaceID |#workspaceid |A Naplóelemzési munkaterület azonosítója. |
| Súlyosság |#severity |Állítsa be a égetett napló riasztás súlyossága. |

Például megadhatja a következő egyéni payload nevű egyetlen paramétert tartalmazó *szöveg*.  A szolgáltatás, amely behívja a webhook a ennek a paraméternek, akkor rendszer.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Ez a példa hasznos volna oldja fel a következőhöz, ha a webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Keresési eredmények belefoglalása az egyéni adattartalom, ügyeljen arra, hogy **IncudeSearchResults** a json-adattartalmat legfelső szintű tulajdonság be van állítva.


Riasztási szabály létrehozása a webhook: egy külső szolgáltatás indítására átfogó példát keresztül végigvezetheti [riasztási webhook művelet létrehozása a Log Analyticshez üzenetet küldeni a Slackhez](../log-analytics/log-analytics-alerts-webhooks.md).

## <a name="sample-payload"></a>A minta forgalma
Ez a szakasz bemutatja a webhook minta hasznos napló riasztások, beleértve a szabványos hasznos esetén, és ha az egyéni.

> [!NOTE]
> Előző verziókkal való kompatibilitás érdekében Azure Naplóelemzés riasztásokról szabványos webhook hasznos legyen, mint [OMS kezelési riasztási](../log-analytics/log-analytics-solution-alert-management.md). De napló riasztásokhoz [Application Insights](../application-insights/app-insights-analytics.md), a standard webhook forgalma művelet csoport séma alapján

### <a name="standard-webhook-for-log-alerts"></a>Standard Webhook napló riasztások
Ezek a példák azt a típusú hasznos adatok között csak két oszlopok és sorok két jeleztük mindegyikét.

#### <a name="log-alert-for-azure-log-analytics"></a>Az Azure Naplóelemzés napló riasztás
Az alábbiakban látható egy minta hasznos nélkül egyéni Json napló analytics napló-értesítések a használatakor a szabványos webhook művelet.

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
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }



#### <a name="log-alert-for-azure-application-insights"></a>Napló figyelmeztetés a következő Azure Application insights szolgáltatással
Az alábbiakban látható egy minta hasznos nélkül egyéni Json application insights-alapú napló-riasztások a használatakor a szabványos webhook művelet.


    {
    "schemaId":"LogAlert","data":
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
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }
    }


#### <a name="log-alert-with-custom-json-payload"></a>Egyéni JSON-adattartalmat napló riasztás
Például egy egyéni adattartalom, amely tartalmazza a riasztás neve és a keresési eredmények létrehozásához használhatja a következő.

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }

Az alábbiakban látható egy minta hasznos bármely napló riasztás egyéni webhook művelethez.


    {
    "AlertRuleName":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
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




## <a name="next-steps"></a>További lépések
- Létrehozásához és kezeléséhez [művelet csoportok az Azure-ban](monitoring-action-groups.md)
- További tudnivalók [napló riasztásait az Azure-riasztások (előzetes verzió)](monitor-alerts-unified-log.md)
