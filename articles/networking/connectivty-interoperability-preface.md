---
title: 'Interoperabilitás az Azure háttérkapcsolati funkcióiban: Tesztelés beállítása | Microsoft dokumentumok'
description: Ez a cikk egy tesztbeállításokat ismertet, amelyekkel elemezheti az ExpressRoute, a helyek közötti VPN és az Azure virtuális hálózati társviszony-létesítése közötti interoperabilitást.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 0cbd4b620a03ed26e95679cf7cb1abef277a9471
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873795"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Együttműködés az Azure háttérkapcsolati funkcióiban: Tesztelés beállítása

Ez a cikk egy tesztbeállításokat ismertet, amelyekkel elemezheti, hogy az Azure hálózati szolgáltatások hogyan működnek össze a vezérlősík és az adatsík szintjén. Röviden nézzük meg az Azure hálózati összetevőit:

-   **Azure ExpressRoute:** Az Azure ExpressRoute privát társviszony-létesítésével közvetlenül összekapcsolhatja a helyszíni hálózat magánhálózati IP-területeit az Azure virtuális hálózati központi telepítésekkel. Az ExpressRoute segítségével nagyobb sávszélességet és privát kapcsolatot érhet el. Számos ExpressRoute-alapú eco partner kínál ExpressRoute-kapcsolatot slos-okkal. Az ExpressRoute-ról és az ExpressRoute konfigurálásáról az [ExpressRoute – Bevezetés című témakörben olvashat bővebben.][ExpressRoute]
-   **Helyek közötti VPN:** Az Azure VPN-átjárót helyről helyekre történő VPN-ként használhatja a helyszíni hálózat biztonságos csatlakoztatásához az Azure-hoz az interneten keresztül vagy az ExpressRoute használatával. Ha tudni szeretné, hogyan konfigurálhat egy helyek közötti VPN-t az Azure-hoz való csatlakozáshoz, olvassa [el a VPN-átjáró konfigurálása című témakört.][VPN]
-   **Virtuális hálózat társviszony-létesítése:** A virtuális hálózat (VNet) társviszony-létesítési virtuális hálózatok közötti kapcsolat létesítése az Azure virtuális hálózatban. Ha többet szeretne megtudni a virtuális hálózatok társviszony-létesítéséről, olvassa el a [virtuális hálózatok társviszony-létesítési útmutatójában.][VNet]

## <a name="test-setup"></a>Teszt beállítása

Az alábbi ábra a teszt beállítását mutatja be:

![1][1]

A tesztbeállítás központi eleme az Azure 1-es régió ban található központi virtuális hálózat. A központi virtuális hálózat a következő módokon csatlakozik különböző hálózatokhoz:

-   A központi virtuális hálózat virtuális hálózat virtuális társviszony-létesítés használatával csatlakozik a küllővirtuális hálózathoz. A küllővirtuális hálózat távoli hozzáféréssel rendelkezik a központi virtuális hálózat mindkét átjárójához.
-   A központi virtuális hálózat a helyek közötti VPN használatával csatlakozik az ág virtuális hálózatához. A kapcsolat az eBGP segítségével váltja ki az útvonalakat.
-   A központi virtuális hálózat csatlakozik a helyszíni Location 1 hálózathoz az ExpressRoute privát társviszony-létesítés elsődleges elérési úthasználatával. A helyek közötti VPN-kapcsolatot használja biztonsági mentési útvonalként. A cikk további részében ezt az ExpressRoute-áramkört ExpressRoute 1-nek nevezzük. Alapértelmezés szerint az ExpressRoute-áramkörök redundáns kapcsolatot biztosítanak a magas rendelkezésre állás érdekében. Az ExpressRoute 1-en a másodlagos ügyfélperemútválasztó alilleszte, amely a másodlagos Microsoft Enterprise Edge router (MSEE) felé néz, le van tiltva. Az előző ábrán a kétvonalas nyíl felett piros vonal jelöli a letiltott CE-útválasztó alfelületét.
-   A központi virtuális hálózat egy másik ExpressRoute privát társviszony-létesítés használatával csatlakozik a helyszíni Location 2 hálózathoz. A cikk további részében a második ExpressRoute-kapcsolatexpressRoute 2 néven hivatkozunk.
-   ExpressRoute 1 is csatlakozik a központi virtuális hálózat és a helyszíni Location 1 hálózat egy távoli virtuális hálózat az Azure 2 régióban.

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

További információ a teszttopológia [konfigurációs részleteiről.][Configuration]

Ismerje meg a [vezérlő sík elemzése][Control-Analysis] a teszt beállítás és a nézetek a különböző virtuális hálózatok vagy VLAN-ok a topológia.

Ismerje meg a tesztbeállítás és az Azure hálózati figyelési funkciónézetek [adatsík-elemzését.][Data-Analysis]

Lásd az [ExpressRoute gyIK-et:][ExR-FAQ]
-   Ismerje meg, hogy hány ExpressRoute-kapcsolatcsoport csatlakozhat egy ExpressRoute-átjáróhoz.
-   Ismerje meg, hogy hány ExpressRoute-átjárócsatlakozhat egy ExpressRoute-kapcsolathoz.
-   További információ az ExpressRoute egyéb méretezési korlátairól.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "A vizsgálati topológia diagramja"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha


