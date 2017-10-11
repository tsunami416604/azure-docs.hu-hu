---
title: "Kapcsolatos az ExpressRoute virtuális hálózati átjárók |} Microsoft Docs"
description: "További információk a virtuális hálózati átjárók az ExpressRoute."
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 7e0d9658-bc00-45b0-848f-f7a6da648635
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2017
ms.author: cherylmc
ms.openlocfilehash: a6363fa380d0bab05d7500141cc6019d1d3f68b8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="about-virtual-network-gateways-for-expressroute"></a>Az ExpressRoute virtuális hálózati átjáróinak ismertetése
A virtuális hálózati átjáró küldhető Azure virtuális hálózatok közötti hálózati forgalom és a helyszíni helyek. Amikor konfigurál egy ExpressRoute-kapcsolatot, hozzon létre és konfigurálnia kell a virtuális hálózati átjáró és a virtuális hálózati átjáró kapcsolat.

Egy virtuális hálózati átjáró létrehozásakor több beállítást is meg kell adnia. A szükséges beállítások egyikének határozza meg, hogy az átjáró expressroute-on vagy a telephelyek közötti VPN-forgalomhoz használható. A Resource Manager üzembe helyezési modellel, az érték "-GatewayType".

Amikor a hálózati adatforgalom a kapcsolatot, az átjáró típusa "ExpressRoute" használhatja. Ezt ExpressRoute-átjárónak is hívják. Amikor a hálózati adatforgalom titkosított a nyilvános interneten keresztül, használhatja az átjáró típusa "Vpn". Ez VPN-átjáróként ismert. A hely–hely, pont–hely és a virtuális hálózatok közötti kapcsolat kapcsolatok mind VPN-átjárót használnak.

Mindegyik virtuális hálózat csak egy virtuális hálózati átjáróval rendelkezhet átjárótípusonként. Rendelkezhet például egy virtuális hálózati átjáróval, amely a -GatewayType Vpn típust, és egy másikkal, amelyik a -GatewayType ExpressRoute típust használja. Ez a cikk foglalkozik az ExpressRoute virtuális hálózati átjáróhoz.

## <a name="gwsku"></a>Átjáró-termékváltozatok
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Ha szeretné frissíteni az átjárót egy nagyobb teljesítményű átjáró SKU, a legtöbb esetben használhatja a "Átméretezési-AzureRmVirtualNetworkGateway" PowerShell-parancsmagot. Ez a Standard és a HighPerformance termékváltozatok frissítésekre fog működni. Azonban a UltraPerformance SKU frissítéséhez, akkor hozza létre újra az átjárót.

### <a name="aggthroughput"></a>Összesített becsült gateway SKU
Az alábbi táblázatban az átjárótípusok és azok becsült összesített átviteli sebessége tekinthető meg. Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Alkalmazás átviteli sebességére több tényezőtől függ, többek között a végpontok közötti késés, és megnyílik az alkalmazás forgalom számát. A táblázatban szereplő számok jelölik a felső korlátja, amely az alkalmazás is theorectically elérése ideális környezetben. 
> 
>

## <a name="resources"></a>REST API-k és a PowerShell-parancsmagok
További technikai erőforrások és konkrét szintaxis használatával kapcsolatos követelményeket REST API-k és a PowerShell-parancsmagok a virtuális hálózati átjáró konfigurációjában: a következő lapok:

| **Klasszikus** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Következő lépések
Lásd: [ExpressRoute – áttekintés](expressroute-introduction.md) használható kapcsolat konfigurációival kapcsolatos további információkért. 

