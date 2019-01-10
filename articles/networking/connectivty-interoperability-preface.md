---
title: 'Együttműködés az Azure háttér-kapcsolati funkciók: Beállítások ellenőrzése |} A Microsoft Docs'
description: Ez a cikk ismerteti egy teszt beállítása segítségével elemezheti az együttműködés között ExpressRoute, a site-to-site VPN és a virtuális hálózati társviszony az Azure-ban.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4e077e496479d146306bd301f303b4e8c0f97d05
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191870"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Együttműködés az Azure háttér-kapcsolati funkciók: Teszt beállítása

Ez a cikk ismerteti egy teszt beállítása segítségével elemezheti az Azure hálózati szolgáltatások együttműködik a vezérlési sík szint és az adatsík szintnek. Nézzük röviden, az Azure hálózati összetevők:

-   **Az Azure ExpressRoute**: Magánhálózati társviszony-létesítés az Azure ExpressRoute használatával privát IP-címterek a helyszíni hálózat közvetlenül csatlakozhat az Azure Virtual Network üzemelő példányok. Az ExpressRoute segítségével nagyobb sávszélességet és a egy privát kapcsolaton érhető el. Számos ExpressRoute leggazdaságosabb partnere biztosít SLA-k az ExpressRoute-kapcsolat. További információ az ExpressRoute és az ExpressRoute konfigurálása: [ExpressRoute bemutatása][ExpressRoute].
-   **Site-to-site VPN**: Azure VPN Gateway as a site-to-site VPN segítségével biztonságosan csatlakozzon egy helyszíni hálózat az Azure-ba, az interneten keresztül vagy az ExpressRoute használatával. Ismerje meg, hogyan csatlakozhat az Azure site-to-site VPN konfigurálása, lásd: [VPN-átjáró konfigurálása][VPN].
-   **Virtuális hálózatok közötti társviszony**: Használja a virtuális hálózatok (VNet) közötti társviszony az Azure Virtual Network virtuális hálózatok közötti kapcsolatot. Virtuális hálózatok közötti társviszony-létesítés kapcsolatos további információkért tekintse meg a [virtuális hálózatok közötti társviszony arra vonatkozó útmutató,][VNet].

## <a name="test-setup"></a>Teszt beállítása

Az alábbi ábra mutatja be a teszt beállítása:

[![1]][1]

A teszt beállítása áll majd az agyi virtuális hálózat az Azure-régió 1. Az agyi virtuális hálózat más hálózatokhoz csatlakozik a következő módon:

-   Az agyi virtuális hálózat csatlakozik a küllő virtuális hálózat virtuális hálózatok közötti társviszony segítségével. A küllő virtuális hálózatok közötti átjárók távoli hozzáféréssel rendelkezik az agyi virtuális hálózat.
-   Az agyi virtuális hálózat csatlakozik az ág VNet site-to-site VPN-en keresztül. A kapcsolat eBGP használatával útvonalakat cserél.
-   Az agyi virtuális hálózat ExpressRoute privát társviszony-létesítési as az elsődleges elérési út használatával a helyszíni hely 1 hálózathoz csatlakozik. A biztonsági mentési elérési helyek közötti VPN-kapcsolatot használ. Ez a cikk többi hogy tekintse meg az ExpressRoute-kapcsolatcsoport ExpressRoute 1. Az ExpressRoute-Kapcsolatcsoportok alapértelmezés szerint adja meg a redundáns kapcsolat magas rendelkezésre állás érdekében. Az ExpressRoute-1 a másodlagos ügyfél (CE) peremhálózati útválasztó alkapcsolat, amely az arcokat a másodlagos Microsoft Enterprise peremhálózati útválasztóhoz (MSEE) le van tiltva. A piros vonalat az előző ábrán a beágyazott dupla nyíl felett a letiltott CE útválasztó alkapcsolat jelöli.
-   Az agyi virtuális hálózat egy másik ExpressRoute privát társviszony-létesítés használatával a helyszíni hely 2 hálózathoz csatlakozik. Ez a cikk a többi nevezzük a második az ExpressRoute-kapcsolatcsoport ExpressRoute 2.
-   Az ExpressRoute 1 mind az agyi virtuális hálózat és a helyszíni hely 1 hálózati is egy távoli virtuális hálózat az Azure-régió 2 kapcsolódik.

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Területtel ExpressRoute- és helyek közötti VPN-kapcsolat

###  <a name="site-to-site-vpn-over-expressroute"></a>Site-to-site VPN expressroute-on keresztül

A site-to-site VPN ExpressRoute Microsoft társviszony-létesítést úgy, hogy privát módon exchange-adatok a helyszíni hálózat és az Azure virtuális hálózatok közötti használatával konfigurálhatja. Ezzel a konfigurációval adatokat cserélni bizalmas hitelességét és integritását. Adatcsere során a visszajátszás. IPsec helyek közötti VPN konfigurálása bújtatási mód a Microsoft társviszony-létesítési ExpressRoute használatával kapcsolatos további információkért lásd: [Site-to-site VPN ExpressRoute Microsoft társviszony-létesítésen keresztül] [S2S-Over-ExR]. 

Az elsődleges korlátozása a Microsoft társviszony-létesítést használó site-to-site VPN konfigurálása az átviteli sebességet. Az IPsec-alagúton keresztül átviteli sebességet a VPN-átjáró kapacitása korlátozza. A VPN gateway teljesítménye is kevesebbet, mint az ExpressRoute átviteli sebességet. Ebben a forgatókönyvben a rendkívül biztonságos forgalmat az IPsec-alagút használatával, és privát társviszony-létesítést az összes többi forgalom használatával segít optimalizálni a ExpressRoute sávszélesség-felhasználás.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-site VPN biztonságos feladatátvételi útvonalként az expressroute-hoz

Az ExpressRoute kapcsolatcsoport redundáns két, magas rendelkezésre állás biztosítása érdekében szolgálja ki. Konfigurálhatja a georedundáns az ExpressRoute-kapcsolat másik Azure-régióban. Is ahogyan az a teszt beállítása egy Azure-régióban is használhatja a site-to-site VPN feladatátvételi útvonalként az ExpressRoute-kapcsolat létrehozása. Ha ugyanazokat az előtagokat vannak hirdetve az ExpressRoute- és a egy site-to-site VPN, az Azure ExpressRoute rangsorolja. Aszimmetrikus útválasztás az ExpressRoute és a site-to-site VPN közötti elkerülése érdekében a helyszíni hálózati konfiguráció is kell reciprocate site-to-site VPN-kapcsolat használata előtt az ExpressRoute-kapcsolat használatával.

Az ExpressRoute és site-to-site VPN egyidejű kapcsolatok konfigurálásával kapcsolatos további információkért lásd: [ExpressRoute- és helyek közötti együttműködés] [ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Háttér-küllő virtuális hálózatok és a fiókirodában dolgozó csatlakozásának kiterjesztése

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>A virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok közötti küllőkapcsolat

Küllős architektúra virtuális hálózatok közötti szokták gyakran használni. A hub egy Vnetet az Azure-ban, amely egy központi csatlakozási pontra van szükség a küllő virtuális hálózatok között, és a helyszíni hálózathoz funkcionál. A küllők az agyhoz kapcsolódó virtuális hálózatok, és amelyekkel számítási feladatok elkülönítésére. A forgalom a helyszíni adatközpont és a egy expressroute-on vagy VPN-kapcsolaton keresztül a hub között. Az architektúrával kapcsolatos további információkért lásd: [az Azure-ban, egy küllős hálózati topológia implementálása] [n-Küllős].

Virtuális hálózatok közötti társviszony-létesítés egy adott régión belül, a küllő virtuális hálózatok használatával hub virtuális hálózati átjáró (VPN- és ExpressRoute-átjárók) kommunikálni a távoli hálózatokhoz.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Virtuális hálózatok közötti kapcsolat ágban site-to-site VPN használatával

Érdemes lehet virtuális hálózatokat, amelyek a különböző régiókban, és a egy VNet-központon keresztül kommunikálnak egymással a helyszíni hálózatok ág. A natív Azure-megoldás a cofiguration a site-to-site VPN-kapcsolat egy VPN-en keresztül. Alternatív, hogy egy hálózati virtuális készüléket (NVA) útválasztás az agyban.

További információkért lásd: [Mi az a VPN-átjáró?] [ VPN] [központi telepítés magas rendelkezésre állású NVA] és [NVA-telepítés].

## <a name="next-steps"></a>További lépések

Ismerje meg [konfigurációs részletek] [ Configuration] a tesztelési topológia.

További tudnivalók: [vezérlési sík elemzési] [vezérlőelem-elemzést] a teszt beállítása és a nézetek különböző virtuális hálózatok vagy VLAN-okat a topológia.

Ismerje meg a [adatsík adatelemzés] [adatelemzési] a teszt beállítása és figyelési nézetei a szolgáltatás az Azure network.

Tekintse meg a [ExpressRoute – gyakori kérdések] [ExR – gyakori kérdések] a:
-   Ismerje meg, hány ExpressRoute-kapcsolatcsoporttal csatlakozhat egy ExpressRoute-átjárót.
-   Ismerje meg, hogy hány ExpressRoute-átjáró egy ExpressRoute-kapcsolatcsoporttal csatlakozhat.
-   Ismerje meg a többi skálázási korlátait expressroute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "a test-topológia ábrája"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: connectivty-interoperability-configuration.md
[Vezérlőelem-elemzést] connectivty-együttműködés – vezérlő – plane.md [adatelemzési]: connectivty-együttműködés – adatok-plane.md [ExR – gyakori kérdések]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs [S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering [ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager [n-Küllős]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke [ Üzembe helyezése NVA-]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-haa


