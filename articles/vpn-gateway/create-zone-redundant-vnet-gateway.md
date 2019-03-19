---
title: A zónaredundáns virtuális hálózati átjáró létrehozása az Azure-beli rendelkezésre állási zónák |} A Microsoft Docs
description: Rendelkezésre állási zónában a VPN-átjáró és ExpressRoute-átjárók üzembe helyezése
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: cherylmc
ms.openlocfilehash: 00c50f95324e18f738740a9bec572ca676ab9165
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58010612"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>A zónaredundáns virtuális hálózati átjáró létrehozása az Azure-beli rendelkezésre állási zónák

Az Azure-beli rendelkezésre állási zónák a VPN és ExpressRoute-átjárók telepítheti. Ekkor a rugalmasság, a méretezhetőség és a magasabb rendelkezésre állás virtuális hálózati átjárók. Egy adott régión belül átjárók telepítése átjárók az Azure-beli rendelkezésre állási zónák fizikailag és logikailag elkülöníti a során a helyszíni hálózati kapcsolatok védelme az Azure-bA a zónaszintű hibák. További információ: [kapcsolatos zónaredundáns virtuális hálózati átjárók](about-zone-redundant-vnet-gateways.md) és [kapcsolatos Azure-beli rendelkezésre állási zónák](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell-lel helyben telepítve a számítógépen, vagy az Azure Cloud Shellt használhatja. Ha helyi telepítése és használata a PowerShell, a funkcióhoz a PowerShell-modul legújabb verzióját.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Ha a PowerShell segítségével helyileg

Ha a Powershellt használ helyileg a számítógépen, nem pedig a Cloud Shell használata esetén telepítenie kell PowerShell-modul 1.0.0 vagy újabb verziója. PowerShell telepített verziójának ellenőrzéséhez használja a következő parancsot:

```azurepowershell
Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
```

Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="variables"></a>1. A változók deklarálása

A példa lépései használt értékek az alábbiak. Emellett néhány példája a deklarált változók belül a lépéseket használja. Ha ezeket a lépéseket a saját környezetében használ, mindenképpen ezeket az értékeket a saját értékeire cserélni. Adja meg a helyet, győződjön meg arról, hogy a megadott régióban támogatott. További információkért lásd: a [– gyakori kérdések](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>2. A virtuális hálózat létrehozása

Hozzon létre egy erőforráscsoportot.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Hozzon létre egy virtuális hálózatot.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>3. Az átjáró alhálózatának hozzáadása

Az átjáróalhálózat tartalmazza a fenntartott IP-címek, amelyek a virtuális hálózati átjáró-szolgáltatások használják. Az alábbi példák segítségével adja hozzá, és állítsa be az átjáró-alhálózat:

Adja hozzá az átjáró alhálózatát.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Állítsa be a virtuális hálózati átjáró alhálózati konfigurációját.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="publicip"></a>4. Nyilvános IP-cím kérése
 
Ebben a lépésben válassza ki a létrehozni kívánt átjáróhoz érvényes utasításokat. Az átjárók telepítésére zónák a kijelölt a zónák a nyilvános IP-címhez megadott függ.

### <a name="ipzoneredundant"></a>Az átjárók zónaredundáns

Kérjen egy nyilvános IP-címet egy **Standard** Termékváltozat nyilvános IP-címre, és ne adjon meg minden zónában. Ebben az esetben a Standard nyilvános IP-cím létrehozott zónaredundáns nyilvános IP-cím lesz.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>A zónaszintű átjárók esetében

Kérjen egy nyilvános IP-címet egy **Standard** Termékváltozat nyilvános IP-címre. Adja meg a zóna (1, 2 vagy 3). Ebben a zónában lesz telepítve, az összes átjárópéldányához.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Regionális átjárók esetében

Kérjen egy nyilvános IP-címet egy **alapszintű** Termékváltozat nyilvános IP-címre. Ebben az esetben az átjáró regionális átjáróként üzemel, és minden zóna-redundanciát építve az átjáró nem rendelkezik. Az átjárópéldányok rendre minden zónában létrejönnek.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5. Az IP-konfiguráció létrehozása

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6. Az átjáró létrehozása

A virtuális hálózati átjáró létrehozásához.

### <a name="for-expressroute"></a>Az expressroute-hoz

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>A VPN Gateway

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="faq"></a>GYIK

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Mi változik, ha új Termékváltozatokban üzembe helyezhetem?

A szempontból az átjárók zóna redundanciával is telepítheti. Ez azt jelenti, hogy az átjárók az összes példányát telepíti, az Azure rendelkezésre állási zónák között, és minden egyes rendelkezésre állási zónában egy másik tartalék és frissítési tartományban. Ez lehetővé teszi az átjárók megbízható, elérhető és rugalmas zóna hibákkal szemben.

### <a name="can-i-use-the-azure-portal"></a>Használható az Azure Portalon?

Igen, az Azure portal használatával üzembe helyezése az új termékváltozatokra. Azonban csak az adott Azure-régiók, amelyek az Azure-beli rendelkezésre állási zónák új Termékváltozatokban megjelenik.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Mely régiók érhetők el az új termékváltozatokra használni?

Lásd: [rendelkezésre állási zónák](../availability-zones/az-overview.md#regions-that-support-availability-zones) a teljes lista az elérhető régiók.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Helyezhetem el a módosítása/áttelepítése/frissítése a meglévő virtuális hálózati átjárók zónaredundáns vagy zónaszintű átjárók?

A meglévő virtuális hálózati átjárók zónaredundáns vagy zónaszintű átjárók migrálása jelenleg nem támogatott. Azonban, törölje a meglévő átjárót és hozhat újra létre zónaredundáns vagy zónaszintű átjáró.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Telepítheti a VPN- és Express Route átjárók azonos virtuális hálózatba?

Az azonos virtuális hálózatba VPN- és Express Route-átjárók együttes jelenléte használata támogatott. Azonban, / 27-es kell lefoglalni az átjáró-alhálózat IP-címtartományt.
