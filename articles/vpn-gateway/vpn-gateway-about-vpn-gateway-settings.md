---
title: VPN-átjáró beállításokat a létesítmények közötti Azure-kapcsolatok |} Microsoft Docs
description: További tudnivalók az Azure virtuális hálózati átjárók VPN-átjáró beállításainak.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ae665bc5-0089-45d0-a0d5-bc0ab4e79899
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: cherylmc
ms.openlocfilehash: 60cdc7bbe08df7816560e9720f96edc51769c342
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824846"
---
# <a name="about-vpn-gateway-configuration-settings"></a>VPN-átjáró konfigurációs beállításairól

VPN-átjáró olyan virtuális hálózati átjáró által a titkosított forgalmat a virtuális hálózat és a helyszíni hely közötti nyilvános kapcsolaton keresztül. VPN-átjáró segítségével virtuális hálózatok közötti Azure gerincét között forgalmat küldeni.

VPN gateway-kapcsolattal konfigurálható beállításokat tartalmaz, amelyek mindegyike több erőforrás konfigurációjának támaszkodik. Ebben a cikkben a szakaszok tárgyalják az erőforrások és a Resource Manager üzembe helyezési modellel létrehozott virtuális hálózat VPN-átjáró szoftverközponthoz kapcsolódó beállításokat. Minden kapcsolat megoldás megtalálhatja leírásokat és topológiai diagramot a [VPN-átjáró](vpn-gateway-about-vpngateways.md) cikk.

>[!NOTE]
> Ebben a cikkben szereplő értékek a - GatewayType "Vpn" használó virtuális hálózati átjárók vonatkozik. Ezért ezek adott virtuális hálózati átjárók VPN-átjáróként hivatkozunk. Az ExpressRoute-átjáró értékei nem ugyanazokat az értékeket a VPN-átjáróként használt.
>
>-GatewayType "ExpressRoute" vonatkozó értékeket, lásd: [ExpressRoute a virtuális hálózati átjárók](../expressroute/expressroute-about-virtual-network-gateways.md).
>
>

## <a name="gwtype"></a>Átjáró típusa

Minden egyes virtuális hálózati csak egy virtuális hálózati átjáró típusonkénti van. Ha a virtuális hálózati átjáró hoz létre, győződjön meg arról, hogy helyesek-e az átjáró típusa a konfigurációhoz.

A - GatewayType a lehetséges értékek a következők:

* Vpn
* ExpressRoute

A VPN-átjáró által igényelt a `-GatewayType` *Vpn*.

Példa:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>Átjáró-termékváltozatok

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Konfiguráljon egy átjárót Termékváltozat

#### <a name="azure-portal"></a>Azure Portal

Ha egy erőforrás-kezelő virtuális hálózati átjáró létrehozásához használhatja az Azure-portálon, válassza a legördülő lista használatával az átjáró-Termékváltozat. A beállítások, lehetősége lesz az átjáró típusa és a választott VPN-típus felel meg.

#### <a name="powershell"></a>PowerShell

A következő PowerShell-példa meghatározza, hogy a `-GatewaySku` VpnGw1 szerint. Amikor egy átjáró létrehozása a PowerShell használatával, akkor először hozza létre az IP-konfigurációt, majd a változó segítségével hivatkozik rá. Ebben a példában a konfigurációs változó $gwipconfig.

```powershell
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizechange"></a>Átméretezése, vagy egy SKU módosítása

Ha van VPN-átjáró és egy másik átjáró SKU használni kívánt, az alábbi lehetőségek állnak az átjáró-Termékváltozat vagy átméretezése, vagy egy másikra módosításához. Ha módosítja egy másik átjáró-Termékváltozat, a meglévő átjáró teljes törlése, és egy új build. Ez legfeljebb 45 perc is beletelhet. Szemben ha azt szeretné, hogy egy átjáró-Termékváltozat, fog minimális állásidő mert Önnek nincs törlése, és építse újra az átjárót. Lehetősége van az átjáró-Termékváltozat átméretezése, mint megváltoztatnia, ha ehhez célszerű. Van azonban szabályok vonatkozó átméretezése:

1. Az átméretezés során a VpnGw1, a VpnGw2 és a VpnGw3 termékváltozatok közül választhat.
2. Ha a régi átjárók termékváltozataival dolgozik, az átméretezéskor az Alapszintű, a Standard és a Nagy teljesítményű termékváltozatok közül választhat.
3. Az Alapszintű/Standard/Nagy teljesítményű termékváltozatokról **nem** méretezhet a VpnGw1/VpnGw2/VpnGw3 termékváltozatokra. Ehelyett kell [módosítása](#change) az új termékváltozatok számára.

#### <a name="resizegwsku"></a>Átjáró átméretezése

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="change"></a>Egy új másikra régi (örökölt) termékváltozatból módosítása

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connectiontype"></a>Kapcsolat típusai

A Resource Manager üzembe helyezési modellel minden egyes konfiguráció szükséges egy adott virtuális hálózati átjáró kapcsolattípus. A `-ConnectionType` lehetséges Resource Manager PowerShell-értékei a következők:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

A következő PowerShell-példa egy S2S kapcsolatot igényel a kapcsolat típusa létrehozhatunk *IPsec*.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>VPN-típusai

A virtuális hálózati átjáró VPN-átjáró konfigurációt létrehozásakor meg kell adnia egy VPN-típus. A VPN-típus az Ön által a létrehozni kívánt kapcsolat topológia függ. P2S kapcsolatot például egy RouteBased VPN típust igényli. A VPN-típus az Ön által használt hardver is függ. S2S-konfigurációk esetén van szükség, a VPN-eszközön. VPN-eszközök csak egy meghatározott VPN-típus támogatja.

A VPN-típus választja meg kell felelnie a minden kapcsolat megoldásra vonatkozó követelményeket a szeretne létrehozni. Például, ha szeretne létrehozni egy S2S VPN gateway-kapcsolatot, és az azonos virtuális hálózat P2S VPN gateway-kapcsolattal, használhatja VPN-típus *RouteBased* mert P2S egy RouteBased VPN-típus szükséges. Módosítania kell, hogy a VPN-eszköz támogatja-e RouteBased VPN-kapcsolat ellenőrzése. 

A virtuális hálózati átjáró létrehozása után nem módosíthatja a VPN-típus. Ki kell törölni a virtuális hálózati átjárót, és hozzon létre egy újat. Kétféle VPN-típus létezik:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

A következő PowerShell-példa meghatározza, hogy a `-VpnType` , *RouteBased*. Egy átjáró létrehozásakor biztosítania kell, hogy -VpnType megfeleljen a konfigurációnak.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>Átjáró követelményei

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Átjáró-alhálózatot

VPN-átjáró létrehozása előtt létre kell hoznia egy átjáró-alhálózatot. Az átjáró alhálózatának az IP-címek, amelyek a virtuális hálózati átjáró virtuális gépeket és szolgáltatásokat tartalmazza. A virtuális hálózati átjáró létrehozásakor átjáró virtuális gépek az átjáró alhálózatának telepítve és konfigurálva a VPN-átjáró szükséges beállításokat. Soha ne telepítenie kell semmi mást (például további virtuális gépek) az átjáró-alhálózathoz. Az átjáró alhálózatának a "GatewaySubnet" nevű kell megfelelően működjön. Az átjáró alhálózatának elnevezése "GatewaySubnet" lehetővé teszi, hogy tudja, hogy ez az alhálózat, a virtuális hálózati átjáró virtuális gépek és szolgáltatások telepítése Azure.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. Az átjáró alhálózatának IP-címek az átjáró virtuális gépek és az átjáró szolgáltatások foglal le. Egyes konfigurációknak a többinél nagyobb számú IP-címre van szükségük. Nézze meg az utasításokat, amelyet szeretne létrehozni, és győződjön meg arról, hogy az átjáró alhálózatának szeretne létrehozni a konfiguráció megfelel ezeknek a követelményeknek. Emellett érdemes lehet ellenőrizze, hogy az átjáró-alhálózatot tartalmaz elég IP-cím lehetséges jövőbeli további konfiguráció alkalmazásához. Létrehozhat egy átjáró-alhálózat mérete /29 legyen, de javasolt, hogy hozzon létre egy átjáró-alhálózatot /28 vagy nagyobb (/ 28, /27, /26 stb.). Ily módon való hozzáadásakor funkcióit a jövőben nem kell az átjáró szakadjon el, majd törölje és hozza létre újra az átjáró alhálózatának további IP-címek lehetővé.

A következő erőforrás-kezelő PowerShell-példa bemutatja egy GatewaySubnet nevű átjáró-alhálózatot. A CIDR-jelöléssel Meghatározza, hogy egy /27 lehetővé teszi, hogy elegendő IP-címek a jelenleg létező legtöbb konfiguráció látható.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Helyi hálózati átjáró

A VPN-átjáró konfiguráció létrehozásakor a helyi hálózati átjáró gyakran jelöli a helyszíni hely. A klasszikus üzembe helyezési modellben a helyi hálózati átjárót a Helyi névvel illettük. 

Nevezze el a helyi hálózati átjáró, a helyszíni VPN-eszköz nyilvános IP-címét, és adja meg a címelőtagokat a helyszíni hely található. Azure ellenőrzi, hogy a hálózati forgalom cél címelőtagokat, a konfiguráció a helyi hálózati átjáró megadott olvas, és ennek megfelelően irányítja a csomagokat. Helyi hálózati átjáró VPN gateway-kapcsolatot használó VNet – VNet-konfigurációk is adja meg.

A következő PowerShell-példa létrehoz egy új helyi hálózati átjáró:

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Néha módosítania kell a helyi hálózati átjáró beállításainak. Például ha fel vagy módosít a címtartományt, vagy ha megváltozik-e a VPN-eszköz IP-címét. Lásd: [PowerShell használatával a helyi hálózati átjáró beállításainak módosítása](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>REST API-k, PowerShell-parancsmagok és parancssori felület

További technikai erőforrások és konkrét szintaxis használatával kapcsolatos követelményeket REST API-k, a PowerShell-parancsmagok vagy az Azure CLI VPN Gateway-konfigurációk esetén tekintse meg a következő oldalakon:

| **Klasszikus** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/azure#networking) |[PowerShell](/powershell/module/azurerm.network#vpn) |
| [REST API](https://msdn.microsoft.com/library/jj154113) |[REST API](/rest/api/network/virtualnetworkgateways) |
| Nem támogatott | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>További lépések

Használható kapcsolat konfigurációkkal kapcsolatos további információkért lásd: [VPN-átjáró](vpn-gateway-about-vpngateways.md).
