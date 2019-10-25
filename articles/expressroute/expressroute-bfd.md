---
title: BFD konfigurálása a ExpressRoute-ben – Azure | Microsoft Docs
description: Ez a cikk útmutatást nyújt arról, hogyan konfigurálhatja a BFD (kétirányú továbbítás észlelése) egy ExpressRoute-áramkör privát társának használatával.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 8/17/2018
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: e33e90d988251afde630401bed165a4d3614d2cd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881461"
---
# <a name="configure-bfd-over-expressroute"></a>BFD konfigurálása a ExpressRoute-en keresztül

A ExpressRoute támogatja a kétirányú továbbítási észlelést (BFD) a privát kapcsolaton keresztül. A BFD ExpressRoute-en keresztüli engedélyezésével felgyorsíthatja a Microsoft Enterprise Edge-(MSEE-) eszközök és azon útválasztók közötti kapcsolódási hibák észlelését, amelyeken leállítja a ExpressRoute-áramkört (PE/CE). A ExpressRoute lemondhatja az ügyfél peremhálózati útválasztási eszközein vagy a partner peremhálózati útválasztási eszközein (ha a felügyelt 3. rétegbeli kapcsolat szolgáltatással járt). Ez a dokumentum részletesen ismerteti a BFD szükségességét, valamint azt, hogy hogyan engedélyezhető a BFD a ExpressRoute-en keresztül.

## <a name="need-for-bfd"></a>BFD szükséges

A következő ábra a BFD engedélyezésének előnyeit mutatja be a ExpressRoute-áramkörön keresztül: [![1]][1]

A ExpressRoute áramkört a 2. rétegbeli kapcsolatok vagy a felügyelt 3. rétegbeli kapcsolatok is lehetővé teszik. Mindkét esetben, ha egy vagy több 2. rétegbeli eszköz van a ExpressRoute-kapcsolat elérési útjában, az elérési úton fennálló kapcsolati hibák észlelésének felelőssége a túlterhelt BGP.

A MSEE-eszközökön a BGP életben tartása és a tartási idő általában 60-as és 180 másodpercen belül van konfigurálva. Ezért a kapcsolódási hiba után akár három percet is igénybe vehet a kapcsolódási hibák észlelése és a forgalom másik kapcsolatra váltásakor.

A BGP-időzítők szabályozásához konfigurálja az alacsonyabb BGP-t és a megtartási időt az ügyfél peremhálózati partneri eszközén. Ha a BGP-időzítők nem egyeznek a két társ eszköz között, a társak közötti BGP-munkamenet az alsó időzítő értéket fogja használni. A BGP életben tartása legfeljebb három másodpercre állítható be, a tartási idő pedig több tízezer másodperc. A BGP-időzítők azonban agresszíven kevésbé előnyösek, mert a protokoll intenzíven dolgozza fel a folyamatokat.

Ebben az esetben a BFD segíthet. A BFD alacsony terhelésű kapcsolati hibák észlelését teszi lehetővé egy másodperces időintervallumban. 


## <a name="enabling-bfd"></a>BFD engedélyezése

A BFD alapértelmezés szerint a Msee összes újonnan létrehozott ExpressRoute-társítási felületén van konfigurálva. Ezért a BFD engedélyezéséhez egyszerűen konfigurálnia kell a BFD a PEs/CEs-ben (az elsődleges és a másodlagos eszközön egyaránt). A BFD konfigurálása kétlépéses folyamat: be kell állítania a BFD a csatolón, majd csatolni kell a BGP-munkamenethez.

Alább látható egy példa PE/CE (Cisco IOS XE használatával) konfiguráció. 

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
>A BFD engedélyezése már meglévő privát társak között; alaphelyzetbe kell állítania a társítást. Lásd: ExpressRoute-társítások [alaphelyzetbe állítása][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD időzítő egyeztetése

A BFD-partnerek között a két partner lassabban határozzák meg az átviteli sebességet. A Msee BFD átviteli/fogadási intervallumai 300 ezredmásodpercre vannak beállítva. Bizonyos esetekben az intervallum értéke 750 ezredmásodpercnél nagyobb lehet. A magasabb értékek beállításával az intervallumok hosszabbak lehetnek. de nem rövidebb.

>[!NOTE]
>Ha már konfigurálta a Geo-redundáns ExpressRoute magánhálózati összekapcsolási áramköröket, vagy a helyek közötti IPSec VPN-kapcsolatot használja biztonsági másolatként a ExpressRoute privát társításához; Ha engedélyezi a BFD a privát kapcsolaton keresztül, akkor a ExpressRoute kapcsolódási hibája után gyorsabb a feladatátvétel. 
>

## <a name="next-steps"></a>Következő lépések

További információért és segítségért tekintse meg az alábbi hivatkozásokat:

- [Az ExpressRoute-kapcsolatcsoport létrehozása és módosítása][CreateCircuit]
- [ExpressRoute-áramkör útválasztásának létrehozása és módosítása][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.pnga "BFD meggyorsítja a kapcsolati hibák levonásának idejét"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






