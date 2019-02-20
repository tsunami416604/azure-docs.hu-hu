---
title: Örökölt az Azure virtual network VPN gateway Termékváltozatoktól |} A Microsoft Docs
description: A régi virtuális hálózati átjárók Termékváltozatainak; használata Alapszintű, Standard és HighPerformance.
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
ms.openlocfilehash: 5a9e3f63a484069bf8cd39f8a545d7c37f05c63c
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417312"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Virtuális hálózati átjáró Termékváltozatai (örökölt termékváltozatok) használata

Ez a cikk az örökölt (régi) virtuális hálózati átjáró-Termékváltozatokkal kapcsolatos információkat tartalmaz. Az örökölt termékváltozatok továbbra is működni a már létrehozott VPN-átjárók mindkét üzemi modellben. Klasszikus VPN-átjárók továbbra is a meglévő átjárók, és új átjárók örökölt termékváltozatok használata. Amikor hoz létre új Resource Manager VPN-átjárók, használja az új átjáró SKU-k. Az új termékváltozatokra vonatkozó információkért lásd: [információk a VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>Átjáró-termékváltozatok

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Becsült összesített átviteli sebessége Termékváltozat

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Írja be a Termékváltozat és a VPN által támogatott konfigurációk

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Az átjáró átméretezése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az átjárók Termékváltozatainak belül az azonos Termékváltozat-család átjáró méretezheti. Például ha a Standard Termékváltozat, átméretezheti, a HighPerformance termékváltozatra. Azonban nem tudják átméretezni a régi termékváltozatokról az új Termékváltozat-családokra közötti VPN-átjárót. Ha például már nem válthat a Standard Termékváltozat VpnGw2 Termékváltozat vagy alapszintű Termékváltozat a VpnGw1.

Méretezze át a klasszikus üzemi modell esetében egy átjárót, használja a következő parancsot:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Méretezze át a Resource Manager üzemi modell PowerShell használatával egy átjárót, használja a következő parancsot:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
Az Azure Portalon egy átjárót is méretezheti.

## <a name="change"></a>Módosítsa az új átjáró SKU-k

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>További lépések

Az új átjáró-termékváltozatok kapcsolatos további információkért lásd: [átjáró-termékváltozatok](vpn-gateway-about-vpngateways.md#gwsku).

Konfigurációs beállításaival kapcsolatos további információkért lásd: [információk a VPN Gateway konfigurációs beállításairól](vpn-gateway-about-vpn-gateway-settings.md).
