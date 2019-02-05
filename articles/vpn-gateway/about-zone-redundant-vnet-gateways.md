---
title: Tudnivalók az Azure rendelkezésre állási zónákban a zónaredundáns virtuális hálózati átjárók |} A Microsoft Docs
description: Ismerje meg a rendelkezésre állási zónák VPN Gateway és ExpressRoute-átjárókra.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 0ba818ef3c24d0e88e662adf87b22cc938fe5fab
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697806"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Tudnivalók az Azure rendelkezésre állási zónákban a zónaredundáns virtuális hálózati átjárók

Telepítheti a VPN és ExpressRoute-átjárókra [Azure-beli rendelkezésre állási zónák](../availability-zones/az-overview.md). Ekkor a rugalmasság, a méretezhetőség és a magasabb rendelkezésre állás virtuális hálózati átjárók. Egy adott régión belül átjárók telepítése átjárók az Azure-beli rendelkezésre állási zónák fizikailag és logikailag elkülöníti a során a helyszíni hálózati kapcsolatok védelme az Azure-bA a zónaszintű hibák.

### <a name="zrgw"></a>Átjárók zónaredundáns

A virtuális hálózati átjárók a rendelkezésre állási zónák között automatikusan üzembe helyezéséhez, használhatja a zónaredundáns virtuális hálózati átjárók. Az átjárók zónaredundáns élvezheti az Azure-ban üzleti szempontból alapvető fontosságú, skálázható szolgáltatás-hozzáférésének zónarugalmasságot.

<br>
<br>

![átjárók zónaredundáns ábrája](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>A zónaszintű átjárók

A megadott zónában átjárók üzembe helyezéséhez használhatja a zónaszintű átjárók. Zónaszintű az átjáró telepítésekor az átjáró összes példánya ugyanabban a rendelkezésre állási zónában üzemelnek.

<br>
<br>

![a zónaszintű átjárók ábrája](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>Átjáró-termékváltozatok

Zónaszintű és zónaredundáns az átjárók új termékváltozatairól, érhetők el. Új virtuális hálózati átjárók Termékváltozatainak hozzáadtuk Azure AZ-régióban. Termékváltozatokban hasonlóak a megfelelő meglévő termékváltozata az ExpressRoute és VPN-átjáró, azzal a különbséggel, hogy ezek átjárók zónaredundáns és zónaszintű jellemzőek.

Az új átjáró termékváltozatok a következők:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Nyilvános IP-cím termékváltozatok

Átjárók zónaredundáns és a zónaszintű átjárókat egyaránt támaszkodjon az Azure nyilvános IP-erőforrásból *Standard* Termékváltozat. Az Azure nyilvános IP-erőforrásból konfigurációját határozza meg, hogy az átjáró üzembe helyezett zónaredundáns, vagy a zónaszintű. Ha létrehoz egy nyilvános IP-erőforráshoz az egy *alapszintű* SKU, az átjáró nem fog bármilyen zone redudancy, és az átjáró-erőforrásokat regionális lesz.

### <a name="pipzrg"></a>Átjárók zónaredundáns

Amikor hoz létre egy nyilvános IP cím használatával a **Standard** nyilvános IP SKU zóna megadása nélkül, a viselkedés eltér attól függően, hogy az átjáró VPN-átjáró vagy ExpressRoute-átjárónak. 

* A VPN-átjárót az két átjárópéldányok lesz telepítve, ezek három zónát zóna redundanciájának kívül bármely 2. 
* Egy ExpressRoute-átjárót, az is lehet több mint két olyan példányt, mert az átjáró is kiterjedhet a három zónák között.

### <a name="pipzg"></a>A zónaszintű átjárók

Amikor hoz létre egy nyilvános IP cím használatával a **Standard** nyilvános IP-Termékváltozat és adja meg a zóna (1, 2 vagy 3), az átjárópéldányok lesz telepítve, ugyanabban a zónában.

### <a name="piprg"></a>Regionális átjárók

Amikor hoz létre egy nyilvános IP cím használatával a **alapszintű** nyilvános IP-SKU, az átjáró regionális átjáróként üzemel, és minden zóna-redundanciát építve az átjáró nem rendelkezik.

## <a name="faq"></a>GYIK

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Mi változik, ha új Termékváltozatokban üzembe helyezhetem?

A szempontból az átjárók zóna redundanciával is telepítheti. Ez azt jelenti, hogy az átjárók az összes példányát telepíti, az Azure rendelkezésre állási zónák között, és minden egyes rendelkezésre állási zónában egy másik tartalék és frissítési tartományban. Ez lehetővé teszi az átjárók megbízható, elérhető és rugalmas zóna hibákkal szemben.

### <a name="can-i-use-the-azure-portal"></a>Használható az Azure Portalon?

Igen, az Azure portal használatával üzembe helyezése az új termékváltozatokra. Azonban csak az adott Azure-régiók, amelyek az Azure-beli rendelkezésre állási zónák új Termékváltozatokban megjelenik.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Mely régiók érhetők el az új termékváltozatokra használni?

Az új termékváltozatokra az Azure-régióban, amely rendelkezik az Azure-beli rendelkezésre állási zónák – USA középső RÉGIÓJA, közép-Franciaország, Észak-Európa, Nyugat-Európa és USA 2. nyugati régióban érhetők el. Továbbítja, hogy elérhetővé teszi átjárók zónaredundáns meg más nyilvános Azure-régióban.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Helyezhetem el a módosítása/áttelepítése/frissítése a meglévő virtuális hálózati átjárók zónaredundáns vagy zónaszintű átjárók?

A meglévő virtuális hálózati átjárók zónaredundáns vagy zónaszintű átjárók migrálása jelenleg nem támogatott. Azonban, törölje a meglévő átjárót és hozhat újra létre zónaredundáns vagy zónaszintű átjáró.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Telepítheti a VPN- és Express Route átjárók azonos virtuális hálózatba?

Az azonos virtuális hálózatba VPN- és Express Route-átjárók együttes jelenléte használata támogatott. Azonban, / 27-es kell lefoglalni az átjáró-alhálózat IP-címtartományt.

## <a name="next-steps"></a>További lépések

[Zónaredundáns virtuális hálózati átjáró létrehozása](create-zone-redundant-vnet-gateway.md)
