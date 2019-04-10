---
title: Az Azure webalkalmazás-tűzfal figyelés és naplózás
description: A monitorozási és naplózási FrontDoor ismerteti a webalkalmazási tűzfal (WAF)
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 5368ed8d1e60a646366065e2cf617fb2f3735b53
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59363034"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Az Azure webalkalmazás-tűzfal figyelés és naplózás 

Azure-WebApp tűzfala (WAF) megfigyelés és naplózás biztosítják naplózási és integrációs szolgáltatásaival az Azure Monitor és az Azure Monitor naplókat.

> [!IMPORTANT]
> A WAF monitorozási és naplózási szolgáltatás Azure bejárati ajtajának jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="azure-monitor"></a>Azure Monitor

WAF-FrontDoor log integrálva van [Azure Monitor](../azure-monitor/overview.md). Azure Monitor segítségével nyomon követheti a diagnosztikai információk, beleértve a WAF-riasztások és a naplókat. Konfigurálhatja a WAF-figyelés alatt a portálon a bejárati ajtajának erőforrás belül a **diagnosztikai** lapon vagy az Azure monitoron keresztül szolgáltatás közvetlenül.

Az Azure Portalon lépjen a bejárati ajtajának erőforrástípus. A **figyelés**/**metrikák** lapra a bal oldalon hozzáadhat **WebApplicationFirewallRequestCount** nyomon követésére, amelyek megfelelnek a WAF-szabályok kérelmek száma. Egyéni szűrők Művelettípusok és szabálynév alapján lehet létrehozni.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Naplók és diagnosztika

WAF bejárati ajtajának az összes észlelt fenyegetésről részletes jelentéseket biztosít. A naplózás integrálva van az Azure Diagnostics naplóival, a riasztások pedig JSON formátumban vannak rögzítve. Ezek a naplók integrálhatók az [naplózza az Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog összes kérelem továbbított ügyfél háttérrendszereket naplóz. FrontdoorWebApplicationFirewallLog naplózza a WAF-szabályának kérelmet.

A következő példalekérdezés szerzi be a WAF-naplókban a blokkolt kérelmek:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

A következő példalekérdezés AccessLogs bejegyzések kéri le:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [bejárati ajtajának](front-door-overview.md).

