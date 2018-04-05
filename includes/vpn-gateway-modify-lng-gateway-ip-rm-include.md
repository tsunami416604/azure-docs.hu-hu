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
ms.openlocfilehash: 4ee182202cf1ecbbb0845541269f7241de26c170
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
### <a name="gwipnoconnection"></a> Helyi hálózati átjáró „GatewayIpAddress” értékének módosítása – nincs átjárókapcsolat

Ha a VPN-eszköz, amelyhez csatlakozni akar, megváltoztatta nyilvános IP-címét, a változtatásnak megfelelően módosítania kell a helyi hálózati átjárót. A példa alapján módosítsa a helyi hálózati átjárót, amelynek nincs átjárókapcsolata.

Az érték módosításával egy időben a címelőtagokat is módosíthatja. Ügyeljen arra, hogy a helyi hálózati átjáró meglévő nevét használja az aktuális beállítások felülírásához. Ha más nevet használ, új helyi hálózati átjárót hoz létre a meglévő felülírása helyett.

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="gwipwithconnection"></a>Helyi hálózati átjáró „GatewayIpAddress” értékének módosítása – létező átjárókapcsolat

Ha a VPN-eszköz, amelyhez csatlakozni akar, megváltoztatta nyilvános IP-címét, a változtatásnak megfelelően módosítania kell a helyi hálózati átjárót. Ha már létezik egy átjárókapcsolat, először el kell távolítania a kapcsolatot. Ha a kapcsolatot eltávolította, módosíthatja az átjáró IP-címét, és létrehozhat egy új kapcsolatot. Ugyanekkor módosíthatja a címelőtagokat is. Ez némi állásidőt jelent a VPN-kapcsolata számára. Mikor módosítja az átjáró IP-címét, nem kell törölnie a VPN-átjárót. Csak a kapcsolatot kell eltávolítania.
 

1. Távolítsa el a kapcsolatot. A kapcsolat nevét a Get-AzureRmVirtualNetworkGatewayConnection parancsmaggal keresheti meg.

  ```azurepowershell-interactive
  Remove-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 `
  -ResourceGroupName TestRG1
  ```
2. Módosítsa a GatewayIpAddress értéket. Ugyanekkor módosíthatja a címelőtagokat is. Ügyeljen arra, hogy a helyi hálózati átjáró meglévő nevét használja az aktuális beállítások felülírásakor. Ha nem ezt teszi, új helyi hálózati átjárót hoz létre a meglévő felülírása helyett.

  ```azurepowershell-interactive
  New-AzureRmLocalNetworkGateway -Name Site1 `
  -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
  ```
3. Hozza létre a kapcsolatot. Ebben a példában egy IPsec kapcsolattípust konfigurálunk. A kapcsolat létrehozásakor a konfigurációban meghatározott kapcsolattípust használja. További kapcsolattípusok esetén tekintse meg a [PowerShell-parancsmag](https://msdn.microsoft.com/library/mt603611.aspx) oldalát.  A VirtualNetworkGateway nevét a Get-AzureRmVirtualNetworkGateway parancsmag futtatásával szerezheti be.
   
    Állítsa be a változókat.

  ```azurepowershell-interactive
  $local = Get-AzureRMLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
  ```
   
    Hozza létre a kapcsolatot.

  ```azurepowershell-interactive 
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
  -Location "East US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```