---
title: Tudnivalók az Azure ExpressRoute FastPath
description: Tudnivalók az Azure ExpressRoute FastPath hálózati forgalom küldéséhez az átjáró megkerülésével
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: cherylmc
ms.openlocfilehash: 6ff1dac312bcb4df1e1afc9679df09fc8a2b28ff
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897363"
---
# <a name="about-expressroute-fastpath"></a>Tudnivalók a ExpressRoute FastPath

A ExpressRoute virtuális hálózati átjáró a hálózati útvonalak cseréjére és a hálózati forgalom irányítására szolgál. A FastPath úgy lett kialakítva, hogy javítsa a helyszíni hálózat és a virtuális hálózat adatelérési útjainak teljesítményét. Ha engedélyezve van, a FastPath közvetlenül a virtuális hálózatban lévő virtuális gépekre küldi a hálózati forgalmat, megkerülve az átjárót.

## <a name="requirements"></a>Követelmények

### <a name="circuits"></a>Kapcsolatcsoportok

A FastPath az összes ExpressRoute-áramkörön elérhető.

### <a name="gateways"></a>Átjárók

A FastPath továbbra is létre kell hoznia egy virtuális hálózati átjárót a virtuális hálózat és a helyszíni hálózat közötti útvonalak cseréjéhez. A virtuális hálózati átjárókkal és a ExpressRoute kapcsolatos további információkért lásd: [ExpressRoute virtuális hálózati átjárók](expressroute-about-virtual-network-gateways.md).

A virtuális hálózati átjárónak a következők egyikének kell lennie:

* Ultranagy teljesítmény
* ErGw3AZ

## <a name="supported-features"></a>Támogatott funkciók

Míg a FastPath támogatja a legtöbb konfigurációt, nem támogatja a következő funkciókat:

* UDR az átjáró alhálózatán: Ha UDR alkalmaz a virtuális hálózat átjáró-alhálózatára, a helyszíni hálózatról érkező hálózati forgalom továbbra is a virtuális hálózati átjárónak lesz elküldve.

* VNet-társítás: Ha más, a ExpressRoute-hoz csatlakoztatott virtuális hálózatokkal rendelkezik, a helyszíni hálózatról a másik virtuális hálózatra irányuló hálózati forgalmat (azaz az úgynevezett "küllős" virtuális hálózatok) továbbra is a virtuális hálózatra küldi a rendszer. átjáró. A megkerülő megoldás az összes virtuális hálózat közvetlen összekapcsolása a ExpressRoute-áramkörrel.

* Alapszintű Load Balancer: Ha alapszintű belső terheléselosztó üzembe helyezését végzi a virtuális hálózatban, vagy a virtuális hálózatban üzembe helyezett Azure Pásti szolgáltatás alapszintű belső terheléselosztó, a helyszíni hálózatról a hálózati forgalom a (z) rendszerben üzemeltetett virtuális IP-címekre A rendszer az alapszintű Load balancert a virtuális hálózati átjárónak küldi el. A megoldás az alapszintű Load Balancer [standard Load balancerre](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)való frissítése.

* Privát hivatkozás: Ha a virtuális hálózatban lévő [privát végponthoz](../private-link/private-link-overview.md) csatlakozik a helyszíni hálózatról, a kapcsolat a virtuális hálózati átjárón keresztül fog haladni.
 
## <a name="next-steps"></a>Következő lépések

A FastPath engedélyezéséhez lásd: [virtuális hálózat összekapcsolása a ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
