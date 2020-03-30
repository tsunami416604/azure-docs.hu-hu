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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179652"
---
A feladat lépései virtuális hálózatot használnak a következő konfigurációs hivatkozási lista értékei alapján. A további beállításokat és neveket is felsoroljuk ebben a listában. Ezt a listát nem használjuk közvetlenül a lépések egyikében sem, bár a listában szereplő értékek alapján változókat adunk hozzá. A listát átmásolhatja referenciaként, és az értékeket a sajátjára cserélheti.

* Virtuális hálózat neve = "TestVNet"
* Virtuális hálózat címterülete = 192.168.0.0/16
* Erőforráscsoport = "TestRG"
* Alhálózat1 neve = "Előtér" 
* Alhálózat1 címtér = "192.168.1.0/24"
* Átjáró-alhálózat neve: "GatewaySubnet" Mindig el kell neveznie egy átjáró *alhálózatát GatewaySubnet*.
* Átjáró-alhálózati címtér = "192.168.200.0/26"
* Régió = "USA keleti régiója"
* Átjáró neve = "GW"
* Átjáró IP-neve = "GWIP"
* Átjáró IP-konfigurációjának neve = "gwipconf"
* Type = "ExpressRoute" Ez a típus szükséges egy ExpressRoute-konfigurációhoz.
* Átjáró nyilvános IP-neve = "gwpip"

## <a name="add-a-gateway"></a>Átjáró hozzáadása
1. Csatlakozzon Azure-előfizetéséhez.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Deklarálja a változókat ehhez a gyakorlathoz. Ügyeljen arra, hogy a mintát úgy szerkesztse, hogy az tükrözze a használni kívánt beállításokat.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. A virtuális hálózati objektumot változóként tárolja.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Átjáró-alhálózat hozzáadása a virtuális hálózathoz. Az átjáró alhálózatának "GatewaySubnet" nevűnek kell lennie. Hozzon létre egy átjáró alhálózatot, amely /27 vagy nagyobb (/26, /25 stb.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Állítsa be a konfigurációt.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Az átjáró alhálózatát változóként tárolja.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Kérjen egy nyilvános IP-címet. Az IP-cím az átjáró létrehozása előtt szükséges. Nem adhatja meg a használni kívánt IP-címet; dinamikusan van elosztva. Ezt az IP-címet a következő konfigurációs szakaszban kell majd használni. A Felosztási módszernek dinamikusnak kell lennie.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Hozza létre az átjáró konfigurációját. Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. Ebben a lépésben megadja az átjáró létrehozásakor használni kívánt konfigurációt. Ez a lépés valójában nem hozza létre az átjáróobjektumot. Az alábbi minta használatával hozza létre az átjáró konfigurációját.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Hozza létre az átjárót. Ebben a lépésben a **-GatewayType** különösen fontos. Az ExpressRoute értéket kell **használnia.** A parancsmagok futtatása után az átjáró létrehozása 45 percet vagy még többet is igénybe vehet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Az átjáró létrehozásának ellenőrzése
Az alábbi parancsokkal ellenőrizheti, hogy az átjáró létrejött-e:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Átjáró átméretezése
Számos [átjáró-ska létezik.](../articles/expressroute/expressroute-about-virtual-network-gateways.md) A következő paranccsal bármikor módosíthatja az átjáró termékváltozatát.

> [!IMPORTANT]
> Ez a parancs nem működik az UltraPerformance átjárónál. Az átjáró UltraPerformance-átjáróvá való módosításához először távolítsa el a meglévő ExpressRoute-átjárót, majd hozzon létre egy új UltraPerformance-átjárót. Az átjáró UltraPerformance-átjáróról való visszaminősítéséhez először távolítsa el az UltraPerformance átjárót, majd hozzon létre egy új átjárót.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Átjáró eltávolítása
Az átjáró eltávolításához használja a következő parancsot:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
