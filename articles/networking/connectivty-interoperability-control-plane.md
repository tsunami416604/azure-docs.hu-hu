---
title: 'ExpressRoute, a Site-to-site VPN és a virtuális hálózatok közötti Társviszony - együttműködési szabályozhatja az Adatsík-elemzés: Azure Háttérkapcsolathoz együttműködési funkciók |} A Microsoft Docs'
description: Ez az oldal ismerteti a vezérlési sík analysis a teszt beállítása elemezheti az együttműködést a funkciók ExpressRoute, a Site-to-site VPN és a virtuális hálózatok közötti Társviszony létrehozása.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: ee887da18b5666e61bc25365791b2e7dffb925e0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947276"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---control-plane-analysis"></a>Az ExpressRoute, a Site-to-site VPN és a virtuális hálózatok közötti társviszony-létesítési - vezérlési sík elemzési együttműködési

Ebben a cikkben vegyük végig a vezérlési sík analysis a vizsgálat beállítása. Ha szeretné ellenőrizni a teszt telepítés, tekintse meg a [teszt telepítő][Setup]. A teszt telepítő konfiguráció részletei áttekintéséhez lásd: [telepítő Tesztkonfiguráció][Configuration].

Vezérlési sík elemzési lényegében megvizsgálja a topológia hálózatok között cserélődő útvonalak. Szabályozhatja a különböző adatsík elemzési súgó megtekintése a topológia hálózati.

##<a name="hub-and-spoke-vnet-perspective"></a>Eseményközpont és a küllő virtuális hálózatok közötti perspektíva

A következő ábra szemlélteti a hálózaton lévő agyi virtuális hálózat és a küllő virtuális hálózat (kék színnel) szempontjából. Az ábrán is látható a különböző hálózati és a különböző hálózatok között cserélődő útvonalak autonóm rendszer száma (ASN). 

[![1]][1]

Figyelje meg, hogy a VNet ASN ExpressRoute-átjáró eltér az ASN-t a Microsoft Enterprise peremhálózati útválasztóhoz (Msee). ExpressRoute-átjárót használ egy privát ASN (65515), és Msee globálisan használjon nyilvános ASN-t (12076). Az ExpressRoute-társviszony-létesítéshez, mert a társ MSEE konfigurálásakor használja 12076 számot használja a társ ASN-t. Az Azure részéről MSEE eBGP társviszony-létesítést az ExpressRoute-Átjáró hoz létre. A kettős eBGP társviszony-létesítést, amely az MSEE hoz létre minden egyes ExpressRoute-társviszony-létesítéshez átlátható a vezérlősík szintjén. Ezért az ExpressRoute útválasztási táblázat megtekintve láthatja a virtuális hálózat ExpressRoute az Átjáró ASN a VNet-előtagok esetében. Alább látható minta az ExpressRoute útválasztási táblázat képernyőképet: 

[![5]][5]

Azure-ban az ASN-t csak akkor jelentős a társviszony-létesítési szempontjából. Alapértelmezés szerint az ExpressRoute-Átjáró és a VPN-Átjáró ASN-je a 65515.

##<a name="on-premises-location-1-and-remote-vnet-perspective-via-expressroute-1"></a>1. ExpressRoute-n keresztül a helyszíni helyre-1. és távoli virtuális hálózat perspektíva

A helyszíni hely-1 és a távoli virtuális hálózat is is agyi virtuális hálózat ExpressRoute-1-n keresztül csatlakozik, és ezért a topológiát, azonos szempontjából oszthatnak meg, ahogyan az alábbi ábrán látható.

[![2]][2]

##<a name="on-premises-location-1-and-branch-vnet-perspective-via-site-to-site-vpn"></a>Site-to-Site VPN-en keresztül a helyszíni helyre-1. és fiókirodai VNet perspektíva

A helyszíni helyre-1. és fiókirodai VNet is csatlakozik, agyi virtuális hálózat VPN-Átjáró Site-to-Site VPN-kapcsolatok keresztül és ezért oszthatnak meg a topológiát, azonos szempontjából, ahogyan az alábbi ábrán látható.

[![3]][3]

##<a name="on-premises-location-2-perspective"></a>A helyszíni helyre – 2 perspektíva

A helyszíni hely-2 2. ExpressRoute privát társviszony-n keresztül csatlakozik agyi virtuális hálózat. 

[![4]][4]

## <a name="further-reading"></a>További információ

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute- és helyek közötti VPN-kapcsolat használatával területtel

####  <a name="site-to-site-vpn-over-expressroute"></a>Site-to-Site VPN expressroute-on keresztül

Site-to-Site VPN konfigurálható az ExpressRoute Microsoft közvetlenül a Microsoftnak Exchange bizalmas, visszajátszás, hitelességét és integritásának a helyszíni hálózat és az Azure virtuális hálózatok közötti társviszony-létesítésen keresztül. További információ a helyek közötti IPSec VPN konfigurálása bújtatási mód az ExpressRoute a Microsoft társviszony-létesítésen keresztül: [Site-to-site VPN ExpressRoute Microsoft-társviszony-létesítésen keresztül][S2S-Over-ExR]. 

A fő szolgáltatásoknak a S2S VPN konfigurálása Microsoft társviszony-létesítésen keresztül az átviteli sebességet. Az IPSec-alagúton keresztül átviteli sebességet a VPN-Átjáró kapacitása korlátozza. A VPN-Átjáró átviteli sebesség, kisebb ExpressRoute átviteli képest. Ilyen esetekben az IPSec-alagút használatával nagy biztonságos forgalmat és a privát társviszony-létesítést az összes többi forgalom segít az ExpressRoute sávszélesség-használat optimalizálása.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-Site VPN biztonságos feladatátvételi útvonalként az expressroute-hoz
Az ExpressRoute kapcsolatcsoport redundáns pár magas rendelkezésre állás biztosítása érdekében érhető el. Konfigurálhatja a georedundáns az ExpressRoute-kapcsolat másik Azure-régióban. Emellett a teszt a telepítés indításához, mint egy adott Azure-régióban, ha feladatátvételi útvonalként az ExpressRoute-kapcsolat használni kívánt megteheti Site-to-Site VPN használatával. Ha ugyanazokat az előtagokat vannak hirdetve az ExpressRoute- és S2S VPN, Azure ExpressRoute S2S VPN-kapcsolaton keresztül részesíti előnyben. Aszimmetrikus útválasztás az ExpressRoute és S2S VPN között elkerülése érdekében a helyszíni hálózati konfiguráció is reciprocate előnyben részesítve – ExpressRoute keresztül S2S VPN-kapcsolat.

További információ az ExpressRoute és Site-to-Site VPN egyidejű kapcsolatok konfigurálása: [ExpressRoute- és helyek közötti együttműködés][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Kiterjeszti a háttérkapcsolathoz küllő virtuális hálózatokhoz és a fiókirodában dolgozó

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Használatával a virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok közötti küllőkapcsolat

Központ-küllő virtuális hálózatok közötti architektúra széles körben használt. A hub egy virtuális hálózatot (VNet) az Azure-ban, amely egy központi csatlakozási pontra van szükség a küllő virtuális hálózatok között, és a helyszíni hálózathoz funkcionál. A küllők az agyhoz, és a számítási feladatok elkülönítésére használhatók virtuális hálózatok. A forgalom a helyszíni adatközpont és a egy expressroute-on vagy VPN-kapcsolaton keresztül a hub között. További architektúrájával kapcsolatos további információkért lásd: [és-Küllős architektúra][Hub-n-Spoke]

Virtuális hálózatok közötti társviszony-létesítés egy adott régión belül lehetővé teszi, hogy a küllő virtuális hálózatok hub virtuális hálózati átjáró (VPN- és ExpressRoute-átjárók) használatával kommunikál távoli hálózatok.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Virtuális hálózatok közötti kapcsolat Site-to-Site VPN használatával ág

Ha azt szeretné, hogy a virtuális hálózatok (különböző régiókban) ágat, és a helyszíni hálózatok egy agyi virtuális hálózat keresztül kommunikálnak egymással, a natív Azure-megoldás a site-to-site VPN-kapcsolat VPN használatával. Egy másik lehetőség, hogy az NVA-útválasztási az agyban.

VPN-átjárók konfigurálásához lásd: [VPN-átjáró konfigurálása][VPN]. Magas rendelkezésre állású NVA telepítése, lásd: [üzembe helyezése magas rendelkezésre állású NVA][Deploy-NVA].

## <a name="next-steps"></a>További lépések

Adatsík adatelemzés a teszt beállítása és az Azure hálózatfigyelési funkciók nézetek: [Adatsík elemzési][Data-Analysis].

Ismerje meg, hány ExpressRoute-kapcsolatcsoporttal csatlakozhat egy ExpressRoute-átjárót vagy ExpressRoute-átjárók számát egy ExpressRoute-kapcsolatcsoporttal csatlakozhat, vagy más skálázási korlátait expressroute további, lásd: [ExpressRoute – gyakori kérdések][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "hubot és a küllő virtuális hálózatok közötti szempontjából a topológia."
[2]: ./media/backend-interoperability/Loc1ExRView.png "hely-1 és a távoli virtuális hálózat perspektíva ExpressRoute 1 a topológia használatával"
[3]: ./media/backend-interoperability/Loc1VPNView.png "helyét-1. és fiókirodai VNet perspektíva keresztül S2S VPN-a topológia"
[4]: ./media/backend-interoperability/Loc2View.png "helye – 2 szempontjából a topológia."
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "ExpressRoute 1 RouteTable"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
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




