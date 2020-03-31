---
title: Metrikák és riasztások az Azure virtuális hálózati NAT-hoz
titleSuffix: Azure Virtual Network
description: Ismerje meg az Azure Monitor metrikák és riasztások elérhető virtuális hálózati hálózati kapcsolatonként.
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
ms.openlocfilehash: 4f1760c32117b34d4d453964473cba3c7d07f725
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79202178"
---
# <a name="azure-virtual-network-nat-metrics"></a>Az Azure virtuális hálózati NAT-mérőszámok

Az Azure Virtual Network NAT átjáróerőforrásai többdimenziós metrikákat biztosítanak. Ezekkel a mérőszámokkal megfigyelheti a műveletet és [a hibaelhárításhoz.](nat-metrics.md)  Riasztások konfigurálható a kritikus problémák, például az SNAT-kimerültség.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtuális hálózati hálózati hálózati kapcsolatiat az internetre való kimenő kapcsolathoz">
</p>

*Ábra: Virtuális hálózati hálózati hálózati kapcsolatiat az internetre való kifelé irányuló kapcsolathoz*

## <a name="metrics"></a>Mérőszámok

A NAT-átjáró-erőforrások a következő többdimenziós metrikákat biztosítják az Azure Monitorban:

| Metrika | Leírás | Ajánlott összesítés | Dimenziók |
|---|---|---|---|
| Bájt | Bejövő és kimenő feldolgozott bájtok | Összeg | Irány (Be; Kimenő), protokoll (6 TCP; 17 UDP) |
| Csomagok | Bejövő és kimenő feldolgozott csomagok | Összeg | Irány (Be; Kimenő), protokoll (6 TCP; 17 UDP) |
| Eldobott csomagok | A NAT-átjáró által eldobott csomagok | Összeg | / |
| SNAT-kapcsolatok száma | Állapotátmenetek intervallumonként | Összeg | Csatlakozási állapot, protokoll (6 TCP; 17 UDP) |
| Az SNAT-kapcsolatok teljes száma | Aktuális aktív SNAT-kapcsolatok (~ használatban lévő SNAT-portok) | Összeg | Protokoll (6 TCP; 17 UDP) |


## <a name="alerts"></a>Riasztások

A metrikák riasztásai konfigurálhatók az Azure Monitorban az egyes előző [metrikákhoz.](#metrics)

## <a name="limitations"></a>Korlátozások

Az erőforrás állapota nem támogatott.

## <a name="next-steps"></a>További lépések

* További információ a [virtuális hálózati hálózati hálózati kapcsolatról](nat-overview.md)
* Tudnivalók a [NAT átjáró-erőforrásról](nat-gateway-resource.md)
* További információ az [Azure Monitorról](../azure-monitor/overview.md)
* További információ a [NAT-átjáró erőforrásainak hibaelhárításáról.](troubleshoot-nat.md)
* [Mondja el, mit kell építeni a következő virtuális hálózati NAT a UserVoice](https://aka.ms/natuservoice).


