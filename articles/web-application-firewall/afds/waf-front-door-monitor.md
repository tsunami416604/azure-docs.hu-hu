---
title: Azure webalkalmazási tűzfal figyelése és naplózása
description: A webalkalmazási tűzfal (WAF) megismerése FrontDoor-figyeléssel és-naplózással
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: b4f666415a96307b89022c6caf6af90581f294f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115363"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure webalkalmazási tűzfal figyelése és naplózása 

Az Azure webalkalmazási tűzfal (WAF) figyelése és naplózása a Azure Monitor-és Azure Monitor-naplókkal való naplózással és integrációval történik.

## <a name="azure-monitor"></a>Azure Monitor

A FrontDoor naplóval rendelkező WAF integrálva van a [Azure monitor](../../azure-monitor/overview.md). Azure Monitor lehetővé teszi a diagnosztikai információk nyomon követését, beleértve a WAF-riasztásokat és-naplókat. A WAF-figyelést a portálon belül, a **diagnosztika** lapon vagy közvetlenül a Azure monitor szolgáltatáson keresztül konfigurálhatja.

Azure Portal válassza a bejárati erőforrás típusa lehetőséget. A bal oldali **figyelési**/**metrikák** lapról hozzáadhat **WebApplicationFirewallRequestCount** a WAF-szabályoknak megfelelő kérelmek számának nyomon követéséhez. Az egyéni szűrők a műveleti típusok és a szabályok nevei alapján hozhatók létre.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Naplók és diagnosztika

A WAF az észlelt fenyegetésekkel kapcsolatos részletes jelentéseket biztosít. A naplózás integrálva van az Azure Diagnostics naplóival, a riasztások pedig JSON formátumban vannak rögzítve. Ezek a naplók [Azure monitor naplókkal](../../azure-monitor/insights/azure-networking-analytics.md)integrálhatók.

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

A FrontdoorAccessLog az ügyfélnek visszaküldött összes kérelmet naplózza. A FrontdoorWebApplicationFirewallLog minden olyan kérést naplóz, amely megfelel egy WAF-szabálynak.

A következő példában szereplő lekérdezés a WAF-naplókat szerzi be a blokkolt kérelmeknél:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Íme egy példa egy naplózott kérelemre a WAF-naplóban:

``` WAFlogQuerySample
{
    "PreciseTimeStamp": "2020-01-25T00:11:19.3866091Z",
    "time": "2020-01-25T00:11:19.3866091Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "xx.xx.xxx.xxx",
        "socketIP": "xx.xx.xxx.xxx",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/?q=../../x",
        "ruleName": "Microsoft_DefaultRuleSet-1.1-LFI-930100",
        "policy": "WafDemoCustomPolicy",
        "action": "Block",
        "host": "wafdemofrontdoorwebapp.azurefd.net",
        "refString": "0p4crXgAAAABgMq5aIpu0T6AUfCYOroltV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode": "prevention"
    }
}

``` 

A következő példában szereplő lekérdezés beolvassa a AccessLogs-bejegyzéseket:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Íme egy példa egy naplózott kérelemre a hozzáférési naplóban:

``` AccessLogSample
{
    "PreciseTimeStamp": "2020-01-25T00:11:12.0160150Z",
    "time": "2020-01-25T00:11:12.0160150Z",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0n4crXgAAAACnRKbdALbyToAqNfSHssDvV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "httpMethod": "GET",
        "httpVersion": "2.0",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/",
        "requestBytes": "710",
        "responseBytes": "3116",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4017.0 Safari/537.36 Edg/81.0.389.2",
        "clientIp": "xx.xx.xxx.xxx",
        "timeTaken": "0.598",
        "securityProtocol": "TLS 1.2",
        "routingRuleName": "WAFdemoWebAppRouting",
        "backendHostname": "wafdemouksouth.azurewebsites.net:443",
        "sentToOriginShield": false,
        "httpStatusCode": "200",
        "httpStatusDetails": "200"
    }
}

```

## <a name="next-steps"></a>További lépések

- További információ a [bejárati ajtóról](../../frontdoor/front-door-overview.md).
