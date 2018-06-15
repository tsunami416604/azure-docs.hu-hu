---
title: A hagyományos Azure virtuális hálózat VPN gateway SKU-n |} Microsoft Docs
description: A régi virtuális hálózati átjáró termékváltozatok; használata Basic, Standard és a HighPerformance.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: cherylmc
ms.openlocfilehash: 4feecb9c1e91e1bc6c66a610c092e7bf894886e5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30190228"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Virtuális hálózati átjáró termékváltozatok (örökölt SKU) használata

Ez a cikk az örökölt (régi) virtuális hálózati átjáró termékváltozatok információkat tartalmaz. Az örökölt termékváltozatok továbbra is működni a két üzembe helyezési modell, amely már létre van hozva VPN-átjárók. Hagyományos VPN-átjárók továbbra is a hagyományos termékváltozatok meglévő átjáró, valamint az új átjáró. Új erőforrás-kezelő VPN átjáró létrehozása esetén az új átjáró termékváltozatok használatához. Az új termékváltozatok kapcsolatos információkért lásd: [VPN-átjáró](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>Átjáró-termékváltozatok

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>A Termékváltozat becsült összesített átviteli sebesség

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Termékváltozat- és VPN-típus által támogatott konfigurációk

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Átjáró méretezése

Az átjáró egy Gateway SKU belül az azonos SKU-család is átméretezhetők. Például ha egy Standard Termékváltozat, átméretezheti a HighPerformance másikra. Azonban nem tudja átméretezni a régi termékváltozatok és az új SKU-családok közötti VPN-átjárót. Például nem léphet a szabványos Termékváltozatáról egy VpnGw2 SKU, vagy egy alapszintű Termékváltozat VpnGw1.

A klasszikus üzembe helyezési modell átjáró átméretezéséhez használja a következő parancsot:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

A Resource Manager üzembe helyezési modellel PowerShell használatával az átjáró átméretezéséhez használja a következő parancsot:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
Az Azure portálon átjáró is méretezheti.

## <a name="change"></a>Az új átjáróra termékváltozatok módosítása

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>További lépések

Az új átjáró-termékváltozat kapcsolatos további információkért lásd: [Gateway SKU-n](vpn-gateway-about-vpngateways.md#gwsku).

Konfigurációs beállításaival kapcsolatos további információkért lásd: [VPN-átjáró konfigurációs beállítások](vpn-gateway-about-vpn-gateway-settings.md).