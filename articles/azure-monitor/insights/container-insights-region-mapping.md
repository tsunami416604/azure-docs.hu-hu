---
title: Azure Monitor tárolók régióleképezéséhez
description: Ez a cikk ismerteti a régió leképezések az Azure Monitor tárolók, Log Analytics-munkaterület és az egyéni metrikák támogatott.
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403425"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Az Azure Monitor által támogatott régióleképezések tárolókhoz

 Az Azure Monitor tárolók ra vonatkozó engedélyezésekor csak bizonyos régiók támogatják a Log Analytics-munkaterület és az AKS-fürt összekapcsolását, valamint az Azure Monitorba küldött egyéni metrikák gyűjtését.

## <a name="log-analytics-workspace-supported-mappings"></a>A Log Analytics munkaterület által támogatott hozzárendelések

Az AKS-fürt erőforrásai vagy a Log Analytics-munkaterület más régiókban is elhelyezkedhetnek, és az alábbi táblázat a leképezéseinket mutatja be.

|**AKS-fürtrégió** | **Log Analytics-munkaterület régió** |
|-----------------------|------------------------------------|
|**Afrika** | |
|Dél-AfrikaÉszak |WestEurope |
|Dél-AfrikaNyugat |WestEurope |
|**Ausztrália** | |
|AusztráliaKelet |AusztráliaKelet |
|AusztráliaKözponti |AusztráliaKözponti |
|AusztráliaCentral2 |AusztráliaKözponti |
|AusztráliaKelet |AusztráliaKelet |
|**Ázsia és a Csendes-óceáni térség** | |
|Kelet-Ázsia |Kelet-Ázsia |
|Délkelet-Ázsia |Délkelet-Ázsia |
|**Brazília** | |
|BrazíliaDél | SouthCentralUS (Dél-Közép- és Közép- |
|**Kanada** ||
|KanadaKözponti |KanadaKözponti |
|KanadaKelet |KanadaKözponti |
|**Európa** | |
|FranciaországKözponti |FranciaországKözponti |
|FranciaországDél |FranciaországKözponti |
|Észak-Európa |Észak-Európa |
|UKSouth |UKSouth |
|UKWest között |UKSouth |
|WestEurope |WestEurope |
|**India** | |
|Közép-India |Közép-India |
|Dél-India |Közép-India |
|Nyugat-India |Közép-India |
|**Japán** | |
|JapánKelet |JapánKelet |
|JapánNyugat |JapánKelet |
|**Dél-Korea** | |
|KoreaKözponti |KoreaKözponti |
|KoreaDél |KoreaKözponti |
|**USA** | |
|Közép-Amerikai Egyesült Államok |Közép-Amerikai Egyesült Államok|
|KeletUSA |KeletUSA |
|KeletUS2 |KeletUS2 |
|Nyugat-Nyugat-Nyugat |Nyugat-Nyugat-Nyugat |
|Nyugat-us2 |Nyugat-us2 |
|WestCentralUS<sup>1</sup>|Kelet-US<sup>1</sup>|
|USA-beli államigazgatás – Virginia |USA-beli államigazgatás – Virginia |

<sup>1</sup> A kapacitáskorlátok miatt a régió nem érhető el új erőforrások létrehozásakor. Ez magában foglalja a Log Analytics munkaterületet. A régióban már meglévő összekapcsolt erőforrásoknak azonban továbbra is működniük kell.

## <a name="custom-metrics-supported-regions"></a>Egyéni metrikák támogatott régiók

Metrikák gyűjtése az Azure Kubernetes Services (AKS) fürtök csomópontok és podok számára támogatott a közzététel egyéni metrikák csak a következő [Azure-régiókban.](../platform/metrics-custom-overview.md#supported-regions)

## <a name="next-steps"></a>További lépések

Az AKS-fürt figyelésének megkezdéséhez tekintse át [a Hogyan engedélyezheti az Azure Monitor tárolók](container-insights-onboard.md) a követelmények és a rendelkezésre álló módszerek lehetővé teszi a figyelés.  
