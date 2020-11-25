---
title: fájlbefoglalás
description: fájlbefoglalás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a3ae2a876d6a3772d941fec0b8a1ea3f537e60c3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010802"
---
A `Resize-AzVirtualNetworkGateway` PowerShell-parancsmag segítségével frissítheti vagy visszaminősítheti a Generation1 vagy a GENERATION2 SKU-t (az összes VpnGw SKU átméretezhető az alapszintű SKU-ok kivételével). Ha az alapszintű átjáró-SKU-t használja, [használja az alábbi utasításokat](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) az átjáró átméretezéséhez.

A következő PowerShell-példa egy átjáró SKU-jának átméretezését mutatja be a VpnGw2-re.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```