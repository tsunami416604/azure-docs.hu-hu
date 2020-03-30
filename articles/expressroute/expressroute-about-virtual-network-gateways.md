---
title: Az ExpressRoute virtuális hálózati átjárói – Azure| Microsoft dokumentumok
description: További információ az ExpressRoute virtuális hálózati átjáróiról. Ez a cikk az átjáró termékfelhasználóival és típusokkal kapcsolatos információkat tartalmaz.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281417"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Az ExpressRoute virtuális hálózati átjárói

Az Azure virtuális hálózat és a helyszíni hálózat ExpressRoute-on keresztül történő csatlakoztatásához először létre kell hoznia egy virtuális hálózati átjárót. A virtuális hálózati átjáró két célt szolgál: IP-útvonalak cseréje a hálózatok között és útvonal hálózati forgalom. Ez a cikk ismerteti az átjárótípusokat, az átjáró termékváltozatokat és a termékváltozat becsült teljesítményét. Ez a cikk az ExpressRoute [FastPath](#fastpath)szolgáltatást is ismerteti, amely lehetővé teszi, hogy a helyszíni hálózat hálózati forgalma megkerülje a virtuális hálózati átjárót a teljesítmény javítása érdekében.

## <a name="gateway-types"></a>Átjárótípusok

Virtuális hálózati átjáró létrehozásakor több beállítást kell megadnia. A szükséges beállítások egyike, a "-GatewayType" azt határozza meg, hogy az átjáróexpressroute-hoz vagy VPN-forgalomhoz van-e használva. A két átjárótípus a következő:

* **Vpn** - A titkosított forgalom küldéséhez a nyilvános interneten keresztül a "Vpn" átjárótípust használja. Ezt VPN-átjárónak is nevezik. A hely–hely, pont–hely és a virtuális hálózatok közötti kapcsolat kapcsolatok mind VPN-átjárót használnak.

* **ExpressRoute** – A hálózati forgalom privát kapcsolaton történő küldéséhez használja az "ExpressRoute" átjárótípust. Ezt ExpressRoute-átjárónak is nevezik, és az ExpressRoute konfigurálásakor használt átjáró típus.

Mindegyik virtuális hálózat csak egy virtuális hálózati átjáróval rendelkezhet átjárótípusonként. Rendelkezhet például egy virtuális hálózati átjáróval, amely a -GatewayType Vpn típust, és egy másikkal, amelyik a -GatewayType ExpressRoute típust használja.

## <a name="gateway-skus"></a><a name="gwsku"></a>Átjáró-termékváltozatok
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Ha az átjárót egy hatékonyabb átjáró termékváltozatra szeretné frissíteni, a legtöbb esetben használhatja a "Resize-AzVirtualNetworkGateway" PowerShell-parancsmagját. Ez a standard és nagy teljesítményű skus-okra való frissítés esetén is működni fog. Az UltraPerformance termékváltozatra való frissítéshez azonban újra létre kell hoznia az átjárót. Az átjáró újbóli létrehozása állásidőt okoz.

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>Becsült teljesítmények az átjáró termékváltozatának szerint
Az alábbi táblázat az átjárótípusokat és a becsült teljesítményt mutatja be. Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Az alkalmazás teljesítménye több tényezőtől függ, például a végpontok késése, és az alkalmazás által megnyitandó forgalom száma. A táblázatban szereplő számok azt a felső korlátot jelölik, amelyet az alkalmazás elméletileg elérhet egy ideális környezetben.
>
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>Átjáró alhálózata

ExpressRoute-átjáró létrehozása előtt létre kell hoznia egy átjáró alhálózatot. Az átjáró alhálózata tartalmazza a virtuális hálózati átjáró virtuális gépei és szolgáltatásai által használt IP-címeket. A virtuális hálózati átjáró létrehozásakor az átjáró virtuális gépei az átjáró alhálózatára kerülnek, és a szükséges ExpressRoute-átjáróbeállításokkal vannak konfigurálva. Soha ne telepítsen semmi mást (például további virtuális gépeket) az átjáró alhálózatára. Az átjáró alhálózatának "GatewaySubnet" nevet kell adnia a megfelelő működéshez. Az átjáró alhálózatának "GatewaySubnet" elnevezése lehetővé teszi az Azure számára, hogy ez az alhálózat telepítse a virtuális hálózati átjáró virtuális gépeit és szolgáltatásait.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. Az átjáró alhálózatában lévő IP-címek az átjáró virtuális gépei és az átjárószolgáltatások számára vannak lefoglalva. Egyes konfigurációknak a többinél nagyobb számú IP-címre van szükségük. 

Az átjáró alhálózatának méretének tervezésekor tekintse meg a létrehozni kívánt konfiguráció dokumentációját. Például az ExpressRoute/VPN-átjáró egymás mellett létezik konfiguráció hoz egy nagyobb átjáró alhálózat, mint a legtöbb más konfigurációk. Emellett érdemes lehet győződjön meg arról, hogy az átjáró alhálózata elegendő IP-címet tartalmaz a lehetséges jövőbeli további konfigurációk befogadásához. Bár létrehozhat egy átjáró alhálózatot, amely /29-es méretű, javasoljuk, hogy hozzon létre egy /27 vagy nagyobb (/27, /26 stb.) átjáró alhálózatot, ha rendelkezik a rendelkezésre álló címterülettel. Ez a legtöbb konfigurációt befogadja.

A következő Erőforrás-kezelő PowerShell-példában egy GatewaySubnet nevű átjáró-alhálózat látható. Láthatja, hogy a CIDR jelölés egy /27-et ad meg, amely elegendő IP-címet tesz lehetővé a legtöbb jelenleg létező konfigurációhoz.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>Zónaredundáns átjáró-ska-k

ExpressRoute-átjárók at is telepíthet az Azure rendelkezésre állási zónáiban. Ez fizikailag és logikailag különböző rendelkezésre állási zónákra bontja őket, megvédve a helyszíni hálózati kapcsolatot az Azure-ral a zónaszintű hibáktól.

![Zónaredundáns ExpressRoute-átjáró](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

A zónaredundáns átjárók adott új átjáró-sk-eket használnak az ExpressRoute-átjáróhoz.

* Ergw1AZ
* Ergw2AZ
* Ergw3AZ

Az új átjáró-ska-k az igényeinek leginkább megfelelő egyéb üzembe helyezési lehetőségeket is támogatnak. Amikor virtuális hálózati átjárót hoz létre az új átjáró-változatok használatával, lehetősége van az átjáró t egy adott zónában való üzembe helyezésére is. Ezt zónaszintű átjárónak nevezzük. Zónaszintű átjáró telepítésekor az átjáró összes példánya ugyanabban a rendelkezésre állási zónában van telepítve.

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

Az ExpressRoute virtuális hálózati átjárója hálózati útvonalak cseréjére és hálózati forgalom irányítására szolgál. A FastPath célja, hogy javítsa az adatelérési út teljesítményét a helyszíni hálózat és a virtuális hálózat között. Ha engedélyezve van, a FastPath közvetlenül a virtuális hálózat virtuális gépeinek küldi a hálózati forgalmat, megkerülve az átjárót.

A FastPath-ról további információt, többek között a korlátozásokról és követelményekről a [FastPath – például](about-fastpath.md)a

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST API-k és PowerShell-parancsmagok
További technikai erőforrásokat és speciális szintaxiskövetelményeket a REST API-k és a PowerShell-parancsmagok virtuális hálózati átjárókonfigurációkhoz való használatakor az alábbi lapokon.

| **Klasszikus** | **Resource Manager** |
| --- | --- |
| [Powershell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[Powershell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>További lépések

Az elérhető kapcsolatkonfigurációkról az [ExpressRoute áttekintése](expressroute-introduction.md)című témakörben olvashat bővebben.

Az ExpressRoute-átjárók létrehozásáról további információt az [ExpressRoute virtuális hálózati átjárólétrehozása című témakörben talál.](expressroute-howto-add-gateway-resource-manager.md)

A zónaredundáns átjárók konfigurálásáról további információt a [Zónaredundáns virtuális hálózati átjáró létrehozása című témakörben](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md)talál.

A FastPath-ról további információt a [FastPath – betekintés !](about-fastpath.md)