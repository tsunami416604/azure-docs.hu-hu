---
title: Kapcsolatos az ExpressRoute virtuális hálózati átjárók – Azure |} A Microsoft Docs
description: További információ a virtuális hálózati átjárók az expressroute-hoz. Ez a cikk az átjáró-termékváltozatok és típusok kapcsolatos információkat tartalmaz.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 18615cf737eedcd188fd59d2aa98482210b9333a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65991589"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>ExpressRoute virtuális hálózati átjáró és FastPath
Csatlakozás az Azure virtuális hálózat és a helyszíni hálózat expressroute-on keresztül, először egy virtuális hálózati átjárót kell létrehoznia. A virtuális hálózati átjáró két célra szolgál: exchange IP-útvonalakat a hálózatok és a hálózati forgalom továbbítása között. Ez a cikk ismerteti az átjárótípusokról, az átjáró-termékváltozatok és a Termékváltozat által becsült teljesítmény. Ez a cikk emellett ismerteti az ExpressRoute [FastPath](#fastpath), lehetővé teszi a hálózati forgalmat a Mellőzés a teljesítmény javítása érdekében a virtuális hálózati átjáró a helyszíni hálózatból.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="gateway-types"></a>Átjárótípusok

A virtuális hálózati átjáró létrehozásakor több beállítást adja meg kell. A szükséges beállításokat egyik "-GatewayType", megadja, hogy az átjáró, az expressroute vagy VPN-forgalmat. A két átjáró típusok a következők:

* **VPN** – a titkosított adatforgalmat továbbít a nyilvános interneten a "Vpn" átjárótípust használja. Ez is nevezik VPN-átjáró. A hely–hely, pont–hely és a virtuális hálózatok közötti kapcsolat kapcsolatok mind VPN-átjárót használnak.

* **Az ExpressRoute** – a hálózati forgalom elküldése egy privát kapcsolaton, az "ExpressRoute" átjárótípust használja. Ez egy ExpressRoute-átjárót is nevezik, és az ExpressRoute konfigurálása során használt átjáró típusát.

Mindegyik virtuális hálózat csak egy virtuális hálózati átjáróval rendelkezhet átjárótípusonként. Rendelkezhet például egy virtuális hálózati átjáróval, amely a -GatewayType Vpn típust, és egy másikkal, amelyik a -GatewayType ExpressRoute típust használja.

## <a name="gwsku"></a>Átjáró-termékváltozatok
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Ha azt szeretné, hogy nagyobb teljesítményű átjárók Termékváltozatainak az átjáró frissítése, a legtöbb esetben használhatja a "Átméretezése-AzVirtualNetworkGateway" PowerShell-parancsmagot. Ez a Standard és a nagy teljesítményű termékváltozatok frissítéseket fog működni. Azonban az UltraPerformance Termékváltozattal frissíteni, meg kell hozza létre újra az átjárót. Újra létre kellene hoznia egy átjáró leállást.

### <a name="aggthroughput"></a>Átjárók Termékváltozata alapján becsült teljesítmény
Az alábbi táblázatban az átjárótípusok és a becsült teljesítményét mutatja. Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Alkalmazások teljesítményének attól függ, hogy több tényezővel bővül, például a végpontok közötti késés és a forgalom adatfolyamait, megnyílik az alkalmazás számát. A táblában szereplő számok jelölik a felső határ, amely ideális környezetet a elméletileg érheti el az alkalmazást.
>
>

### <a name="zrgw"></a>Zónaredundáns átjáró-termékváltozatok

ExpressRoute-átjárók az Azure-beli rendelkezésre állási zónák is telepítheti. Ez fizikailag és logikailag a két különböző rendelkezésre állási zónában, a helyszíni hálózati kapcsolatok védelme az Azure-bA a zónaszintű hibák történő.

![Zónaredundáns ExpressRoute-átjáró](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Átjárók zónaredundáns adott új átjáró-termékváltozatok ExpressRoute-átjárót használja.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Az új átjáró-termékváltozatok támogatják az igényeinek leginkább megfelelő egyéb telepítési lehetőségekért is. Az új átjáró SKU-k használatával virtuális hálózati átjáró létrehozásakor, akkor is telepíteni az átjárót a megadott zónában. Ez a zónaszintű átjáró nevezzük. Zónaszintű az átjáró telepítésekor az átjáró összes példánya ugyanabban a rendelkezésre állási zónában üzemelnek.

## <a name="fastpath"></a>FastPath
Az ExpressRoute virtuális hálózati átjáró célja hálózati útvonalakat cserél, és a hálózati forgalom irányítását. A helyszíni hálózat és a virtuális hálózat között az adatok elérési útja a teljesítmény javítása FastPath tervezték. Ha engedélyezve van, FastPath küld hálózati forgalom közvetlenül a virtuális hálózaton lévő virtuális gépek megkerüli az átjáró. 

FastPath érhető el az [ExpressRoute közvetlen](expressroute-erdirect-about.md) csak. Más szóval ez a funkció csak akkor, ha engedélyezheti, [csatlakozzon a virtuális hálózat](expressroute-howto-linkvnet-arm.md) az ExpressRoute-kapcsolatcsoport létrehozása az ExpressRoute közvetlen portra. FastPath továbbra is létrehozni, virtuális hálózat és helyszíni hálózat között útvonalakat cserél virtuális hálózati átjáróra van szükség. A virtuális hálózati átjáró Ultranagy teljesítményű vagy ErGw3AZ kell lennie.

FastPath nem támogatja a következő funkciókat:
* Átjáró alhálózatán található UDR: Ha az átjáró-alhálózatot a virtuális hálózat, a hálózati forgalmat a helyszíni hálózatból továbbra is a virtuális hálózati átjárót kell küldeni egy UDR alkalmaz.
* Virtuális hálózatok közötti társviszony-létesítés: Ha más virtuális hálózatok társviszonyban, amely kapcsolódik a hálózati forgalmat a helyszíni hálózatból a más virtuális hálózatokkal (azaz az úgynevezett "Küllő" virtuális hálózatok) továbbra is kell küldeni a virtuális hálózat ExpressRoute átjáró. A megoldás, hogy a virtuális hálózatok csatlakoztatása ExpressRoute-kapcsolatcsoporthoz közvetlenül.

## <a name="resources"></a>REST API-k és PowerShell-parancsmagok
További technikai erőforrások és a megadott szintaxissal kapcsolatos követelményeket REST API-k és PowerShell-parancsmagok használata a virtuális hálózati átjáró-konfigurációk tekintse meg a következő lapokon:

| **Klasszikus** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>További lépések
Lásd: [ExpressRoute áttekintése](expressroute-introduction.md) elérhető kapcsolati konfigurációkkal kapcsolatos további részletekért.

Lásd: [az ExpressRoute virtuális hálózati átjáró létrehozása](expressroute-howto-add-gateway-resource-manager.md) további információ az ExpressRoute-átjáró létrehozásához.

Lásd: [zónaredundáns virtuális hálózati átjáró létrehozása](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) átjárók zónaredundáns konfigurálásáról további információt.

Lásd: [virtuális hálózati kapcsolat expressroute](expressroute-howto-linkvnet-arm.md) FastPath engedélyezésével kapcsolatos további információt. 
