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
ms.openlocfilehash: fc614626131236361246664a1bcef34f82b54ec5
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848471"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>ExpressRoute virtuális hálózati átjáró és FastPath
Az Azure-beli virtuális hálózat és a helyszíni hálózat ExpressRoute-on keresztüli összekapcsolásához először létre kell hoznia egy virtuális hálózati átjárót. A virtuális hálózati átjáró két célt szolgál: az Exchange IP-útvonalakat a hálózatok között, és átirányítja a hálózati forgalmat. Ez a cikk az átjárók típusát, az átjáró-SKU-ket és az SKU becsült teljesítményét ismerteti. Ez a cikk a ExpressRoute [FastPath](#fastpath)is ismerteti, amely lehetővé teszi a helyszíni hálózatról érkező hálózati forgalmat a teljesítmény javítása érdekében a virtuális hálózati átjáró megkerülése érdekében.

## <a name="gateway-types"></a>Átjárótípusok

A virtuális hálózati átjáró létrehozásakor több beállítást adja meg kell. A szükséges beállításokat egyik "-GatewayType", megadja, hogy az átjáró, az expressroute vagy VPN-forgalmat. A két átjáró típusok a következők:

* **VPN** – a titkosított adatforgalmat továbbít a nyilvános interneten a "Vpn" átjárótípust használja. Ez is nevezik VPN-átjáró. A hely–hely, pont–hely és a virtuális hálózatok közötti kapcsolat kapcsolatok mind VPN-átjárót használnak.

* **Az ExpressRoute** – a hálózati forgalom elküldése egy privát kapcsolaton, az "ExpressRoute" átjárótípust használja. Ez egy ExpressRoute-átjárót is nevezik, és az ExpressRoute konfigurálása során használt átjáró típusát.

Mindegyik virtuális hálózat csak egy virtuális hálózati átjáróval rendelkezhet átjárótípusonként. Rendelkezhet például egy virtuális hálózati átjáróval, amely a -GatewayType Vpn típust, és egy másikkal, amelyik a -GatewayType ExpressRoute típust használja.

## <a name="gwsku"></a>Átjáró-termékváltozatok
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Ha az átjárót egy nagyobb teljesítményű átjáró-SKU-ra szeretné frissíteni, akkor a legtöbb esetben használhatja az "átméretezés – AzVirtualNetworkGateway" PowerShell-parancsmagot. Ez a Standard és a nagy teljesítményű termékváltozatok frissítéseket fog működni. Azonban az UltraPerformance Termékváltozattal frissíteni, meg kell hozza létre újra az átjárót. Újra létre kellene hoznia egy átjáró leállást.

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
A ExpressRoute virtuális hálózati átjáró a hálózati útvonalak cseréjére és a hálózati forgalom irányítására szolgál. A FastPath úgy lett kialakítva, hogy javítsa a helyszíni hálózat és a virtuális hálózat adatelérési útjainak teljesítményét. Ha engedélyezve van, a FastPath közvetlenül a virtuális hálózatban lévő virtuális gépekre küldi a hálózati forgalmat, megkerülve az átjárót. 

A FastPath csak a [ExpressRoute Direct](expressroute-erdirect-about.md) szolgáltatásban érhető el. Más szóval ezt a funkciót csak akkor engedélyezheti, ha [a virtuális hálózatot](expressroute-howto-linkvnet-arm.md) egy ExpressRoute közvetlen porton létrehozott ExpressRoute áramkörhöz kapcsolja. A FastPath továbbra is létre kell hoznia egy virtuális hálózati átjárót a virtuális hálózat és a helyszíni hálózat közötti útvonalak cseréjéhez. A virtuális hálózati átjárónak Ultra Performance vagy ErGw3AZ értékűnek kell lennie.

A FastPath nem támogatja a következő funkciókat:
* UDR az átjáró alhálózatán: Ha UDR alkalmaz a virtuális hálózat átjáró-alhálózatára, a helyszíni hálózatról érkező hálózati forgalmat a rendszer továbbra is elküldi a virtuális hálózati átjárónak.
* VNet-társítás: Ha más virtuális hálózatokkal is rendelkezik, amelyekkel a rendszer csatlakoztatja a helyi hálózatról a másik virtuális hálózatra irányuló hálózati forgalmat (azaz az úgynevezett "küllős" virtuális hálózatok), a rendszer továbbra is a virtuális hálózatra küldi a ExpressRoute. átjáró. A megkerülő megoldás az összes virtuális hálózat közvetlen összekapcsolása a ExpressRoute-áramkörrel.

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

A FastPath engedélyezésével kapcsolatos további információkért tekintse meg a [virtuális hálózat ExpressRoute való csatolását](expressroute-howto-linkvnet-arm.md) ismertető témakört. 
