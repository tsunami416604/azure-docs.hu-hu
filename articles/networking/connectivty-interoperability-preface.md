---
title: 'Az ExpressRoute, a Site-to-site VPN és a virtuális hálózatok közötti Társviszony - együttműködési beállítások ellenőrzése: Azure Háttérkapcsolathoz együttműködési funkciók |} A Microsoft Docs'
description: Ez az oldal nyújt egy teszt díjszabásáért együttműködési ExpressRoute, a Site-to-site VPN és a virtuális hálózatok közötti Társviszony funkciók használatával.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: e859a0a3ac35a9d9f2dab579b7609192e599f90f
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947272"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-setup"></a>Együttműködés az ExpressRoute, a Site-to-site VPN és a virtuális hálózatok közötti társviszony-létesítési - teszt beállítása
Ebben a cikkben hozzunk azonosítása egy teszt beállítás, amelyek segítségével elemezheti a hogyan a különböző szolgáltatásokat tudjanak működni egymással mindkét a vezérlési sík és adatsík szintjén. Előtt megvizsgálja a test-telepítés, rövid időre tekintse meg ezeket a különböző Azure hálózati funkciókat jelenti be.

ExpressRoute: ExpressRoute használatával privát társviszony-létesítés, közvetlenül csatlakozhat a magánhálózati IP-címterek a helyszíni hálózat az Azure-beli virtuális környezetekben.  Az ExpressRoute segítségével érheti el nagyobb sávszélességet és a privát kapcsolat. Nincsenek számos ExpressRoute leggazdaságosabb partnerek, akik az ExpressRoute-kapcsolat SLA-val. ExpressRoute- és annak konfigurálásával kapcsolatos további információkért lásd: [ExpressRoute bemutatása][ExpressRoute]

Helyek közötti VPN: Biztonságosan a helyszíni hálózat csatlakoztatása az Azure-bA interneten vagy expressroute-on keresztül, a Site-to-Site (S2S) VPN lehetőség érhető el. S2S VPN konfigurálása az Azure-ba történő összekapcsolására vonatkozó kapcsolatos további információkért lásd: [VPN-átjáró konfigurálása][VPN]

Virtuális hálózatok közötti társviszony-létesítési: Virtuális hálózatok közötti társviszony érhető el a kapcsolatot a virtuális hálózatok (Vnetek) között. Virtuális hálózatok közötti társviszony-létesítés kapcsolatos további információkért lásd: [virtuális hálózatok közötti Társviszony-oktatóanyag][VNet].

##<a name="test-setup"></a>Teszt beállítása

Az alábbi ábra szemlélteti a vizsgálat beállítása.

[![1]][1]

A teszt beállítása a center darab az agyi virtuális hálózat az Azure-régió 1. Az agyi virtuális hálózat következő csatlakozik a különböző hálózatok:

1.  A küllő virtuális hálózat virtuális hálózatok közötti társviszony útján. A küllő virtuális hálózatok mindkét átjáró távoli hozzáféréssel rendelkezik az agyi virtuális hálózat.
2.  Fiókirodai hálózat Site-to-Site VPN-en keresztül. A kapcsolat eBGP használatával útvonalakat cserél.
3.  Hely-1-re a helyszíni hálózat ExpressRoute privát társviszony-létesítési as az elsődleges elérési út és a biztonsági mentési elérési helyek közötti VPN-kapcsolat használatával. Ez a dokumentum többi részén hivatkozunk az ExpressRoute-kapcsolatcsoport ExpressRoute1 szerint. Alapértelmezés szerint az ExpressRoute-Kapcsolatcsoportok redundáns kapcsolat nyújtása magas rendelkezésre állás. A ExpressRoute1 szemben a másodlagos MSEE a másodlagos CE útválasztó alkapcsolat le van tiltva. Ez jelzi a fenti diagramon a beágyazott dupla nyíl felett egy piros vonal használatával.
4.  Hely – 2. a helyszíni hálózaton keresztül egy másik ExpressRoute privát társviszony-létesítés. Ez a dokumentum többi részén hivatkozunk a második az ExpressRoute-kapcsolatcsoport ExpressRoute2 szerint.
5.  ExpressRoute1 az agyi virtuális hálózat és a hely-1 a helyszínen is csatlakozik egy távoli virtuális hálózat az Azure-régió 2.

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

A tesztelési topológia a konfigurációs részletek: [konfigurációs részletek][Configuration].

Vezérlési sík elemzés, a teszt beállítása és a nézetek különböző virtuális hálózatot és VLAN a topológia, lásd: [Vezérlősík elemzési][Control-Analysis].

Adatsík adatelemzés a teszt beállítása és az Azure hálózatfigyelési funkciók nézetek: [Adatsík elemzési][Data-Analysis].

Ismerje meg, hány ExpressRoute-kapcsolatcsoporttal csatlakozhat egy ExpressRoute-átjárót vagy ExpressRoute-átjárók számát egy ExpressRoute-kapcsolatcsoporttal csatlakozhat, vagy más skálázási korlátait expressroute további, lásd: [ExpressRoute – gyakori kérdések][ExR-FAQ]



<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "a tesztelési topológia"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha




