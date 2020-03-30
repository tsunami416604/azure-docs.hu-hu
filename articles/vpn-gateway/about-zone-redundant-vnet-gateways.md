---
title: Zónaredundáns virtuális hálózati átjárók az Azure rendelkezésre állási zónáiban
description: Ismerje meg a VPN-átjárók és az ExpressRoute-átjárók at a rendelkezésre állási zónákban.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: cherylmc
ms.openlocfilehash: f1bbaab99b6422de4053839e2099869d2d08db95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864298"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Zónaredundáns virtuális hálózati átjárók az Azure rendelkezésre állási zónáiban

VPN- és ExpressRoute-átjárókat telepíthet az [Azure rendelkezésre állási zónáiban.](../availability-zones/az-overview.md) Ez rugalmasságot, méretezhetőséget és magasabb szintű rendelkezésre állást biztosít a virtuális hálózati átjárók számára. Az átjárók Azure-beli rendelkezésre állási zónákban történő üzembe helyezésével fizikailag és logikailag is elválaszthatók a régióban található átjárók, miközben az Azure-ral létesített helyszíni hálózati kapcsolat megvédhető a zónaszintű hibáktól.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Zónaredundáns átjárók

A virtuális hálózati átjárók automatikus üzembe helyezéséhez a rendelkezésre állási zónák között, használhatja zónaredundáns virtuális hálózati átjárók. A zónaredundáns átjárók segítségével a zóna rugalmasságából profitálhat az Azure-beli létfontosságú, méretezhető szolgáltatásaihoz.

<br>
<br>

![zónaredundáns átjárók grafikus](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Övezeti átjárók

Átjárók üzembe helyezése egy adott zónában, használhatja a zónaszintű átjárók. Zónaszintű átjáró telepítésekor az átjáró összes példánya ugyanabban a rendelkezésre állási zónában van telepítve.

<br>
<br>

![zónaátjárók grafikus](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>Átjáró-termékváltozatok

Zónaredundáns és zónaszintű átjárók érhetők el új átjáró-skus. Új virtuális hálózati átjáró-sk-eket adtunk hozzá az Azure AZ régióiban. Ezek a termékkészletek hasonlóak az ExpressRoute és a VPN-átjáró megfelelő meglévő termékkészleteihez, azzal a különbséggel, hogy a zónaredundáns és a zónaszintű átjárókra jellemzőek. Ezeket a termékváltozatokat a termékváltozat nevében az "AZ" alapján azonosíthatja.

Az átjárótermékkel kapcsolatos további tudnivalókért tekintse meg a [VPN-átjáró termékkészleteit](vpn-gateway-about-vpngateways.md#gwsku) és az [ExpressRoute átjárótermékkel kapcsolatos termékinformációs készleteket.](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku)

## <a name="public-ip-skus"></a><a name="pipskus"></a>Nyilvános IP SKUs

Zónaredundáns átjárók és zónaszintű átjárók egyaránt támaszkodnak az Azure nyilvános IP-erőforrás *standard* termékváltozat. Az Azure nyilvános IP-erőforrás konfigurációja határozza meg, hogy a telepített átjáró zónaredundáns vagy zónaszintű. Ha egy nyilvános IP-erőforrást hoz létre *egy egyszerű* termékváltozattal, az átjáró nem rendelkezik zónaredundanciával, és az átjáró erőforrásai regionálisak lesznek.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Zónaredundáns átjárók

Ha zóna megadása nélkül hoz létre nyilvános IP-címet a **standard** nyilvános IP-termékváltozat használatával, a viselkedés attól függően változik, hogy az átjáró VPN-átjáró vagy ExpressRoute-átjáró. 

* A VPN-átjáró konkretincia a két átjárópéldányok lesznek telepítve bármely 2 a három zónából, hogy zóna-redundancia. 
* ExpressRoute-átjáró esetén, mivel kettőnél több példány is lehet, az átjáró a három zónára is kiterjedhet.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Övezeti átjárók

Ha nyilvános IP-címet hoz létre a **szabványos** nyilvános IP-termékváltozat használatával, és megadja a zónát (1, 2 vagy 3), az összes átjárópéldány ugyanabban a zónában lesz telepítve.

### <a name="regional-gateways"></a><a name="piprg"></a>Regionális átjárók

Ha nyilvános IP-címet hoz létre az **alapszintű** nyilvános IP-termékváltozat használatával, az átjáró regionális átjáróként lesz telepítve, és nem rendelkezik az átjáróba beépített zónaredundanciával.

## <a name="faq"></a><a name="faq"></a>GYIK

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Mi változik az új sk-ek telepítésekor?

Az Ön szemszögéből az átjárók zónaredundanciával telepíthetők. Ez azt jelenti, hogy az átjárók összes példánya az Azure rendelkezésre állási zónáiban lesz telepítve, és minden rendelkezésre állási zóna egy másik hiba és frissítési tartomány. Ez megbízhatóbbá, elérhetőbbé és zónahibákkal szembenellenállóbbá teszi az átjárókat.

### <a name="can-i-use-the-azure-portal"></a>Használhatom az Azure Portalt?

Igen, az Azure Portal segítségével telepítheti az új sk-eket. Ezek az új termékkészletek azonban csak azokban az Azure-régiókban jelennek meg, amelyek rendelkeznek az Azure rendelkezésre állási zónáival.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Milyen régiók érhetők el az új sk-ek használatához?

Az új termékverziók az Azure-régiókban érhetők el, amelyek rendelkeznek az Azure rendelkezésre állási zónáival – Közép-USA, Franciaország Közép-, Észak-Európa, Nyugat-Európa és AZ USA nyugati régiói 2 régió, USA keleti régiója, USA keleti régiója 2, Délkelet-Ázsia, Kelet-Japán, Az Egyesült Királyság déli része. A jövőben zónaredundáns átjárókat teszünk elérhetővé az Ön számára más Azure nyilvános régiókban.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Módosíthatom/áttelepíthetem/frissíthetem a meglévő virtuális hálózati átjáróimat zónaredundáns vagy zónaszintű átjárókra?

A meglévő virtuális hálózati átjárók áttelepítése zónaredundáns vagy zónaszintű átjárókba jelenleg nem támogatott. Azonban törölheti a meglévő átjárót, és újra létrehozhat egy zónaredundáns vagy zónaszintű átjárót.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Telepíthetek VPN- és Express Route átjárókat is ugyanabban a virtuális hálózatban?

Az ugyanazon virtuális hálózatban lévő VPN- és express route átjárók együttélése támogatott. Azonban le kell foglalnia egy /27 IP-címtartományt az átjáró alhálózathoz.

## <a name="next-steps"></a>További lépések

[Zónaredundáns virtuális hálózati átjáró létrehozása](create-zone-redundant-vnet-gateway.md)
