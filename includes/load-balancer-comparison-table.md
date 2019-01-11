---
title: fájl belefoglalása
description: fájl belefoglalása
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 01/09/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: c4989016d31880e1c1990c0eb46091c8f50018bc
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54211852"
---
| | Standard termékváltozat | Alapszintű termékváltozat |
| --- | --- | --- |
| Háttérkiszolgáló-készlet mérete | Akár 1000 példányt támogat. | Akár 100 példányt támogat. |
| Háttérbeli címkészlet végpontok | bármelyik virtuális gépet, egyetlen virtuális hálózattal, programhoz való Blend alkalmazást a virtuális gépek rendelkezésre állási csoportok, köztük a virtuálisgép-méretezési csoport állítja be. | Állítsa be egy egyetlen rendelkezésre állási csoport vagy a virtuális gép méretezési csoportban lévő virtuális gépek. |
| [Állapotminták](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Az állapotfigyelő mintavételező le viselkedés](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP-kapcsolatok a példány mintavételi le életben maradjon __és__ az összes mintavételek le. | A példány mintavételi le életben maradjon, TCP-kapcsolatokat. Összes TCP-kapcsolatok leállítja az összes mintavételek működnek. |
| Rendelkezésre állási zónák | A Standard Termékváltozat, a bejövő és kimenő, és a zónaszintű és zónaredundáns az előtérrendszerek a kimenő forgalom leképezések stabilitást biztosít az zóna hiba, a zónák közötti terheléselosztást. | Nem érhető el. |
| Diagnosztika | Az Azure Monitor bájt és csomag számlálókat, egészségügyi többdimenziós metrikák mintavételi állapota, (TCP SZIN) kapcsolódási kísérletek, kimenő kapcsolat állapota (SNAT sikeres és sikertelen folyamatok), aktív adatsík mérések | Az Azure Log Analytics nyilvános Load Balancer csak, SNAT Erőforrásfogyás riasztást, háttérbeli címkészlet állapotfigyelő száma. |
| Magas rendelkezésre ÁLLÁS portok | Belső terheléselosztó | Nem érhető el. |
| Alapértelmezés szerint biztonságossá tétele | Nyilvános IP-cím, nyilvános Load Balancer végpontok, a belső Load Balancer végpontok zárva a bejövő forgalom, kivéve, ha egy hálózati biztonsági csoport is szerepel az engedélyezési listán. | Nyissa meg alapértelmezettként, a hálózati biztonsági csoport nem kötelező. |
| [Kimenő kapcsolatok](../articles/load-balancer/load-balancer-outbound-connections.md) | Explicit módon meghatározhatja a kimenő NAT-készlet alapú [kimenő szabályok](../articles/load-balancer/load-balancer-outbound-rules-overview.md). A betöltés terheléselosztási szabályt az elutasítás kiszolgálónként több előtérrendszer is használhatja. Egy kimenő forgatókönyv _kell_ explicit módon hozhatók létre a virtuális gép, rendelkezésre állási csoport, virtuálisgép-méretezési kimenő kapcsolat használatára.  Virtuális hálózati Szolgáltatásvégpontok kimenő kapcsolat definiálása nélkül érhető el, és nem számítanak bele a feldolgozott adatok felé.  Bármely nyilvános IP-címeket, beleértve a nem érhető el, mint a virtuális hálózati Szolgáltatásvégpontokkal az Azure PaaS-szolgáltatások keresztül kimenő kapcsolat és a feldolgozott adatok felé számát el kell érni. Ha csak egy belső terheléselosztó szolgálja ki a virtuális gépek, a rendelkezésre állási csoportot, vagy a virtuálisgép-méretezési csoportot, kimenő kapcsolatok alapértelmezett SNAT használatával nem érhetők el; használjon [kimenő szabályok](../articles/load-balancer/load-balancer-outbound-rules-overview.md) helyette. Kimenő SNAT programozás az átviteli protokoll adott protokoll, a bejövő terheléselosztási szabály alapján. | Egyetlen előtér véletlenszerűen kiválasztott, amikor több előtérrendszer jelen.  Belső Load Balancer szolgálja ki egy virtuális gépet, ha rendelkezésre állási csoportban, vagy virtuális gép méretezési, SNAT használt alapértelmezett. |
| [Kimenő szabályok](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Deklaratív kimenő NAT-konfigurációt található, nyilvános IP-címeket vagy nyilvános IP-előtagjai vagy mindkettő használatával konfigurálható kimenő üresjárati időkorlát, egyéni SNAT portkiosztással | Nem érhető el. |
|  [Az üresjárati TCP-visszaállítás](../articles/load-balancer/load-balancer-tcp-reset.md) | Engedélyezze a TCP üresjárati időtúllépés miatt (TCP ÜZE) alaphelyzetbe állítása a szabályok | Nem érhető el |
| [Több előtérrendszer](../articles/load-balancer/load-balancer-multivip-overview.md) | Bejövő és [kimenő](../articles/load-balancer/load-balancer-outbound-connections.md) | Csak bejövő |
| Kezelési műveletek | A legtöbb műveletek < 30 másodperc | 60 – 90 másodperc jellemző. |
| SLA | 99,99 %-a két kifogástalan állapotú virtuális gépek elérési útjához. | Nem alkalmazható. | 
| Díjszabás | Bejövő szabályok száma alapján a feldolgozott adatok, a kimenő társított erőforrás.  | Díjmentes. |
|  |  |  |
