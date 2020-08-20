---
title: SKU Azure Load Balancer
description: Azure Load Balancer SKU-áttekintés
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: 3509b99d1b222fc4f0bfa48effe42496c209d9ae
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654480"
---
# <a name="azure-load-balancer-skus"></a>SKU Azure Load Balancer

Azure Load Balancer két SKU-t tartalmaz.

## <a name="sku-comparison"></a><a name="skus"></a> SKU-összehasonlítás

A Load Balancer a standard és az alapszintű SKU-t is támogatja. Ezek az SKU-ket a forgatókönyvek skálázása, a funkciók és a díjszabás különbözik. Az alapszintű Load balancerrel lehetséges forgatókönyvek a standard Load Balancer használatával hozhatók létre.

A különbségek összehasonlításához és megértéséhez lásd az alábbi táblázatot. További információ: az [Azure standard Load Balancer áttekintése](load-balancer-standard-overview.md).

>[!NOTE]
> A Microsoft a standard Load balancert javasolja.
Az önálló virtuális gépeket, a rendelkezésre állási csoportokat és a Virtual Machine Scale-készleteket csak egy termékváltozathoz csatlakoztathatja, sosem kettőhöz egyszerre. A terheléselosztó és a nyilvános IP-cím SKU egyeznie kell, ha nyilvános IP-címekkel használja őket. A Load Balancer és a nyilvános IP-címek nem változtathatók meg.

| | standard Load Balancer | Alapszintű Load Balancer |
| --- | --- | --- |
| **[Háttérbeli készlet mérete](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)** | Legfeljebb 1000 példányt támogat. | Legfeljebb 300 példányt támogat. |
| **Háttérbeli készlet végpontjai** | Bármely virtuális gép vagy virtuálisgép-méretezési csoport egyetlen virtuális hálózatban. | Egyetlen rendelkezésre állási csoport vagy virtuálisgép-méretezési csoport virtuális gépei. |
| **[Állapotminták](./load-balancer-custom-probe-overview.md#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Állapot-mintavételi leállási viselkedés](./load-balancer-custom-probe-overview.md#probedown)** | A TCP-kapcsolatok egy példányon maradnak életben __, és__ az összes mintavétel le van kapcsolva. | A TCP-kapcsolatok egy példányon maradnak életben. Minden TCP-kapcsolat megszűnik, ha az összes mintavétel le van nyomva. |
| **Availability Zones** | Zóna – redundáns és zónákhoz tartozó előtér a bejövő és kimenő forgalomhoz. | Nem elérhető |
| **Diagnosztika** | [Többdimenziós metrikák Azure Monitor](./load-balancer-standard-diagnostics.md) | [Naplók Azure Monitor](./load-balancer-monitor-log.md) |
| **HA portok** | [Belső Load Balancer számára elérhető](./load-balancer-ha-ports-overview.md) | Nem elérhető |
| **Alapértelmezés szerint biztonságos** | Lezárva a bejövő folyamatokhoz, kivéve, ha a hálózati biztonsági csoport engedélyezi azt. A virtuális hálózatról a belső terheléselosztó felé irányuló belső forgalom engedélyezett. | Alapértelmezés szerint megnyílik. A hálózati biztonsági csoport nem kötelező. |
| **Kimenő szabályok** | [Deklaratív kimenő NAT-konfiguráció](./load-balancer-outbound-rules-overview.md) | Nem elérhető |
| **TCP alaphelyzetbe állítása üresjáratban** | [Bármely szabályban elérhető](./load-balancer-tcp-reset.md) | Nem elérhető |
| **[Több előtér](./load-balancer-multivip-overview.md)** | Bejövő és [kimenő](./load-balancer-outbound-connections.md) | Csak bejövő |
| **Felügyeleti műveletek** | A legtöbb művelet < 30 másodperc | 60 – 90 + másodperc átlagos |
| **SLA** | [99,99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Nem elérhető | 

További információ: [Load Balancer korlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). A Standard Load Balancerről további részleteket az [áttekintés](load-balancer-standard-overview.md), a [díjszabás](https://aka.ms/lbpricing) és az [SLA](https://aka.ms/lbsla) szakaszban talál.

## <a name="limitations"></a>Korlátozások

- Az SKU nem változtatható. Meglévő erőforrás SKU-jának nem módosítható.
- Egy önálló virtuálisgép-erőforrás, rendelkezésre állási készlet erőforrás vagy virtuálisgép-méretezési csoport erőforrása hivatkozhat egyetlen SKU-ra, soha nem mindkettőre.
- Az [előfizetési műveletek](../azure-resource-manager/management/move-resource-group-and-subscription.md) nem támogatottak standard Load Balancer és a standard nyilvános IP-erőforrások esetében.

## <a name="next-steps"></a>További lépések

- A Load Balancer használatának első lépéseiért tekintse meg [a nyilvános standard Load Balancer létrehozását](quickstart-load-balancer-standard-public-portal.md) ismertető témakört.
- Tudnivalók a [standard Load Balancer és a Availability Zones](load-balancer-standard-availability-zones.md)használatáról.
- Tudnivalók az [állapot](load-balancer-custom-probe-overview.md)-mintavételekről.
- Tudnivalók a [Kimenő kapcsolatok Load Balancer](load-balancer-outbound-connections.md)használatáról.
- További tudnivalók a HA-portok terheléselosztási [szabályaival standard Load Balancer](load-balancer-ha-ports-overview.md).
- További információ a [hálózati biztonsági csoportokról](../virtual-network/security-overview.md).
