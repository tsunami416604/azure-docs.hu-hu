---
title: 'Azure VPN-átjáró: konfigurációs beállítások'
description: Ismerje meg az Azure virtuális hálózati átjáróinak VPN-átjáróbeállításait.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: d7a2040748d170b4e536df59947ea811f149d931
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244861"
---
# <a name="about-vpn-gateway-configuration-settings"></a>A VPN-átjáró konfigurációs beállításai

A VPN-átjáró olyan virtuális hálózati átjáró, amely titkosított forgalmat küld a virtuális hálózat és a helyszíni hely között nyilvános kapcsolaton keresztül. Vpn-átjáró használatával is küldhet forgalmat a virtuális hálózatok között az Azure gerinchálózatában.

A VPN-átjárókapcsolat több erőforrás konfigurációján alapul, amelyek mindegyike konfigurálható beállításokat tartalmaz. A cikk szakaszai ismertetik az erőforrás-kezelő telepítési modellben létrehozott virtuális hálózat VPN-átjárójához kapcsolódó erőforrásokat és beállításokat. Az egyes csatlakozási megoldások leírását és topológiai diagramjait a [VPN-átjáró ról szóló](vpn-gateway-about-vpngateways.md) cikkben találja.

A cikkben szereplő értékek VPN-átjárókat (a -GatewayType Vpn-t használó virtuális hálózati átjárókat) alkalmaznak. Ez a cikk nem terjed ki az összes átjárótípusra vagy zónaredundáns átjáróra.

* A -GatewayType "ExpressRoute" értékre vonatkozó értékeket az [ExpressRoute virtuális hálózati átjárói ban olvassa](../expressroute/expressroute-about-virtual-network-gateways.md)el.

* Zónaredundáns átjárók, [lásd: Zónaredundáns átjárók](about-zone-redundant-vnet-gateways.md).

* A Virtual [WAN-ról a Virtual WAN -ról.](../virtual-wan/virtual-wan-about.md)



## <a name="gateway-types"></a><a name="gwtype"></a>Átjárótípusok

Minden virtuális hálózatnak minden típushoz csak egy virtuális hálózati átjárója lehet. Virtuális hálózati átjáró létrehozásakor meg kell győződnie arról, hogy az átjáró típusa megfelelő a konfigurációnak.

A -GatewayType típus elérhető értékei a következők:

* Vpn
* ExpressRoute

A VPN-átjáróhoz `-GatewayType` *vpn*szükséges.

Példa:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gateway-skus"></a><a name="gwsku"></a>Átjáró-termékváltozatok

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Átjáró termékváltozatának konfigurálása

#### <a name="azure-portal"></a>Azure portál

Ha az Azure Portalon hozzon létre egy Resource Manager virtuális hálózati átjáró, kiválaszthatja az átjáró termékváltozat a legördülő menü használatával. A megjelenő beállítások megfelelnek a kiválasztott átjárótípusnak és VPN-típusnak.

#### <a name="powershell"></a>PowerShell

A következő PowerShell-példa `-GatewaySku` adja meg a vpnGw1. Amikor a PowerShell használatával hozzon létre egy átjárót, először létre kell hoznia az IP-konfigurációt, majd egy változót kell használnia, hogy hivatkozzon rá. Ebben a példában a konfigurációs változó $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizing-or-changing-a-sku"></a><a name="resizechange"></a>Termékváltozat átméretezése vagy módosítása

Ha rendelkezik egy VPN-átjáróval, és egy másik átjáró termékváltozatot szeretne használni, a beállítások az átjáró termékváltozatának átméretezése vagy egy másik termékváltozatra való váltás. Amikor egy másik átjáró termékváltozatra vált, teljesen törli a meglévő átjárót, és újat hoz létre. Egy átjáró létrehozása akár 45 percet is igénybe vehet. Ehhez képest egy átjáró termékváltozat ának átméretezésekor nincs sok állásidő, mert nem kell törölnie és újraépítenie az átjárót. Ha lehetősége van átméretezni az átjáró termékváltozatát, és nem módosíthatja azt, akkor ezt kell tennie. Vannak azonban szabályok átméretezés:

1. Az alapszintű termékváltozat kivételével átméretezheti a VPN-átjáró termékváltozatát egy másik VPN-átjáró termékváltozatra ugyanazon a generáción belül (Generation1 vagy Generation2). Például a Generation1 VpnGw1 átméretezhető az 1 generáció VpnGw2-re, de a Generation2 VpnGw2-re nem.
2. Ha a régi átjárók termékváltozataival dolgozik, az átméretezéskor az Alapszintű, a Standard és a Nagy teljesítményű termékváltozatok közül választhat.
3. Basic/Standard/HighPerformance termékkihívékről VPnGw ski-kre **nem méretezhető** át. Ehelyett az új sk-okra kell [váltania.](#change)

#### <a name="to-resize-a-gateway"></a><a name="resizegwsku"></a>Átjáró átméretezése

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="to-change-from-an-old-legacy-sku-to-a-new-sku"></a><a name="change"></a>Váltás régi (örökölt) termékváltozatról új termékváltozatra

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connection-types"></a><a name="connectiontype"></a>Kapcsolattípusok

Az Erőforrás-kezelő telepítési modelljében minden konfigurációhoz egy adott virtuális hálózati átjárókapcsolat-típusra van szükség. A `-ConnectionType` lehetséges Resource Manager PowerShell-értékei a következők:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

A következő PowerShell-példában létrehozunk egy S2S-kapcsolatot, amelyhez *iPsec*kapcsolattípus szükséges.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpn-types"></a><a name="vpntype"></a>VPN-típusok

Amikor virtuális hálózati átjárót hoz létre egy VPN-átjáró konfigurációjához, meg kell adnia egy VPN-típust. A választott VPN-típus a létrehozni kívánt kapcsolattopológiától függ. Egy P2S-kapcsolathoz például routealapú VPN-típus szükséges. A VPN-típus a használt hardvertől is függhet. Az S2S-konfigurációkhoz VPN-eszköz szükséges. Egyes VPN-eszközök csak egy bizonyos VPN-típust támogatnak.

A kiválasztott VPN-típusnak meg kell felelnie a létrehozni kívánt megoldás összes csatlakozási követelményének. Ha például s2s VPN-átjárókapcsolatot és P2S VPN-átjárókapcsolatot szeretne létrehozni ugyanahhoz a virtuális hálózathoz, akkor RouteBased típusú VPN-típust kell *használnia,* mivel a P2S-hez útvonalalapú VPN-típus szükséges. Azt is ellenőriznie kell, hogy a VPN-eszköz támogatja-e a RouteBased VPN-kapcsolatot. 

A virtuális hálózati átjáró létrehozása után nem módosíthatja a VPN típusát. Törölnie kell a virtuális hálózati átjárót, és létre kell hoznia egy újat. Kétféle VPN-típus létezik:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

A következő PowerShell-példa `-VpnType` *a RouteBased*.. Egy átjáró létrehozásakor biztosítania kell, hogy -VpnType megfeleljen a konfigurációnak.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="gateway-requirements"></a><a name="requirements"></a>Az átjáróra vonatkozó követelmények

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gateway-subnet"></a><a name="gwsub"></a>Átjáró alhálózata

Vpn-átjáró létrehozása előtt létre kell hoznia egy átjáró alhálózatot. Az átjáró alhálózata tartalmazza a virtuális hálózati átjáró virtuális gépei és szolgáltatásai által használt IP-címeket. A virtuális hálózati átjáró létrehozásakor az átjáró virtuális gépei az átjáró alhálózatára kerülnek, és a szükséges VPN-átjáróbeállításokkal vannak konfigurálva. Soha ne telepítsen semmi mást (például további virtuális gépeket) az átjáró alhálózatára. Az átjáró alhálózatának "GatewaySubnet" nevet kell adnia a megfelelő működéshez. Az átjáró alhálózatának "GatewaySubnet" elnevezése lehetővé teszi az Azure számára, hogy ez az alhálózat telepítse a virtuális hálózati átjáró virtuális gépeit és szolgáltatásait.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. Az átjáró alhálózatában lévő IP-címek az átjáró virtuális gépei és az átjárószolgáltatások számára vannak lefoglalva. Egyes konfigurációknak a többinél nagyobb számú IP-címre van szükségük. 

Az átjáró alhálózatának méretének tervezésekor tekintse meg a létrehozni kívánt konfiguráció dokumentációját. Például az ExpressRoute/VPN-átjáró egymás mellett létezik konfiguráció hoz egy nagyobb átjáró alhálózat, mint a legtöbb más konfigurációk. Emellett érdemes lehet győződjön meg arról, hogy az átjáró alhálózata elegendő IP-címet tartalmaz a lehetséges jövőbeli további konfigurációk befogadásához. Bár létrehozhat egy átjáró alhálózatot, amely /29-es méretű, javasoljuk, hogy hozzon létre egy /27 vagy nagyobb (/27, /26 stb.) átjáró alhálózatot, ha rendelkezik a rendelkezésre álló címterülettel. Ez a legtöbb konfigurációt befogadja.

A következő Erőforrás-kezelő PowerShell-példában egy GatewaySubnet nevű átjáró-alhálózat látható. Láthatja, hogy a CIDR jelölés egy /27-et ad meg, amely elegendő IP-címet tesz lehetővé a legtöbb jelenleg létező konfigurációhoz.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="local-network-gateways"></a><a name="lng"></a>Helyi hálózati átjárók

 A helyi hálózati átjáró nem más, mint a virtuális hálózati átjáró. VPN-átjáró konfigurációjának létrehozásakor a helyi hálózati átjáró általában a helyszíni helyet jelöli. A klasszikus üzembe helyezési modellben a helyi hálózati átjárót a Helyi névvel illettük.

Nevet ad a helyi hálózati átjárónak, a helyszíni VPN-eszköz nyilvános IP-címét, és megadja a helyszíni helyen található címelőtagokat. Az Azure megvizsgálja a hálózati forgalom célcím-előtagait, megvizsgálja a helyi hálózati átjáróhoz megadott konfigurációt, és ennek megfelelően irányítja a csomagokat. A VPN-átjárókapcsolatot használó virtuális hálózat és virtuális hálózat konfigurációihoz helyi hálózati átjárókat is megadhat.

A következő PowerShell-példa új helyi hálózati átjárót hoz létre:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Néha módosítania kell a helyi hálózati átjáró beállításait. Például, ha hozzáadja vagy módosítja a címtartományt, vagy ha a VPN-eszköz IP-címe megváltozik. Lásd: [Helyi hálózati átjáró beállításainak módosítása a PowerShell használatával](vpn-gateway-modify-local-network-gateway.md).

## <a name="rest-apis-powershell-cmdlets-and-cli"></a><a name="resources"></a>REST API-k, PowerShell-parancsmagok és CLI

A REST API-k, a PowerShell-parancsmagok vagy az Azure CLI VPN-átjárókonfigurációk használatakor további technikai erőforrásokat és speciális szintaxisi követelményeket a következő lapokon lehet látni:

| **Klasszikus** | **Resource Manager** |
| --- | --- |
| [Powershell](/powershell/module/az.network/#networking) |[Powershell](/powershell/module/az.network#vpn) |
| [REST API](https://msdn.microsoft.com/library/jj154113) |[REST API](/rest/api/network/virtualnetworkgateways) |
| Nem támogatott | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>További lépések

Az elérhető kapcsolatkonfigurációkról a [VPN-átjáró – további](vpn-gateway-about-vpngateways.md)információt talál.
