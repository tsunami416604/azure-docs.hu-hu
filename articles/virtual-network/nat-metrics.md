---
title: Metrikák és riasztások az Azure Virtual Network NAT-hoz
titleSuffix: Azure Virtual Network
description: A Virtual Network NAT-hoz elérhető Azure Monitor mérőszámok és riasztások ismertetése.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 51713db8532eb33f102de9320dea9eaeb98c4019
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359085"
---
# <a name="azure-virtual-network-nat-metrics"></a>Azure Virtual Network NAT-metrikák

Az Azure Virtual Network NAT-átjáró erőforrásai több dimenziós mérőszámokat biztosítanak. Ezekkel a metrikákkal megfigyelheti a műveletet, és [hibaelhárítást](nat-metrics.md)hajthat meg.  A riasztások olyan kritikus fontosságú problémákhoz konfigurálhatók, mint például a SNAT-kimerültség.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT Virtual Network az internet felé">
</p>

*Ábra: Virtual Network NAT a kimenő internethez*

>[!NOTE] 
>Virtual Network NAT jelenleg nyilvános előzetes verzióként érhető el. Jelenleg csak korlátozott számú [régióban](nat-overview.md#region-availability)érhető el. Ez az előzetes verzió szolgáltatási szintű szerződés nélkül érhető el, ezért nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="metrics"></a>Mérőszámok

A NAT-átjáró erőforrásai a következő többdimenziós mérőszámokat biztosítják a Azure Monitorban:

| Metrika | Leírás | Ajánlott összesítés | Dimenziók |
|---|---|---|---|
| Bájt | Bejövő és kimenő bájtok feldolgozott száma | Összeg | Irány (a; Kimenő), protokoll (6 TCP; 17 UDP) |
| Csomagok | A bejövő és kimenő csomagok feldolgozása folyamatban | Összeg | Irány (a; Kimenő), protokoll (6 TCP; 17 UDP) |
| Eldobott csomagok | A NAT-átjáró által eldobott csomagok | Összeg | / |
| SNAT-kapcsolatok száma | Állapot-átmenetek/időköz | Összeg | Kapcsolat állapota, protokoll (6 TCP; 17 UDP) |
| SNAT-kapcsolatok száma összesen | Aktuális aktív SNAT-kapcsolatok (~ SNAT-portok használatban) | Összeg | Protokoll (6 TCP; 17 UDP) |


## <a name="alerts"></a>Riasztások

A metrikákkal kapcsolatos riasztások az összes korábbi [metrika](#metrics)esetében Azure monitor konfigurálhatók.

## <a name="limitations"></a>Korlátozások

A Resource Health nem támogatott.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a [Virtual Network NAT](nat-overview.md) -ról
* Tudnivalók a [NAT-átjáró erőforrásáról](nat-gateway-resource.md)
* Tudnivalók a [Azure monitor](../azure-monitor/overview.md)
* További információ a [NAT-átjárók erőforrásainak hibaelhárításáról](troubleshoot-nat.md).
* [Ossza meg velünk a következőt Virtual Network NAT UserVoice-ben való létrehozásához](https://aka.ms/natuservoice).


