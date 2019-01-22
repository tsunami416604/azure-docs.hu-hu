---
title: 'Útválasztó-alapú Azure VPN-átjáró létrehozása: PowerShell |} A Microsoft Docs'
description: Gyorsan létrehozhat egy útvonalalapú VPN-átjáró PowerShell-lel
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/18/2018
ms.author: cherylmc
ms.openlocfilehash: 9460f184e3da6769048b30ca743169c5a6044bd0
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431848"
---
# <a name="create-a-route-based-vpn-gateway-using-powershell"></a>PowerShell-lel az útvonalalapú VPN-átjáró létrehozása

Ez a cikk segít gyorsan létrehozhat egy útválasztó-alapú Azure VPN gatewayen a PowerShell használatával. VPN-átjáró a helyszíni hálózathoz egy VPN-kapcsolat létrehozása során használatos. Virtuális hálózatok csatlakoztatása VPN gateway használhatja.

A jelen cikkben ismertetett lépések hoz létre virtuális hálózat, alhálózat, egy átjáró-alhálózatot és egy útvonalalapú VPN-átjáró (virtuális hálózati átjáró). Miután az átjáró létrehozása befejeződött, majd létrehozhat kapcsolatokat. Ezeket a lépéseket az Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.3.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name TestRG1 -Location EastUS
```

## <a name="vnet"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. A következő példában létrehozunk egy nevű virtuális hálózatot **VNet1** a a **EastUS** helye:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
```

Hozzon létre egy alhálózati konfigurációt a [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) parancsmagot.

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Az alhálózati konfigurációt a virtuális hálózat használatához állítsa a [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) parancsmagot.


```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="gwsubnet"></a>Átjáró-alhálózat hozzáadása

Az átjáróalhálózat tartalmazza a fenntartott IP-címek, amelyek a virtuális hálózati átjáró-szolgáltatások használják. Használja az alábbi példák egy átjáró alhálózatának hozzáadása:

Egy változó beállítása a virtuális hálózat számára.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
```

Hozzon létre az átjáró alhálózatot a [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/Add-AzureRmVirtualNetworkSubnetConfig) parancsmagot.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
```

Az alhálózati konfigurációt a virtuális hálózat használatához állítsa a [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) parancsmagot.

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="PublicIP"></a>Nyilvános IP-cím kérése

VPN-átjáró dinamikusan lefoglalt nyilvános IP-címmel kell rendelkeznie. VPN-átjáró kapcsolatot hoz létre, ha ez az Ön által megadott IP-címét. Használja az alábbi példa egy nyilvános IP-cím:

```azurepowershell-interactive
$gwpip= New-AzureRmPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>Hozzon létre az átjáró IP-címének konfigurációja

Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. A következő példa használatával hozza létre az átjáró konfigurációját.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```
## <a name="CreateGateway"></a>A VPN-átjáró létrehozása

A VPN-átjáró létrehozása több mint 45 percet is igénybe vehet. Az átjáró befejezése után létrehozhat egy kapcsolatot a virtuális hálózat és a egy másik virtuális hálózat között. Vagy hozzon létre egy kapcsolatot a virtuális hálózat és a egy helyszíni hely között. Hozzon létre egy VPN-átjárót a [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway) parancsmaggal.

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="viewgw"></a>A VPN-átjáró megtekintése

A VPN gateway-t is megtekintheti a [Get-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Get-AzureRmVirtualNetworkGateway) parancsmagot.

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGateway -Name Vnet1GW -ResourceGroup TestRG1
```

A kimenet ebben a példában hasonlóan néz ki:

```
Name                   : VNet1GW
ResourceGroupName      : TestRG1
Location               : eastus
Id                     : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provide
                         rs/Microsoft.Network/virtualNetworkGateways/VNet1GW
Etag                   : W/"0952d-9da8-4d7d-a8ed-28c8ca0413"
ResourceGuid           : dc6ce1de-2c4494-9d0b-20b03ac595
ProvisioningState      : Succeeded
Tags                   :
IpConfigurations       : [
                           {
                             "PrivateIpAllocationMethod": "Dynamic",
                             "Subnet": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet"
                             },
                             "PublicIpAddress": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP"
                             },
                             "Name": "default",
                             "Etag": "W/\"0952d-9da8-4d7d-a8ed-28c8ca0413\"",
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                         RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/de
                         fault"
                           }
                         ]
GatewayType            : Vpn
VpnType                : RouteBased
EnableBgp              : False
ActiveActive           : False
GatewayDefaultSite     : null
Sku                    : {
                           "Capacity": 2,
                           "Name": "VpnGw1",
                           "Tier": "VpnGw1"
                         }
VpnClientConfiguration : null
BgpSettings            : {
     
```

## <a name="viewgwpip"></a>Nyilvános IP-címének megtekintése

A VPN-átjáró nyilvános IP-cím megtekintéséhez használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/Get-AzureRmPublicIpAddress) parancsmagot.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1
```

A példa választ, az IP-cím értéke a nyilvános IP-címet.

```
Name                     : VNet1GWIP
ResourceGroupName        : TestRG1
Location                 : eastus
Id                       : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provi
                           ders/Microsoft.Network/publicIPAddresses/VNet1GWIP
Etag                     : W/"5001666a-bc2a-484b-bcf5-ad488dabd8ca"
ResourceGuid             : 3c7c481e-9828-4dae-abdc-f95b383
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.90.153.3
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                           RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/
                           default"
                           }
DnsSettings              : null
Zones                    : {}
Sku                      : {
                             "Name": "Basic"
                           }
IpTags                   : {}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a létrehozott erőforrásokra, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal eltávolíthatja az erőforráscsoportot. Ez a parancs törli az erőforráscsoportot és a benne lévő összes erőforrást.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name TestRG1
```

## <a name="next-steps"></a>További lépések

Miután az átjáró létrehozása befejeződött, létrehozhat egy kapcsolatot a virtuális hálózat és a egy másik virtuális hálózat között. Vagy hozzon létre egy kapcsolatot a virtuális hálózat és a egy helyszíni hely között.

> [!div class="nextstepaction"]
> [Hozzon létre egy helyek közötti kapcsolat](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Pont – hely kapcsolat létrehozása](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Hozzon létre egy kapcsolatot egy másik virtuális hálózathoz](vpn-gateway-vnet-vnet-rm-ps.md)
