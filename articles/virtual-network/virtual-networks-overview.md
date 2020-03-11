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
ms.openlocfilehash: 3b908406c8717d2fa8834bc4dff1bcd27ec4761f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372389"
---
# <a name="what-is-azure-virtual-network"></a>Mi az Azure Virtual Network?

Az Azure Virtual Network (VNet) az Azure-beli magánhálózat alapvető építőeleme. A VNet számos Azure-erőforrást, például Azure Virtual Machines (VM) tesz lehetővé az egymással, az internettel és a helyszíni hálózatokkal való biztonságos kommunikációhoz. A VNet hasonló egy hagyományos hálózathoz, amelyet a saját adatközpontban fog használni, de az Azure infrastruktúrájának, például a méretezhetőség, a rendelkezésre állás és az elkülönítés további előnyeit biztosítja.

## <a name="vnet-concepts"></a>VNet fogalmak

- **Címterület:** VNet létrehozásakor egyéni magánhálózati IP-címtartományt kell megadnia nyilvános és magánhálózati (RFC 1918) címek használatával. Az Azure magánhálózati IP-címet rendel hozzá a virtuális hálózatokon belüli erőforrásokhoz a hozzárendelt címtérből. Ha például egy virtuális gépet központilag telepít egy VNet, a 10.0.0.0/16, a virtuális gép egy magánhálózati IP-címet (például 10.0.0.4) fog rendelni.
- **Alhálózatok:** Az alhálózatok lehetővé teszik a virtuális hálózat egy vagy több alhálózatra való felosztását, és a virtuális hálózat címterület egy részének lefoglalását az egyes alhálózatokra. Ezután üzembe helyezheti az Azure-erőforrásokat egy adott alhálózaton. A hagyományos hálózatokhoz hasonlóan az alhálózatok is lehetővé teszik, hogy a VNet-címtartomány a szervezet belső hálózatának megfelelő szegmensekre legyen felosztva. Ez javítja a címek kiosztásának hatékonyságát is. Az alhálózatokon belüli erőforrásokat hálózati biztonsági csoportokkal is biztonságossá teheti. További információ: [biztonsági csoportok](security-overview.md).
- **Régiók**: a VNet egyetlen régióra/helyre vonatkozik; azonban a különböző régiókban található több virtuális hálózat összekapcsolható Virtual Network-társítással.
- **Előfizetés:** A VNet hatóköre egy előfizetés. Több virtuális hálózatot is megvalósíthat az egyes Azure-[előfizetéseken](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) és Azure-[régiókon](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) belül.

## <a name="best-practices"></a>Ajánlott eljárások

A hálózat Azure-ban való létrehozásakor fontos szem előtt tartani a következő általános tervezési alapelveket:

- Győződjön meg arról, hogy nem átfedésben lévő címterület. Győződjön meg arról, hogy a VNet címtartomány (CIDR-blokk) nem fedi át a szervezet más hálózati tartományait.
- Az alhálózatok nem fedik le a VNet teljes címterületját. Tervezze meg előre, és foglaljon le némi címtartományt a jövő számára.
- Azt javasoljuk, hogy kevesebb nagy virtuális hálózatok legyen, mint több kis virtuális hálózatok. Ez megakadályozza a felügyelet terhelését.
- A VNet biztonságossá tételéhez rendeljen hozzá hálózati biztonsági csoportokat (NSG) az alá tartozó alhálózatokhoz.

## <a name="communicate-with-the-internet"></a>Kommunikáció az internettel

A VNet összes erőforrása alapértelmezés szerint képes kommunikálni az internet felé. Bejövő kommunikációt létesíthet egy erőforrással egy nyilvános IP-cím vagy Load Balancer hozzárendelésével. A kimenő kapcsolatok kezeléséhez is használhat nyilvános IP-címet vagy Load Balancert.  Az Azure kimenő kapcsolataira vonatkozó további információkat a [kimenő kapcsolatokat](../load-balancer/load-balancer-outbound-connections.md), a [nyilvános IP-címeket](virtual-network-public-ip-address.md) vagy a [Load Balancert](../load-balancer/load-balancer-overview.md) ismertető cikkekben talál.

>[!NOTE]
>Ha csak belső [Standard Load Balancert](../load-balancer/load-balancer-standard-overview.md) használ, akkor a kimenő kapcsolat mindaddig nem lesz elérhető, amíg meg nem határozza, hogyan működjenek együtt a [kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md) egy példányszintű nyilvános IP-vel vagy egy nyilvános Load Balancerrel.

## <a name="communicate-between-azure-resources"></a>Azure-erőforrások közötti kommunikáció

Az Azure-erőforrások biztonságosan kommunikálnak egymással az alábbi módok egyikével:

- **Virtuális hálózaton keresztül**: Virtuális gépeket és számos egyéb típusú Azure-erőforrást is üzembe helyezhet egy virtuális hálózaton (pl. Azure App Service Environment-környezetek, Azure Kubernetes Service (AKS) és Azure Virtual Machine Scale Sets). A virtuális hálózatokon üzembe helyezhető Azure-erőforrások teljes listájáért lásd: [Virtuális hálózati szolgáltatás integrálása](virtual-network-for-azure-services.md).
- **Virtuális hálózati szolgáltatásvégponton keresztül**: Kibővítheti virtuális hálózatának privát címterét és identitását az Azure-szolgáltatási erőforrásokra (pl. Azure Storage-fiókokra és Azure SQL-adatbázisokra) egy közvetlen kapcsolaton keresztül. A szolgáltatásvégpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak egy virtuális hálózaton legyenek elérhetőek. További információ: [A virtuális hálózati szolgáltatásvégpontok áttekintése](virtual-network-service-endpoints-overview.md).
- **A VNet**-társításon keresztül: a virtuális hálózatokat összekapcsolhatja egymással, így a virtuális hálózatban lévő erőforrások a virtuális hálózatok közötti kommunikációt is lehetővé teszik. Az összekacsolt virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban. További információ: [Virtuális hálózatok közötti társviszony létesítése](virtual-network-peering-overview.md).

## <a name="communicate-with-on-premises-resources"></a>Kommunikáció helyszíni erőforrásokkal

Az alábbiak bármely kombinációjával csatlakoztathatja helyszíni számítógépeit és hálózatait egy virtuális hálózathoz:

- **Pont–hely típusú virtuális magánhálózat (VPN):** Egy virtuális hálózat és a hálózat egyetlen számítógépe között létesített kapcsolat. Minden számítógép kapcsolatát konfigurálni kell, amely csatlakozni szeretne egy virtuális hálózathoz. Ez a kapcsolattípus remek választás, ha csak most ismerkedik az Azure szolgáltatással, illetve a fejlesztők számára, mert a meglévő hálózatot csak kis mértékben vagy egyáltalán nem kell módosítani. A számítógép és a virtuális hálózat közötti kommunikáció egy titkosított csatornán, az interneten keresztül zajlik. További tudnivalókért lásd: [Pont–hely típusú VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **Helyek közötti VPN:** A helyszíni VPN-eszköz és egy virtuális hálózaton üzembe helyezett Azure VPN Gateway átjáró között létesített kapcsolat. Ez a kapcsolattípus lehetővé teszi az összes engedélyezett helyszíni erőforrás számára, hogy elérjék a virtuális hálózatot. A helyszíni VPN-eszköz és egy Azure VPN Gateway átjáró közötti kommunikáció egy titkosított csatornán, az interneten keresztül zajlik. További tudnivalókért lásd: [Helyek közötti VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute**: Saját hálózata és az Azure között egy ExpressRoute-partneren keresztül létesített kapcsolat. Ez a kapcsolat nem nyilvános. A forgalom nem az interneten keresztül halad át. További tudnivalókért lásd: [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Hálózati forgalom szűrése

A hálózati forgalom alhálózatok közötti szűrése az alábbi lehetőségek egyikével vagy akár mindkettővel elvégezhető:

- **Biztonsági csoportok:** A hálózati biztonsági csoportok és az alkalmazás biztonsági csoportjai több bejövő és kimenő biztonsági szabályt is tartalmazhatnak, amelyekkel a forrás és a cél IP-címe, portja és protokollja alapján szűrheti a forgalmat és az erőforrásokat. További információ: [hálózati biztonsági csoportok](security-overview.md#network-security-groups) vagy [alkalmazás biztonsági csoportjai](security-overview.md#application-security-groups).
- **Hálózati virtuális berendezések:** A hálózati virtuális berendezések olyan virtuális gépek, amelyek egy hálózati funkciót látnak el, például tűzfal, WAN-optimalizáció vagy egyéb hálózati funkciók. A virtuális hálózatokban üzembe helyezhető hálózati virtuális berendezések listáját az [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) találja.

## <a name="route-network-traffic"></a>Hálózati forgalom továbbítása

Az Azure alapértelmezés szerint elosztja a forgalmat az alhálózatok, a csatlakoztatott virtuális hálózatok, a helyszíni hálózatok és az internet között. Az Azure által létrehozott alapértelmezett útvonalak felülírásához valósítsa meg az alábbi lehetőségek egyikét, vagy akár mindkettőt:

- **Útválasztási táblázatok:** Létrehozhat egyéni útválasztási táblázatokat, amelyek útvonalai szabályozzák, hogy melyik alhálózat esetében hova érkezzen a forgalom. További tudnivalókért tekintse meg az [útválasztási táblázatokat](virtual-networks-udr-overview.md#user-defined) ismertető cikket.
- **Border Gateway Protocol (BGP-) útvonalak:** Ha a virtuális hálózatát VPN-átjáró vagy ExpressRoute-kapcsolat használatával csatlakoztatja a helyszíni hálózathoz, propagálhatja a helyszíni BGP-útvonalakat a virtuális hálózatára. Tudjon meg többet a BGP [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange) szolgáltatással együtt való használatáról

## <a name="azure-vnet-limits"></a>Az Azure VNet korlátai

Bizonyos korlátozások vonatkoznak az üzembe helyezhető Azure-erőforrások számának körére. A legtöbb Azure-hálózat korlátja a maximális érték. Azonban [bizonyos hálózati korlátokat](../azure-portal/supportability/networking-quota-requests.md) a [VNet korlátai lapon](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)adhat meg. 

## <a name="pricing"></a>Díjszabás

Az Azure VNet díjmentesen vehető igénybe. A standard díjak olyan erőforrásokra vonatkoznak, mint a Virtual Machines (VM) és más termékek. További információ: a [VNet díjszabása](https://azure.microsoft.com/pricing/details/virtual-network/) és az Azure [díjszabásának kalkulátora](https://azure.microsoft.com/pricing/calculator/).

## <a name="next-steps"></a>Következő lépések

 A virtuális hálózatok használatának megkezdéséhez hozzon létre egyet, helyezzen üzembe rajta néhány virtuális gépet, majd létesítsen kommunikációt a virtuális gépek között. Ennek ismertetéséhez tekintse meg a [Virtuális hálózat létrehozása](quick-create-portal.md) című rövid útmutatót.
