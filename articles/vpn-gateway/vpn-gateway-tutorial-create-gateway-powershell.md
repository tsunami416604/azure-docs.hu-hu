---
title: Oktatóanyag – Átjáró létrehozása és kezelése az Azure VPN-átjáróhasználatával
description: Oktatóanyag – Windows rendszerű VPN-átjáró létrehozása és felügyelete az Azure PowerShell-modullal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: cherylmc
ms.openlocfilehash: 66efa0f2922e70908616c7c447d782efee8f6b1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79137177"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Oktatóanyag: VPN-átjáró létrehozása és kezelése a PowerShell használatával

Az Azure VPN-átjárók helyszínek közötti kapcsolatot biztosítanak az ügyfél helyszínei és az Azure között. Ez az oktatóanyag az Azure VPN-átjárók üzembe helyezésének alapvető elemeit ismerteti, például a VPN-átjárók létrehozását és felügyeletét. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * VPN-átjáró létrehozása
> * A nyilvános IP-cím megtekintése
> * VPN-átjáró átméretezése
> * VPN-átjáró alaphelyzetbe állítása

Az alábbi diagram az oktatóanyag keretében létrehozott virtuális hálózatot és VPN-átjárót mutatja.

![VNet és VPN-átjáró](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="working-with-azure-cloud-shell-and-azure-powershell"></a>Az Azure Cloud Shell és az Azure PowerShell együttműködése

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>A hálózati paraméterek gyakori értékei

Az alábbiakban az oktatóanyaghoz használt paraméterértékek et talál. A példákban a változók a következőkre mutatnak:

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

Módosítsa az alábbi értékeket a környezet és a hálózat beállítása alapján, majd másolja és illessze be az oktatóanyag változóinak beállításához. Ha a Cloud Shell munkamenet időtúllépés, vagy egy másik PowerShell-ablakot kell használnia, másolja és illessze be a változókat az új munkamenetbe, és folytassa az oktatóanyag.

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

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Először az erőforráscsoportot kell létrehozni. A következő példában egy *TestRG1* nevű erőforráscsoportot hozunk létre az *USA keleti régiójában*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Az Azure VPN-átjáró helyszínek közötti kapcsolatot és P2S VPN-kiszolgálói funkciókat biztosít a virtuális hálózatán. A VPN-átjárót hozzáadhatja egy meglévő virtuális hálózathoz, vagy létrehozhat egy új virtuális hálózatot az átjáróval együtt. Figyelje meg, hogy a példa kifejezetten az átjáró alhálózatának nevét adja meg. Ahhoz, hogy megfelelően működjön, mindig meg kell adnia az átjáró alhálózatának nevét "GatewaySubnet" néven. Ez a példa egy új virtuális hálózatot hoz létre három alhálózattal: Előtér, Háttérrendszer és GatewaySubnet a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) és [a New-AzVirtualNetwork használatával:](/powershell/module/az.network/new-azvirtualnetwork)

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

Az Azure VPN-átjárók a helyszíni VPN-eszközökkel az interneten keresztül kommunikálnak az IKE (internetes kulcscsere) egyeztetéséhez és az IPsec-alagutak kiépítéséhez. Hozzon létre és rendeljen hozzá egy nyilvános IP-címet a VPN-átjáróhoz, amint az az alábbi példában a [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) és a [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig)segítségével látható:

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

A VPN-átjáró létrehozása több mint 45 percet is igénybe vehet. Amint az átjáró létrejött, létrehozhat egy kapcsolatot a virtuális hálózat és egy másik VNet között. Vagy létrehozhat egy kapcsolatot a virtuális hálózat és egy helyszíni hely között. Hozzon létre egy VPN-átjárót a [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) parancsmag használatával.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Fő paraméterértékek:
* GatewayType: Használja a **Vpn** értéket a helyek közötti és a virtuális hálózatok közötti kapcsolatokhoz
* VpnType: A **RouteBased** értékkel VPN-eszközök szélesebb választékát használhatja, több útválasztási szolgáltatással
* GatewaySku: **VpnGw1** az alapértelmezett; változtassa meg egy másik VpnGw Termékváltozatra, ha nagyobb átviteli vagy több kapcsolatra van szüksége. További információkért lásd: [Az átjárók termékváltozatai](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Ha a TryIt-et használja, előfordulhat, hogy a munkamenet időtúlodjára áll. rendben van. Az átjáró továbbra is létre fog hozni.

Amint az átjáró létrejött, létrehozhat egy kapcsolatot a virtuális hálózat és egy másik VNet vagy a virtuális hálózat és egy helyszíni hely között. Konfigurálhat egy P2S-kapcsolatot is a VNethez egy ügyfélszámítógéptől.

## <a name="view-the-gateway-public-ip-address"></a>Az átjáró nyilvános IP-címének megtekintése

Ha ismeri a nyilvános IP-cím nevét, a [Get-AzPublicIpAddress függesztse](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) fel az átjáróhoz rendelt nyilvános IP-címet.

Ha a munkamenet idővel elévül, másolja a közös hálózati paramétereket az oktatóanyag elejétől az új munkamenetbe, majd folytassa, majd folytassa.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Átjáró átméretezése

A VPN-átjáró termékváltozata az átjáró létrehozása után is módosítható. A különböző átjáró termékazonosítók különböző specifikációkat támogatnak, például az átviteli putokat, a kapcsolatok számát stb. A következő példa [a Resize-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) segítségével méretezi át az átjárót VpnGw1-ről VpnGw2-re. További információkért lásd: [Az átjárók termékváltozatai](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

A VPN-átjárók átméretezése szintén hozzávetőleg 30–45 percet vesz igénybe, bár ez a művelet **nem** szakítja meg vagy törli a meglévő kapcsolatokat és konfigurációkat.

## <a name="reset-a-gateway"></a>Átjáró alaphelyzetbe állítása

Hibaelhárítás keretében az Azure VPN-átjáró alaphelyzetbe állításával kényszerítheti a VPN-átjárót az IPsec/IKE-alagútkonfigurációk újraindítására. Az átjáró alaphelyzetbe állításához használja a [Reset-AzVirtualNetworkGateway átjárót.](/powershell/module/az.network/Reset-azVirtualNetworkGateway)

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

További információ: [VPN-átjáró alaphelyzetbe állítása](vpn-gateway-resetgw-classic.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a [következő oktatóanyagra](vpn-gateway-tutorial-vpnconnection-powershell.md)lép előre, érdemes megtartani ezeket az erőforrásokat, mert ezek az előfeltételek.

Ha azonban az átjáró egy prototípus, teszt vagy koncepciópróba-telepítés része, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolíthatja az erőforráscsoportot, a VPN-átjárót és az összes kapcsolódó erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a VPN-átjárók létrehozásának és kezelésének alapvető műveleteivel ismerkedett meg, például:

> [!div class="checklist"]
> * VPN-átjáró létrehozása
> * A nyilvános IP-cím megtekintése
> * VPN-átjáró átméretezése
> * VPN-átjáró alaphelyzetbe állítása

Folytassa a következő oktatóanyaggal, amelyben a helyek közötti (S2S), a virtuális hálózatok közötti (VNet-to-VNet) és a pont–hely (P2S) kapcsolatokkal ismerkedhet meg.

> [!div class="nextstepaction"]
> * [S2S-kapcsolatok létrehozása](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Virtuálishálózat és virtuális hálózat között kapcsolatok létrehozása](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Pont–hely kapcsolatok létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
