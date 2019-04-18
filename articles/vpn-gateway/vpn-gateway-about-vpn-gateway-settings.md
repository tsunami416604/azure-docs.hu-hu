---
title: Azure-kapcsolatok létesítmények közötti VPN-átjáró beállításai |} A Microsoft Docs
description: Ismerje meg a VPN Gateway beállításairól az Azure virtuális hálózati átjárókhoz.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: cherylmc
ms.openlocfilehash: 4030c196d6a4de721b640f5da0b692f4d8157d12
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59049863"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Tudnivalók a VPN Gateway konfigurációs beállításairól

VPN-átjáró olyan virtuális hálózati átjáró, amely a virtuális hálózat és a helyszíni hely titkosított adatforgalmat továbbít nyilvános kapcsolaton keresztül. VPN-átjáró használatával az Azure gerinchálózatán keresztül a virtuális hálózatok közötti forgalom küldése.

VPN gateway-kapcsolat támaszkodik a konfiguráció több erőforrást, amelyek mindegyike tartalmazza a konfigurálható beállítások. Ez a cikk a szakaszok tárgyalják az erőforrások és a Resource Manager-alapú üzemi modellben létrehozott virtuális hálózat VPN-átjáró szoftverközponthoz kapcsolódó beállításokat. Leírások és topológia-diagramok megtalálhatja az összes kapcsolat-megoldás a [információk a VPN Gateway](vpn-gateway-about-vpngateways.md) cikk.

Ebben a cikkben szereplő értékek érvényesek (a a - gatewaytype Vpn típust használó virtuális hálózati átjárók) VPN-átjárókkal. Ez a cikk nem fedi le átjárótípusok vagy a átjárók zónaredundáns.

* A - GatewayType "ExpressRoute" vonatkozó értékek, tekintse meg a [az ExpressRoute virtuális hálózati átjárók](../expressroute/expressroute-about-virtual-network-gateways.md).

* Átjárók zónaredundáns lásd [kapcsolatos átjárók zónaredundáns](about-zone-redundant-vnet-gateways.md).

* Virtuális WAN lásd [kapcsolatos virtuális WAN](../virtual-wan/virtual-wan-about.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="gwtype"></a>Átjárótípusok

Minden virtuális hálózatnak csak egy átjárója lehet virtuális hálózati minden típusú. Ha egy virtuális hálózati átjárót hoz létre, győződjön meg arról, hogy az átjáró típusa megfeleljen a konfigurációnak.

A - GatewayType az elérhető értékek a következők:

* Vpn
* ExpressRoute

VPN-átjáró szükséges a `-GatewayType` *Vpn*.

Példa:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>Átjáró-termékváltozatok

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Átjárók Termékváltozatainak konfigurálása

#### <a name="azure-portal"></a>Azure Portal

Ha az Azure portal használatával a Resource Manager virtuális hálózati átjáró létrehozása, az átjáró-Termékváltozatot is választhat a legördülő menüben. A beállítások, lehetősége lesz az átjáró típusa és a VPN-típust választja felelnek meg.

#### <a name="powershell"></a>PowerShell

Az alábbi PowerShell-példa meghatározza az `-GatewaySku` VpnGw1. Ha-átjáró létrehozása a PowerShell segítségével, akkor először az IP-konfiguráció létrehozása, majd hivatkoznia kell rá a változók használata. Ebben a példában a konfigurációs változó $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizechange"></a>Átméretezés vagy a Termékváltozat módosítása

Ha a különböző átjárók Termékváltozatainak használni kívánt VPN-átjárót, a lehetőségek a következők vagy a az átjárók Termékváltozatainak átméretezése, vagy egy másik termékváltozatra módosításához. Ha módosítja egy másik átjáró-Termékváltozatot, teljesen törölje a meglévő átjárót, és hozhat létre egy újat. Az átjáró létrehozása akár 45 percet is igénybe vehet. Ezzel szemben ha az átjáró-Termékváltozatok, átméretezése nem áll mennyi állásidő mert törölnie kell, és építse újra az átjáró nem rendelkezik. Ha az átjárók Termékváltozatainak átméretezése helyett módosítsa a beállítást, ehhez célszerű. Előfordulhatnak azonban olyan szabályok átméretezésével kapcsolatos:

1. Az átméretezés során a VpnGw1, a VpnGw2 és a VpnGw3 termékváltozatok közül választhat.
2. Ha a régi átjárók termékváltozataival dolgozik, az átméretezéskor az Alapszintű, a Standard és a Nagy teljesítményű termékváltozatok közül választhat.
3. Az Alapszintű/Standard/Nagy teljesítményű termékváltozatokról **nem** méretezhet a VpnGw1/VpnGw2/VpnGw3 termékváltozatokra. Ehelyett kell [módosítása](#change) az új termékváltozatokra.

#### <a name="resizegwsku"></a>Az átjáró átméretezése

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="change"></a>(Örökölt) régiről az új Termékváltozatra módosítása

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connectiontype"></a>Kapcsolattípusok

A Resource Manager-alapú üzemi modellben az egyes konfigurációkhoz egy adott virtuális hálózati átjárójának kapcsolattípusa van szükség. A `-ConnectionType` lehetséges Resource Manager PowerShell-értékei a következők:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

A következő PowerShell-példa hozunk létre, amely megköveteli a kapcsolat típusa S2S kapcsolat *IPsec*.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>VPN-típusok

A VPN-átjáró konfigurálása a virtuális hálózati átjáró létrehozásakor meg kell adnia egy VPN-típussal. A VPN-típust választja a létrehozni kívánt kapcsolat topológia függ. A P2S-kapcsolat például egy Útvonalalapú VPN-típussal van szükség. Egy VPN-típussal is használ a hardverek is függ. S2S-konfigurációk VPN-eszközre van szükség. VPN-eszközök csak egy meghatározott VPN-típust támogatja.

A VPN-típust választja meg kell felelniük minden kapcsolat megoldásra vonatkozó követelményeket a szeretne létrehozni. Például, ha azt szeretné, hozhat létre S2S VPN gateway-kapcsolat és a egy P2S VPN gateway-kapcsolattal az azonos virtuális hálózatban, használja VPN-típust *RouteBased* mert P2S szükséges Útvonalalapú VPN-típust. Ellenőrizze, hogy a VPN-eszköz támogatott egy Útvonalalapú VPN-kapcsolatot is kellene. 

A virtuális hálózati átjáró létrehozása után nem módosíthatja a VPN-típust. Törölje a virtuális hálózati átjárót, és hozzon létre egy újat kell. Kétféle VPN-típus létezik:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Az alábbi PowerShell-példa meghatározza az `-VpnType` , *RouteBased*. Egy átjáró létrehozásakor biztosítania kell, hogy -VpnType megfeleljen a konfigurációnak.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>Átjáróra vonatkozó követelmények

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Átjáró-alhálózat

Mielőtt létrehozna egy VPN-átjárót, létre kell hoznia egy átjáró-alhálózatot. Az átjáró-alhálózat tartalmaz IP-címek a virtuális hálózati átjáró virtuális gépek és szolgáltatások használatához. A virtuális hálózati átjáró létrehozásakor átjáróként működő virtuális gépekhez az átjáró-alhálózat telepítve és konfigurálva a VPN-átjáró szükséges beállításokat. Semmit ne helyezzen üzembe másnak (például további VM-EK) az átjáró-alhálózathoz. Az átjáró-alhálózat neve "GatewaySubnet" megfelelően működjön. Az átjáró-alhálózat "GatewaySubnet" elnevezése lehetővé teszi, hogy tudja, hogy ez az alhálózat a virtuális hálózati átjáró virtuális gépek és szolgáltatások üzembe helyezéséhez Azure.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. Az átjáró-alhálózat IP-címekkel kiosztott az átjáróként működő virtuális gépekhez és átjárószolgáltatásokat. Egyes konfigurációknak a többinél nagyobb számú IP-címre van szükségük. Tekintse meg a konfigurációt, amelyet szeretne létrehozni, és győződjön meg arról, hogy szeretne létrehozni az átjáró-alhálózat megfelel ezeknek a követelményeknek megfelelő utasításokat. Emellett érdemes, hogy az átjáróalhálózat tartalmazza a elegendő IP-címet, lehetséges, további jövőbeni konfigurációk megvalósításához. Létrehozni egy átjáró-alhálózat kisebb, akár/29 méretű, javasoljuk, hogy hozzon létre egy átjáróalhálózatot/28-as vagy nagyobb (/ 28, / 27, / 26-os stb.). Ezzel a módszerrel funkciókat adhat a későbbiekben, ha nem kell bontania az átjárót, majd törölje és hozza létre újra az átjáró-alhálózat, hogy a további IP-címek.

A következő Resource Manager PowerShell-példa bemutatja egy GatewaySubnet nevű átjáró-alhálózatot. Láthatja, hogy a CIDR-jelölésrendszer/27-es, amely lehetővé teszi, hogy elegendő IP-címet, amely a jelenleg létező legtöbb konfiguráció esetében.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Helyi hálózati átjárók

 A helyi hálózati átjáró a virtuális hálózati átjáró eltér. Egy VPN-átjárókonfigurációtól létrehozásakor, az a helyi hálózati átjáró általában a helyszíni helyet jelöli. A klasszikus üzembe helyezési modellben a helyi hálózati átjárót a Helyi névvel illettük.

Nevezze el a helyi hálózati átjárót, a helyszíni VPN-eszköz nyilvános IP-címét, és a helyszínen található címelőtagok. Az Azure megvizsgálja a hálózati forgalmat a cél-címelőtagjainak, consults szintűre frissül, hogy a helyi hálózati átjáró a megadott konfigurációval, és ennek megfelelően irányítja a csomagokat. Helyi hálózati átjárókkal VPN gateway-kapcsolatot használó virtuális hálózatok közötti konfigurációkhoz is megad.

A következő PowerShell-példa egy új helyi hálózati átjárót hoz létre:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Néha szüksége a helyi hálózati átjáró beállításainak módosítására. Például ha felvétele vagy módosítása a címtartomány, vagy ha a VPN-eszköz IP-címe megváltozik. Lásd: [PowerShell-lel a helyi hálózati átjáró beállításainak módosítása](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>REST API-k, PowerShell-parancsmagok és parancssori felület

További technikai erőforrások és a megadott szintaxissal kapcsolatos követelményeket REST API-k, PowerShell-parancsmagok vagy Azure parancssori felület használatával a VPN Gateway-konfigurációk tekintse meg a következő lapokon:

| **Klasszikus** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/az.network/#networking) |[PowerShell](/powershell/module/az.network#vpn) |
| [REST API](https://msdn.microsoft.com/library/jj154113) |[REST API](/rest/api/network/virtualnetworkgateways) |
| Nem támogatott | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>További lépések

Rendelkezésre álló kapcsolati konfigurációkkal kapcsolatos további információkért lásd: [információk a VPN Gateway](vpn-gateway-about-vpngateways.md).
