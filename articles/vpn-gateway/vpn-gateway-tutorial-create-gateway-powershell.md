---
title: Tutorial - Create and manage a gateway using Azure VPN Gateway
description: Oktatóanyag – Windows rendszerű VPN-átjáró létrehozása és felügyelete az Azure PowerShell-modullal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: b144a70ee88138966d9cc38a56e1cff1e63fca1b
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424141"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Tutorial: Create and manage a VPN gateway using PowerShell

Az Azure VPN-átjárók helyszínek közötti kapcsolatot biztosítanak az ügyfél helyszínei és az Azure között. Ez az oktatóanyag az Azure VPN-átjárók üzembe helyezésének alapvető elemeit ismerteti, például a VPN-átjárók létrehozását és felügyeletét. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * VPN-átjáró létrehozása
> * View the public IP address
> * VPN-átjáró átméretezése
> * VPN-átjáró alaphelyzetbe állítása

Az alábbi diagram az oktatóanyag keretében létrehozott virtuális hálózatot és VPN-átjárót mutatja.

![VNet és VPN-átjáró](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell és Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>A hálózati paraméterek gyakori értékei

Below are the parameter values used for this tutorial. In the examples, the variables translate to the following:

```
#$RG1         = The name of the resource group
#$VNet1       = The name of the virtual network
#$Location1   = The location region
#$FESubnet1   = The name of the first subnet
#$BESubnet1   = The name of the second subnet
#$VNet1Prefix = The address range for the virtual network
#$FEPrefix1   = Addresses for the first subnet
#$BEPrefix1   = Addresses for the second subnet
#$GwPrefix1   = Addresses for the GatewaySubnet
#$VNet1ASN    = ASN for the virtual network
#$DNS1        = The IP address of the DNS server you want to use for name resolution
#$Gw1         = The name of the virtual network gateway
#$GwIP1       = The public IP address for the virtual network gateway
#$GwIPConf1   = The name of the IP configuration
```

Change the values below based on your environment and network setup, then copy and paste to set the variables for this tutorial. If your Cloud Shell session times out, or you need to use a different PowerShell window, copy and paste the variables to your new session and continue the tutorial.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Create a resource group with the [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) command. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Először az erőforráscsoportot kell létrehozni. A következő példában egy *TestRG1* nevű erőforráscsoportot hozunk létre az *USA keleti régiójában*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Az Azure VPN-átjáró helyszínek közötti kapcsolatot és P2S VPN-kiszolgálói funkciókat biztosít a virtuális hálózatán. A VPN-átjárót hozzáadhatja egy meglévő virtuális hálózathoz, vagy létrehozhat egy új virtuális hálózatot az átjáróval együtt. Notice that the example specifies the name of the gateway subnet specifically. You must always specify the name of the gateway subnet as "GatewaySubnet" in order for it to function properly. This example creates a new virtual network with three subnets: Frontend, Backend, and GatewaySubnet using [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) and [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Nyilvános IP-cím kérése a VPN-átjáró számára

Az Azure VPN-átjárók a helyszíni VPN-eszközökkel az interneten keresztül kommunikálnak az IKE (internetes kulcscsere) egyeztetéséhez és az IPsec-alagutak kiépítéséhez. Create and assign a public IP address to your VPN gateway as shown in the example below with [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) and [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig):

> [!IMPORTANT]
> Jelenleg csak dinamikus nyilvános IP-címeket használhat az átjárókhoz. A statikus IP-címek nem támogatottak az Azure VPN-átjárókon.

```azurepowershell-interactive
$gwpip    = New-AzPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-a-vpn-gateway"></a>VPN-átjáró létrehozása

A VPN-átjáró létrehozása több mint 45 percet is igénybe vehet. Amint az átjáró létrejött, létrehozhat egy kapcsolatot a virtuális hálózat és egy másik VNet között. Vagy létrehozhat egy kapcsolatot a virtuális hálózat és egy helyszíni hely között. Create a VPN gateway using the [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) cmdlet.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Fő paraméterértékek:
* GatewayType: Használja a **Vpn** értéket a helyek közötti és a virtuális hálózatok közötti kapcsolatokhoz
* VpnType: A **RouteBased** értékkel VPN-eszközök szélesebb választékát használhatja, több útválasztási szolgáltatással
* GatewaySku: **VpnGw1** is the default; change it to another VpnGw SKU if you need higher throughputs or more connections. További információkért lásd: [Az átjárók termékváltozatai](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

If you are using the TryIt, your session may time out. That's OK. The gateway will still create.

Amint az átjáró létrejött, létrehozhat egy kapcsolatot a virtuális hálózat és egy másik VNet vagy a virtuális hálózat és egy helyszíni hely között. Konfigurálhat egy P2S-kapcsolatot is a VNethez egy ügyfélszámítógéptől.

## <a name="view-the-gateway-public-ip-address"></a>View the gateway public IP address

If you know the name of the public IP address, use [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) to show the public IP address assigned to the gateway.

If your session timed out, copy the common network parameters from the beginning of this tutorial into your new session and proceed, then proceed.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Resize a gateway

A VPN-átjáró termékváltozata az átjáró létrehozása után is módosítható. Different gateway SKUs support different specifications such as throughputs, number of connections, etc. The following example uses [Resize-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) to resize your gateway from VpnGw1 to VpnGw2. További információkért lásd: [Az átjárók termékváltozatai](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

A VPN-átjárók átméretezése szintén hozzávetőleg 30–45 percet vesz igénybe, bár ez a művelet **nem** szakítja meg vagy törli a meglévő kapcsolatokat és konfigurációkat.

## <a name="reset-a-gateway"></a>Reset a gateway

Hibaelhárítás keretében az Azure VPN-átjáró alaphelyzetbe állításával kényszerítheti a VPN-átjárót az IPsec/IKE-alagútkonfigurációk újraindítására. Use [Reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) to reset your gateway.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

További információ: [VPN-átjáró alaphelyzetbe állítása](vpn-gateway-resetgw-classic.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

If you're advancing to the [next tutorial](vpn-gateway-tutorial-vpnconnection-powershell.md), you will want to keep these resources because they are the prerequisites.

However, if the gateway is part of a prototype, test, or proof-of-concept deployment, you can use the [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) command to remove the resource group, the VPN gateway, and all related resources.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a VPN-átjárók létrehozásának és kezelésének alapvető műveleteivel ismerkedett meg, például:

> [!div class="checklist"]
> * VPN-átjáró létrehozása
> * View the public IP address
> * VPN-átjáró átméretezése
> * VPN-átjáró alaphelyzetbe állítása

Folytassa a következő oktatóanyaggal, amelyben a helyek közötti (S2S), a virtuális hálózatok közötti (VNet-to-VNet) és a pont–hely (P2S) kapcsolatokkal ismerkedhet meg.

> [!div class="nextstepaction"]
> * [S2S-kapcsolatok létrehozása](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Virtuális hálózatok közötti kapcsolatok létrehozása](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Pont–hely kapcsolatok létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
