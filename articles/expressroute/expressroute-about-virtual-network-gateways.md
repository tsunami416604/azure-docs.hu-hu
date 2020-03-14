---
title: Kapcsolatos az ExpressRoute virtuális hálózati átjárók – Azure |} A Microsoft Docs
description: További információ a virtuális hálózati átjárók az expressroute-hoz. Ez a cikk az átjáró-termékváltozatok és típusok kapcsolatos információkat tartalmaz.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281417"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Tudnivalók a ExpressRoute virtuális hálózati átjárókkal kapcsolatban

Az Azure-beli virtuális hálózat és a helyszíni hálózat ExpressRoute-on keresztüli összekapcsolásához először létre kell hoznia egy virtuális hálózati átjárót. A virtuális hálózati átjáró két célt szolgál: az Exchange IP-útvonalakat a hálózatok között, és átirányítja a hálózati forgalmat. Ez a cikk az átjárók típusait, az átjáró-SKU-ket és a becsült teljesítményt mutatja be SKU alapján. Ez a cikk a ExpressRoute [FastPath](#fastpath)is ismerteti, amely lehetővé teszi a helyszíni hálózatról érkező hálózati forgalmat a teljesítmény javítása érdekében a virtuális hálózati átjáró megkerülése érdekében.

## <a name="gateway-types"></a>Átjárótípusok

A virtuális hálózati átjáró létrehozásakor több beállítást adja meg kell. A szükséges beállításokat egyik "-GatewayType", megadja, hogy az átjáró, az expressroute vagy VPN-forgalmat. A két átjáró típusok a következők:

* **VPN** – titkosított forgalom küldése a nyilvános interneten keresztül, a "VPN" átjáró-típust használja. Ez is nevezik VPN-átjáró. A hely–hely, pont–hely és a virtuális hálózatok közötti kapcsolat kapcsolatok mind VPN-átjárót használnak.

* **ExpressRoute** – a hálózati forgalom privát kapcsolatban való küldéséhez használja a "ExpressRoute" átjáró típusát. Ez egy ExpressRoute-átjárót is nevezik, és az ExpressRoute konfigurálása során használt átjáró típusát.

Mindegyik virtuális hálózat csak egy virtuális hálózati átjáróval rendelkezhet átjárótípusonként. Rendelkezhet például egy virtuális hálózati átjáróval, amely a -GatewayType Vpn típust, és egy másikkal, amelyik a -GatewayType ExpressRoute típust használja.

## <a name="gwsku"></a>Átjáró-termékváltozatok
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Ha az átjárót egy nagyobb teljesítményű átjáró-SKU-ra szeretné frissíteni, akkor a legtöbb esetben használhatja az "átméretezés – AzVirtualNetworkGateway" PowerShell-parancsmagot. Ez a Standard és a nagy teljesítményű termékváltozatok frissítéseket fog működni. Azonban az UltraPerformance Termékváltozattal frissíteni, meg kell hozza létre újra az átjárót. Újra létre kellene hoznia egy átjáró leállást.

### <a name="aggthroughput"></a>Az átjárói SKU becsült teljesítménye
Az alábbi táblázatban az átjárótípusok és a becsült teljesítményét mutatja. Ez a tábla a Resource Managerrel végzett és a klasszikus üzembe helyezési modellre is érvényes.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Alkalmazások teljesítményének attól függ, hogy több tényezővel bővül, például a végpontok közötti késés és a forgalom adatfolyamait, megnyílik az alkalmazás számát. A táblában szereplő számok jelölik a felső határ, amely ideális környezetet a elméletileg érheti el az alkalmazást.
>
>

## <a name="gwsub"></a>Átjáró-alhálózat

ExpressRoute-átjáró létrehozása előtt létre kell hoznia egy átjáró-alhálózatot. Az átjáró-alhálózat tartalmazza azokat az IP-címeket, amelyeket a Virtual Network Gateway-beli virtuális gépek és szolgáltatások használnak. A virtuális hálózati átjáró létrehozásakor az átjáróként működő virtuális gépek üzembe helyezése az átjáró alhálózatán történik, és a szükséges ExpressRoute-átjáró beállításaival van konfigurálva. Soha ne helyezzen üzembe semmilyen mást (például további virtuális gépeket) az átjáró-alhálózathoz. A megfelelő működéshez az átjáró alhálózatának "GatewaySubnet" nevűnek kell lennie. A "GatewaySubnet" átjáró alhálózatának elnevezése lehetővé teszi, hogy az Azure tudja, hogy ez az alhálózat a virtuális hálózati átjárók és a szolgáltatások üzembe helyezéséhez.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. Az átjáró-alhálózat IP-címei az átjáró virtuális gépei és az átjáró szolgáltatások számára vannak lefoglalva. Egyes konfigurációknak a többinél nagyobb számú IP-címre van szükségük. 

Az átjáró-alhálózat méretének tervezésekor tekintse meg a létrehozni kívánt konfiguráció dokumentációját. A ExpressRoute/VPN Gateway egyazon konfiguráció például nagyobb átjáró-alhálózatot igényel, mint a legtöbb más konfiguráció. Emellett érdemes lehet gondoskodni arról, hogy az átjáró-alhálózat elegendő IP-címet tartalmazzon a lehetséges jövőbeli további konfigurációkhoz. Míg az átjáró-alhálózatot kisebb as/29-ként is létrehozhatja, javasoljuk, hogy hozzon létre egy/27-ös vagy nagyobb átjáró-alhálózatot (/27,/26 stb.), ha a rendelkezésre álló címtartomány erre van szüksége. Ez a legtöbb konfigurációt kielégíti.

A következő Resource Manager PowerShell-példa egy GatewaySubnet nevű átjáró-alhálózatot mutat be. Láthatja, hogy a CIDR jelölése egy/27, amely elegendő IP-címet biztosít a jelenleg létező konfigurációkhoz.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zrgw"></a>Zóna – redundáns átjárók SKU-i

ExpressRoute-átjárók az Azure-beli rendelkezésre állási zónák is telepítheti. Ez fizikailag és logikailag a két különböző rendelkezésre állási zónában, a helyszíni hálózati kapcsolatok védelme az Azure-bA a zónaszintű hibák történő.

![Zónaredundáns ExpressRoute-átjáró](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Átjárók zónaredundáns adott új átjáró-termékváltozatok ExpressRoute-átjárót használja.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Az új átjáró-termékváltozatok támogatják az igényeinek leginkább megfelelő egyéb telepítési lehetőségekért is. Az új átjáró SKU-k használatával virtuális hálózati átjáró létrehozásakor, akkor is telepíteni az átjárót a megadott zónában. Ez a zónaszintű átjáró nevezzük. Zónaszintű az átjáró telepítésekor az átjáró összes példánya ugyanabban a rendelkezésre állási zónában üzemelnek.

## <a name="fastpath"></a>FastPath

A ExpressRoute virtuális hálózati átjáró a hálózati útvonalak cseréjére és a hálózati forgalom irányítására szolgál. A FastPath úgy lett kialakítva, hogy javítsa a helyszíni hálózat és a virtuális hálózat adatelérési útjainak teljesítményét. Ha engedélyezve van, a FastPath közvetlenül a virtuális hálózatban lévő virtuális gépekre küldi a hálózati forgalmat, megkerülve az átjárót.

További információ a FastPath, beleértve a korlátozásokat és a követelményeket, lásd: [a FastPath névjegye](about-fastpath.md).

## <a name="resources"></a>REST API-k és PowerShell-parancsmagok
További technikai erőforrások és a megadott szintaxissal kapcsolatos követelményeket REST API-k és PowerShell-parancsmagok használata a virtuális hálózati átjáró-konfigurációk tekintse meg a következő lapokon:

| **Klasszikus** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Következő lépések

További információ az elérhető kapcsolatok konfigurációjáról: [ExpressRoute – áttekintés](expressroute-introduction.md).

A ExpressRoute-átjárók létrehozásával kapcsolatos további információkért lásd: [virtuális hálózati átjáró létrehozása a ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

A zóna redundáns átjárók konfigurálásával kapcsolatos további információkért lásd: [zóna – redundáns virtuális hálózati átjáró létrehozása](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

A FastPath kapcsolatos további információkért lásd: [a FastPath névjegye](about-fastpath.md).