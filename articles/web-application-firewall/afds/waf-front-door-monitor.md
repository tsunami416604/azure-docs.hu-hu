---
title: Az Azure Web Application Firewall figyelése és naplózása
description: Ismerje meg a Web Application Firewall (WAF) frontajtó figyelésése és naplózása
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 4488fadf5db3b32049b5dce4bbee1fa76c320e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284143"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Az Azure Web Application Firewall figyelése és naplózása 

Az Azure Web Application Firewall (WAF) figyelése és naplózása az Azure Monitor és az Azure Monitor naplóival való naplózás és integráció révén biztosított.

## <a name="azure-monitor"></a>Azure Monitor

A WAF frontajtónaplóval integrálva van az [Azure Monitorral.](../../azure-monitor/overview.md) Az Azure Monitor lehetővé teszi a diagnosztikai információk, beleértve a WAF-riasztások és naplók nyomon követését. Konfigurálhatja a WAF-figyelést a bejárati ajtó erőforráson belül a portálon a **Diagnosztika** lapon vagy közvetlenül az Azure Monitor szolgáltatáson keresztül.

Az Azure Portalon nyissa meg a Bejárati ajtó erőforrástípusát. A bal oldali/**Monitormetrikák** lapon hozzáadhatja a **WebApplicationFirewallRequestCount** alkalmazást a WAF-szabályoknak megfelelő kérelmek számának nyomon követéséhez. **Monitoring** Egyéni szűrők hozhatók létre művelettípusok és szabálynevek alapján.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Naplók és diagnosztika

A WAF a Bejárati ajtóval részletes jelentést nyújt az észlelt minden egyes fenyegetésről. A naplózás integrálva van az Azure Diagnostics naplóival, a riasztások pedig JSON formátumban vannak rögzítve. Ezek a naplók integrálhatók az [Azure Monitor naplóiba.](../../azure-monitor/insights/azure-networking-analytics.md)

![WAFDiag között](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

A FrontdoorAccessLog naplózza az ügyfél háttérrendszerébe továbbított összes kérelmet. A FrontdoorWebApplicationFirewallLog naplózza a WAF-szabálynak megfelelő kérelmeket.

A következő példalekérdezés WAF-naplókat kér be a blokkolt kérelmeken:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_name_s == "Block"

```

Íme egy példa a WAF-naplóban naplózott kérelemre:

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

A következő példalekérdezés AccessLogs bejegyzéseket kap:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Íme egy példa az Access-naplóban naplózott kérelemre:

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

- További információ a [Bejárati ajtóról.](../../frontdoor/front-door-overview.md)
