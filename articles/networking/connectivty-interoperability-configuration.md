---
title: 'Együttműködés az Azure háttérkapcsolati szolgáltatásaiban: Konfigurációs részletek | Microsoft dokumentumok'
description: Ez a cikk az ExpressRoute, a helyek közötti VPN és az Azure-beli virtuális hálózati társviszony-létesítés közötti együttműködés elemzéséhez használható tesztbeállítás konfigurációs részleteit ismerteti.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 9c4a57111566248d3537cab0d9d85c0c3be874a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68335944"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Együttműködés az Azure háttérkapcsolati funkcióiban: A konfiguráció részleteinek tesztelése

Ez a cikk a tesztbeállítás konfigurációs részleteit [ismerteti.][Setup] A tesztbeállítás segítségével elemezheti, hogy az Azure hálózati szolgáltatásai hogyan működnek össze a vezérlősík és az adatsík szintjén.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Küllős virtuális kapcsolat virtuális hálózattal a virtuális hálózatok társviszony-létesítésével

Az alábbi ábra az Azure Virtual Network társviszony-létesítési részleteit mutatja be egy küllővirtuális hálózat (VNet). A virtuális hálózatok közötti társviszony-létesítés beállításáról a [Virtuális hálózatok társviszony-létesítésének kezelése témakörben][VNet-Config]olvashat. Ha azt szeretné, hogy a küllővirtuális hálózat a központi virtuális hálózathoz csatlakoztatott átjárókat használja, válassza a **Távoli átjárók használata**lehetőséget.

[![1]][1]

Az alábbi ábra a virtuális hálózat társviszony-létesítési részleteit mutatja be. Ha azt szeretné, hogy a központi virtuális hálózat engedélyezze a küllővirtuális hálózat számára a hub átjáróinak használatát, jelölje be az **Átjáró átvitelének engedélyezése**jelölőnégyzetet.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Branch VNet-kapcsolat helyek közötti VPN használatával

Az Azure VPN-átjáróvpn-átjáróinak vpn-átjáróival beállíthatja a hub és az ág virtuális hálózatai közötti helyek közötti VPN-kapcsolatot. Alapértelmezés szerint a VPN-átjárók és az Azure ExpressRoute-átjárók **65515-ös**autonóm rendszerszám (ASN) értéket használnak. Módosíthatja az ASN-értéket a VPN-átjáróban. A tesztbeállításokban az elágazó virtuális hálózati VPN-átjáró ASN-értéke **65516-ra** változik, hogy támogassa az eBGP-útválasztást a hub és az ág virtuális hálózatai között.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Helyszíni 1.

### <a name="expressroute-1-configuration-details"></a>Az ExpressRoute 1 konfigurációs adatai

Az alábbi ábra az Azure Region 1 ExpressRoute-áramkör konfigurációját mutatja be a helyszíni, 1.

[![4]][4]

Az alábbi ábra az ExpressRoute 1-kapcsolat konfigurációját mutatja be az ExpressRoute 1-kapcsolat és a központi virtuális hálózat között:

[![5]][5]

Az alábbi lista az ExpressRoute privát társviszony-létesítési kapcsolatelsődleges CE-útválasztó-konfigurációját tartalmazza. (A Cisco ASR1000 útválasztók CE-útválasztóként használatosak a tesztbeállítássorán.) Ha a helyek közötti VPN- és ExpressRoute-áramkörök párhuzamosan vannak konfigurálva egy helyszíni hálózat Azure-hoz való csatlakoztatásához, az Azure alapértelmezés szerint rangsorolja az ExpressRoute-áramkört. Az aszimmetrikus útválasztás elkerülése érdekében a helyszíni hálózatnak az ExpressRoute-kapcsolatot is előnyben kell részesítenie a helyek közötti VPN-kapcsolattal szemben. A következő konfiguráció a BGP helyi **preferencia** attribútumhasználatával állapítja meg a rangsorolást:

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

### <a name="site-to-site-vpn-configuration-details"></a>Helyek közötti VPN-konfiguráció részletei

Az alábbi lista a helyek közötti VPN-kapcsolat elsődleges CE-útválasztó-konfigurációját mutatja be:

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

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Helyszíni 2.

Egy második ExpressRoute-kapcsolat, közelebb a helyszíni 2. Az alábbi ábra a második ExpressRoute-konfigurációt mutatja be:

[![6]][6]

Az alábbi ábra a második ExpressRoute-kapcsolat konfigurációját mutatja be a központi virtuális hálózat között:

[![7]][7]

Az ExpressRoute 1 a központi virtuális hálózatot és a helyszíni 1-es helyet is egy másik Azure-régióban lévő távoli virtuális hálózathoz csatlakoztatja:

[![8]][8]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute és helyek közötti VPN-kapcsolat párhuzamosan

###  <a name="site-to-site-vpn-over-expressroute"></a>Helyek közötti VPN az ExpressRoute-on keresztül

A helyek közötti VPN konfigurálható az ExpressRoute Microsoft társviszony-létesítésével a helyszíni hálózat és az Azure virtuális hálózatok közötti személyes adatcseréhez. Ezzel a konfigurációval bizalmasan, hitelesen és sértetlenül cserélhet adatokat. Az adatcsere is anti-visszajátszás. A helyek közötti IPsec VPN bújtatási módban az ExpressRoute Microsoft társviszony-létesítésével történő konfigurálásáról további információt a Helyek közötti [VPN expressroute-i Microsoft-társviszony-létesítés című][S2S-Over-ExR]témakörben talál. 

A Microsoft társviszony-létesítést használó helyek közötti VPN konfigurálásának elsődleges korlátozása az átviteli érték. Az IPsec-alagúton keresztüli átviteli kapacitást a VPN-átjáró kapacitása korlátozza. A VPN-átjáró átviteli-ventilátor a ExpressRoute átviteli. Ebben a forgatókönyvben az IPsec-alagút használata a rendkívül biztonságos forgalom és a privát társviszony-létesítés használata az összes többi forgalom segít optimalizálni az ExpressRoute sávszélesség-kihasználtsága.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Helyek közötti VPN az ExpressRoute biztonságos feladatátvételi útvonalaként

ExpressRoute szolgál redundáns áramkör pár magas rendelkezésre állás biztosítása érdekében. Georedundáns ExpressRoute-kapcsolatot konfigurálhat különböző Azure-régiókban. Amint azt a tesztbeállítás is mutatja, egy Azure-régión belül, a helyek közötti VPN segítségével hozzon létre egy feladatátvételi útvonalat az ExpressRoute-kapcsolathoz. Ha ugyanazokat az előtagokat az ExpressRoute és a helyek közötti VPN-en keresztül is meghirdeti, az Azure prioritásként kezeli az ExpressRoute-ot. Az ExpressRoute és a helyek közötti VPN közötti aszimmetrikus útválasztás elkerülése érdekében a helyszíni hálózati konfigurációnak az ExpressRoute-kapcsolat használatával is viszonoznia kell, mielőtt a helyek közötti VPN-kapcsolatot használná.

Az ExpressRoute és a helyek közötti VPN-kapcsolatok együtt létező kapcsolatainak konfigurálásáról az ExpressRoute és a helyek közötti [együttélés][ExR-S2S-CoEx]című témakörben talál további információt.

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Háttérkapcsolat kiterjesztése a küllővirtuális hálózatokra és az elágazási helyekre

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Küllős virtuális kapcsolat virtuális hálózattal a virtuális hálózatok társviszony-létesítésével

Hub és küllős virtuális hálózat architektúra széles körben használják. A hub egy virtuális hálózat az Azure-ban, amely a küllővirtuális hálózatok és a helyszíni hálózat közötti kapcsolat központi pontjaként működik. A küllők olyan virtuális hálózatok, amelyek a hubkal egyenrangúak, és amelyek segítségével elkülönítheti a számítási feladatokat. A helyszíni adatközpont és a hub közötti forgalom expresszroute- vagy VPN-kapcsolaton keresztül áramlik. Az architektúráról további információt a [Küllős hálózati topológia megvalósítása az Azure-ban című témakörben talál.][Hub-n-Spoke]

A virtuális hálózatok egy régión belüli társviszony-létesítés, küllővirtuális hálózatok segítségével hub virtuális hálózatok (VPN és ExpressRoute átjárók) a távoli hálózatokkal való kommunikációhoz.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Branch VNet-kapcsolat helyek közötti VPN-kapcsolat használatával

Előfordulhat, hogy a különböző régiókban található és a helyszíni hálózatokban lévő fiókvirtuális hálózatokat egy hub virtuális hálózaton keresztül szeretné kommunikálni egymással. A konfiguráció natív Azure-megoldása a helyek közötti VPN-kapcsolat VPN használatával. Egy másik lehetőség egy hálózati virtuális berendezés (NVA) használata a hubban történő útválasztáshoz.

További információ: [Mi a VPN-átjáró?][VPN] [Deploy a highly available NVA][Deploy-NVA]

## <a name="next-steps"></a>További lépések

Ismerje meg a [vezérlő sík elemzése][Control-Analysis] a teszt beállítás és a nézetek a különböző virtuális hálózatok vagy VLAN-ok a topológia.

Ismerje meg a tesztbeállítás és az Azure hálózati figyelési [funkciónézeteinek adatsík-elemzését.][Data-Analysis]

Lásd az [ExpressRoute gyIK-et:][ExR-FAQ]
-   Ismerje meg, hogy hány ExpressRoute-kapcsolatcsoport csatlakozhat egy ExpressRoute-átjáróhoz.
-   Ismerje meg, hogy hány ExpressRoute-átjárócsatlakozhat egy ExpressRoute-kapcsolathoz.
-   További információ az ExpressRoute egyéb méretezési korlátairól.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "Küllős virtuális hálózat virtuális társviszony-létesítése"
[2]: ./media/backend-interoperability/HubVNet-peering.png "A Hub virtuális hálózat virtuális társviszony-létesítése"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.pngEGY ÁG virtuális "hálózat VPN-átjárókonfigurációja"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute 1 konfiguráció"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Az ExpressRoute 1 kapcsolatkonfigurációja egy központi Virtuálishálózat ExR átjáróhoz"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute 2 konfiguráció"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Az ExpressRoute 2 kapcsolatkonfigurációja egy központi Virtuálishálózat ExR átjáróhoz"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Az ExpressRoute 2 kapcsolatkonfigurációja távoli Virtuálishálózat-exr átjáróval"

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


