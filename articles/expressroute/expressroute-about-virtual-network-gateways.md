---
title: Kapcsolatos az ExpressRoute virtuális hálózati átjárók |} Microsoft Docs
description: További információk a virtuális hálózati átjárók az ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager, azure-service-management
ms.assetid: 7e0d9658-bc00-45b0-848f-f7a6da648635
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: cherylmc
ms.openlocfilehash: ae971e7743d6dd3269c0a4f976bd2a5316300f58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30235325"
---
# <a name="about-virtual-network-gateways-for-expressroute"></a>Az ExpressRoute virtuális hálózati átjáróinak ismertetése
A virtuális hálózati átjáró küldhető Azure virtuális hálózatok közötti hálózati forgalom és a helyszíni helyek. Amikor konfigurál egy ExpressRoute-kapcsolatot, hozzon létre és konfigurálnia kell a virtuális hálózati átjáró és a virtuális hálózati átjáró kapcsolat.

Egy virtuális hálózati átjáró létrehozásakor több beállítást is meg kell adnia. A szükséges beállítások egyikének határozza meg, hogy az átjáró expressroute-on vagy a telephelyek közötti VPN-forgalomhoz használható. A Resource Manager üzembe helyezési modellel, az érték "-GatewayType".

Amikor a hálózati adatforgalom a kapcsolatot, az átjáró típusa "ExpressRoute" használhatja. Ezt ExpressRoute-átjárónak is hívják. Amikor a hálózati adatforgalom titkosított a nyilvános interneten keresztül, használhatja az átjáró típusa "Vpn". Ez VPN-átjáróként ismert. A hely–hely, pont–hely és a virtuális hálózatok közötti kapcsolat kapcsolatok mind VPN-átjárót használnak.

Mindegyik virtuális hálózat csak egy virtuális hálózati átjáróval rendelkezhet átjárótípusonként. Rendelkezhet például egy virtuális hálózati átjáróval, amely a -GatewayType Vpn típust, és egy másikkal, amelyik a -GatewayType ExpressRoute típust használja. Ez a cikk foglalkozik az ExpressRoute virtuális hálózati átjáróhoz.

## <a name="gwsku"></a>Átjáró-termékváltozatok
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Ha szeretné frissíteni az átjárót egy nagyobb teljesítményű átjáró SKU, a legtöbb esetben használhatja a "Átméretezési-AzureRmVirtualNetworkGateway" PowerShell-parancsmagot. Ez a Standard és a HighPerformance termékváltozatok frissítésekre fog működni. Azonban a UltraPerformance SKU frissítéséhez, akkor hozza létre újra az átjárót.

### <a name="aggthroughput"></a>Termékváltozat-átjáró által becsült teljesítmény
A következő táblázat az átjáró típusa és a becsült teljesítményét. Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Alkalmazásteljesítmény több tényezőtől függ, többek között a végpontok közötti késés, és megnyílik az alkalmazás forgalom számát. A táblázatban szereplő számok jelölik a felső korlátja, amely az alkalmazás a ideális környezet elméletileg érhető el. 
> 
>

## <a name="resources"></a>REST API-k és a PowerShell-parancsmagok
További technikai erőforrások és konkrét szintaxis használatával kapcsolatos követelményeket REST API-k és a PowerShell-parancsmagok a virtuális hálózati átjáró konfigurációjában: a következő lapok:

| **Klasszikus** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>További lépések
Lásd: [ExpressRoute – áttekintés](expressroute-introduction.md) használható kapcsolat konfigurációival kapcsolatos további információkért.

Lásd: [hozzon létre egy virtuális hálózati átjáró ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) ExpressRoute-átjáró létrehozásával kapcsolatos további információt.
