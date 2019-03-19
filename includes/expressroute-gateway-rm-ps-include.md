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
ms.openlocfilehash: 03a56951b68163a9160cc4a57f15354b5f210eb7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125219"
---
Ez a feladat lépései használják egy virtuális hálózathoz, a következő konfigurációs hivatkozás listában található értékek alapján. Ez a lista további beállításokat és neveket is rendszerkövetelményeknek. Nem használjuk ezt a listát minden lépést, közvetlenül a bár adjuk hozzá a változók a listában szereplő értékek alapján. A listában, hivatkozásként van listázva, használata és cserélje le az értékeket saját másolhatja.

* Virtuális hálózat neve = "TestVNet"
* Virtuális hálózat címtere = 192.168.0.0/16
* Erőforráscsoport = "TestRG"
* Subnet1 Name = "Előtér" 
* Subnet1 címtér = "192.168.1.0/24"
* Átjáróalhálózat neve: Egy átjáró-alhálózatot kell neve "GatewaySubnet" *GatewaySubnet*.
* Átjáró-alhálózat címtere = "192.168.200.0/26"
* Régió = "USA keleti RÉGIÓJA"
* Átjáró neve = "GW"
* Átjáró IP-név = "GWIP"
* Átjáró IP-konfiguráció neve = "gwipconf"
* Írja be "az ExpressRoute" = a típusát kötelező megadni egy ExpressRoute-konfigurációhoz.
* Gateway Public IP Name = "gwpip"

## <a name="add-a-gateway"></a>Átjáró hozzáadása
1. Csatlakozás az Azure-előfizetéshez.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Deklarálja a változókat a gyakorlatban. Ügyeljen arra, hogy a használni kívánt beállításokat a minta szerkesztéséhez.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. A virtuális hálózati objektum Store változóként.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Adjon hozzá egy átjáró-alhálózatot a virtuális hálózathoz. Az átjáró-alhálózat "GatewaySubnet" névvel kell lennie. Hozzunk létre egy átjáró-alhálózatot, amely legfeljebb/27 vagy nagyobb (/ 26-os vagy/25-stb.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Állítsa be a konfigurációt.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Az átjáró-alhálózat Store változóként.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Kérjen egy nyilvános IP-címet. A IP-címre van szükség az átjáró létrehozása előtt. Nem adhat meg, amelyet szeretne használni; az IP-cím dinamikusan kiosztott. Ezt az IP-címet a következő konfigurációs szakaszban kell majd használni. Az AllocationMethod dinamikusnak kell lennie.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Hozzon létre az átjáró konfigurációját. Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. Ebben a lépésben adja meg a konfigurációt, amely az átjáró létrehozásakor használható. Ez a lépés nem hoz létre az átjáró objektum. Az alábbi minta használatával hozza létre az átjáró konfigurációját.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Az átjáró létrehozásához. Ebben a lépésben a **- GatewayType** különösen fontos. Az értéket kell használnia **ExpressRoute**. Miután ezek a parancsmagok, a az átjárót is igénybe vehet, 45 percet vagy többet hozhat létre.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Az átjáró létrehozásának ellenőrzéséhez
A következő parancsok használatával győződjön meg arról, hogy az átjáró létrehozása:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Az átjáró átméretezése
A következő számú [átjáró-termékváltozatok](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Az alábbi parancs segítségével bármikor módosíthatja az átjáró-Termékváltozat.

> [!IMPORTANT]
> Ez a parancs az UltraPerformance átjáró nem működik. Ha módosítani szeretné az átjárót az UltraPerformance átjáró, először távolítsa el a meglévő ExpressRoute-átjárót, és hozzon létre egy új UltraPerformance átjáró. Alacsonyabb szolgáltatásszintre váltásához az átjáró az UltraPerformance átjáró, először távolítsa el az UltraPerformance átjáró, és hozzon létre egy új átjárót.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Átjáró eltávolítása
A következő paranccsal átjáró eltávolítása:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
