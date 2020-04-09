---
title: Azure Virtual Network | Microsoft Docs
description: Megismerheti az Azure Virtual Network alapfogalmait és jellemzőit.
services: virtual-network
documentationcenter: na
author: anavinahar
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: anavin
ms.openlocfilehash: 967d391d4ac9a9704688dce9636d9a71b2002549
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879360"
---
# <a name="what-is-azure-virtual-network"></a>Mi az Azure Virtual Network?

Az Azure Virtual Network (VNet) az Azure-beli magánhálózat alapvető építőköve. A virtuális hálózat számos Azure-erőforrást, például az Azure virtuális gépeket (VM) teszi lehetővé, hogy biztonságosan kommunikáljanak egymással, az interneten és a helyszíni hálózatokon. A virtuális hálózat hasonló a saját adatközpontjában üzemeltetett hagyományos hálózathoz, de az Azure infrastruktúrájának további előnyeit, például a méretezést, a rendelkezésre állást és az elkülönítést is magával hozza.

## <a name="vnet-concepts"></a>A virtuális hálózatok fogalmai

- **Címterület:** Virtuális hálózat létrehozásakor meg kell adnia egy egyéni privát IP-címteret nyilvános és privát (RFC 1918) címek használatával. Az Azure magánhálózati IP-címet rendel hozzá a virtuális hálózatokon belüli erőforrásokhoz a hozzárendelt címtérből. Ha például egy virtuális gép üzembe helyezése egy virtuális hálózat címterülettel, 10.0.0.0/16, a virtuális gép kap egy privát IP-cím, például 10.0.0.4.
- **Alhálózatok:** Az alhálózatok lehetővé teszik a virtuális hálózat egy vagy több alhálózatra történő szegmentálását, és a virtuális hálózat címterületének egy részét az egyes alhálózatokhoz. Ezután üzembe helyezheti az Azure-erőforrásokat egy adott alhálózatban. A hagyományos hálózatokhoz hasonlóan az alhálózatok is lehetővé teszik a virtuális hálózat címterének szegmensekre történő szegmentálását, amelyek megfelelnek a szervezet belső hálózatának. Ez javítja a címkiosztás hatékonyságát is. Az alhálózatokon belüli erőforrásokat a hálózati biztonsági csoportok segítségével biztosíthatja. További információt a Biztonsági csoportok című [témakörben talál.](security-overview.md)
- **Régiók**: A virtuális hálózat hatóköre egyetlen régióra/helyre terjed ki; azonban több virtuális hálózatok különböző régiókból is csatlakoztatható együtt virtuális hálózati társviszony-létesítés.
- **Előfizetés:** A virtuális hálózat hatóköre előfizetés. Több virtuális hálózatot is megvalósíthat az egyes Azure-[előfizetéseken](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) és Azure-[régiókon](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) belül.

## <a name="best-practices"></a>Ajánlott eljárások

A hálózat Azure-beli kiépítése során fontos, hogy tartsa szem előtt az alábbi általános tervezési elveket:

- Biztosítsa az átnem fedő címterek biztosítását. Győződjön meg arról, hogy a virtuális hálózat címterülete (CIDR-blokk) nem fedi át a szervezet más hálózati tartományait.
- Az alhálózatok nem fedhetik le a virtuális hálózat teljes címterét. Tervezzen előre, és foglaljon le egy kis címteret a jövőre nézve.
- Ajánlott kevesebb nagy virtuális hálózat, mint több kis virtuális hálózat. Ez megakadályozza a felügyeleti többletterhelést.
- Biztosítsa a virtuális hálózatok at hálózati biztonsági csoportok (NSG-k) hozzárendelésével az alattuk lévő alhálózatokhoz.

## <a name="communicate-with-the-internet"></a>Kommunikáció az internettel

A virtuális hálózat minden erőforrása alapértelmezés szerint kommunikálhat az internettel. Bejövő kommunikációt létesíthet egy erőforrással egy nyilvános IP-cím vagy Load Balancer hozzárendelésével. A kimenő kapcsolatok kezeléséhez is használhat nyilvános IP-címet vagy Load Balancert.  Az Azure kimenő kapcsolataira vonatkozó további információkat a [kimenő kapcsolatokat](../load-balancer/load-balancer-outbound-connections.md), a [nyilvános IP-címeket](virtual-network-public-ip-address.md) vagy a [Load Balancert](../load-balancer/load-balancer-overview.md) ismertető cikkekben talál.

>[!NOTE]
>Ha csak belső [Standard Load Balancert](../load-balancer/load-balancer-standard-overview.md) használ, akkor a kimenő kapcsolat mindaddig nem lesz elérhető, amíg meg nem határozza, hogyan működjenek együtt a [kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md) egy példányszintű nyilvános IP-vel vagy egy nyilvános Load Balancerrel.

## <a name="communicate-between-azure-resources"></a>Azure-erőforrások közötti kommunikáció

Az Azure-erőforrások biztonságosan kommunikálnak egymással az alábbi módok egyikével:

- **Virtuális hálózaton keresztül**: Virtuális gépeket és számos egyéb típusú Azure-erőforrást is üzembe helyezhet egy virtuális hálózaton (pl. Azure App Service Environment-környezetek, Azure Kubernetes Service (AKS) és Azure Virtual Machine Scale Sets). A virtuális hálózatokon üzembe helyezhető Azure-erőforrások teljes listájáért lásd: [Virtuális hálózati szolgáltatás integrálása](virtual-network-for-azure-services.md).
- **Virtuális hálózati szolgáltatásvégponton keresztül**: Kibővítheti virtuális hálózatának privát címterét és identitását az Azure-szolgáltatási erőforrásokra (pl. Azure Storage-fiókokra és Azure SQL-adatbázisokra) egy közvetlen kapcsolaton keresztül. A szolgáltatásvégpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak egy virtuális hálózaton legyenek elérhetőek. További információ: [A virtuális hálózati szolgáltatásvégpontok áttekintése](virtual-network-service-endpoints-overview.md).
- **Virtuális hálózati társviszony-létesítés :** Virtuális hálózatok csatlakoztathatók egymáshoz, lehetővé téve, hogy a virtuális hálózat erőforrásai kommunikáljanak egymással, virtuális hálózati társviszony-létesítés használatával. Az összekacsolt virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban. További információ: [Virtuális hálózatok közötti társviszony létesítése](virtual-network-peering-overview.md).

## <a name="communicate-with-on-premises-resources"></a>Kommunikáció helyszíni erőforrásokkal

Az alábbiak bármely kombinációjával csatlakoztathatja helyszíni számítógépeit és hálózatait egy virtuális hálózathoz:

- **Pont–hely típusú virtuális magánhálózat (VPN):** Egy virtuális hálózat és a hálózat egyetlen számítógépe között létesített kapcsolat. Minden számítógép kapcsolatát konfigurálni kell, amely csatlakozni szeretne egy virtuális hálózathoz. Ez a kapcsolattípus remek választás, ha csak most ismerkedik az Azure szolgáltatással, illetve a fejlesztők számára, mert a meglévő hálózatot csak kis mértékben vagy egyáltalán nem kell módosítani. A számítógép és a virtuális hálózat közötti kommunikáció egy titkosított csatornán, az interneten keresztül zajlik. További tudnivalókért lásd: [Pont–hely típusú VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **Helyek közötti VPN:** A helyszíni VPN-eszköz és egy virtuális hálózaton üzembe helyezett Azure VPN Gateway átjáró között létesített kapcsolat. Ez a kapcsolattípus lehetővé teszi az összes engedélyezett helyszíni erőforrás számára, hogy elérjék a virtuális hálózatot. A helyszíni VPN-eszköz és egy Azure VPN Gateway átjáró közötti kommunikáció egy titkosított csatornán, az interneten keresztül zajlik. További tudnivalókért lásd: [Helyek közötti VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute**: Saját hálózata és az Azure között egy ExpressRoute-partneren keresztül létesített kapcsolat. Ez a kapcsolat nem nyilvános. A forgalom nem az interneten keresztül halad át. További tudnivalókért lásd: [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Hálózati forgalom szűrése

A hálózati forgalom alhálózatok közötti szűrése az alábbi lehetőségek egyikével vagy akár mindkettővel elvégezhető:

- **Biztonsági csoportok:** A hálózati biztonsági csoportok és az alkalmazásbiztonsági csoportok több bejövő és kimenő biztonsági szabályt is tartalmazhatnak, amelyek lehetővé teszik az erőforrásokba és erőforrásokból érkező forgalom forrás- és célIP-cím, port és protokoll szerinti szűrését. További információ: [Hálózati biztonsági csoportok](security-overview.md#network-security-groups) vagy [alkalmazásbiztonsági csoportok](security-overview.md#application-security-groups).
- **Hálózati virtuális berendezések:** A hálózati virtuális berendezések olyan virtuális gépek, amelyek egy hálózati funkciót látnak el, például tűzfal, WAN-optimalizáció vagy egyéb hálózati funkciók. A virtuális hálózatokban üzembe helyezhető hálózati virtuális berendezések listáját az [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) találja.

## <a name="route-network-traffic"></a>Hálózati forgalom továbbítása

Az Azure alapértelmezés szerint elosztja a forgalmat az alhálózatok, a csatlakoztatott virtuális hálózatok, a helyszíni hálózatok és az internet között. Az Azure által létrehozott alapértelmezett útvonalak felülírásához valósítsa meg az alábbi lehetőségek egyikét, vagy akár mindkettőt:

- **Útválasztási táblázatok:** Létrehozhat egyéni útválasztási táblázatokat, amelyek útvonalai szabályozzák, hogy melyik alhálózat esetében hova érkezzen a forgalom. További tudnivalókért tekintse meg az [útválasztási táblázatokat](virtual-networks-udr-overview.md#user-defined) ismertető cikket.
- **Border Gateway Protocol (BGP-) útvonalak:** Ha a virtuális hálózatát VPN-átjáró vagy ExpressRoute-kapcsolat használatával csatlakoztatja a helyszíni hálózathoz, propagálhatja a helyszíni BGP-útvonalakat a virtuális hálózatára. Tudjon meg többet a BGP [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange) szolgáltatással együtt való használatáról

## <a name="virtual-network-integration-for-azure-services"></a>Virtuális hálózat integrációja Azure-szolgáltatásokhoz

Az Azure-szolgáltatások integrálása egy Azure virtuális hálózatba lehetővé teszi a szolgáltatás hoz való privát hozzáférést a virtuális gépekről vagy a virtuális hálózat számítási erőforrásairól.
Az Azure-szolgáltatásokat a következő lehetőségekkel integrálhatja a virtuális hálózatba:
- A [szolgáltatás dedikált példányainak](virtual-network-for-azure-services.md) üzembe helyezése egy virtuális hálózatba. A szolgáltatások ezután privát módon érhetők el a virtuális hálózaton belül és a helyszíni hálózatokon.
- Privát [kapcsolat](../private-link/private-link-overview.md) használata a szolgáltatás egy adott példányának privát eléréséhez a virtuális hálózatról és a helyszíni hálózatokból.
- A szolgáltatás nyilvános végpontok használatával is elérhető, ha egy virtuális hálózatot terjeszt a szolgáltatásvégpontokon keresztül a [szolgáltatásra.](virtual-network-service-endpoints-overview.md) A szolgáltatásvégpontok lehetővé teszik a szolgáltatáserőforrások virtuális hálózatra való védelmét.
 

## <a name="azure-vnet-limits"></a>Az Azure virtuális hálózat korlátai

Bizonyos korlátok vannak az Azure-erőforrások üzembe helyezhető száma körül. A legtöbb Azure hálózati korlátok vannak a maximális értékeket. A virtuális hálózatok korlátai lapon megadottak szerint azonban növelheti a [hálózati korlátokat.](../azure-portal/supportability/networking-quota-requests.md) [VNet limits page](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) 

## <a name="pricing"></a>Díjszabás

Az Azure VNet használata díjmentes. Az általános díjak az erőforrásokra, például a virtuális gépekre (VM-ek) és más termékekre vonatkoznak. További információ: [Virtuális hálózatok díjszabása](https://azure.microsoft.com/pricing/details/virtual-network/) és az Azure [díjszabási kalkulátor.](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>További lépések

 A virtuális hálózatok használatának megkezdéséhez hozzon létre egyet, helyezzen üzembe rajta néhány virtuális gépet, majd létesítsen kommunikációt a virtuális gépek között. Ennek ismertetéséhez tekintse meg a [Virtuális hálózat létrehozása](quick-create-portal.md) című rövid útmutatót.
