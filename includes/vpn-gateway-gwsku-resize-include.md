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
ms.openlocfilehash: c06e69dd9d1997500589659e936dc25ee01ed145
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "30196780"
---
Az átjáró frissítése a hatékonyabb egy SKU átméretezése szeretné a jelenlegi termékváltozatok (VpnGw1 VpnGw2 és VPNGW3) is használhatja a `Resize-AzureRmVirtualNetworkGateway` PowerShell-parancsmagot. Az átjáró-Termékváltozat-méretét, ez a parancsmag segítségével is visszaminősítheti. Ha használja az alapszintű átjáró-Termékváltozat, [ehelyett kövesse ezeket az utasításokat](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) átméretezése az átjárót.

A következő PowerShell-példa látható egy átjáró VpnGw2 az átméretezett Termékváltozat.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Az Azure portálon átjáró méretezze át a a **konfigurációs** lapon a virtuális hálózati átjáró és egy másik SKU kiválasztása a legördülő listából.