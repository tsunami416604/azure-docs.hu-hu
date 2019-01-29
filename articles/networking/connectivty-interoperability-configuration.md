---
title: 'Együttműködés az Azure háttér-kapcsolati funkciók: Konfigurációs részletek |} A Microsoft Docs'
description: Ez a cikk ismerteti a test-beállítás segítségével elemezheti az ExpressRoute, a site-to-site VPN és a virtuális hálózati társviszony az Azure-ban együttműködésével konfigurációs adatait.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4aa594769a3665908f0adce498a4a2bf3a4f4f83
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55189068"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Együttműködés az Azure háttér-kapcsolati funkciók: Tesztelési konfiguráció részletei

Ez a cikk ismerteti a konfigurációs adatait a [beállítások ellenőrzése][Setup]. A teszt beállítása segítségével elemezheti az Azure hálózati szolgáltatások együttműködik a vezérlési sík szint és az adatsík szintnek.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>A virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok közötti küllőkapcsolat

Az alábbi ábra bemutatja az Azure virtuális hálózati társviszony-létesítés részleteinek küllő virtuális hálózat (VNet). Ismerje meg, hogyan állítsa be a társviszony-létesítés két virtuális hálózat között, lásd: [kezelése virtuális hálózatok közötti társviszony-létesítés][VNet-Config]. Ha azt szeretné, hogy a küllő virtuális hálózat számára, amely csatlakozik az agyi virtuális hálózat, válassza ki az átjárók használatát **távoli átjárók használata**.

[![1]][1]

Az alábbi ábra az agyi virtuális hálózat virtuális hálózatok közötti társviszony-létesítési részleteit jeleníti meg. Ha azt szeretné, hogy a küllő virtuális hálózat a hub VNet-átjárók használatára, válassza ki a **távoli átjárók használata**.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Ág virtuális hálózatok közötti kapcsolat egy helyek közötti VPN használatával

A hub és a fiókiroda virtuális hálózatok között helyek közötti VPN-kapcsolat beállítása az Azure VPN Gateway VPN-átjárók használatával. Alapértelmezés szerint a VPN-átjárók és az Azure ExpressRoute-átjáró használata privát autonóm rendszer száma (ASN) érték **65515**. VPN-átjáró ASN értéke módosíthatja. A teszt beállítása az ág virtuális hálózatok közötti VPN-átjáró ASN értéke megváltozott **65516** eBGP a hub és a fiókiroda virtuális hálózatok közötti útválasztást támogatásához.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>A helyszíni hely 1 kapcsolat ExpressRoute- és helyek közötti VPN használatával

### <a name="expressroute-1-configuration-details"></a>1. ExpressRoute-konfiguráció részletei

Az alábbi ábra bemutatja az Azure régió 1 ExpressRoute-kapcsolatcsoport konfiguráció a helyszíni hely 1 ügyfél (CE) peremhálózati útválasztók felé:

[![4]][4]

A következő ábrán látható, a kapcsolat konfigurációját az 1. ExpressRoute-kapcsolatcsoport és az agyi virtuális hálózat között:

[![5]][5]

A következő lista tartalmazza az ExpressRoute privát társviszony-létesítési kapcsolat elsődleges CE útválasztó konfigurációját. (Cisco ASR1000 útválasztók használt CE útválasztóként a vizsgálat beállítása.) Site-to-site VPN és ExpressRoute-Kapcsolatcsoportok úgy vannak konfigurálva, a helyszíni hálózat csatlakoztatása az Azure-ba történő párhuzamos, amikor az Azure alapértelmezés szerint az ExpressRoute-kapcsolatcsoport rangsorolja. Aszimmetrikus útválasztás elkerüléséhez a helyszíni hálózathoz is előnyben részesíti az ExpressRoute-kapcsolat keresztül site-to-site VPN-kapcsolat. A következő konfigurációt a BGP használatával hoz létre rangsorolási **helyi-szabályozó** attribútum:

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

### <a name="site-to-site-vpn-configuration-details"></a>Site-to-site VPN-konfiguráció részletei

Az alábbi lista tartalmazza a helyek közötti VPN-kapcsolat elsődleges CE útválasztó konfigurációját:

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>A helyszíni hely 2 kapcsolat ExpressRoute használatával

Egy második ExpressRoute-kapcsolatcsoport közelebb közelében a helyszíni hely 2, a helyszíni hely 2 csatlakozik az agyi virtuális hálózat. Az alábbi ábra a második az ExpressRoute-konfigurációját mutatja be:

[![6]][6]

A következő ábrán látható, a második ExpressRoute-kapcsolatcsoportot, és az agyi virtuális hálózat közötti kapcsolat konfigurálása:

[![7]][7]

Az ExpressRoute 1 az agyi virtuális hálózat és a helyszíni hely 1 csatlakozik egy távoli virtuális hálózat egy másik Azure-régióban:

[![8]][8]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Területtel ExpressRoute- és helyek közötti VPN-kapcsolat

###  <a name="site-to-site-vpn-over-expressroute"></a>Site-to-site VPN expressroute-on keresztül

A site-to-site VPN ExpressRoute Microsoft társviszony-létesítést úgy, hogy privát módon exchange-adatok a helyszíni hálózat és az Azure virtuális hálózatok közötti használatával konfigurálhatja. Ezzel a konfigurációval adatokat cserélni bizalmas hitelességét és integritását. Adatcsere során a visszajátszás. IPsec helyek közötti VPN konfigurálása bújtatási mód a Microsoft társviszony-létesítési ExpressRoute használatával kapcsolatos további információkért lásd: [Site-to-site VPN ExpressRoute Microsoft társviszony-létesítésen keresztül][S2S-Over-ExR]. 

Az elsődleges korlátozása a Microsoft társviszony-létesítést használó site-to-site VPN konfigurálása az átviteli sebességet. Az IPsec-alagúton keresztül átviteli sebességet a VPN-átjáró kapacitása korlátozza. A VPN gateway teljesítménye is kevesebbet, mint az ExpressRoute átviteli sebességet. Ebben a forgatókönyvben a rendkívül biztonságos forgalmat az IPsec-alagút használatával, és privát társviszony-létesítést az összes többi forgalom használatával segít optimalizálni a ExpressRoute sávszélesség-felhasználás.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-site VPN biztonságos feladatátvételi útvonalként az expressroute-hoz

Az ExpressRoute kapcsolatcsoport redundáns két, magas rendelkezésre állás biztosítása érdekében szolgálja ki. Konfigurálhatja a georedundáns az ExpressRoute-kapcsolat másik Azure-régióban. Is ahogyan az a teszt beállítása egy Azure-régióban is használhatja a site-to-site VPN feladatátvételi útvonalként az ExpressRoute-kapcsolat létrehozása. Ha ugyanazokat az előtagokat vannak hirdetve az ExpressRoute- és a egy site-to-site VPN, az Azure ExpressRoute rangsorolja. Aszimmetrikus útválasztás az ExpressRoute és a site-to-site VPN közötti elkerülése érdekében a helyszíni hálózati konfiguráció is kell reciprocate site-to-site VPN-kapcsolat használata előtt az ExpressRoute-kapcsolat használatával.

Az ExpressRoute és site-to-site VPN egyidejű kapcsolatok konfigurálásával kapcsolatos további információkért lásd: [ExpressRoute és site-to-site párhuzamossági][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Háttér-küllő virtuális hálózatok és a fiókirodában dolgozó csatlakozásának kiterjesztése

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>A virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok közötti küllőkapcsolat

Küllős architektúra virtuális hálózatok közötti szokták gyakran használni. A hub egy Vnetet az Azure-ban, amely egy központi csatlakozási pontra van szükség a küllő virtuális hálózatok között, és a helyszíni hálózathoz funkcionál. A küllők az agyhoz kapcsolódó virtuális hálózatok, és amelyekkel számítási feladatok elkülönítésére. A forgalom a helyszíni adatközpont és a egy expressroute-on vagy VPN-kapcsolaton keresztül a hub között. Az architektúrával kapcsolatos további információkért lásd: [küllős hálózati topológia implementálása az Azure-ban][Hub-n-Spoke].

Virtuális hálózatok közötti társviszony-létesítés egy adott régión belül, a küllő virtuális hálózatok használatával hub virtuális hálózati átjáró (VPN- és ExpressRoute-átjárók) kommunikálni a távoli hálózatokhoz.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Virtuális hálózatok közötti kapcsolat ágban site-to-site VPN használatával

Érdemes lehet virtuális hálózatokat, amelyek a különböző régiókban, és a egy VNet-központon keresztül kommunikálnak egymással a helyszíni hálózatok ág. Ehhez a konfigurációhoz a natív Azure-megoldás közötti VPN használatával site-to-site VPN-kapcsolat. Alternatív, hogy egy hálózati virtuális készüléket (NVA) útválasztás az agyban.

További információkért lásd: [Mi az a VPN-átjáró?] [ VPN] és [üzembe helyezése egy magas rendelkezésre állású nva-t][Deploy-NVA].

## <a name="next-steps"></a>További lépések

Ismerje meg [szabályozhatja az adatsík elemzési] [ Control-Analysis] a teszt beállítása és a nézetek különböző virtuális hálózatok vagy VLAN-okat a topológia.

Ismerje meg [data analysis adatsík] [ Data-Analysis] a teszt beállítása és figyelési nézetei a szolgáltatás az Azure network.

Tekintse meg a [ExpressRoute – gyakori kérdések] [ ExR-FAQ] való:
-   Ismerje meg, hány ExpressRoute-kapcsolatcsoporttal csatlakozhat egy ExpressRoute-átjárót.
-   Ismerje meg, hogy hány ExpressRoute-átjáró egy ExpressRoute-kapcsolatcsoporttal csatlakozhat.
-   Ismerje meg a többi skálázási korlátait expressroute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "küllő virtuális hálózat virtuális hálózatok közötti társviszony-létesítés"
[2]: ./media/backend-interoperability/HubVNet-peering.png "agyi virtuális hálózat virtuális hálózatok közötti társviszony-létesítés"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "ág virtuális hálózatok közötti VPN-átjáró konfigurációját"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute 1-konfigurációt"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "hubra ExR virtuális hálózati átjáró ExpressRoute 1 kapcsolat konfigurációja"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute 2 konfiguráció"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "hubra ExR virtuális hálózati átjáró ExpressRoute 2. a kapcsolat konfigurációját"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "egy távoli virtuális hálózat ExR átjáróhoz ExpressRoute 2 kapcsolat konfigurációja"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


