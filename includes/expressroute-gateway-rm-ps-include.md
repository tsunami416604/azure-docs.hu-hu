---
title: fájl belefoglalása
description: fájl belefoglalása
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922ac7eb6cb9676af65700a6a2fe7fbae35a0dc5
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67179652"
---
A feladat lépései a következő konfigurációs hivatkozási listán szereplő értékek alapján VNet használnak. Ezen a listán a további beállítások és nevek is szerepelnek. Ezt a listát nem használjuk közvetlenül a lépések egyikében sem, de a listában szereplő értékek alapján is hozzáadhatunk változókat. A listát a hivatkozásként való használatra másolhatja, és az értékeket saját értékre cserélheti.

* Virtual Network neve = "TestVNet"
* Virtual Network címtartomány = 192.168.0.0/16
* Erőforráscsoport = "TestRG"
* Subnet1 neve = "FrontEnd" 
* Subnet1 címterület = "192.168.1.0/24"
* Átjáró alhálózatának neve: "GatewaySubnet" az átjáró alhálózatának *GatewaySubnet*kell lennie.
* Átjáró-alhálózati címtartomány = "192.168.200.0/26"
* Region = "USA keleti régiója"
* Átjáró neve = "GW"
* Átjáró IP-neve = "GWIP"
* Átjáró IP-konfigurációjának neve = "gwipconf"
* Type = "ExpressRoute" Ez a típus szükséges egy ExpressRoute-konfigurációhoz.
* Átjáró nyilvános IP-neve = "gwpip"

## <a name="add-a-gateway"></a>Átjáró hozzáadása
1. Kapcsolódjon az Azure-előfizetéséhez.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Deklarálja a gyakorlat változóit. Ügyeljen arra, hogy a mintát a használni kívánt beállításoknak megfelelően szerkessze.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. Tárolja a virtuális hálózati objektumot változóként.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Adjon hozzá egy átjáró-alhálózatot a Virtual Networkhoz. Az átjáró-alhálózatnak "GatewaySubnet" névvel kell rendelkeznie. Hozzon létre egy átjáró-alhálózatot, amely/27 vagy nagyobb (/26,/25 stb.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Állítsa be a konfigurációt.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Az átjáró-alhálózatot változóként tárolja.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Kérjen egy nyilvános IP-címet. Az IP-címet az átjáró létrehozása előtt kéri a rendszer. Nem adhatja meg a használni kívánt IP-címet; a szolgáltatás dinamikusan van lefoglalva. Ezt az IP-címet a következő konfigurációs szakaszban kell majd használni. A AllocationMethod dinamikusnak kell lennie.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Hozza létre az átjáró konfigurációját. Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. Ebben a lépésben megadhatja a konfigurációt, amelyet az átjáró létrehozásakor használni fog. Ez a lépés valójában nem hozza létre az átjáró objektumot. Az alábbi minta használatával hozza létre az átjáró konfigurációját.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Hozzon létre egy átjárót. Ebben a lépésben a **-GatewayType** különösen fontos. A **ExpressRoute**értéket kell használnia. A parancsmagok futtatása után az átjáró 45 percet is igénybe vehet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Az átjáró létrehozásának ellenőrzése
Az átjáró létrehozásának ellenőrzéséhez használja a következő parancsokat:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Átjáró átméretezése
Számos [átjáró-SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)van. A következő parancs használatával bármikor módosíthatja az átjáró SKU-jának változását.

> [!IMPORTANT]
> Ez a parancs nem működik a UltraPerformance-átjáró esetében. Ha módosítani szeretné az átjárót egy UltraPerformance-átjáróra, először távolítsa el a meglévő ExpressRoute-átjárót, majd hozzon létre egy új UltraPerformance-átjárót. Ha vissza szeretné állítani az átjárót egy UltraPerformance-átjáróról, először távolítsa el az UltraPerformance-átjárót, majd hozzon létre egy új átjárót.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Átjáró eltávolítása
Átjáró eltávolításához használja az alábbi parancsot:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
