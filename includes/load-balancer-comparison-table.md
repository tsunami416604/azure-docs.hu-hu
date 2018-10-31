---
title: fájl belefoglalása
description: fájl belefoglalása
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 10/29/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 5517cd236c503edce88ab45edbeff5604ef9322b
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50254551"
---
| | Standard termékváltozat | Alapszintű termékváltozat |
| --- | --- | --- |
| Háttérkiszolgáló-készlet mérete | Támogatja az akár 1000 példányt | Akár 100 példányt támogat. |
| Háttérbeli címkészlet végpontok | bármelyik virtuális gépet, egyetlen virtuális hálózattal, programhoz való Blend alkalmazást a virtuális gépek rendelkezésre állási csoportok, köztük a virtuálisgép-méretezési csoport állítja be. | Állítsa be egy egyetlen rendelkezésre állási csoport vagy a virtuális gép méretezési csoportban lévő virtuális gépek. |
| [Állapotadat-mintavételek](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Az állapotfigyelő mintavételező le viselkedés](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP-kapcsolatok a példány mintavételi le életben maradjon __és__ az összes mintavételek le. | A példány mintavételi le életben maradjon, TCP-kapcsolatokat. Összes TCP kapcsolatok teljes az összes mintavételek működnek. |
| Rendelkezésre állási zónák | A Standard Termékváltozat, a bejövő és kimenő, és a zónaszintű és zónaredundáns az előtérrendszerek a kimenő forgalom leképezések stabilitást biztosít az zóna hiba, a zónák közötti terheléselosztást. | Nincs |
| Diagnosztika | Az Azure Monitor bájt és csomag számlálókat, egészségügyi többdimenziós metrikák mintavételi állapota, (TCP SZIN) kapcsolódási kísérletek, kimenő kapcsolat állapota (SNAT sikeres és sikertelen folyamatok), aktív adatsík mérések | Az Azure Log Analytics nyilvános Load Balancer csak, SNAT Erőforrásfogyás riasztást, háttérbeli címkészlet állapotfigyelő száma. |
| Magas rendelkezésre ÁLLÁS portok | Belső terheléselosztó | Nem érhető el |
| Alapértelmezés szerint biztonságossá tétele | Az alapértelmezett bejövő lezárt, a nyilvános IP-cím és a Load Balancer végpontok és a egy hálózati biztonsági csoport explicit módon haladjon a forgalom az engedélyezési listához. | Alapértelmezett nyissa meg a hálózati biztonsági csoport nem kötelező. |
| [Kimenő kapcsolatok](../articles/load-balancer/load-balancer-outbound-connections.md) | Explicit módon meghatározhatja a kimenő NAT-készlet alapú [kimenő szabályok](../articles/load-balancer/load-balancer-outbound-rules-overview.md). A betöltés terheléselosztási szabályt az elutasítás kiszolgálónként több előtérrendszer is használhatja. Egy kimenő forgatókönyv _kell_ explicit módon hozhatók létre a virtuális gép kimenő kapcsolat használatára.  Virtuális hálózati Szolgáltatásvégpontok kimenő kapcsolat nélkül érhető el, és nem számítanak bele a feldolgozott adatok felé.  Bármely nyilvános IP-címeket, beleértve a nem érhető el, mint a virtuális hálózati Szolgáltatásvégpontokkal az Azure PaaS-szolgáltatások keresztül kimenő kapcsolat és a feldolgozott adatok felé számát el kell érni. Ha csak egy belső terheléselosztó szolgálja ki a virtuális gépek, alapértelmezett SNAT keresztül kimenő kapcsolatok nem érhetők el; használjon [kimenő szabályok](../articles/load-balancer/load-balancer-outbound-rules-overview.md) helyette. Kimenő SNAT programozás az átviteli protokoll adott protokoll, a bejövő terheléselosztási szabály alapján. | Egyetlen előtér véletlenszerűen kiválasztott, amikor több előtérrendszer jelen.  Belső Load Balancer szolgálja ki a virtuális gépek, alapértelmezett SNAT használ. |
| [Kimenő szabályok](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Deklaratív kimenő NAT-konfigurációt található, beleértve a mely nyilvános IP-cím vagy nyilvános IP-előtag, kimenő üresjárat időkorlátja konfigurálható, egyéni SNAT portkiosztással | Nem érhető el |
|  [Az üresjárati TCP-visszaállítás](../articles/load-balancer/load-balancer-tcp-reset.md) | Engedélyezze a TCP üresjárati időtúllépés miatt (TCP ÜZE) alaphelyzetbe állítása a szabályok | Nem érhető el |
| [Több előtérrendszer](../articles/load-balancer/load-balancer-multivip-overview.md) | Bejövő és [kimenő](../articles/load-balancer/load-balancer-outbound-connections.md) | Csak bejövő |
| Kezelési műveletek | A legtöbb műveletek < 30 másodperc | 60 – 90 másodperc jellemző. |
| SLA | 99,99 %-a két kifogástalan állapotú virtuális gépek elérési útjához. | [A virtuális gép SLA implicit](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/). | 
| Díjszabás | Feldolgozott adatok bejövő szabályok száma alapján, vagy kimenő társított erőforrás.  | díjmentes |
|  |  |  |
