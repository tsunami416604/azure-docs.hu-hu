---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6505b12b35ee436930ba6571c27db30c12030041
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418282"
---
### <a name="gwipnoconnection"></a> Helyi hálózati átjáró „GatewayIpAddress” értékének módosítása – nincs átjárókapcsolat

Ha a VPN-eszköz, amelyhez csatlakozni akar, megváltoztatta nyilvános IP-címét, a változtatásnak megfelelően módosítania kell a helyi hálózati átjárót. A példa alapján módosítsa a helyi hálózati átjárót, amelynek nincs átjárókapcsolata.

Az érték módosításával egy időben a címelőtagokat is módosíthatja. Ügyeljen arra, hogy a helyi hálózati átjáró meglévő nevét használja az aktuális beállítások felülírásához. Ha más nevet használ, új helyi hálózati átjárót hoz létre a meglévő felülírása helyett.

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="gwipwithconnection"></a>Helyi hálózati átjáró „GatewayIpAddress” értékének módosítása – létező átjárókapcsolat

Ha a VPN-eszköz, amelyhez csatlakozni akar, megváltoztatta nyilvános IP-címét, a változtatásnak megfelelően módosítania kell a helyi hálózati átjárót. Ha már létezik egy átjárókapcsolat, először el kell távolítania a kapcsolatot. Ha a kapcsolatot eltávolította, módosíthatja az átjáró IP-címét, és létrehozhat egy új kapcsolatot. Ugyanekkor módosíthatja a címelőtagokat is. Ez némi állásidőt jelent a VPN-kapcsolata számára. Mikor módosítja az átjáró IP-címét, nem kell törölnie a VPN-átjárót. Csak a kapcsolatot kell eltávolítania.
 

1. Távolítsa el a kapcsolatot. A kapcsolat neve a "Get-AzVirtualNetworkGatewayConnection" parancsmag használatával is megtalálhatja.

   ```azurepowershell-interactive
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1
   ```
2. Módosítsa a GatewayIpAddress értéket. Ugyanekkor módosíthatja a címelőtagokat is. Ügyeljen arra, hogy a helyi hálózati átjáró meglévő nevét használja az aktuális beállítások felülírásakor. Ha nem ezt teszi, új helyi hálózati átjárót hoz létre a meglévő felülírása helyett.

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name Site1 `
   -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
   -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
   ```
3. Hozza létre a kapcsolatot. Ebben a példában egy IPsec kapcsolattípust konfigurálunk. A kapcsolat létrehozásakor a konfigurációban meghatározott kapcsolattípust használja. További kapcsolattípusok esetén tekintse meg a [PowerShell-parancsmag](https://msdn.microsoft.com/library/mt603611.aspx) oldalát.  Szerezze be a VirtualNetworkGateway nevét, a 'Get-AzVirtualNetworkGateway' parancsmagot futtathatja.
   
    Állítsa be a változókat.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1

   $vnetgw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   ```
   
    Hozza létre a kapcsolatot.

   ```azurepowershell-interactive 
   New-AzVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
   -Location "East US" `
   -VirtualNetworkGateway1 $vnetgw `
   -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```