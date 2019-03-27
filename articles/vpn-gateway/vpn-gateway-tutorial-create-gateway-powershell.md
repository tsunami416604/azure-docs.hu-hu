---
title: Azure VPN-átjárók létrehozása és felügyelete a PowerShell használatával | Microsoft Docs
description: Oktatóanyag – Windows rendszerű VPN-átjáró létrehozása és felügyelete az Azure PowerShell-modullal
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 790a8b74f437fe8fd7b8660c2ac9d208328b487f
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445217"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Oktatóanyag: Létrehozása és kezelése a PowerShell használatával a VPN-átjáró

Az Azure VPN-átjárók helyszínek közötti kapcsolatot biztosítanak az ügyfél helyszínei és az Azure között. Ez az oktatóanyag az Azure VPN-átjárók üzembe helyezésének alapvető elemeit ismerteti, például a VPN-átjárók létrehozását és felügyeletét. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * VPN-átjáró létrehozása
> * Nyilvános IP-címének megtekintése
> * VPN-átjáró átméretezése
> * VPN-átjáró alaphelyzetbe állítása

Az alábbi diagram az oktatóanyag keretében létrehozott virtuális hálózatot és VPN-átjárót mutatja.

![VNet és VPN-átjáró](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell és Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>A hálózati paraméterek gyakori értékei

Módosítsa az értékeket az alábbi a környezet és a hálózat beállítása, majd másolja be a állíthatja be a változókat a jelen oktatóanyag alapján. Ha a Cloud Shell-munkamenetek túllépi az időkorlátot, vagy szeretné használni egy másik PowerShell-ablakot, másolja és illessze be a változókat az új munkamenet, és folytassa az oktatóanyagot.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
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

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Először az erőforráscsoportot kell létrehozni. A következő példában egy *TestRG1* nevű erőforráscsoportot hozunk létre az *USA keleti régiójában*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Az Azure VPN-átjáró helyszínek közötti kapcsolatot és P2S VPN-kiszolgálói funkciókat biztosít a virtuális hálózatán. A VPN-átjárót hozzáadhatja egy meglévő virtuális hálózathoz, vagy létrehozhat egy új virtuális hálózatot az átjáróval együtt. Ez a példa létrehoz egy új virtuális hálózatot három alhálózattal: Előtér, háttér és átjáró-alhálózat használatával [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) és [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Nyilvános IP-cím kérése a VPN-átjáró számára

Az Azure VPN-átjárók a helyszíni VPN-eszközökkel az interneten keresztül kommunikálnak az IKE (internetes kulcscsere) egyeztetéséhez és az IPsec-alagutak kiépítéséhez. Hozzon létre, és a egy nyilvános IP-cím hozzárendelése a VPN-átjáróhoz, az alábbi példában látható módon [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) és [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig):

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

A VPN-átjáró létrehozása több mint 45 percet is igénybe vehet. Amint az átjáró létrejött, létrehozhat egy kapcsolatot a virtuális hálózat és egy másik VNet között. Vagy létrehozhat egy kapcsolatot a virtuális hálózat és egy helyszíni hely között. Hozzon létre egy VPN gateway-t a [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) parancsmagot.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Fő paraméterértékek:
* GatewayType: Használat **Vpn** a helyek közötti és VNet – VNet kapcsolatokhoz
* VpnType: Használat **RouteBased** szélesebb tartományban a VPN-eszközök és több útválasztási szolgáltatással kommunikál
* A GatewaySku: **VpnGw1** az alapértelmezett érték; a módosításhoz VpnGw2 és VpnGw3, ha magasabb szintű termékváltozatot vagy több kapcsolat van szüksége. További információkért lásd: [Az átjárók termékváltozatai](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Ha a TryIt használ, a munkamenet előfordulhat, hogy időtúllépés. rendben van. Az átjáró továbbra is létrehoz.

Amint az átjáró létrejött, létrehozhat egy kapcsolatot a virtuális hálózat és egy másik VNet vagy a virtuális hálózat és egy helyszíni hely között. Konfigurálhat egy P2S-kapcsolatot is a VNethez egy ügyfélszámítógéptől.

## <a name="view-the-gateway-public-ip-address"></a>Az átjáró nyilvános IP-címének megtekintése

Ha tudja, hogy a nyilvános IP-cím nevére, [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) átjáróhoz rendelt nyilvános IP-cím megjelenítéséhez.

A munkamenet túllépte az időkorlátot, ha közös hálózati paraméterek másolja az új munkamenetbe, ez az oktatóanyag elején található folytatja, majd a folytatáshoz.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Az átjáró átméretezése

A VPN-átjáró termékváltozata az átjáró létrehozása után is módosítható. A különböző átjáró-termékváltozatok különböző specifikációkat, például sebességet, kapcsolatszámot stb. támogatnak. Az alábbi példában [átméretezése-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) méretezze át az átjáró a VpnGw1, VpnGw2. További információkért lásd: [Az átjárók termékváltozatai](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

A VPN-átjárók átméretezése szintén hozzávetőleg 30–45 percet vesz igénybe, bár ez a művelet **nem** szakítja meg vagy törli a meglévő kapcsolatokat és konfigurációkat.

## <a name="reset-a-gateway"></a>Egy átjáró alaphelyzetbe állítása

Hibaelhárítás keretében az Azure VPN-átjáró alaphelyzetbe állításával kényszerítheti a VPN-átjárót az IPsec/IKE-alagútkonfigurációk újraindítására. Használat [alaphelyzetbe állítása – AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) az átjáró alaphelyzetbe állítása.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

További információ: [VPN-átjáró alaphelyzetbe állítása](vpn-gateway-resetgw-classic.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Ön nézetváltás a [tovább az oktatóanyag](vpn-gateway-tutorial-vpnconnection-powershell.md), fog szeretné megőrizni ezeket az erőforrásokat, mert az előfeltételként szükséges szoftvert.

Azonban ha az átjáró egy prototípust, tesztelési vagy proof-of-concept-telepítés része, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolítható az erőforráscsoport, a VPN-átjáró és az összes kapcsolódó erőforrás.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a VPN-átjárók létrehozásának és kezelésének alapvető műveleteivel ismerkedett meg, például:

> [!div class="checklist"]
> * VPN-átjáró létrehozása
> * Nyilvános IP-címének megtekintése
> * VPN-átjáró átméretezése
> * VPN-átjáró alaphelyzetbe állítása

Folytassa a következő oktatóanyaggal, amelyben a helyek közötti (S2S), a virtuális hálózatok közötti (VNet-to-VNet) és a pont–hely (P2S) kapcsolatokkal ismerkedhet meg.

> [!div class="nextstepaction"]
> * [S2S-kapcsolatok létrehozása](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Virtuális hálózatok közötti kapcsolatok létrehozása](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Pont–hely kapcsolatok létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
