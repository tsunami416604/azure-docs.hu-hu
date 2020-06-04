---
title: Metrikák és riasztások az Azure Virtual Network NAT-hoz
titleSuffix: Azure Virtual Network
description: A Virtual Network NAT-hoz elérhető Azure Monitor mérőszámok és riasztások ismertetése.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 7b4f433ec5782c7a819ffcd6f48bf277d7b13de2
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84341354"
---
# <a name="azure-virtual-network-nat-metrics"></a>Azure Virtual Network NAT-metrikák

Az Azure Virtual Network NAT-átjáró erőforrásai több dimenziós mérőszámokat biztosítanak. Ezekkel a metrikákkal megfigyelheti a műveletet, és [hibaelhárítást](troubleshoot-nat.md)hajthat meg.  A riasztások olyan kritikus fontosságú problémákhoz konfigurálhatók, mint például a SNAT-kimerültség.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT Virtual Network az internet felé">
</p>

*Ábra: Virtual Network NAT a kimenő internethez*

## <a name="metrics"></a>Mérőszámok

A NAT-átjáró erőforrásai a következő többdimenziós mérőszámokat biztosítják a Azure Monitorban:

| Metric | Leírás | Ajánlott összesítés | Dimenziók |
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


