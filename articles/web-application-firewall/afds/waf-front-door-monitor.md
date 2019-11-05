---
title: Azure webalkalmazási tűzfal figyelése és naplózása
description: A webalkalmazási tűzfal (WAF) megismerése FrontDoor-figyeléssel és-naplózással
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 0c705139c082f13f40362e598f0fda9ba0a128a5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512482"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure webalkalmazási tűzfal figyelése és naplózása 

Az Azure webalkalmazási tűzfal (WAF) figyelése és naplózása a Azure Monitor-és Azure Monitor-naplókkal való naplózással és integrációval történik.

## <a name="azure-monitor"></a>Azure Monitor

A FrontDoor naplóval rendelkező WAF integrálva van a [Azure monitor](../../azure-monitor/overview.md). Azure Monitor lehetővé teszi a diagnosztikai információk nyomon követését, beleértve a WAF-riasztásokat és-naplókat. A WAF-figyelést a portálon belül, a **diagnosztika** lapon vagy közvetlenül a Azure monitor szolgáltatáson keresztül konfigurálhatja.

Azure Portal válassza a bejárati erőforrás típusa lehetőséget. A bal oldali **figyelés**/**metrikák** lapon hozzáadhat **WebApplicationFirewallRequestCount** a WAF-szabályoknak megfelelő kérelmek számának nyomon követéséhez. Az egyéni szűrők a műveleti típusok és a szabályok nevei alapján hozhatók létre.

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

A következő példában szereplő lekérdezés beolvassa a AccessLogs-bejegyzéseket:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>További lépések

- További információ a [bejárati ajtóról](../../frontdoor/front-door-overview.md).

