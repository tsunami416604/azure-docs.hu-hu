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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73495758"
---
A PowerShell-parancsmag `Resize-AzVirtualNetworkGateway` segítségével frissítheti vagy visszaminősítheti a Generation1 vagy a Generation2 SKU-t (az összes VpnGw SKU átméretezhető az alapszintű SKU-ok kivételével). Ha az alapszintű átjáró-SKU-t használja, [használja az alábbi utasításokat](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) az átjáró átméretezéséhez.

A következő PowerShell-példa egy átjáró SKU-jának átméretezését mutatja be a VpnGw2-re.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Az átjárókat a Azure Portal átméretezheti úgy is, hogy a virtuális hálózati átjáró **konfiguráció** lapjára kattint, és kiválasztja a legördülő listából egy másik SKU-t.
