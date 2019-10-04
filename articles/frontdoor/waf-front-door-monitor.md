---
title: Azure webalkalmazási tűzfal figyelése és naplózása
description: A webalkalmazási tűzfal (WAF) megismerése FrontDoor-figyeléssel és-naplózással
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 675d06f3d2071022da3867a4c45137efb818980d
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849144"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure webalkalmazási tűzfal figyelése és naplózása 

Az Azure webalkalmazási tűzfal (WAF) figyelése és naplózása a Azure Monitor-és Azure Monitor-naplókkal való naplózással és integrációval történik.

## <a name="azure-monitor"></a>Azure Monitor

A FrontDoor naplóval rendelkező WAF integrálva van a [Azure monitor](../azure-monitor/overview.md). Azure Monitor lehetővé teszi a diagnosztikai információk nyomon követését, beleértve a WAF-riasztásokat és-naplókat. A WAF-figyelést a portálon belül, a **diagnosztika** lapon vagy közvetlenül a Azure monitor szolgáltatáson keresztül konfigurálhatja.

Azure Portal válassza a bejárati erőforrás típusa lehetőséget. A/bal oldali figyelési**metrikák** lapról hozzáadhat **WebApplicationFirewallRequestCount** a WAF-szabályoknak megfelelő kérelmek számának nyomon követéséhez. Az egyéni szűrők a műveleti típusok és a szabályok nevei alapján hozhatók létre.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Naplók és diagnosztika

A WAF az észlelt fenyegetésekkel kapcsolatos részletes jelentéseket biztosít. A naplózás integrálva van az Azure Diagnostics naplóival, a riasztások pedig JSON formátumban vannak rögzítve. Ezek a naplók [Azure monitor naplókkal](../azure-monitor/insights/azure-networking-analytics.md)integrálhatók.

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

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

- További információ a [](front-door-overview.md)bejárati ajtóról.

