---
title: BFD konfigurálása expressroute-on keresztül |} A Microsoft Docs
description: Ez a dokumentum útmutatásokat nyújt BFD konfigurálásához privát társviszony-létesítési ExpressRoute-Kapcsolatcsoportok keresztül.
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 8/17/2018
ms.author: rambala
ms.openlocfilehash: 97ac49ff5cc50621fd902f77c2543a428c7e8bae
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49384752"
---
# <a name="configure-bfd-over-expressroute"></a>BFD konfigurálása expressroute-on keresztül

Az ExpressRoute privát társviszony-létesítésen keresztül támogatja a kétirányú továbbítás észlelése (BFD). Expressroute-on keresztül BFD engedélyezésével felgyorsíthatja a hibaészlelés hivatkozás között a Microsoft Enterprise peremhálózati (MSEE) eszközök és az útválasztók, amelyen az ExpressRoute-kapcsolatcsoport (PE) leáll. Ügyfélperemhálózat útválaszt eszközöknek vagy Partner útválasztási peremeszközökre ExpressRoute leállíthatja, (Ha a hiba történt a felügyelt 3. rétegbeli kapcsolatot szolgáltatással). Ez a dokumentum végigvezeti BFD és expressroute-on keresztül BFD engedélyezése szükséges.

## <a name="need-for-bfd"></a>BFD szükség

Az alábbi ábra bemutatja az ExpressRoute-kapcsolatcsoporton keresztül BFD engedélyezése előnyeit: [ ![1]][1]

Engedélyezheti az ExpressRoute-kapcsolatcsoport vagy a 2. rétegbeli kapcsolatokat vagy felügyelt 3. rétegbeli kapcsolatokat. Mindkét esetben ha az ExpressRoute kapcsolati útvonal egy vagy több 2. rétegbeli eszközök felelőssége minden olyan hivatkozás hibák észlelése az elérési út rejlik a felsőbb szintű BGP-t.

A MSEE eszközökön BGP életben tartási és fenntartási idő általában határozzák 60 és 180 másodperc jelölik. Ezért az akár időbe hivatkozás hiba után 3 percenként, majd felderítéséhez hivatkozásra a hiba, és váltson a forgalmat a másodlagos kapcsolathoz.

A BGP időzítők alacsonyabb BGP életben tartási és fenntartási-idő az az ügyfél társviszony-létesítési edge-eszköz konfigurálásával szabályozhatja. Ha a BGP időzítők nem egyeznek a két társviszony-létesítési eszközök között, a BGP-munkamenetben az egyenrangú társak közötti alacsonyabb időzítő értékét használja. A BGP életben tartási akár három másodperc alatt, és a visszatartás idő több tíz másodpercet sorrendjében is beállítható. Azonban időzítőket BGP kevésbé agresszív előnyös, mert a protokoll az intenzív folyamat.

Ebben a forgatókönyvben BFD segítségével. BFD biztosít alacsony terheléssel járó hivatkozás kiszűrésénél az adott subsecond idő alatt. 


## <a name="enabling-bfd"></a>BFD engedélyezése

BFD alapértelmezés szerint minden újonnan létrehozott ExpressRoute privát társviszony-létesítési illesztői az msee-k van konfigurálva. Ezért BFD engedélyezéséhez szüksége BFD csak a PEs konfigurálása. Konfigurálás BFD két lépésből álló folyamat: kell engedélyezéséhez konfigurálja a BFD és összekapcsolása a BGP-munkamenetben.

Az alábbiakban látható egy példa konfiguráció látható előtelepítési környezet (Cisco IOS XE használatával). 

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
>Alapján egy már meglévő magánhálózati társviszony-létesítés; BFD engedélyezése a társviszony-létesítés alaphelyzetbe kell. Lásd: [új ExpressRoute-társviszony][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD időzítő egyeztetése

Közötti BFD kapcsolatba szaktársakkal a két társaknak lassabb határozza meg, az átviteli sebességet. Msee BFD átviteli/fogadása időközök 300 ezredmásodpercben vannak állítva. A magasabb értékek beállításával kényszerítheti a hosszabb; intervallumok de nem rövidebb.

>[!NOTE]
>Ha konfigurálta a georedundáns ExpressRoute privát társviszony-létesítési Kapcsolatcsoportok, vagy használja a helyek közötti IPSec VPN kapcsolatot tartalékként az ExpressRoute privát társviszony-létesítés; a privát társviszony-létesítésen keresztül BFD engedélyezése segít gyorsabb ExpressRoute-kapcsolat hibát követő feladatátvétel. 
>

## <a name="next-steps"></a>További lépések

További információ vagy a Súgó tekintse meg az alábbi hivatkozásokat:

- [Létrehozása és módosítása egy ExpressRoute-kapcsolatcsoporttal][CreateCircuit]
- [Útválasztás az ExpressRoute-kapcsolatcsoport létrehozása vagy módosítása][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD felgyorsítja hiba levonása kötésidő"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






