---
title: Tudnivalók az Azure ExpressRoute virtuális hálózati átjárók |} A Microsoft Docs
description: További információ a virtuális hálózati átjárók az expressroute-hoz.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: cherylmc
ms.openlocfilehash: d89463826cb05bf4cafd36cf4a38e60852d93094
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054064"
---
# <a name="about-virtual-network-gateways-for-expressroute"></a>Az ExpressRoute virtuális hálózati átjáróinak ismertetése
A virtuális hálózati átjáró Azure virtuális hálózatok közötti hálózati adatforgalom elküldésére használatosak, és a helyszínek. Használhatja egy virtuális hálózati átjáró vagy ExpressRoute-forgalmat, vagy VPN-forgalmat is használható. Ez a cikk az ExpressRoute virtuális hálózati átjárók összpontosít.

## <a name="gateway-types"></a>Átjárótípusok

Egy virtuális hálózati átjáró létrehozásakor több beállítást is meg kell adnia. A szükséges beállításokat egyik "-GatewayType", megadja, hogy az átjáró, az expressroute vagy VPN-forgalmat. A két átjáró típusok a következők: 

* **VPN** – a titkosított adatforgalmat továbbít a nyilvános interneten a "Vpn" átjárótípust használja. Ez is nevezik VPN-átjáró. A hely–hely, pont–hely és a virtuális hálózatok közötti kapcsolat kapcsolatok mind VPN-átjárót használnak.

* **Az ExpressRoute** – a hálózati forgalom elküldése egy privát kapcsolaton, az "ExpressRoute" átjárótípust használja. Ez egy ExpressRoute-átjárót is nevezik, és az ExpressRoute konfigurálása során használt átjáró típusát.


Mindegyik virtuális hálózat csak egy virtuális hálózati átjáróval rendelkezhet átjárótípusonként. Rendelkezhet például egy virtuális hálózati átjáróval, amely a -GatewayType Vpn típust, és egy másikkal, amelyik a -GatewayType ExpressRoute típust használja.

## <a name="gwsku"></a>Átjáró-termékváltozatok
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Ha azt szeretné, hogy nagyobb teljesítményű átjárók Termékváltozatainak az átjáró frissítése, a legtöbb esetben használhatja a "Átméretezése-AzureRmVirtualNetworkGateway" PowerShell-parancsmagot. Ez a Standard és a nagy teljesítményű termékváltozatok frissítéseket fog működni. Azonban az UltraPerformance Termékváltozattal frissíteni, meg kell hozza létre újra az átjárót.

### <a name="aggthroughput"></a>Átjárók Termékváltozata alapján becsült teljesítmény
Az alábbi táblázatban az átjárótípusok és a becsült teljesítményét mutatja. Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Alkalmazások teljesítményének attól függ, hogy több tényezővel bővül, például a végpontok közötti késés és a forgalom adatfolyamait, megnyílik az alkalmazás számát. A táblában szereplő számok jelölik a felső határ, amely ideális környezetet a elméletileg érheti el az alkalmazást. 
> 
>

### <a name="zrgw"></a>Zónaredundáns átjáró-termékváltozatok (előzetes verzió)

ExpressRoute-átjárók az Azure-beli rendelkezésre állási zónák is telepítheti. Ez fizikailag és logikailag a két különböző rendelkezésre állási zónában, a helyszíni hálózati kapcsolatok védelme az Azure-bA a zónaszintű hibák történő.

![Zónaredundáns ExpressRoute-átjáró](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Átjárók zónaredundáns adott új átjáró-termékváltozatok ExpressRoute-átjárót használja. Az új termékváltozatok a következők jelenleg elérhető **nyilvános előzetes verzióban**.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Az új átjáró-termékváltozatok támogatják az igényeinek leginkább megfelelő egyéb telepítési lehetőségekért is. Az új átjáró SKU-k használatával virtuális hálózati átjáró létrehozásakor, akkor is telepíteni az átjárót a megadott zónában. Ez a zónaszintű átjáró nevezzük. Zónaszintű az átjáró telepítésekor az átjáró összes példánya ugyanabban a rendelkezésre állási zónában üzemelnek. Az előzetes verzióban érhető el a regisztrációhoz lásd: [zónaredundáns virtuális hálózati átjáró létrehozása](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

## <a name="resources"></a>REST API-k és PowerShell-parancsmagok
További technikai erőforrások és a megadott szintaxissal kapcsolatos követelményeket REST API-k és PowerShell-parancsmagok használata a virtuális hálózati átjáró-konfigurációk tekintse meg a következő lapokon:

| **Klasszikus** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>További lépések
Lásd: [ExpressRoute áttekintése](expressroute-introduction.md) elérhető kapcsolati konfigurációkkal kapcsolatos további részletekért.

Lásd: [az ExpressRoute virtuális hálózati átjáró létrehozása](expressroute-howto-add-gateway-resource-manager.md) további információ az ExpressRoute-átjáró létrehozásához.
