---
title: fájl belefoglalása
description: fájl belefoglalása
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 9/26/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 74835734a1dd37e07efa96fd3aa76fc0d349e47f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47454478"
---
| | Standard termékváltozat | Alapszintű termékváltozat |
| --- | --- | --- |
| Háttérkiszolgáló-készlet mérete | Támogatja az akár 1000 példányt | Akár 100 példányt támogat. |
| Háttérbeli címkészlet végpontok | bármelyik virtuális gépet, egyetlen virtuális hálózattal, programhoz való Blend alkalmazást a virtuális gépek rendelkezésre állási csoportok, köztük a virtuálisgép-méretezési csoport állítja be. | Állítsa be egy egyetlen rendelkezésre állási csoport vagy a virtuális gép méretezési csoportban lévő virtuális gépek. |
| [Állapotadat-mintavételek](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Az állapotfigyelő mintavételező le viselkedés](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP-kapcsolatok a példány mintavételi le életben maradjon __és__ az összes mintavételek le. | A példány mintavételi le életben maradjon, TCP-kapcsolatokat. Összes TCP-kapcsolat leállítása az összes mintavételek le. |
| Rendelkezésre állási zónák | Az alapszintű Termékváltozatban a zónaszintű és zónaredundáns az előtérrendszer bejövő és kimenő, kimenő forgalom leképezések stabilitást biztosít az zóna hiba, zónák közötti terheléselosztást. | Nincs |
| Diagnosztika | Az Azure Monitor bájt és csomag számlálókat, egészségügyi többdimenziós metrikák mintavételi állapota, (TCP SZIN) kapcsolódási kísérletek, kimenő kapcsolat állapota (SNAT sikeres és sikertelen folyamatok), aktív adatsík mérések | Az Azure Log Analytics nyilvános Load Balancer csak, SNAT Erőforrásfogyás riasztást, háttérbeli címkészlet állapotfigyelő száma. |
| Magas rendelkezésre ÁLLÁS portok | Belső terheléselosztó | Nem érhető el |
| Alapértelmezés szerint biztonságossá tétele | Alapértelmezett bejövő nyilvános IP-cím és a terheléselosztó-végpontokhoz zárva, és a egy hálózati biztonsági csoportot kell használni kifejezetten engedélyezett a forgalom áramlását. | Alapértelmezett nyissa meg a hálózati biztonsági csoport nem kötelező. |
| [Kimenő kapcsolatok](../articles/load-balancer/load-balancer-outbound-connections.md) | Explicit módon meghatározhatja a kimenő NAT-készlet alapú [kimenő szabályok](../articles/load-balancer/load-balancer-outbound-rules-overview.md). A betöltés terheléselosztási szabályt az elutasítás kiszolgálónként több előtérrendszer is használhatja. Egy kimenő forgatókönyv _kell_ explicit módon hozhatók létre a virtuális gép nem tudja használni a kimenő kapcsolatot.  Virtuális hálózati Szolgáltatásvégpontok kimenő kapcsolat nélkül érhető el, és nem beleszámít a feldolgozott adatokat.  Bármely nyilvános IP-címeket, beleértve a nem érhető el, mint a virtuális hálózati Szolgáltatásvégpontokkal az Azure PaaS-szolgáltatások keresztül kimenő kapcsolat és a feldolgozott adatok felé számát el kell érni. Ha csak egy belső terheléselosztó szolgálja ki a virtuális gépek, alapértelmezett SNAT keresztül kimenő kapcsolatok nem érhetők el; használjon [kimenő szabályok](../articles/load-balancer/load-balancer-outbound-rules-overview.md) helyette. Kimenő SNAT programozás az átviteli protokoll adott protokoll, a bejövő terheléselosztási szabály alapján. | Egyetlen előtér véletlenszerűen kiválasztott, amikor több előtérrendszer jelen.  Belső Load Balancer szolgálja ki a virtuális gépek, alapértelmezett SNAT használ. |
| [Kimenő szabályok](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Deklaratív kimenő NAT-konfiguráció található, többek között, amelyek nyilvános IP-cím vagy nyilvános IP prefix(es), konfigurálható kimenő üresjárati időkorlát, egyéni SNAT foglalási port | Nem érhető el |
|  [Az üresjárati TCP-visszaállítás](../articles/load-balancer/load-balancer-tcp-reset.md) | Engedélyezze a TCP üresjárati időtúllépés miatt (TCP ÜZE) alaphelyzetbe állítása a szabályok | Nem érhető el |
| [Több előtérrendszer](../articles/load-balancer/load-balancer-multivip-overview.md) | Bejövő és [kimenő](../articles/load-balancer/load-balancer-outbound-connections.md) | Csak bejövő |
| Kezelési műveletek | A legtöbb műveletek < 30 másodperc | 60 – 90 másodperc jellemző. |
| SLA | 99,99 %-a két kifogástalan állapotú virtuális gépek elérési útjához. | [A virtuális gép SLA implicit](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/). | 
| Díjszabás | Feldolgozott adatok bejövő szabályok száma alapján, számlázunk ki vagy kimenő társított erőforrás.  | díjmentes |
|  |  |  |
