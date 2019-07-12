---
title: Az Azure Log Analytics használatával vizsgálja meg az Application Gateway webalkalmazási tűzfal naplói
description: Ez a cikk bemutatja, hogyan használhatja az Azure Log Analytics, vizsgálja meg az Application Gateway webalkalmazási tűzfal naplói
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: aa867e33ef0faa96b6a66a9075a3a5b8b0b0bca4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712175"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Vizsgálja meg az Application Gateway webalkalmazási tűzfal naplói a Log Analytics használatával

Amint az Application Gateway WAF működési, engedélyezheti a naplók vizsgálata, mi történik minden egyes kérelemmel. A WAF milyen tűzfal naplók számára alkalmazásüzemeltetés kiértékelése, megfelelő, és blokkolja-e. A Log Analytics-szel vizsgálja meg a még részletesebb elemzéseket biztosítanak a tűzfalnaplók lévő adatok. Log Analytics-munkaterület létrehozásával kapcsolatos további információkért lásd: [Log Analytics-munkaterület létrehozása az Azure Portalon](../azure-monitor/learn/quick-create-workspace.md). Napló lekérdezésekkel kapcsolatos további információkért lásd: [napló áttekintése az Azure monitorban lekérdezések](../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>WAF-naplók importálása

A tűzfalnaplók importálni a Log Analytics, lásd: [háttérrendszer állapota, diagnosztikai naplók és mérőszámok az Application Gateway](application-gateway-diagnostics.md#diagnostic-logging). Ha a tűzfal naplói a Log Analytics-munkaterület, adatok megtekintéséhez, lekérdezéseket írni, vizualizációkat hozhat létre, és hozzáadhatja őket az irányítópulton.

## <a name="explore-data-with-examples"></a>Példák az adatok megismerése

A nyers adatok megtekintése a tűzfal naplóban, futtassa a következő lekérdezést:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Ez fog hasonlítani, a következő lekérdezést:

![Log Analytics-lekérdezés](media/log-analytics/log-query.png)

Az adatok feltárásához és a grafikonon jeleníti meg, vagy hozzon létre vizualizációt itt. Tekintse meg a következő lekérdezéseket kiindulási pontként:

### <a name="matchedblocked-requests-by-ip"></a>Egyező/letiltott kérelmek IP-cím alapján

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Egyező/letiltott kérelmek URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Felső minden egyező szabályt

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Az első öt szabály egyező csoportot

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Hozzáadás a saját irányítópulthoz

Ha egy lekérdezést hoz létre, hozzáadhat az irányítópulthoz.  Válassza ki a **rögzítés az irányítópulton** felső, jobb a log analytics-munkaterületet. Egy példa irányítópultra rögzített előző négy lekérdezésekkel Ez az egyetlen pillantással láthatja az adatokat:

![Irányítópult](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>További lépések

[Háttérrendszer állapota, diagnosztikai naplók és mérőszámok az Application Gateway számára](application-gateway-diagnostics.md)
