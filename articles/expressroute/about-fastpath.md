---
title: Tudnivalók az Azure ExpressRoute FastPath
description: Tudnivalók az Azure ExpressRoute FastPath hálózati forgalom küldéséhez az átjáró megkerülésével
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 265004b1171d1df95b3090676d5836b951c28a28
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80282851"
---
# <a name="about-expressroute-fastpath"></a>Az ExpressRoute FastPath ismertetése

A ExpressRoute virtuális hálózati átjáró a hálózati útvonalak cseréjére és a hálózati forgalom irányítására szolgál. A FastPath úgy lett kialakítva, hogy javítsa a helyszíni hálózat és a virtuális hálózat adatelérési útjainak teljesítményét. Ha engedélyezve van, a FastPath közvetlenül a virtuális hálózatban lévő virtuális gépekre küldi a hálózati forgalmat, megkerülve az átjárót.

## <a name="requirements"></a>Követelmények

### <a name="circuits"></a>Kapcsolatcsoportok

A FastPath az összes ExpressRoute-áramkörön elérhető.

### <a name="gateways"></a>Átjárók

A FastPath továbbra is létre kell hoznia egy virtuális hálózati átjárót a virtuális hálózat és a helyszíni hálózat közötti útvonalak cseréjéhez. A virtuális hálózati átjárókkal és ExpressRoute kapcsolatos további információk, beleértve a teljesítménnyel kapcsolatos információkat és az átjárók SKU-ját lásd: [ExpressRoute virtuális hálózati átjárók](expressroute-about-virtual-network-gateways.md).

A FastPath konfigurálásához a virtuális hálózati átjárónak a következőknek kell lennie:

* Ultra Performance
* ErGw3AZ

## <a name="supported-features"></a>Támogatott funkciók

Míg a FastPath támogatja a legtöbb konfigurációt, nem támogatja a következő funkciókat:

* UDR az átjáró alhálózatán: Ha UDR alkalmaz a virtuális hálózat átjáró-alhálózatára, a helyszíni hálózatról érkező hálózati forgalom továbbra is a virtuális hálózati átjárónak lesz elküldve.

* VNet-társítás: Ha más virtuális hálózatokkal rendelkezik, amelyek az ExpressRoute-hez csatlakoznak, a helyszíni hálózatról a másik virtuális hálózatra irányuló hálózati forgalmat (azaz az úgynevezett "küllős" virtuális hálózatok) továbbra is a virtuális hálózati átjárónak küldi a rendszer. A megkerülő megoldás az összes virtuális hálózat közvetlen összekapcsolása a ExpressRoute-áramkörrel.

* Alapszintű Load Balancer: Ha alapszintű belső terheléselosztó üzembe helyezését végzi a virtuális hálózaton, vagy a virtuális hálózatban üzembe helyezett Azure Pásti szolgáltatás alapszintű belső terheléselosztást használ, a helyszíni hálózatról az alapszintű Load balancerben üzemeltetett virtuális IP-címekre irányuló hálózati forgalmat a rendszer a virtuális hálózati átjárónak küldi el. A megoldás az alapszintű Load Balancer [standard Load balancerre](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)való frissítése.

* Privát hivatkozás: Ha a virtuális hálózatban lévő [privát végponthoz](../private-link/private-link-overview.md) csatlakozik a helyszíni hálózatról, a kapcsolat a virtuális hálózati átjárón keresztül fog haladni.
 
## <a name="next-steps"></a>További lépések

A FastPath engedélyezéséhez lásd: [virtuális hálózat összekapcsolása a ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
