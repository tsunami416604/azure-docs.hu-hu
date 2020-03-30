---
title: Zónaredundáns virtuális hálózati átjáró létrehozása az Azure rendelkezésre állási zónáiban
description: VPN-átjárók és ExpressRoute-átjárók telepítése a rendelkezésre állási zónákban
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: d8c6b68a38d4b60cf7a3194e6a5ded8804cc416f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77150170"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Zónaredundáns virtuális hálózati átjáró létrehozása az Azure rendelkezésre állási zónáiban

VPN- és ExpressRoute-átjárók at helyezheti üzembe az Azure rendelkezésre állási zónáiban. Ez rugalmasságot, méretezhetőséget és magasabb szintű rendelkezésre állást biztosít a virtuális hálózati átjárók számára. Az átjárók Azure-beli rendelkezésre állási zónákban történő üzembe helyezésével fizikailag és logikailag is elválaszthatók a régióban található átjárók, miközben az Azure-ral létesített helyszíni hálózati kapcsolat megvédhető a zónaszintű hibáktól. További információt a [Zónaredundáns virtuális hálózati átjárók és](about-zone-redundant-vnet-gateways.md) [az Azure rendelkezésre állási zónái – tudnivalókcímű témakörben talál.](../availability-zones/az-overview.md)

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-declare-your-variables"></a><a name="variables"></a>1. Deklarálja a változókat

Deklarálja a használni kívánt változókat. Használja a következő példát, és szükség szerint cserélje le az értékeket a sajátjaira. Ha bezárja a PowerShell/Cloud Shell munkamenet bármely pontján az edzés során, csak másolja és illessze be az értékeket újra deklarálni a változók. Hely megadásakor ellenőrizze, hogy a megadott terület támogatott-e. További információt a [GYAKORI KÉRDÉSEK című témakörben talál.](#faq)

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

## <a name="2-create-the-virtual-network"></a><a name="configure"></a>2. A virtuális hálózat létrehozása

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

## <a name="3-add-the-gateway-subnet"></a><a name="gwsub"></a>3. Az átjáró alhálózatának hozzáadása

Az átjáró alhálózata tartalmazza a virtuális hálózati átjárószolgáltatások által használt fenntartott IP-címeket. Az alábbi példák segítségével vegyen fel és állítson be átjáró-alhálózatot:

Adja hozzá az átjáró alhálózatát.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Állítsa be a virtuális hálózat átjáróalhálózat-konfigurációját.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="4-request-a-public-ip-address"></a><a name="publicip"></a>4. Nyilvános IP-cím kérése
 
Ebben a lépésben válassza ki a létrehozni kívánt átjáróra vonatkozó utasításokat. Az átjárók üzembe helyezéséhez szolgáló zónák kiválasztása a nyilvános IP-címhez megadott zónáktól függ.

### <a name="for-zone-redundant-gateways"></a><a name="ipzoneredundant"></a>Zónaredundáns átjárók esetén

Kérjen nyilvános IP-címet **szabványos** PublicIpaddress termékváltozattal, és ne adjon meg zónát. Ebben az esetben a létrehozott szabványos nyilvános IP-cím zónaredundáns nyilvános IP lesz.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="for-zonal-gateways"></a><a name="ipzonalgw"></a>Zónaszintű átjárókhoz

Kérjen nyilvános IP-címet **egy szabványos** PublicIpaddress Termékváltozattal. Adja meg a zónát (1, 2 vagy 3). Az összes átjárópéldány ebben a zónában lesz telepítve.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="for-regional-gateways"></a><a name="ipregionalgw"></a>Regionális átjárók esetén

Kérjen nyilvános IP-címet **egy egyszerű** PublicIpaddress Termékváltozattal. Ebben az esetben az átjáró regionális átjáróként van telepítve, és nincs beépített zónaredundancia az átjáróba. Az átjárópéldányok bármely zónában jönnek létre.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="5-create-the-ip-configuration"></a><a name="gwipconfig"></a>5. Az IP-konfiguráció létrehozása

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="6-create-the-gateway"></a><a name="gwconfig"></a>6. Az átjáró létrehozása

Hozza létre a virtuális hálózati átjárót.

### <a name="for-expressroute"></a>Az ExpressRoute-hoz

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>VPN-átjáróhoz

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a><a name="faq"></a>GYIK

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Mi változik az új sk-ek telepítésekor?

Az Ön szemszögéből az átjárók zónaredundanciával telepíthetők. Ez azt jelenti, hogy az átjárók összes példánya az Azure rendelkezésre állási zónáiban lesz telepítve, és minden rendelkezésre állási zóna egy másik hiba és frissítési tartomány. Ez megbízhatóbbá, elérhetőbbé és zónahibákkal szembenellenállóbbá teszi az átjárókat.

### <a name="can-i-use-the-azure-portal"></a>Használhatom az Azure Portalt?

Igen, az Azure Portal segítségével telepítheti az új sk-eket. Ezek az új termékkészletek azonban csak azokban az Azure-régiókban jelennek meg, amelyek rendelkeznek az Azure rendelkezésre állási zónáival.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Milyen régiók érhetők el az új sk-ek használatához?

Az elérhető régiók legfrissebb listáját a [rendelkezésre állási zónák](../availability-zones/az-overview.md#services-support-by-region) című témakörben található.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Módosíthatom/áttelepíthetem/frissíthetem a meglévő virtuális hálózati átjáróimat zónaredundáns vagy zónaszintű átjárókra?

A meglévő virtuális hálózati átjárók áttelepítése zónaredundáns vagy zónaszintű átjárókba jelenleg nem támogatott. Azonban törölheti a meglévő átjárót, és újra létrehozhat egy zónaredundáns vagy zónaszintű átjárót.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Telepíthetek VPN- és Express Route átjárókat is ugyanabban a virtuális hálózatban?

Az ugyanazon virtuális hálózatban lévő VPN- és express route átjárók együttélése támogatott. Azonban le kell foglalnia egy /27 IP-címtartományt az átjáró alhálózathoz.
