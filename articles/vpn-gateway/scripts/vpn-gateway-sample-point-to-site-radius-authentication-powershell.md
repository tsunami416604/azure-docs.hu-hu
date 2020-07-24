---
title: Azure PowerShell-példaszkript – Pont-hely típusú VPN konfigurálása felhasználóneves és jelszavas RADIUS-hitelesítéssel | Microsoft Docs
description: Pont-hely típusú VPN konfigurálása felhasználóneves és jelszavas RADIUS-hitelesítéssel. Ebben a cikkben a PowerShellt fogjuk használni.
services: vpn-gateway
documentationcenter: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 05/30/2018
ms.author: alzam
ms.openlocfilehash: d546fe2568c0c061c9a08e7fa4a98aa8c5e55c55
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87068558"
---
# <a name="create-a-vpn-gateway-and-add-point-to-site-configuration-using-powershell"></a>VPN-átjáró létrehozása és pont–hely konfiguráció hozzáadása a PowerShell használatával

Ez a szkript létrehoz egy útvonalalapú VPN-átjárót, és pont–hely konfigurációt ad ahhoz felhasználónevet és jelszót használó RADIUS-hitelesítéssel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "10.0.0.0/16"
  $FESubPrefix = "10.1.0.0/24"
  $BESubPrefix = "10.1.1.0/24"
  $GWSubPrefix = "10.1.255.0/27"
  $VPNClientAddressPool = "192.168.0.0/24"
  $RG = "TestRG1"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWIP"
  $GWIPconfName = "gwipconf"
# Create a resource group
New-AzResourceGroup -Name TestRG1 -Location EastUS
# Create a virtual network
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
# Create a subnet configuration
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzVirtualNetwork
# Request a public IP address
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
 -Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
# Create a secure string for the RADIUS secret
$Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"

# Add the VPN client address pool and the RADIUS server information
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
 -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
 -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
```

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs szüksége a létrehozott erőforrásokra, használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport törléséhez. Ez a parancs törli az erőforráscsoportot és a benne lévő összes erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hozzáad egy alhálózati konfigurációt. Ez a konfiguráció a virtuális hálózat létrehozására szolgál. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Lekérdezi egy virtuális hálózat részleteit. |
| [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | Lekérdezi egy virtuális hálózati átjáró részleteit. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Lekérdezi egy virtuális hálózati alhálózat-konfiguráció részleteit. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Alhálózati konfigurációt hoz létre. Ez a konfiguráció a virtuális hálózat létrehozására szolgál. |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Egy nyilvános IP-címet hoz létre. |
| [Új – AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Létrehoz egy új IP-konfigurációt egy átjáróhoz. |
| [Új – AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgateway) | Létrehoz egy VPN-átjárót. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Beállítja a virtuális hálózat alhálózati konfigurációját. |
| [Set-AzVirtualNetworkGateway](/powershell/module/az.network/set-azvirtualnetworkgateway) | Beállítja a VPN-átjáró konfigurációját. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).
