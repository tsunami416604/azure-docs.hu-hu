---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73495758"
---
A `Resize-AzVirtualNetworkGateway` PowerShell-parancsmag segítségével frissítheti vagy visszaminősítheti a Generation1 vagy Generation2 termékváltozatokat (az összes VpnGw termékváltozat átméretezhető az egyszerű termékváltozatok kivételével). Ha az alapszintű átjáró termékváltozatát használja, az alábbi [utasításokat használja](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) az átjáró átméretezéséhez.

A következő PowerShell-példában egy átjáró termékváltozat vpnGw2-re van átméretezve.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Az Azure Portalon átméretezheti az átjárót a virtuális hálózati átjáró **konfigurációja** lapján, és a legördülő menüből egy másik termékváltozat kiválasztásával.
