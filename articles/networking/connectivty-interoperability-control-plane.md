---
title: 'Együttműködés az Azure háttér-kapcsolati funkciók: Szabályozhatja az adatsík-elemzés |} A Microsoft Docs'
description: A cikk ismerteti a vezérlési sík analysis a teszt beállítása segítségével elemezheti az együttműködés között ExpressRoute, a site-to-site VPN és a virtuális hálózati társviszony az Azure-ban.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 7ce50ed32f845fb21e8d95f4d2da1786e5f6f540
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772483"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-control-plane-analysis"></a>Együttműködés az Azure háttér-kapcsolati funkciók: Vezérlési sík elemzése

Ez a cikk ismerteti a vezérlési sík elemzése a [beállítások ellenőrzése][Setup]. Is a [telepítő tesztkonfiguráció] [ Configuration] és a [data analysis adatsík] [ Data-Analysis] a vizsgálat beállítása.

Vezérlési sík elemzési lényegében megvizsgálja belül-topológia hálózatok között cserélt útvonalakat. Vezérlési sík elemzés azt segítenek megérteni a különféle hálózatokhoz topológia megtekintéséhez.

## <a name="hub-and-spoke-vnet-perspective"></a>Küllős topológiájú VNet-perspektíva

Az alábbi ábra a hálózaton a központi virtuális hálózaton (VNet) és a egy küllő virtuális hálózat (kék színnel) szempontjából. Az ábra is mutatja az autonóm rendszer száma (ASN) különböző hálózatok és a különböző hálózatok között cserélt útvonalak: 

[![1]][1]

Az ASN-t a virtuális hálózat Azure ExpressRoute-átjáró az ASN-t a Microsoft Enterprise peremhálózati útválasztóhoz (Msee) különbözik. Egy ExpressRoute-átjárót használ egy privát ASN-t (érték **65515**), és Msee nyilvános ASN (érték **12076**) globálisan. Konfigurálásakor ExpressRoute-társviszonyt, mert a társ MSEE, használhat **12076** a társ ASN-t. Az Azure részéről MSEE létesít eBGP társviszony-létesítést az ExpressRoute-átjáróval. A kettős eBGP társviszony-létesítést, amely az MSEE hoz létre minden egyes ExpressRoute-társviszony-létesítéshez átlátható a vezérlési sík szintjén. Ezért az ExpressRoute útválasztási táblázat megtekintésekor láthatja a virtuális hálózat ExpressRoute-átjáró ASN-t a VNet-előtagok esetében. 

A következő ábrán látható egy példa az ExpressRoute útválasztási táblázatot: 

[![5]][5]

Azure-ban az ASN-t azért fontos, csak a társviszony-létesítési szempontjából. Alapértelmezés szerint az ASN-t az ExpressRoute-átjárót és a VPN-átjárót, az Azure VPN Gateway a **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>A helyszíni hely 1 és a távoli virtuális hálózat perspektíva ExpressRoute 1-n keresztül

A helyszíni hely 1 és a távoli virtuális hálózat az agyi virtuális hálózat ExpressRoute-1-n keresztül csatlakozik. Ezek azonos perspektívájú a topológia az alábbi ábrán látható módon:

[![2]][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>A helyszíni hely 1 és a fiókiroda egy helyek közötti VPN-en keresztül a virtuális hálózatok közötti perspektíva

A helyszíni hely 1 mind az ág virtuális hálózat egy agyi virtuális hálózat VPN-átjáró site-to-site VPN-kapcsolaton keresztül csatlakoznak. Ezek azonos perspektívájú a topológia az alábbi ábrán látható módon:

[![3]][3]

## <a name="on-premises-location-2-perspective"></a>A helyszíni hely 2 perspektíva

A helyszíni hely 2 agyi virtuális hálózat 2. ExpressRoute privát társviszony-n keresztül csatlakozik: 

[![4]][4]

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

Ismerje meg [data analysis adatsík] [ Data-Analysis] a teszt beállítása és figyelési nézetei a szolgáltatás az Azure network.

Tekintse meg a [ExpressRoute – gyakori kérdések] [ ExR-FAQ] való:
-   Ismerje meg, hány ExpressRoute-kapcsolatcsoporttal csatlakozhat egy ExpressRoute-átjárót.
-   Ismerje meg, hogy hány ExpressRoute-átjáró egy ExpressRoute-kapcsolatcsoporttal csatlakozhat.
-   Ismerje meg a többi skálázási korlátait expressroute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "küllős VNet szempontjából a topológia."
[2]: ./media/backend-interoperability/Loc1ExRView.png "hely 1 és 1. ExpressRoute-n keresztül a topológia szempontjából távoli virtuális hálózat"
[3]: ./media/backend-interoperability/Loc1VPNView.png "hely 1 és a fiókiroda VNet szempontjából a topológia egy helyek közötti VPN-en keresztül."
[4]: ./media/backend-interoperability/Loc2View.png "hely 2 szempontjából a topológia."
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "ExpressRoute 1 útválasztási táblázat"

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


