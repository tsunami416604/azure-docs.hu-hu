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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78202545"
---
| | Standard terheléselosztó | Alapterhelés-elosztó |
| --- | --- | --- |
| [Háttérkészlet mérete](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Legfeljebb 1000 példányt támogat. | Legfeljebb 300 példányt támogat. |
| Háttérkészlet végpontjai | Minden virtuális gép vagy virtuálisgép-méretezés egyetlen virtuális hálózatban. | Virtuális gépek egyetlen rendelkezésre állási csoportban vagy virtuálisgép-méretezési csoportban. |
| [Állapotminták](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Az állapotvizsgálat viselkedése](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | A TCP-kapcsolatok életben maradnak egy példánymintavételen __lefelé és__ az összes mintavételen. | A TCP-kapcsolatok életben maradnak egy példánymintavételen. Minden TCP-kapcsolat megszakad, ha az összes mintavétel nem található. |
| Rendelkezésre állási zónák | Zónaredundáns és zónaszintű előtér-rendszerek a bejövő és a kimenő forgalomhoz. | Nem érhető el |
| Diagnosztika | [Az Azure Monitor többdimenziós metrikák](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Az Azure Monitor naplói](../articles/load-balancer/load-balancer-monitor-log.md) |
| HA portok | [Belső terheléselosztóhoz kapható](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Nem érhető el |
| Alapértelmezés szerint biztonságos | A bejövő folyamatok hoz zárva, kivéve, ha egy hálózati biztonsági csoport engedélyezi. Kérjük, vegye figyelembe, hogy a belső forgalom a virtuális hálózatról a belső terheléselosztó megengedett. | Alapértelmezés szerint megnyitás. A hálózati biztonsági csoport nem kötelező. |
| Kimenő szabályok | [Deklaratív kimenő NAT-konfiguráció](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Nem érhető el |
| TCP alaphelyzetbe állítása tétlenjáratkor | [Bármely szabályon elérhető](../articles/load-balancer/load-balancer-tcp-reset.md) | Nem érhető el |
| [Több előtér](../articles/load-balancer/load-balancer-multivip-overview.md) | Bejövő és [kimenő](../articles/load-balancer/load-balancer-outbound-connections.md) | Csak bejövő |
| Kezelési műveletek | A legtöbb művelet < 30 másodperc | 60-90+ másodperc tipikus |
| SLA | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Nem érhető el | 
