---
title: 'Azure ExpressRoute: A BFD konfigurálása'
description: Ez a cikk a BFD (Kétirányú továbbítás észlelése) konfigurálásának módját ismerteti az ExpressRoute-kapcsolatexpressroute-kapcsolatain.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: 378b639e89ffd46f6b32d7004f934104dd4b5407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064842"
---
# <a name="configure-bfd-over-expressroute"></a>A BFD konfigurálása az ExpressRoute-on keresztül

Az ExpressRoute támogatja a kétirányú továbbítás észlelését (BFD) mind a magán-, mind a Microsoft társviszony-létesítésén. A BFD ExpressRoute-on keresztüli engedélyezésével felgyorsíthatja a kapcsolathibák észlelését a Microsoft Enterprise edge (MSEE) eszközök és azon útválasztók között, amelyeken megszakítja az ExpressRoute-áramkört (CE/PE). Az ExpressRoute-ot az Ügyfél peremhálózati útválasztási eszközökön vagy a Partner Edge útválasztási eszközökön keresztül is lemondhatja (ha a felügyelt Layer 3 kapcsolatszolgáltatással ment). Ez a dokumentum végigvezeti a BFD szükségességén, és a BFD ExpressRoute-on keresztüli engedélyezésének módját.

## <a name="need-for-bfd"></a>A BFD szükségessége

A következő ábra a BFD expressroute-áramkörön keresztüli engedélyezésének előnyeit mutatja be: [![1]][1]

Az ExpressRoute-áramkört Layer 2 vagy felügyelt Layer 3-kapcsolatok segítségével engedélyezheti. Mindkét esetben, ha egy vagy több Layer-2 eszköz van az ExpressRoute-kapcsolat útvonalán, az elérési úton lévő kapcsolathibák észlelésének felelőssége a fedő BGP feladata.

Az MSEE-eszközökön a BGP életben tartás és a tartásidő általában 60, illetve 180 másodpercre van konfigurálva. Ezért a kapcsolat meghibásodása után akár három percet is igénybe vehet a kapcsolatmeghibásodás észlelése és a forgalom váltása alternatív kapcsolatra.

A BGP-időzítők vezérléséhez állítsa be az alacsonyabb BGP keepalive és hold-idő az ügyfél szélén társviszony-létesítő eszközön. Ha a BGP-időzítők nem egyeznek össze a két társviszony-létesítő eszköz között, a társak közötti BGP-munkamenet az alacsonyabb időzítőértéket fogja használni. A BGP keepalive lehet állítani olyan alacsony, mint három másodperc, és a hold-idő, hogy a tíz másodperc. A BGP-időzítők agresszív beállítása azonban kevésbé előnyös, mivel a protokoll folyamatigényes.

Ebben a forgatókönyvben a BFD segíthet. A BFD alacsony szintű kapcsolathiba-észlelést biztosít egy másodperc alatti időintervallumban. 


## <a name="enabling-bfd"></a>A BFD engedélyezése

A BFD alapértelmezés szerint az MSEE-k összes újonnan létrehozott ExpressRoute privát társviszony-létesítő felülete alatt van konfigurálva. Ezért a BFD engedélyezéséhez csak konfigurálnia kell a BFD-t a számítógép-biztonsági és -eken (mind az elsődleges, mind a másodlagos eszközökön). A BFD konfigurálása kétlépésből áll: konfigurálnia kell a BFD-t a kapcsolaton, majd csatolnia kell a BGP-munkamenethez.

Az alábbiakban egy CE/PE (Cisco IOS XE) konfigurációt mutatunk be. 

    interface TenGigabitEthernet2/0/0.150
       description private peering to Azure
       encapsulation dot1Q 15 second-dot1q 150
       ip vrf forwarding 15
       ip address 192.168.15.17 255.255.255.252
       bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
       address-family ipv4 vrf 15
          network 10.1.15.0 mask 255.255.255.128
          neighbor 192.168.15.18 remote-as 12076
          neighbor 192.168.15.18 fall-over bfd
          neighbor 192.168.15.18 activate
          neighbor 192.168.15.18 soft-reconfiguration inbound
       exit-address-family

>[!NOTE]
>A BFD engedélyezése egy már meglévő privát társviszony-létesítés alatt; vissza kell állítania a társviszony-létesítést. Lásd: [ExpressRoute-társviszony-létesítések visszaállítása][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD időzítő egyeztetése

A BFD-partnerek között a két társ lassabbsebessége határozza meg az átviteli sebességet. A BFD-k átviteli/fogadási időköze300 ezredmásodpercre van állítva. Bizonyos esetekben az időköz magasabb, 750 ezredmásodperces értékben állítható be. Magasabb értékek beállításával kényszerítheti ezeket az intervallumokat hosszabbra; de nem rövidebb.

>[!NOTE]
>Ha georedundáns ExpressRoute-áramköröket konfigurált, vagy a helyek közötti IPSec VPN-kapcsolatot használja biztonsági másolatként; a BFD engedélyezése gyorsabb feladatátvételt tesz lehetővé az ExpressRoute-kapcsolat hibája esetén. 
>

## <a name="next-steps"></a>Következő lépések

További információkért vagy segítségért tekintse meg az alábbi hivatkozásokat:

- [Az ExpressRoute-kapcsolatcsoport létrehozása és módosítása][CreateCircuit]
- [ExpressRoute-kapcsolatcsoport útválasztásának létrehozása és módosítása][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD felgyorsítja kapcsolat hiba levonási idő"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






