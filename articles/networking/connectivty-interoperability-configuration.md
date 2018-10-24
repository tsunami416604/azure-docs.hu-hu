---
title: 'Együttműködés az ExpressRoute, a Site-to-site VPN és a virtuális hálózatok közötti Társviszony - konfiguráció részletei: Azure Háttérkapcsolathoz együttműködési funkciók |} A Microsoft Docs'
description: Ez az oldal nyújt a konfiguráció részletei az ExpressRoute, a Site-to-site VPN és a virtuális hálózatok közötti Társviszony szolgáltatások együttműködési elemzéséhez használt vizsgálat beállítása.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: d94900b764331c6fff0e0384e6edbebc88ac938b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947264"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-configuration-details"></a>Együttműködés az ExpressRoute, a Site-to-site VPN és a virtuális hálózatok közötti társviszony-létesítési - konfiguráció részletei

Ebben a cikkben vegyük végig a vizsgálatot a telepítő konfigurációs adatait. A tesztelési beállítások áttekintéséhez tekintse meg a [teszt telepítő][Setup]. 

##<a name="spoke-vnet-connectivity-using-vnet-peering"></a>Használatával a virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok közötti küllőkapcsolat

A következő az Azure portal képernyőképe a küllő virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok közötti részleteit jeleníti meg. Konfigurálhatja a virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok közötti lépésenkénti útmutatásért lásd: [kezelése virtuális hálózatok közötti Társviszony][VNet-Config]. Ha azt szeretné, hogy a küllő virtuális hálózat számára az agyi virtuális hálózat csatlakozik az átjárók használatát, ellenőrizni kell *távoli átjárók használata*.

[![1]][1]

A következő az Azure portal képernyőképe az agyi virtuális hálózat virtuális hálózatok közötti társviszony-létesítési részleteit jeleníti meg. Ha azt szeretné, hogy a küllő virtuális hálózat az átjárók használata az agyi virtuális hálózat, ellenőrizni kell *távoli átjárók használata*.

[![2]][2]

##<a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Virtuális hálózatok közötti kapcsolat Site-to-Site VPN használatával ág

Site-to-Site VPN-kapcsolat a Hub és a fiókiroda virtuális hálózatok közötti VPN-átjárók használatával van konfigurálva. Alapértelmezés szerint a 65515 privát ASN értéke a VPN és ExpressRoute-átjárók vannak konfigurálva. A VPN gateway lehetővé teszi, hogy módosíthatja az ASN-érték. A teszt beállítása a következő az Azure portal képernyőképen látható módon az ág virtuális hálózatok közötti VPN-átjáró ASN értéke változik 65516 eBGP a Hub és a fiókiroda virtuális hálózatok közötti útválasztás engedélyezése.


[![3]][3]


##<a name="location-1-on-premises-connectivity-using-expressroute-and-site-to-site-vpn"></a>1. hely helyszíni csatlakoztatása ExpressRoute- és helyek közötti VPN használatával

###<a name="expressroute1-configuration-details"></a>ExpressRoute1 konfiguráció részletei

A következő portál képernyőképen látható az Azure régió 1 ExpressRoute-kapcsolatcsoport konfiguráció 1. hely helyszíni CE útválasztók felé.

[![4]][4]

A következő portál képernyőképen látható a ExpressRoute1 kapcsolatcsoport és az agyi virtuális hálózat közötti kapcsolat konfigurálása.

[![5]][5]

A következő beállítási lehetőségek az elsődleges CE útválasztó (Cisco útválasztókat használják a teszt beállítása CE útválasztóként ASR1000) listája konfigurációs kapcsolatos az ExpressRoute privát társviszony-létesítési kapcsolat. Amikor Site-to-Site VPN és ExpressRoute-kapcsolatcsoportot is vannak konfigurálva, a helyszíni hálózat csatlakoztatása az Azure-bA; párhuzamos Az Azure ExpressRoute-Kapcsolatcsoportok alapértelmezés szerint részesíti előnyben. Aszimmetrikus útválasztás elkerüléséhez a helyi hálózaton kell is érdemes előnyben részesíteni ExpressRoute mind az ExpressRoute- és helyek közötti VPN-n keresztül kapott útvonalak a Site-to-Site VPN-kapcsolaton keresztül. Ez a BGP helyi-szabályozó attribútum használata a következő konfiguráció érhető el. 

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

###<a name="site-to-site-vpn-configuration-details"></a>Site-to-Site VPN-konfiguráció részletei

Az alábbiakban látható a listában, az elsődleges CE útválasztó-konfiguráció kapcsolatos Site-to-Site VPN-kapcsolat:

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

##<a name="location-2-on-premises-connectivity-using-expressroute"></a>ExpressRoute-tal hely 2 használó helyszíni kapcsolatok

Egy második ExpressRoute-kapcsolatcsoport közelebb közelséget hely 2 a helyszínen, a hely 2 helyszíni csatlakozik az agyi virtuális hálózat. A következő portál képernyőképe a második az ExpressRoute-konfigurációját mutatja be.

[![6]][6]

A következő portál képernyőképen látható a kapcsolat konfigurációját, a második ExpressRoute-kapcsolatcsoport és az agyi virtuális hálózat között.

[![7]][7]

A ExpressRoute1 az agyi virtuális hálózat és a hely-1 helyszíni csatlakozik egy távoli virtuális hálózat egy másik Azure-régióban.

[![8]][8]

## <a name="further-reading"></a>További információ

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute- és helyek közötti VPN-kapcsolat használatával területtel

#### <a name="site-to-site-vpn-over-expressroute"></a>Site-to-Site VPN expressroute-on keresztül

Site-to-Site VPN konfigurálható az ExpressRoute Microsoft közvetlenül a Microsoftnak Exchange bizalmas, visszajátszás, hitelességét és integritásának a helyszíni hálózat és az Azure virtuális hálózatok közötti társviszony-létesítésen keresztül. Helyek közötti IPSec VPN konfigurálása bújtatási mód az ExpressRoute a Microsoft társviszony-létesítésen keresztül kapcsolatos további információkért lásd: [Site-to-site VPN ExpressRoute Microsoft-társviszony-létesítésen keresztül][S2S-Over-ExR]. 

A fő szolgáltatásoknak a S2S VPN konfigurálása Microsoft társviszony-létesítésen keresztül az átviteli sebességet. Az IPSec-alagúton keresztül átviteli sebességet a VPN-Átjáró kapacitása korlátozza. A VPN-Átjáró átviteli sebesség, kisebb ExpressRoute átviteli képest. Ilyen esetekben az IPSec-alagút használatával nagy biztonságos forgalmat és a privát társviszony-létesítést az összes többi forgalom segít az ExpressRoute sávszélesség-használat optimalizálása.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-Site VPN biztonságos feladatátvételi útvonalként az expressroute-hoz
Az ExpressRoute kapcsolatcsoport redundáns pár magas rendelkezésre állás biztosítása érdekében érhető el. Konfigurálhatja a georedundáns az ExpressRoute-kapcsolat másik Azure-régióban. Emellett a teszt a telepítés indításához, mint egy adott Azure-régióban, ha feladatátvételi útvonalként az ExpressRoute-kapcsolat használni kívánt megteheti Site-to-Site VPN használatával. Ha ugyanazokat az előtagokat vannak hirdetve az ExpressRoute- és S2S VPN, Azure ExpressRoute S2S VPN-kapcsolaton keresztül részesíti előnyben. Aszimmetrikus útválasztás az ExpressRoute és S2S VPN között elkerülése érdekében a helyszíni hálózati konfiguráció is reciprocate előnyben részesítve – ExpressRoute keresztül S2S VPN-kapcsolat.

Az ExpressRoute és Site-to-Site VPN egyidejű kapcsolatok konfigurálása kapcsolatos további információkért lásd: [ExpressRoute- és helyek közötti együttműködés][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Kiterjeszti a háttérkapcsolathoz küllő virtuális hálózatokhoz és a fiókirodában dolgozó

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Használatával a virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok közötti küllőkapcsolat

Központ-küllő virtuális hálózatok közötti architektúra széles körben használt. A hub egy virtuális hálózatot (VNet) az Azure-ban, amely egy központi csatlakozási pontra van szükség a küllő virtuális hálózatok között, és a helyszíni hálózathoz funkcionál. A küllők az agyhoz, és a számítási feladatok elkülönítésére használhatók virtuális hálózatok. A forgalom a helyszíni adatközpont és a egy expressroute-on vagy VPN-kapcsolaton keresztül a hub között. Az architektúrával kapcsolatos további információkért lásd: [és-Küllős architektúra][Hub-n-Spoke]

Virtuális hálózatok közötti társviszony-létesítés egy adott régión belül lehetővé teszi, hogy a küllő virtuális hálózatok hub virtuális hálózati átjáró (VPN- és ExpressRoute-átjárók) használatával kommunikál távoli hálózatok.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Virtuális hálózatok közötti kapcsolat Site-to-Site VPN használatával ág

Ha azt szeretné, hogy a virtuális hálózatok (különböző régiókban) ágat, és a helyszíni hálózatok egy agyi virtuális hálózat keresztül kommunikálnak egymással, a natív Azure-megoldás a site-to-site VPN-kapcsolat VPN használatával. Egy másik lehetőség, hogy az NVA-útválasztási az agyban.

VPN-átjárók konfigurálásához lásd: [VPN-átjáró konfigurálása][VPN]. Magas rendelkezésre állású NVA telepítése, lásd: [üzembe helyezése magas rendelkezésre állású NVA][Deploy-NVA].

## <a name="next-steps"></a>További lépések

Vezérlési sík elemzés, a teszt beállítása és a nézetek különböző virtuális hálózatot és VLAN a topológia, lásd: [Vezérlősík elemzési][Control-Analysis].

Adatsík adatelemzés a teszt beállítása és az Azure hálózatfigyelési funkciók nézetek: [Adatsík elemzési][Data-Analysis].

Ismerje meg, hány ExpressRoute-kapcsolatcsoporttal csatlakozhat egy ExpressRoute-átjárót vagy ExpressRoute-átjárók számát egy ExpressRoute-kapcsolatcsoporttal csatlakozhat, vagy más skálázási korlátait expressroute további, lásd: [ExpressRoute – gyakori kérdések][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "küllő virtuális hálózat virtuális hálózatok közötti társviszony-létesítés"
[2]: ./media/backend-interoperability/HubVNet-peering.png "agyi virtuális hálózat virtuális hálózatok közötti társviszony-létesítés"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "ág virtuális hálózat VPN-Átjáró konfiguráció"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute1 konfiguráció"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "ExpressRoute1 Hub VNet ExR GW való kapcsolat konfigurációja"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute2 konfiguráció"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "ExpressRoute2 Hub VNet ExR GW való kapcsolat konfigurációja"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "a távoli virtuális hálózat ExR GW ExpressRoute2 kapcsolat konfigurációja"

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




