---
title: 'Oktatóanyag – Azure ExpressRoute: átjáró hozzáadása VNet – Azure PowerShell'
description: Ez az oktatóanyag segítséget nyújt a VNet-átjáró hozzáadásához egy már létrehozott Resource Manager-VNet a ExpressRoute-hez a Azure PowerShell használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 9f01961ec7c7f8e0a4e2d72e28e6def50e93ad5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91854307"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Oktatóanyag: virtuális hálózati átjáró konfigurálása ExpressRoute a PowerShell használatával
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Ez az oktatóanyag segítséget nyújt egy virtuális hálózati (VNet-) átjáró hozzáadásához, átméretezéséhez és eltávolításához egy már meglévő VNet. A konfiguráció lépései a ExpressRoute-konfiguráció Resource Manager-alapú üzemi modelljével létrehozott virtuális hálózatok vonatkoznak. További információ: [a ExpressRoute virtuális hálózati átjáróinak ismertetése](expressroute-about-virtual-network-gateways.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - Hozzon létre egy átjáró-alhálózatot.
> - Hozzon létre Virtual Network átjárót.

## <a name="prerequisites"></a>Előfeltételek

### <a name="configuration-reference-list"></a>Konfigurációs hivatkozások listája

A feladat lépései a következő konfigurációs hivatkozási listán szereplő értékek alapján VNet használnak. Ezen a listán a további beállítások és nevek is szerepelnek. Ezt a listát nem használjuk közvetlenül a lépések egyikében sem, de a listában szereplő értékek alapján is hozzáadhatunk változókat. A listát a hivatkozásként való használatra másolhatja, és az értékeket saját értékre cserélheti.

| Beállítás                   | Érték                                              |
| ---                       | ---                                                |
| Virtual Network neve | *TestVNet* |    
| Címterület Virtual Network | *192.168.0.0/16* |
| Erőforráscsoport | *TestRG* |
| Subnet1 neve | *FrontEnd* |   
| Subnet1 címterület | *192.168.1.0/24* |
| Subnet1 neve | *FrontEnd* |
| Átjáró alhálózatának neve | *GatewaySubnet* |    
| Átjáró alhálózatának címtartomány | *192.168.200.0/26* |
| Region | *USA keleti régiója* |
| Átjáró neve | *GW* |   
| Átjáró IP-neve | *GWIP* |
| Átjáró IP-konfigurációjának neve | *gwipconf* |
| Típus | *ExpressRoute* |
| Átjáró nyilvános IP-címe  | *gwpip* |

## <a name="add-a-gateway"></a>Átjáró hozzáadása

1. Az Azure-hoz való kapcsolódáshoz futtassa a parancsot `Connect-AzAccount` .

1. Deklarálja a gyakorlat változóit. Ügyeljen arra, hogy a mintát a használni kívánt beállításoknak megfelelően szerkessze.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
1. Tárolja a virtuális hálózati objektumot változóként.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
1. Adjon hozzá egy átjáró-alhálózatot a Virtual Networkhoz. Az átjáró-alhálózatnak "GatewaySubnet" névvel kell rendelkeznie. Az átjáró-alhálózat/27 vagy nagyobb (/26,/25 stb.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
1. Állítsa be a konfigurációt.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
1. Az átjáró-alhálózatot változóként tárolja.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
1. Kérjen egy nyilvános IP-címet. Az IP-címet az átjáró létrehozása előtt kéri a rendszer. Nem adhatja meg a használni kívánt IP-címet; dinamikusan van hozzárendelve. Ezt az IP-címet a következő konfigurációs szakaszban kell majd használni. A AllocationMethod dinamikusnak kell lennie.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
1. Hozza létre az átjáró konfigurációját. Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. Ebben a lépésben megadhatja a konfigurációt, amelyet az átjáró létrehozásakor használni fog. A következő minta használatával hozza létre az átjáró konfigurációját.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
1. Hozzon létre egy átjárót. Ebben a lépésben a **-GatewayType** különösen fontos. A **ExpressRoute**értéket kell használnia. A parancsmagok futtatása után az átjáró 45 percet is igénybe vehet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Az átjáró létrehozásának ellenőrzése
Az átjáró létrehozásának ellenőrzéséhez használja a következő parancsokat:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Átjáró átméretezése
Számos [átjáró-SKU](expressroute-about-virtual-network-gateways.md)van. A következő parancs használatával bármikor módosíthatja az átjáró SKU-jának változását.

> [!IMPORTANT]
> Ez a parancs nem működik a UltraPerformance-átjáró esetében. Ha módosítani szeretné az átjárót egy UltraPerformance-átjáróra, először távolítsa el a meglévő ExpressRoute-átjárót, majd hozzon létre egy új UltraPerformance-átjárót. Ha vissza szeretné állítani az átjárót egy UltraPerformance-átjáróról, először távolítsa el az UltraPerformance-átjárót, majd hozzon létre egy új átjárót.
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az átjáró eltávolításához használja az alábbi parancsot:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

## <a name="next-steps"></a>További lépések
Miután létrehozta a VNet-átjárót, összekapcsolhatja a VNet egy ExpressRoute-áramkörrel. 

> [!div class="nextstepaction"]
> [Virtual Network összekapcsolása egy ExpressRoute-áramkörrel](expressroute-howto-linkvnet-arm.md)
