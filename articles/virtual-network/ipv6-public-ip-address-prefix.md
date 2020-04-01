---
title: Nyilvános IPv6-címelőtag az Azure virtuális hálózatában
titlesuffix: Azure Virtual Network
description: Ismerje meg a nyilvános IPv6-cím előtagot az Azure virtuális hálózatában.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 096dbcb6b6a732bd21622658f6f30c5158a821c5
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420524"
---
# <a name="reserved-public-ipv6-address-prefix"></a>Fenntartott nyilvános IPv6-cím előtagja

Az Azure-ban a kétverű (IPv4+IPv6) virtuális hálózatok (VNet) és a virtuális gépek (VM-ek) alapértelmezés szerint biztonságosak, mivel nem rendelkeznek internetkapcsolattal. Az Azure load balancers és a virtuális gépek hez egyszerűen hozzáadhat iPv6-internetkapcsolatot az Azure-ból beszerzett nyilvános IPv6-címekkel.

A lefoglalt nyilvános IP-k az Ön által választott Azure-régióhoz és az Azure-előfizetéshez vannak társítva. A fenntartott (statikus) nyilvános IP-cím az Azure Load Balancers vagy a virtuális gépek között az előfizetésben áthelyezheti. Az IPv6 nyilvános IP-címét teljesen elissza, és ha készen áll, akkor az ön számára is megtörténik.

> [!WARNING]
> Legyen óvatos, ha nem törli véletlenül a nyilvános IP-címeket. A nyilvános IP-cím törlésével eltávolítja azt az előfizetésből, és nem fogja tudni helyreállítani (még az Azure-támogatás segítségével sem).

Az egyes IPv6-címek lefoglalása mellett az Azure IPv6-címek (más néven IP-előtag) összefüggő tartományait is lefoglalhatja a használatra.  Az egyes IP-címekhez hasonlóan a fenntartott előtagok az Ön által választott Azure-régióhoz és az Azure-előfizetéshez kapcsolódnak. A címk kiszámítható, összefüggő tartományának lefoglalása számos felhasználási haszna van. Például nagyban leegyszerűsítheti a vállalat és az ügyfelek által üzemeltetett Azure-alkalmazások *IP-engedélyezési listáját,* mivel a statikus IP-tartományok könnyen programozhatók helyszíni tűzfalakba.  Szükség szerint létrehozhat egyéni nyilvános IP-címeket az IP-előtagból, és amikor törli ezeket az egyes nyilvános IP-címeket, azok *visszakerülnek* a fenntartott tartományba, így később újra felhasználhatja őket. Az IP-előtagban lévő összes IP-cím az előtag törléséig van fenntartva az Ön kizárólagos használatára.



## <a name="ipv6-prefix-sizes"></a>IPv6 előtagok méretei
A következő nyilvános IP-előtagok érhetők el:

-  Minimális IPv6 előtagméret: /127 = 2 cím
-  Maximális IPv6 előtagméret: /124 = 16 cím

Az előtag mérete osztály nélküli TARTOMÁNYOK közötti útválasztás (CIDR) maszkméretként van megadva. A /128 maszk például egyedi IPv6-címet jelöl, mivel az IPv6-címek 128 bitből állnak.

## <a name="pricing"></a>Díjszabás
 
Az Azure nyilvános IP-címek , egyéni IP-címek és IP-tartományok használatával kapcsolatos költségekről a [Nyilvános IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses/)című témakörben van.

## <a name="limitations"></a>Korlátozások
Az IPv6 csak "dinamikus" foglalással támogatott az IPv6-alapú ip-címek, ami azt jelenti, hogy az IPv6-cím megváltozik, ha törli és újratelepíti az alkalmazást (virtuális gépek vagy terheléselosztók) az Azure-ban. Standard IPv6 nyilvános IP-támogatás kizárólag statikus (fenntartott) foglalás, bár standard belső terheléselosztók is támogatja a dinamikus elosztás attól az alhálózattól, amelyhez hozzá vannak rendelve.  

Ajánlott eljárásként azt javasoljuk, hogy az IPv6-alkalmazások hoz szabványos nyilvános IP-címeket és standard terheléselosztókat használjon.

## <a name="next-steps"></a>További lépések
- Nyilvános [IPv6-címelőtag lefoglalása](ipv6-reserve-public-ip-address-prefix.md).
- További információ az [IPv6-címekről.](ipv6-overview.md)
- [Megtudhatja, hogyan hozhat létre és használhat nyilvános IP-címeket](virtual-network-public-ip-address.md) (iPv4-et és IPv6-ot egyaránt) az Azure-ban.
