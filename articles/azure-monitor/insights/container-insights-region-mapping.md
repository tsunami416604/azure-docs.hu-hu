---
title: Az Azure Monitor tárolók régióban leképezések
description: Ez a cikk ismerteti a tárolók, a Log Analytics-munkaterületet és egyéni mérőszámok az Azure Monitor között támogatott régióban leképezések.
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 481a2a400be4e983e0a2337a200324061494efa1
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518078"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>A tárolók az Azure Monitor által támogatott régióban leképezések

 Azure Monitor-tárolókhoz engedélyezésekor csak bizonyos régiókban támogatottak összekapcsolása a Log Analytics-munkaterület és a egy AKS-fürtöt, és az Azure Monitor egyéni metrikákat gyűjt benyújtani.

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics-munkaterület támogatott leképezések

Az AKS-fürt erőforrásainak vagy a Log Analytics-munkaterület más régiókban is lehetnek, és az alábbi táblázatban láthatók a leképezéseket.

|**AKS-fürt régió** | **Log Analytics-munkaterület régió** |
|-----------------------|------------------------------------|
|**Afrika** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Ausztrália** | |
|Kelet-Ausztrália |Kelet-Ausztrália |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|Kelet-Ausztrália |Kelet-Ausztrália |
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
|**Korea** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**USA** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|USA keleti régiója<sup>1</sup>|

<sup>1</sup> kapacitás korlátozások, mert a régió nem érhető el új erőforrás létrehozásakor. Ez magában foglalja a Log Analytics-munkaterületet. A régió már létező kapcsolt erőforrások azonban továbbra is működik.

## <a name="custom-metrics-supported-regions"></a>Egyéni metrikák támogatott régiók

Metrikákat gyűjt az Azure Kubernetes-szolgáltatások (AKS)-fürtök csomópontjai, és csak az a következő egyéni mérőszámokként közzététel podok támogatott [Azure-régiók](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>További lépések

Az AKS-fürt a figyelés megkezdése előtt tekintse át a [engedélyezése az Azure Monitor-tárolókhoz](container-insights-onboard.md) engedélyezése a figyelési követelmények és választható módszerek.  