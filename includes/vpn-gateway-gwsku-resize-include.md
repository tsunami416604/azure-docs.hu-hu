---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 081352a23e6a0d8f9e2daa77eca1f8ac85172ff6
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418228"
---
Az aktuális Termékváltozatai (VpnGw1, VpnGw2 és VPNGW3), szeretné méretezze át a Termékváltozat frissítése a nagyobb teljesítményű egy átjárót is használhat a `Resize-AzVirtualNetworkGateway` PowerShell-parancsmagot. Az átjáró SKU-méret, ez a parancsmag használatával is visszaminősítheti. Ha használja az alapszintű átjáró Termékváltozat, [inkább használja a következő utasításokat](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) méretezze át az átjárót.

A következő PowerShell-példa az átjárók Termékváltozatainak átméretezése, a VpnGw2 jeleníti meg.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Át is méretezheti egy átjárót, az Azure Portalon lépjen a **konfigurációs** lapon a virtuális hálózati átjáró és a egy másik Termékváltozatot kiválasztásával a legördülő listából.