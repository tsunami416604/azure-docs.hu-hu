---
title: fájl belefoglalása
description: fájl belefoglalása
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/21/2020
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 33e6fecafa2b9748a27794800365974ebc975d0e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78202545"
---
| | standard Load Balancer | Alapszintű Load Balancer |
| --- | --- | --- |
| [Háttérbeli készlet mérete](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Legfeljebb 1000 példányt támogat. | Legfeljebb 300 példányt támogat. |
| Háttérbeli készlet végpontjai | Bármely virtuális gép vagy virtuálisgép-méretezési csoport egyetlen virtuális hálózatban. | Egyetlen rendelkezésre állási csoport vagy virtuálisgép-méretezési csoport virtuális gépei. |
| [Állapotminták](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Állapot-mintavételi leállási viselkedés](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | A TCP-kapcsolatok egy példányon maradnak életben __, és__ az összes mintavétel le van kapcsolva. | A TCP-kapcsolatok egy példányon maradnak életben. Minden TCP-kapcsolat leáll, ha az összes mintavétel le van állítva. |
| Rendelkezésre állási zónák | Zóna – redundáns és zónákhoz tartozó előtér a bejövő és kimenő forgalomhoz. | Nem érhető el |
| Diagnosztika | [Többdimenziós metrikák Azure Monitor](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Azure Monitor-naplók](../articles/load-balancer/load-balancer-monitor-log.md) |
| HA portok | [Belső Load Balancer számára elérhető](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Nem érhető el |
| Alapértelmezés szerint biztonságos | Lezárva a bejövő folyamatokhoz, kivéve, ha a hálózati biztonsági csoport engedélyezi azt. Vegye figyelembe, hogy a VNet belső forgalma a belső terheléselosztó számára engedélyezett. | Alapértelmezés szerint megnyílik. A hálózati biztonsági csoport nem kötelező. |
| Kimenő szabályok | [Deklaratív kimenő NAT-konfiguráció](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Nem érhető el |
| TCP alaphelyzetbe állítása üresjáratban | [Bármely szabályban elérhető](../articles/load-balancer/load-balancer-tcp-reset.md) | Nem érhető el |
| [Több előtér](../articles/load-balancer/load-balancer-multivip-overview.md) | Bejövő és [kimenő](../articles/load-balancer/load-balancer-outbound-connections.md) | Csak bejövő |
| Felügyeleti műveletek | A legtöbb művelet < 30 másodperc | 60 – 90 + másodperc átlagos |
| SLA | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Nem érhető el | 
