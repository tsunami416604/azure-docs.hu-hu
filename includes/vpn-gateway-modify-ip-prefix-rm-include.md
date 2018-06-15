---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/28/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8803aada61ae58f1e221767aeb382f7d74c63eb4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30326494"
---
### <a name="noconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – nincs átjárókapcsolat

További címelőtagok felvétele:

```azurepowershell-interactive
$local = Get-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.101.0.0/24','10.101.1.0/24','10.101.2.0/24')
```

Címelőtagok eltávolítása:<br>
Hagyja ki azokat címelőtagokat, amelyekre már nincs szüksége. Ebben a példában a Microsoft már nem kell előtag 10.101.2.0/24 (az előző példából), ezért frissítjük a helyi hálózati átjáró, kivéve a ezt az előtagot.

```azurepowershell-interactive
$local = Get-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
```

### <a name="withconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – létező átjárókapcsolat

Ha már rendelkezik átjárókapcsolattal, és szeretné felvenni vagy eltávolítani a helyi hálózati átjáróban tárolt IP-címelőtagokat, akkor az alábbi lépéseket kell sorban végrehajtania. Ez némi állásidőt jelent a VPN-kapcsolata számára. Mikor módosítja az IP-címelőtagokat, nem kell törölnie a VPN-átjárót. Csak a kapcsolatot kell eltávolítania.


1. Távolítsa el a kapcsolatot.

  ```azurepowershell-interactive
  Remove-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
  ```
2. Módosítsa a helyi hálózati átjáró címelőtagjait.
   
  Állítsa be a LocalNetworkGateway változóját.

  ```azurepowershell-interactive
  $local = Get-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
  ```
   
  Módosítsa az előtagokat.
   
  ```azurepowershell-interactive
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```
3. Hozza létre a kapcsolatot. Ebben a példában egy IPsec kapcsolattípust konfigurálunk. A kapcsolat létrehozásakor a konfigurációban meghatározott kapcsolattípust használja. További kapcsolattípusok esetén tekintse meg a [PowerShell-parancsmag](https://msdn.microsoft.com/library/mt603611.aspx) oldalát.
   
  Állítsa be a VirtualNetworkGateway változóját.

  ```azurepowershell-interactive
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name VNet1GW  -ResourceGroupName TestRG1
  ```
   
  Hozza létre a kapcsolatot. Ez a példa a 2. lépésben beállított $local változót használja.

  ```azurepowershell-interactive
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 `
  -ResourceGroupName TestRG1 -Location 'East US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```