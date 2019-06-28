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
ms.openlocfilehash: 0d6762c8f3034923ddc0fe7dcf0cc2df34bd3629
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332107"
---
# <a name="what-is-azure-virtual-network"></a>Mi az Azure Virtual Network?

Az Azure Virtual Network (VNet) az alapvető építőeleme a magánhálózaton az Azure-ban. Virtuális hálózat lehetővé teszi számos különböző típusú Azure-erőforrások, például az Azure Virtual Machines (VM), biztonságosan kommunikálnak egymással, az internethez, és a helyszíni hálózatokkal. Virtuális hálózat hasonlít egy hagyományos hálózati lenne a saját adatközpontban működnek, de számos lehetőséget kínál, például a méretezés, a rendelkezésre állási és az elkülönítés az Azure infrastruktúra előnyeit.

## <a name="vnet-concepts"></a>VNet fogalmaival

- **Címtér:** Virtuális hálózat létrehozásakor meg kell adnia egy egyéni magánhálózati IP-címteret nyilvános és magánhálózati (RFC 1918) címek használatával. Az Azure magánhálózati IP-címet rendel hozzá a virtuális hálózatokon belüli erőforrásokhoz a hozzárendelt címtérből. Például ha telepít egy virtuális Gépet a virtuális hálózat a címtér, 10.0.0.0/16, a virtuális gép lesz hozzárendelve 10.0.0.4 például egy magánhálózati IP-címet.
- **Alhálózatok:** Alhálózatok lehetővé teszi a virtuális hálózat szegmentálása egy vagy több alárendelt hálózatokba és kiosztani a virtuális hálózat címtere a minden alhálózat egy része. Ezután telepítheti az Azure-erőforrások egy meghatározott alhálózatban. Csakúgy, mint egy hagyományos hálózatban, alhálózatok lehetővé teszik a virtuális hálózati címtér oszthatja be szegmenset, amely a szervezet belső hálózat megfelelő. Ez a cím foglalási hatékonyságát is javítja. Hálózati biztonsági csoportok használata alhálózatok belüli erőforrások biztonságát. További információkért lásd: [biztonsági csoportok](/security-overview.md).
- **Régiók**: Egyetlen régió/hely; virtuális hálózat hatókörét azonban különféle régiókból származó több virtuális hálózat csatlakozhatnak egymáshoz a virtuális hálózatok közötti Társviszony segítségével.
- **Előfizetés:** Virtuális hálózat előfizetés hatókörét. Több virtuális hálózatot is megvalósíthat az egyes Azure-[előfizetéseken](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) és Azure-[régiókon](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) belül.

## <a name="best-practices"></a>Ajánlott eljárások

A hálózat az Azure-beli létrehozása, fontos tartsa szem előtt az alábbi univerzális tervezési elvek:

- Győződjön meg arról, egymást nem átfedő címterek. Győződjön meg arról, hogy a virtuális hálózaton címtartomány (CIDR-blokk) nem létezik, a szervezet átfedi a más hálózati tartományok.
- Az alhálózatok nem terjed ki a virtuális hálózat a teljes címtér. Ha előre tervez, és a cím lemezterületet foglalni a jövőben.
- Javasoljuk, hogy kevesebb nagy méretű virtuális hálózatokat, mint a több kis méretű virtuális hálózatok. Ez megakadályozza a munkaterhelést.
- Tegye biztonságossá a virtuális hálózatot a hálózati biztonsági csoportok (NSG-k).

## <a name="communicate-with-the-internet"></a>Kommunikáció az internettel

Egy virtuális hálózaton található összes erőforrást képes kimenő kommunikációra az internettel alapértelmezés szerint. Bejövő kommunikációt létesíthet egy erőforrással egy nyilvános IP-cím vagy Load Balancer hozzárendelésével. A kimenő kapcsolatok kezeléséhez is használhat nyilvános IP-címet vagy Load Balancert.  Az Azure kimenő kapcsolataira vonatkozó további információkat a [kimenő kapcsolatokat](../load-balancer/load-balancer-outbound-connections.md), a [nyilvános IP-címeket](virtual-network-public-ip-address.md) vagy a [Load Balancert](../load-balancer/load-balancer-overview.md) ismertető cikkekben talál.

>[!NOTE]
>Ha csak belső [Standard Load Balancert](../load-balancer/load-balancer-standard-overview.md) használ, akkor a kimenő kapcsolat mindaddig nem lesz elérhető, amíg meg nem határozza, hogyan működjenek együtt a [kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md) egy példányszintű nyilvános IP-vel vagy egy nyilvános Load Balancerrel.

## <a name="communicate-between-azure-resources"></a>Azure-erőforrások közötti kommunikáció

Az Azure-erőforrások biztonságosan kommunikálnak egymással az alábbi módok egyikével:

- **Egy virtuális hálózaton keresztül**: Telepíthet virtuális gépeket, és számos egyéb típusú Azure-erőforrások virtuális hálózathoz, például az Azure App Service Environment-környezetek, az Azure Kubernetes Service (AKS) és az Azure Virtual Machine Scale Sets. A virtuális hálózatokon üzembe helyezhető Azure-erőforrások teljes listájáért lásd: [Virtuális hálózati szolgáltatás integrálása](virtual-network-for-azure-services.md).
- **Egy virtuális hálózati szolgáltatásvégpont keresztül**: Kiterjesztheti a virtuális hálózatának privát címterét és identitását az Azure-szolgáltatási erőforrások, például az Azure Storage-fiókok és az Azure SQL-adatbázisok, virtuális hálózatot, egy közvetlen kapcsolaton keresztül. A szolgáltatásvégpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak egy virtuális hálózaton legyenek elérhetőek. További információ: [A virtuális hálózati szolgáltatásvégpontok áttekintése](virtual-network-service-endpoints-overview.md).
- **Virtuális hálózatok közötti társviszony-Létesítésen keresztül**: A virtuális hálózatok közötti társviszony létesítésével lehetősége van arra, hogy virtuális hálózatokat kapcsoljon össze egymással, ezáltal lehetővé téve, hogy a virtuális hálózatok erőforrásai kommunikálhassanak egymással. Az összekacsolt virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban. További információ: [Virtuális hálózatok közötti társviszony létesítése](virtual-network-peering-overview.md).

## <a name="communicate-with-on-premises-resources"></a>Kommunikáció helyszíni erőforrásokkal

Az alábbiak bármely kombinációjával csatlakoztathatja helyszíni számítógépeit és hálózatait egy virtuális hálózathoz:

- **Pont – hely virtuális magánhálózati (VPN):** Egy virtuális hálózat és a egy a hálózat egyetlen számítógépe között létesített kapcsolat. Minden számítógép kapcsolatát konfigurálni kell, amely csatlakozni szeretne egy virtuális hálózathoz. Ez a kapcsolattípus remek választás, ha csak most ismerkedik az Azure szolgáltatással, illetve a fejlesztők számára, mert a meglévő hálózatot csak kis mértékben vagy egyáltalán nem kell módosítani. A számítógép és a virtuális hálózat közötti kommunikáció egy titkosított csatornán, az interneten keresztül zajlik. További tudnivalókért lásd: [Pont–hely típusú VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **Helyek közötti VPN:** A helyszíni VPN-eszköz és a egy virtuális hálózaton üzembe helyezett Azure VPN-átjáró között létesített kapcsolat. Ez a kapcsolattípus lehetővé teszi az összes engedélyezett helyszíni erőforrás számára, hogy elérjék a virtuális hálózatot. A helyszíni VPN-eszköz és egy Azure VPN Gateway átjáró közötti kommunikáció egy titkosított csatornán, az interneten keresztül zajlik. További tudnivalókért lásd: [Helyek közötti VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** Akkor jön létre a hálózat és az Azure között egy ExpressRoute-partneren keresztül. Ez a kapcsolat nem nyilvános. A forgalom nem az interneten keresztül halad át. További tudnivalókért lásd: [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Hálózati forgalom szűrése

A hálózati forgalom alhálózatok közötti szűrése az alábbi lehetőségek egyikével vagy akár mindkettővel elvégezhető:

- **Biztonsági csoportok:** Hálózati biztonsági csoportok és az alkalmazásbiztonsági csoportok tartalmazhat több bejövő és kimenő biztonsági szabály lehetővé teszi, hogy az erőforrások a forrás és cél IP-cím, port és protokoll forgalom szűrésére. További tudnivalókért lásd: [hálózati biztonsági csoportok](security-overview.md#network-security-groups) vagy [az alkalmazásbiztonsági csoportok](security-overview.md#application-security-groups).
- **Hálózati virtuális berendezések:** Egy hálózati virtuális berendezésen egy virtuális Gépet, amely hálózati funkciót, például egy tűzfal, WAN-optimalizáció vagy egyéb hálózati funkciók. A virtuális hálózatokban üzembe helyezhető hálózati virtuális berendezések listáját az [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) találja.

## <a name="route-network-traffic"></a>Hálózati forgalom továbbítása

Az Azure alapértelmezés szerint elosztja a forgalmat az alhálózatok, a csatlakoztatott virtuális hálózatok, a helyszíni hálózatok és az internet között. Az Azure által létrehozott alapértelmezett útvonalak felülírásához valósítsa meg az alábbi lehetőségek egyikét, vagy akár mindkettőt:

- **Útvonaltáblák:** Létrehozhat egyéni útválasztási táblázatokat az útvonalakat a vezérlőelemre, hova érkezzen a forgalom az egyes alhálózatokon. További tudnivalókért tekintse meg az [útválasztási táblázatokat](virtual-networks-udr-overview.md#user-defined) ismertető cikket.
- **Border gateway protocol (BGP) útvonalak:** Ha a helyszíni hálózathoz egy VPN-átjáró vagy ExpressRoute-kapcsolat használatával csatlakoztatja a virtuális hálózat, a helyszíni BGP-útvonalakat a virtuális hálózatok propagálható. Tudjon meg többet a BGP [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange) szolgáltatással együtt való használatáról

## <a name="azure-vnet-limits"></a>Az Azure VNet-korlátok

Bizonyos kerülő telepítheti az Azure erőforrások száma korlátozva van. A legtöbb Azure hálózati korlátozásait vannak, a maximális értékeket. Azonban továbbra is [bizonyos hálózati korlátok növelése](../azure-supportability/networking-quota-requests.md) megadott faxszáma a [virtuális hálózat lap korlátozza](../azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Díjszabás

Semmilyen díjat nem az Azure virtuális hálózat használatával, díjmentes. Normál erőforrások, például a virtuális gépek (VM) és az egyéb termékek vonatkoznak. További tudnivalókért lásd: [VNet díjszabás](https://azure.microsoft.com/pricing/details/virtual-network/) és az Azure [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/).

## <a name="next-steps"></a>További lépések

 A virtuális hálózatok használatának megkezdéséhez hozzon létre egyet, helyezzen üzembe rajta néhány virtuális gépet, majd létesítsen kommunikációt a virtuális gépek között. Ennek ismertetéséhez tekintse meg a [Virtuális hálózat létrehozása](quick-create-portal.md) című rövid útmutatót.
