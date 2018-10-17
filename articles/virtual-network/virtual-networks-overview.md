---
title: Azure Virtual Network | Microsoft Docs
description: Megismerheti az Azure Virtual Network alapfogalmait és jellemzőit.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 8/8/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: fe84dfcef2a5dad1c170592f933638b984e16a05
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717029"
---
# <a name="what-is-azure-virtual-network"></a>Mi az Azure Virtual Network?

Az Azure Virtual Network számos Azure-erőforrástípus (pl. Azure-beli virtuális gépek) számára teszi lehetővé, hogy biztonságosan kommunikálhassanak egymással, az internettel és a helyszíni hálózatokkal. Az Azure Virtual Network az alábbi fő lehetőségeket nyújtja:

## <a name="isolation-and-segmentation"></a>Elkülönítés és szegmentálás

Több virtuális hálózatot is megvalósíthat az egyes Azure-[előfizetéseken](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) és Azure-[régiókon](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) belül. Minden virtuális hálózat el van különítve a többi virtuális hálózattól. Minden virtuális hálózat esetében megteheti az alábbiakat:
- Megadhat egy egyéni magánhálózati IP-címteret nyilvános és magánhálózati (RFC 1918) címek használatával. Az Azure magánhálózati IP-címet rendel hozzá a virtuális hálózatokon belüli erőforrásokhoz a hozzárendelt címtérből.
- A virtuális hálózatot egy vagy több alhálózatra bonthatja, és lefoglalhatja a virtuális hálózat címterének egy részét az egyes alhálózatok számára.
- Használhatja az Azure által biztosított névfeloldást vagy megadhat saját DNS-kiszolgálót a virtuális hálózatok erőforrásai számára.

## <a name="communicate-with-the-internet"></a>Kommunikáció az internettel

A virtuális hálózatok összes erőforrása alapértelmezés szerint képes kimenő kommunikációra az internettel. Bejövő kommunikációt létesíthet egy erőforrással egy nyilvános IP-cím vagy Load Balancer hozzárendelésével. A kimenő kapcsolatok kezeléséhez is használhat nyilvános IP-címet vagy Load Balancert.  Az Azure kimenő kapcsolataira vonatkozó további információkat a [kimenő kapcsolatokat](../load-balancer/load-balancer-outbound-connections.md), a [nyilvános IP-címeket](virtual-network-public-ip-address.md) vagy a [Load Balancert](../load-balancer/load-balancer-overview.md) ismertető cikkekben talál.

>[!NOTE]
>Ha csak belső [Standard Load Balancert](../load-balancer/load-balancer-standard-overview.md) használ, akkor a kimenő kapcsolat mindaddig nem lesz elérhető, amíg meg nem határozza, hogyan működjenek együtt a [kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md) egy példányszintű nyilvános IP-vel vagy egy nyilvános Load Balancerrel.

## <a name="communicate-between-azure-resources"></a>Azure-erőforrások közötti kommunikáció

Az Azure-erőforrások biztonságosan kommunikálnak egymással az alábbi módok egyikével:

- **Virtuális hálózaton keresztül**: Virtuális gépeket és számos egyéb típusú Azure-erőforrást is üzembe helyezhet egy virtuális hálózaton (pl. Azure App Service Environment-környezetek, Azure Kubernetes Service (AKS) és Azure Virtual Machine Scale Sets). A virtuális hálózatokon üzembe helyezhető Azure-erőforrások teljes listájáért lásd: [Virtuális hálózati szolgáltatás integrálása](virtual-network-for-azure-services.md). 
- **Virtuális hálózati szolgáltatásvégponton keresztül**: Kibővítheti virtuális hálózatának privát címterét és identitását az Azure-szolgáltatási erőforrásokra (pl. Azure Storage-fiókokra és Azure SQL-adatbázisokra) egy közvetlen kapcsolaton keresztül. A szolgáltatásvégpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak egy virtuális hálózaton legyenek elérhetőek. További információ: [A virtuális hálózati szolgáltatásvégpontok áttekintése](virtual-network-service-endpoints-overview.md).
 
## <a name="communicate-with-on-premises-resources"></a>Kommunikáció helyszíni erőforrásokkal

Az alábbiak bármely kombinációjával csatlakoztathatja helyszíni számítógépeit és hálózatait egy virtuális hálózathoz:

- **Pont–hely típusú virtuális magánhálózat (VPN):** Egy virtuális hálózat és a hálózat egyetlen számítógépe között létesített kapcsolat. Minden számítógép kapcsolatát konfigurálni kell, amely csatlakozni szeretne egy virtuális hálózathoz. Ez a kapcsolattípus remek választás, ha csak most ismerkedik az Azure szolgáltatással, illetve a fejlesztők számára, mert a meglévő hálózatot csak kis mértékben vagy egyáltalán nem kell módosítani. A számítógép és a virtuális hálózat közötti kommunikáció egy titkosított csatornán, az interneten keresztül zajlik. További tudnivalókért lásd: [Pont–hely típusú VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **Helyek közötti VPN:** A helyszíni VPN-eszköz és egy virtuális hálózaton üzembe helyezett Azure VPN Gateway átjáró között létesített kapcsolat. Ez a kapcsolattípus lehetővé teszi az összes engedélyezett helyszíni erőforrás számára, hogy elérjék a virtuális hálózatot. A helyszíni VPN-eszköz és egy Azure VPN Gateway átjáró közötti kommunikáció egy titkosított csatornán, az interneten keresztül zajlik. További tudnivalókért lásd: [Helyek közötti VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute**: Saját hálózata és az Azure között egy ExpressRoute-partneren keresztül létesített kapcsolat. Ez a kapcsolat nem nyilvános. A forgalom nem az interneten keresztül halad át. További tudnivalókért lásd: [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Hálózati forgalom szűrése
A hálózati forgalom alhálózatok közötti szűrése az alábbi lehetőségek egyikével vagy akár mindkettővel elvégezhető:
- **Hálózati biztonsági csoportok:** Egy hálózati biztonsági csoport tartalmazhat több bejövő és kimenő biztonsági szabályt, amelyek lehetővé teszik az erőforrások bejövő vagy kimenő forgalmának forrás vagy cél IP-cím, port és protokoll szerinti szűrését. További tudnivalókért lásd: [Hálózati biztonsági csoportok](security-overview.md#network-security-groups).
- **Hálózati virtuális berendezések:** A hálózati virtuális berendezések olyan virtuális gépek, amelyek egy hálózati funkciót látnak el, például tűzfal, WAN-optimalizáció vagy egyéb hálózati funkciók. A virtuális hálózatokban üzembe helyezhető hálózati virtuális berendezések listáját az [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) találja.

## <a name="route-network-traffic"></a>Hálózati forgalom továbbítása

Az Azure alapértelmezés szerint elosztja a forgalmat az alhálózatok, a csatlakoztatott virtuális hálózatok, a helyszíni hálózatok és az internet között. Az Azure által létrehozott alapértelmezett útvonalak felülírásához valósítsa meg az alábbi lehetőségek egyikét, vagy akár mindkettőt:
- **Útválasztási táblázatok:** Létrehozhat egyéni útválasztási táblázatokat, amelyek útvonalai szabályozzák, hogy melyik alhálózat esetében hova érkezzen a forgalom. További tudnivalókért tekintse meg az [útválasztási táblázatokat](virtual-networks-udr-overview.md#user-defined) ismertető cikket.
- **Border Gateway Protocol (BGP-) útvonalak:** Ha a virtuális hálózatát VPN-átjáró vagy ExpressRoute-kapcsolat használatával csatlakoztatja a helyszíni hálózathoz, propagálhatja a helyszíni BGP-útvonalakat a virtuális hálózatára. Tudjon meg többet a BGP [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange) szolgáltatással együtt való használatáról

## <a name="connect-virtual-networks"></a>Virtuális hálózatok csatlakoztatása

A virtuális hálózatok közötti társviszony létesítésével lehetősége van arra, hogy virtuális hálózatokat kapcsoljon össze egymással, ezáltal lehetővé téve, hogy a virtuális hálózatok erőforrásai kommunikálhassanak egymással. Az összekacsolt virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban. További információ: [Virtuális hálózatok közötti társviszony létesítése](virtual-network-peering-overview.md).

## <a name="next-steps"></a>További lépések

A cikk az Azure Virtual Network szolgáltatásról nyújtott áttekintést. A virtuális hálózatok használatának megkezdéséhez hozzon létre egyet, helyezzen üzembe rajta néhány virtuális gépet, majd létesítsen kommunikációt a virtuális gépek között. Ennek ismertetéséhez tekintse meg a [Virtuális hálózat létrehozása](quick-create-portal.md) című rövid útmutatót.
