---
title: "Örökölt Azure virtuális hálózati átjáró termékváltozatok |} Microsoft Docs"
description: "A régi virtuális hálózati átjáró-termékváltozat."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 3b2126b1ecd1613950bbf311ae08fafd4af0d51f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Virtuális hálózati átjáró termékváltozatok (örökölt SKU) használata

Ez a cikk az örökölt (régi) virtuális hálózati átjáró termékváltozatok információkat tartalmaz. Az örökölt termékváltozatok továbbra is működni a két üzembe helyezési modell, amely már létre van hozva VPN-átjárók. Hagyományos VPN-átjárók továbbra is a hagyományos termékváltozatok meglévő átjáró, valamint az új átjáró. Új erőforrás-kezelő VPN átjáró létrehozása esetén az új átjáró termékváltozatok használatához. Az új termékváltozatok kapcsolatos információkért lásd: [VPN-átjáró](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>Átjáró-termékváltozatok

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>A Termékváltozat becsült összesített átviteli sebesség

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Termékváltozat- és VPN-típus által támogatott konfigurációk

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Automatikus oszlopszélesség egy átjáró (egy átjáró SKU módosítása)

Egy átjáró SKU belül az azonos SKU-család is átméretezhetők. Például ha egy Standard Termékváltozat, átméretezheti a HighPerformance másikra. A VPN-átjárók között a régi termékváltozatok és a új SKU-családja nem méretezhető át. Például nem folytatható a szabványos Termékváltozatáról egy VpnGw2 Termékváltozat. 

A klasszikus üzembe helyezési modell átjáró SKU átméretezéséhez használja a következő parancsot:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

A Resource Manager üzembe helyezési modellel átjárót SKU átméretezéséhez használja a következő parancsot:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="migrate"></a>Telepítse át az új átjáró termékváltozatok

Dolgozunk a Resource Manager üzembe helyezési modellel, ha az új átjáróra Termékváltozatok áttelepítheti. Dolgozunk a klasszikus üzembe helyezési modellel, ha az új SKU nem telepíthető át, és helyette továbbra is a hagyományos SKU.

[!INCLUDE [Migrate SKU](../../includes/vpn-gateway-migrate-legacy-sku-include.md)]

## <a name="next-steps"></a>Következő lépések

Az új átjáró-termékváltozat kapcsolatos további információkért lásd: [Gateway SKU-n](vpn-gateway-about-vpngateways.md#gwsku).

Konfigurációs beállításaival kapcsolatos további információkért lásd: [VPN-átjáró konfigurációs beállítások](vpn-gateway-about-vpn-gateway-settings.md).