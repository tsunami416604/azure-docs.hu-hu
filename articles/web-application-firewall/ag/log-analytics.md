---
title: Az Azure Log Analytics használata az Application Gateway webalkalmazás-tűzfal naplóinak vizsgálatához
description: Ez a cikk bemutatja, hogyan használhatja az Azure Log Analytics segítségével az Application Gateway webalkalmazás-tűzfal naplóinak vizsgálatát
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: e1bc3b58f425b374e4cae1da6e9800579e503f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516590"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>A Log Analytics használata az Application Gateway webalkalmazási tűzfal naplóinak vizsgálatához

Miután az Application Gateway WAF működőképessé vált, engedélyezheti a naplókat az egyes kérelmekkel kapcsolatban zajló események vizsgálatához. A tűzfalnaplók betekintést nyújtanak abba, hogy a WAF mit értékel, egyezik és blokkol. A Log Analytics segítségével megvizsgálhatja a tűzfalnaplókban lévő adatokat, hogy még több betekintést nyújtson. A Log Analytics-munkaterület létrehozásáról további információt [a Log Analytics-munkaterület létrehozása az Azure Portalon című témakörben talál.](../../azure-monitor/learn/quick-create-workspace.md) A naplólekérdezésekről az [Azure Monitor naplólekérdezéseinek áttekintése](../../azure-monitor/log-query/log-query-overview.md)című témakörben olvashat bővebben.

## <a name="import-waf-logs"></a>WAF-naplók importálása

A tűzfalnaplók loganalytics-be importálásához olvassa el a [háttérrendszer állapotát, a diagnosztikai naplókat és az Application Gateway metrikáit.](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging) Ha a tűzfal naplók a Log Analytics-munkaterület, megtekintheti az adatokat, lekérdezéseket írhat, vizualizációkat hozhat létre, és adja hozzá őket a portál irányítópultján.

## <a name="explore-data-with-examples"></a>Adatok feltárása példákkal

A tűzfalnaplóban lévő nyers adatok megtekintéséhez futtassa a következő lekérdezést:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Ez a következő lekérdezéshez hasonlóan fog kinézni:

![Log Analytics-lekérdezés](../media/log-analytics/log-query.png)

Leáshat az adatokba, grafikonokat rajzolhat, vagy vizualizációkat hozhat létre innen. Kiindulópontként tekintse meg a következő lekérdezéseket:

### <a name="matchedblocked-requests-by-ip"></a>IP-címegyeztetéses/blokkolt kérések

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Uri egyező/blokkolt kérései

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Top egyező szabályok

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Az öt egyező szabálycsoport első számú csoportja

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Hozzáadás az irányítópulthoz

Miután létrehozott egy lekérdezést, hozzáadhatja azt az irányítópulthoz.  A naplóelemzési munkaterület jobb felső részén válassza ki az **irányítópulthoz való rögzítést.** Az előző négy lekérdezés egy példairányítópultra rögzítve ezeket az adatokat láthatja egy pillantással:

![Irányítópult](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>További lépések

[Háttérrendszer állapota, diagnosztikai naplók és metrikák az Application Gateway-hez](../../application-gateway/application-gateway-diagnostics.md)