---
title: Azure Monitor a tárolók régiójának leképezéséhez
description: Ez a cikk a Azure Monitor for containers, a Log Analytics munkaterület és az egyéni metrikák között támogatott régió-hozzárendeléseket ismerteti.
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: references_regions
ms.openlocfilehash: 3e8ead78c5e0e534e07c1e2ab0e25eb3f5a90c38
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84194986"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>A tárolók Azure Monitor által támogatott régió-hozzárendelések

 A tárolók Azure Monitorának engedélyezésekor csak bizonyos régiók támogatottak egy Log Analytics-munkaterület és egy AK-fürt összekapcsolásához, valamint az Azure Monitor elküldött egyéni metrikák összegyűjtéséhez.

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics munkaterület által támogatott leképezések

Az AK-fürterőforrás vagy Log Analytics munkaterület más régiókban is lehet, és a következő táblázat a leképezéseket mutatja be.

|**AK-fürt régiója** | **Log Analytics munkaterület-régió** |
|-----------------------|------------------------------------|
|**Afrika** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Ausztrália** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Ázsia és a Csendes-óceáni térség** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**Brazília** | |
|BrazilSouth | SouthCentralUS |
|**Kanada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**Európa** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|WestEurope |WestEurope |
|**India** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japán** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**Dél-Korea** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**USA** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|<sup>1</sup> . WestCentralUS|<sup>1</sup> . EastUS|
|USA-beli államigazgatás – Virginia |USA-beli államigazgatás – Virginia |

<sup>1</sup> a kapacitás korlátozásai miatt a régió nem érhető el új erőforrások létrehozásakor. Ez Log Analytics munkaterületet tartalmaz. A régióban azonban a már meglévő társított erőforrások továbbra is működőképesek maradnak.

## <a name="custom-metrics-supported-regions"></a>Egyéni metrikák támogatott régiói

Az Azure Kubernetes Services (ak) fürtök csomópontjaiból és hüvelyből származó metrikák összegyűjtése csak az alábbi [Azure-régiókban](../platform/metrics-custom-overview.md#supported-regions)támogatott egyéni metrikák való közzétételre.

## <a name="next-steps"></a>További lépések

Az AK-fürt figyelésének megkezdéséhez tekintse át az [Azure monitor a tárolók számára című témakört](container-insights-onboard.md) , és Ismerje meg a figyelés engedélyezésének követelményeit és rendelkezésre álló módszereit.  
