---
title: Az Azure ExpressRoute FastPath be
description: Ismerje meg az Azure ExpressRoute FastPath szolgáltatást, amelynek célja a hálózati forgalom küldése az átjáró megkerülésével
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 265004b1171d1df95b3090676d5836b951c28a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282851"
---
# <a name="about-expressroute-fastpath"></a>Az ExpressRoute FastPath ismertetése

Az ExpressRoute virtuális hálózati átjárója hálózati útvonalak cseréjére és hálózati forgalom irányítására szolgál. A FastPath célja, hogy javítsa az adatelérési út teljesítményét a helyszíni hálózat és a virtuális hálózat között. Ha engedélyezve van, a FastPath közvetlenül a virtuális hálózat virtuális gépeinek küldi a hálózati forgalmat, megkerülve az átjárót.

## <a name="requirements"></a>Követelmények

### <a name="circuits"></a>Áramkörök

A FastPath minden ExpressRoute-kapcsolatcsoporton elérhető.

### <a name="gateways"></a>Átjárók

A FastPath használatához továbbra is létre kell hozni egy virtuális hálózati átjárót a virtuális hálózat és a helyszíni hálózat közötti útvonalak cseréjéhez. A virtuális hálózati átjárókról és az ExpressRoute-ról, beleértve a teljesítményadatokat és az átjáró-skus-okat, további információt az [ExpressRoute virtuális hálózati átjárói című témakörben talál.](expressroute-about-virtual-network-gateways.md)

A FastPath konfigurálásához a virtuális hálózati átjárónak a következőnek kell lennie:

* Ultra teljesítmény
* Ergw3AZ

## <a name="supported-features"></a>Támogatott funkciók

Bár a FastPath a legtöbb konfigurációt támogatja, a következő szolgáltatásokat nem támogatja:

* UDR az átjáró alhálózaton: Ha udr-t alkalmaz a virtuális hálózat átjáróalhálózatára, a helyszíni hálózatról érkező hálózati forgalom továbbra is a virtuális hálózati átjáróra kerül.

* Virtuális hálózati társviszony-létesítés: Ha más virtuális hálózatok koncettingetaz ExpressRoute-hoz kapcsolódó, a hálózati forgalmat a helyszíni hálózat a többi virtuális hálózatok (azaz az úgynevezett "Küllő" virtuális hálózatok) továbbra is küldött a virtuális hálózat Átjáró. A megoldás az, hogy az összes virtuális hálózatot közvetlenül csatlakoztatja az ExpressRoute-kapcsolathoz.

* Alapszintű terheléselosztó: Ha egy alapszintű belső terheléselosztót telepít a virtuális hálózatban, vagy a virtuális hálózatban üzembe helyezett Azure PaaS-szolgáltatás egy alapszintű belső terheléselosztót használ, a helyszíni hálózatból a virtuális IP-khez vezető hálózati forgalmat a Az alapszintű terheléselosztó a virtuális hálózati átjárónak lesz elküldve. A megoldás az alapterhelés-elosztó [standard terheléselosztóra](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)való frissítése.

* Privát kapcsolat: Ha a virtuális hálózat [egyik privát végpontjához](../private-link/private-link-overview.md) csatlakozik a helyszíni hálózatról, a kapcsolat a virtuális hálózati átjárón keresztül fog menni.
 
## <a name="next-steps"></a>További lépések

A FastPath engedélyezéséhez olvassa el [A virtuális hálózat összekapcsolása az ExpressRoute-hoz .to enable FastPath,](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)see Link a a virtual network to ExpressRoute .
