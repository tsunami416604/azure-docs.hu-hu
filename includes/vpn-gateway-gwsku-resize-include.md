---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f6fd4039614dbd7c1a2b2c6ba8403502a6420fe3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908552"
---
Az aktuális Termékváltozatai (VpnGw1, VpnGw2 és VPNGW3), szeretné méretezze át a Termékváltozat frissítése a nagyobb teljesítményű egy átjárót is használhat a `Resize-AzVirtualNetworkGateway` PowerShell-parancsmagot. Az átjáró SKU-méret, ez a parancsmag használatával is visszaminősítheti. Ha használja az alapszintű átjáró Termékváltozat, [inkább használja a következő utasításokat](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) méretezze át az átjárót.

A következő PowerShell-példa az átjárók Termékváltozatainak átméretezése, a VpnGw2 jeleníti meg.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Át is méretezheti egy átjárót, az Azure Portalon lépjen a **konfigurációs** lapon a virtuális hálózati átjáró és a egy másik Termékváltozatot kiválasztásával a legördülő listából.