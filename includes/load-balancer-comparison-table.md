---
title: fájl belefoglalása
description: fájl belefoglalása
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 8b08e0ced0c7094890a80c37452c7f1b001fe511
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888697"
---
| | Standard termékváltozat | Alapszintű termékváltozat |
| --- | --- | --- |
| Háttérbeli készlet mérete | Legfeljebb 1000 példányt támogat. | Legfeljebb 100 példányt támogat. |
| Háttérbeli készlet végpontok | Egyetlen virtuális hálózat bármely virtuális gépe, beleértve a virtuális gépeket, a rendelkezésre állási csoportokat és a virtuálisgép-méretezési csoportokat. | Egyetlen rendelkezésre állási csoport vagy virtuálisgép-méretezési csoport virtuális gépei. |
| [Állapotminták](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Állapot-mintavételi leállási viselkedés](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | A TCP-kapcsolatok egy példányon maradnak életben __, és__ az összes mintavétel le van kapcsolva. | A TCP-kapcsolatok egy példányon maradnak életben. Minden TCP-kapcsolat leáll, ha az összes mintavétel le van állítva. |
| Rendelkezésre állási zónák | A bejövő és a kimenő forgalom számára a zóna-redundáns és a zónákhoz tartozó előtér-végpontok. A kimenő folyamatok nem képesek túlélni a zónát. Több zónás terheléselosztás. | Nem érhető el |
| Diagnosztika | Azure Monitor. Többdimenziós mérőszámok, beleértve a bájtok és a csomagok számlálóit. Állapot mintavételi állapota Csatlakozási kísérletek (TCP SYN). Kimenő kapcsolatok állapota (SNAT sikeres és sikertelen folyamatok). Az aktív adatsíkok mérései | Csak nyilvános Load Balancer Azure-Log Analytics. SNAT-kimerültség riasztása. A háttérbeli készlet állapotának száma. |
| HA portok | Belső terheléselosztó | Nem érhető el |
| Alapértelmezés szerint biztonságos | A nyilvános IP-címek, a nyilvános Load Balancer-végpontok és a belső Load Balancer végpontok a bejövő forgalomba vannak zárva, kivéve, ha a hálózati biztonsági csoport engedélyezi őket. | Alapértelmezés szerint megnyílik. A hálózati biztonsági csoport nem kötelező. |
| [Kimenő kapcsolatok](../articles/load-balancer/load-balancer-outbound-connections.md) | A készlet-alapú kimenő NAT explicit módon megadható [kimenő szabályokkal](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Több előtért is használhat terheléses terheléselosztási szabályokkal. A kimenő kapcsolat használatához explicit módon létre _kell_ hozni egy kimenő forgatókönyvet a virtuális gép, a rendelkezésre állási csoport vagy a virtuálisgép-méretezési csoport számára. A virtuális hálózati szolgáltatás végpontjai a kimenő kapcsolat meghatározása nélkül érhetők el, és nem számítanak bele a feldolgozott adatforgalomba. A virtuális hálózati szolgáltatási végpontként nem elérhető nyilvános IP-címeket, például az Azure Pásti-szolgáltatásokat, a kimenő kapcsolat használatával kell elérni, és meg kell számolni a feldolgozott adatforgalom irányába. Ha csak egy belső Load Balancer szolgál a virtuális gép, a rendelkezésre állási csoport vagy a virtuálisgép-méretezési csoport számára, a kimenő kapcsolatok az alapértelmezett SNAT nem érhetők el. Használja helyette a [kimenő szabályokat](../articles/load-balancer/load-balancer-outbound-rules-overview.md) . A kimenő SNAT programozása a bejövő terheléselosztási szabály átviteli protokolljának függvénye. | Egy előtér, amely véletlenszerűen van kiválasztva, ha több előtér van jelen. Ha csak a belső Load Balancer szolgálja ki a virtuális gépet, a rendelkezésre állási készletet vagy a virtuálisgép-méretezési készletet, a rendszer az alapértelmezett SNAT használja. |
| [Kimenő szabályok](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Deklaratív kimenő NAT-konfiguráció, nyilvános IP-címek vagy nyilvános IP-előtag vagy mindkettő használatával. Konfigurálható kimenő Üresjárati időkorlát (4-120 perc). Egyéni SNAT-portok kiosztása | Nem érhető el |
| [TCP alaphelyzetbe állítása üresjáratban](../articles/load-balancer/load-balancer-tcp-reset.md) | A TCP alaphelyzetbe állításának engedélyezése (az első TCP) az üresjárati időkorláton bármely szabálynál | Nem érhető el |
| [Több előtér](../articles/load-balancer/load-balancer-multivip-overview.md) | Bejövő és [kimenő](../articles/load-balancer/load-balancer-outbound-connections.md) | Csak bejövő |
| Felügyeleti műveletek | A legtöbb művelet < 30 másodperc | 60 – 90 + másodperc átlagos |
| SLA | 99,99% adatelérési úthoz két kifogástalan virtuális géppel. | Nem alkalmazható | 
| Díjszabás | Felszámítva a szabályok száma, az erőforráshoz társított bejövő és kimenő adatmennyiség alapján. | Díjmentes |
|  |  |  |
