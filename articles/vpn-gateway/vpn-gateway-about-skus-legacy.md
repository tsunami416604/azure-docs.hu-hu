---
title: Örökölt Azure virtuális hálózati VPN-átjáró termékkészletei
description: A régi virtuális hálózati átjáró-skus-okkal való munka; Alapszintű, normál és magas teljesítmény.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279389"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Virtuális hálózati átjáró-sk-ek (örökölt sk-ek) használatával való együttműködés

Ez a cikk az örökölt (régi) virtuális hálózati átjáró termékkészletével kapcsolatos információkat tartalmazza. Az örökölt termékkészlet továbbra is működik a már létrehozott VPN-átjárók mindkét telepítési modelljében. A klasszikus VPN-átjárók továbbra is használják az örökölt terméktájékoztatókat, mind a meglévő átjárók, mind az új átjárók esetében. Új Erőforrás-kezelő VPN-átjárók létrehozásakor használja az új átjáró termékkészleteit. Az új termékalkalmazásokkal kapcsolatos további tudnivalókért olvassa el [a VPN-átjáró – például a VPN-átjáró – további](vpn-gateway-about-vpngateways.md)információt.

## <a name="gateway-skus"></a><a name="gwsku"></a>Átjáró-termékváltozatok

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

Az örökölt átjáródíjszabást a **Virtuális hálózati átjárók** szakaszban tekintheti meg, amely az [ExpressRoute díjszabási lapján](https://azure.microsoft.com/pricing/details/expressroute)található.

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>A termékváltozat becsült összesített átviteli sebessége

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>Támogatott konfigurációk termékváltozat és VPN-típus szerint

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>Átjáró átméretezése

Az átjáró átméretezése egy átjáró termékváltozat ugyanabban a termékváltozat családon belül. Ha például rendelkezik egy szabványos termékváltozat, átméretezheti a HighPerformance Termékváltozat. A VPN-átjáró azonban nem méretezhető át a régi termékváltozatok és az új termékváltozat-családok között. Például nem lehet egy szabványos termékváltozatból egy VpnGw2 Termékváltozatba, vagy egy alapszintű termékváltozatból vpnGw1-be menni.

### <a name="resource-manager"></a>Resource Manager

Az Erőforrás-kezelő központi telepítési modelljének átjárójának a PowerShell használatával történő átméretezéséhez használja a következő parancsot:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

Az Azure Portalon átméretezheti az átjárót is.

### <a name="classic"></a><a name="classicresize"></a>Klasszikus

A klasszikus központi telepítési modell átjárójának átméretezéséhez a Service Management PowerShell-parancsmagokat kell használnia. Használja az alábbi parancsot:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>Váltás az új átjáró-ska-ra

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>További lépések

Az új átjáró-sk-ekről további információt az [átjáró-sk-ek című témakörben talál.](vpn-gateway-about-vpngateways.md#gwsku)

A konfigurációs beállításokról a [VPN-átjáró konfigurációs beállításai című témakörben olvashat bővebben.](vpn-gateway-about-vpn-gateway-settings.md)
