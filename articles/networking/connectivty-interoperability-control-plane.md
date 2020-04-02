---
title: 'Interoperabilitás az Azure-ban : Vezérlősík-elemzés'
description: Ez a cikk az ExpressRoute, a helyek közötti VPN és az Azure-beli virtuális hálózati társviszony-létesítés közötti együttműködés elemzéséhez használható tesztbeállítás vezérlősík-elemzését tartalmazza.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 5e41bc86533815c394077bf5276d930fe958cd19
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518272"
---
# <a name="interoperability-in-azure--control-plane-analysis"></a>Interoperabilitás az Azure-ban : Vezérlősík-elemzés

Ez a cikk a [tesztbeállítás][Setup]vezérlősík-elemzését ismerteti. Áttekintheti a [tesztbeállítás konfigurációját][Configuration] és a tesztbeállítás [adatsík-elemzését][Data-Analysis] is.

A vezérlősík-elemzés lényegében azokat az útvonalakat vizsgálja, amelyeket egy topológián belül hálózatok cserélnek. A vezérlősík-elemzés segítségével megismerheti, hogy a különböző hálózatok hogyan tekintik meg a topológiát.

## <a name="hub-and-spoke-vnet-perspective"></a>Hub és küllős virtuális hálózat perspektívája

Az alábbi ábra a hálózatot mutatja be egy központi virtuális hálózat (VNet) és egy küllős virtuális hálózat szemszögéből (kék színnel kiemelve). Az ábra a különböző hálózatok közötti kicserélt különböző hálózatok és útvonalak autonóm rendszerszámát (ASN) is mutatja: 

![1][1]

A virtuális hálózat Azure ExpressRoute-átjárójának ASN-je eltér a Microsoft Enterprise Edge-útválasztók (MSEE-k) ASN-étől. Az ExpressRoute-átjáró egy privát ASN-t **(65515-ös**értéket) használ, a KIS- és Nagy-családi vállalkozások pedig nyilvános ASN-t **(12076-os**értéket) használnak világszerte. Az ExpressRoute-társviszony-létesítés konfigurálásakor, mivel az MSEE a társ, az **12076-ot** használja társASN-ként. Az Azure oldalán az MSEE létrehozza az eBGP-társviszony-létesítést az ExpressRoute-átjáróval. Az MSEE által az egyes ExpressRoute-társviszony-létesítéshez létrehozott kettős eBGP-társviszony-létesítés a vezérlősík szintjén transzparens. Ezért egy ExpressRoute-útvonaltábla megtekintésekor a virtuális hálózat ExpressRoute átjáró ASN-je látható a virtuális hálózat előtagjaihoz. 

Az alábbi ábrán egy minta ExpressRoute-útvonaltábla látható: 

![5][5]

Az Azure-on belül az ASN csak társviszony-létesítési szempontból jelentős. Alapértelmezés szerint az ExpressRoute-átjáró és a VPN-átjáró ASN-je az Azure VPN-átjáróban **65515.**

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Helyszíni hely 1 és a távoli virtuális hálózat perspektíva expressroute 1-en keresztül

Mind a helyszíni 1. A topológia ugyanazon perspektívájában van, ahogy az a következő ábrán látható:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Helyszíni hely 1 és az ág virtuális hálózat perspektívája egy helyek közötti VPN-en keresztül

Mind a helyszíni 1. A topológia ugyanazon perspektívájában van, ahogy az a következő ábrán látható:

![3][3]

## <a name="on-premises-location-2-perspective"></a>Helyszíni hely 2 perspektíva

A helyszíni 2. 

![4][4]

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

Ismerje meg a tesztbeállítás és az Azure hálózati figyelési [funkciónézeteinek adatsík-elemzését.][Data-Analysis]

Lásd az [ExpressRoute gyIK-et:][ExR-FAQ]
-   Ismerje meg, hogy hány ExpressRoute-kapcsolatcsoport csatlakozhat egy ExpressRoute-átjáróhoz.
-   Ismerje meg, hogy hány ExpressRoute-átjárócsatlakozhat egy ExpressRoute-kapcsolathoz.
-   További információ az ExpressRoute egyéb méretezési korlátairól.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Hub és küllős vnet perspektívája a topológia"
[2]: ./media/backend-interoperability/Loc1ExRView.png "A topológia 1."
[3]: ./media/backend-interoperability/Loc1VPNView.png "A topológia 1."
[4]: ./media/backend-interoperability/Loc2View.png "A topológia 2."
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "ExpressRoute 1 útvonaltábla"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
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


