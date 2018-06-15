---
title: fájl belefoglalása
description: fájl belefoglalása
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/22/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7e33d4ed7100287ef6b22aa4c90fd52671363902
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31613619"
---
Ez a feladat lépéseit a következő konfigurációs hivatkozás listában található értékek alapján a VNet használja. További beállításokat és a nevek azt is ezen a listán. Nem használjuk a lista összes lépést, közvetlenül a Bár jelenleg felvenni a listában szereplő értékek alapján a változók. A listában referenciaként, az értékeket cserélje le a saját másolhatja.

**Konfigurációs hivatkozáslista**

* Virtuális hálózati név = "TestVNet"
* Virtuális hálózati címterület = 192.168.0.0/16
* Erőforráscsoport = "TestRG"
* Alhalozat_1 Name = "Előtér" 
* Címterület Alhalozat_1 = "192.168.1.0/24"
* Átjáró alhálózati név: "GatewaySubnet" mindig neve egy átjáró-alhálózatot kell *GatewaySubnet*.
* Átjáró alhálózati címtartományt = "192.168.200.0/26"
* A régióban = "USA keleti régiója"
* Átjáró Name = "GW"
* Átjáró IP-név = "GWIP"
* Átjáró IP-konfiguráció neve = "gwipconf"
* Típus = "ExpressRoute" Ez a típus egy ExpressRoute-konfiguráció szükséges.
* Átjáró nyilvános IP-név = "gwpip"

## <a name="add-a-gateway"></a>Átjáró hozzáadása
1. Csatlakozás az Azure-előfizetéshez.

  ```powershell 
  Connect-AzureRmAccount
  Get-AzureRmSubscription 
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Deklarálja a változókat, ehhez a gyakorlathoz. Győződjön meg arról, a minta megfelelően a beállításokat, amely a használni kívánt szerkesztése.

  ```powershell 
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "GW"
  $GWIPName = "GWIP"
  $GWIPconfName = "gwipconf"
  $VNetName = "TestVNet"
  ```
3. A virtuális hálózat objektumot tárolja változóként.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  ```
4. Egy átjáró alhálózatának hozzáadása a virtuális hálózathoz. Az átjáró alhálózatának "GatewaySubnet" nevet kell kapniuk. Hozzon létre egy átjáró-alhálózatot, amely /27 vagy nagyobb (26, / / 25, stb.).

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
  ```
5. Állítsa be a konfigurációt.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Az átjáró alhálózatának tárolására változóként.

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
  ```
7. Kérjen egy nyilvános IP-címet. Az IP-címre van szükség az átjáró létrehozása előtt. Nem adható meg; használni kívánt IP-cím dinamikusan történik. Ezt az IP-címet a következő konfigurációs szakaszban kell majd használni. Az AllocationMethod dinamikus kell lennie.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  ```
8. A konfiguráció az átjáró létrehozása. Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. Ebben a lépésben meg a konfigurációt, az átjáró létrehozásakor használható. Ez a lépés nem hoz létre az átjáró objektum. Az alábbi minta használatával hozza létre az átjáró konfigurációját.

  ```powershell
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```
9. Hozza létre az átjárót. Ebben a lépésben a **- GatewayType** különösen fontos. Az értéket kell használnia **ExpressRoute**. Miután ezek a parancsmagok, az átjáró 45 percig vagy tovább is létre vehet igénybe.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
  ```

## <a name="verify-the-gateway-was-created"></a>Ellenőrizze az átjáró létrejött-e
A következő parancsokkal ellenőrizheti, hogy létrejött-e az átjáró:

```powershell
Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Átjáró méretezése
A több [Gateway SKU-n](../articles/expressroute/expressroute-about-virtual-network-gateways.md). A következő paranccsal átjáró-Termékváltozat bármikor módosíthatja.

> [!IMPORTANT]
> Ez a parancs UltraPerformance átjáró nem működik. Ha módosítani szeretné az átjárót egy UltraPerformance átjárót, először távolítsa el a meglévő ExpressRoute-átjárót, és ezután hozzon létre újat UltraPerformance. Az átjáró egy UltraPerformance átjáró használni, először távolítsa el a UltraPerformance átjáró, és ezután hozzon létre újat.
> 
> 

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Átjáró eltávolítása
A következő paranccsal egy átjáró eltávolítása:

```powershell
Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
