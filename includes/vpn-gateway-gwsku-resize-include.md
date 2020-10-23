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
ms.openlocfilehash: a3ae2a876d6a3772d941fec0b8a1ea3f537e60c3
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424322"
---
A `Resize-AzVirtualNetworkGateway` PowerShell-parancsmag segítségével frissítheti vagy visszaminősítheti a Generation1 vagy a GENERATION2 SKU-t (az összes VpnGw SKU átméretezhető az alapszintű SKU-ok kivételével). Ha az alapszintű átjáró-SKU-t használja, [használja az alábbi utasításokat](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) az átjáró átméretezéséhez.

A következő PowerShell-példa egy átjáró SKU-jának átméretezését mutatja be a VpnGw2-re.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```