---
title: Tudnivalók a ExpressRoute virtuális hálózati átjárókkal kapcsolatban – Azure | Microsoft Docs
description: A ExpressRoute virtuális hálózati átjáróinak megismerése. Ez a cikk az átjáró-SKU-ról és-típusokról tartalmaz információkat.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 8860a297332a3572890ceb4c843040f530b8a897
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935519"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>ExpressRoute virtuális hálózati átjáró és FastPath
Az Azure-beli virtuális hálózat és a helyszíni hálózat ExpressRoute-on keresztüli összekapcsolásához először létre kell hoznia egy virtuális hálózati átjárót. A virtuális hálózati átjáró két célt szolgál: az Exchange IP-útvonalakat a hálózatok között, és átirányítja a hálózati forgalmat. Ez a cikk az átjárók típusát, az átjáró-SKU-ket és az SKU becsült teljesítményét ismerteti. Ez a cikk a ExpressRoute [FastPath](#fastpath)is ismerteti, amely lehetővé teszi a helyszíni hálózatról érkező hálózati forgalmat a teljesítmény javítása érdekében a virtuális hálózati átjáró megkerülése érdekében.

## <a name="gateway-types"></a>Átjárótípusok

Virtuális hálózati átjáró létrehozásakor több beállítást kell megadnia. A kötelező beállítások egyike, a "-GatewayType", megadja, hogy az átjáró a ExpressRoute vagy a VPN-forgalomhoz van-e használva. A két átjáró típusa:

* **VPN** – titkosított forgalom küldése a nyilvános interneten keresztül, a "VPN" átjáró-típust használja. Ezt VPN-átjárónak is nevezzük. A hely–hely, pont–hely és a virtuális hálózatok közötti kapcsolat kapcsolatok mind VPN-átjárót használnak.

* **ExpressRoute** – a hálózati forgalom privát kapcsolatban való küldéséhez használja a "ExpressRoute" átjáró típusát. Ezt a ExpressRoute-átjárónak is nevezik, és a ExpressRoute konfigurálásakor használt átjáró típusa.

Mindegyik virtuális hálózat csak egy virtuális hálózati átjáróval rendelkezhet átjárótípusonként. Rendelkezhet például egy virtuális hálózati átjáróval, amely a -GatewayType Vpn típust, és egy másikkal, amelyik a -GatewayType ExpressRoute típust használja.

## <a name="gwsku"></a>Átjáró-termékváltozatok
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Ha az átjárót egy nagyobb teljesítményű átjáró-SKU-ra szeretné frissíteni, akkor a legtöbb esetben használhatja az "átméretezés – AzVirtualNetworkGateway" PowerShell-parancsmagot. Ez a standard és a HighPerformance SKU-ra való frissítés esetén fog működni. A UltraPerformance SKU-ra való frissítéshez azonban újra létre kell hoznia az átjárót. Az átjárók újbóli létrehozása leállást okoz.

### <a name="aggthroughput"></a>Az átjárói SKU becsült teljesítménye
A következő táblázat az átjárók típusát és a becsült teljesítményt mutatja be. Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Az alkalmazás teljesítménye több tényezőtől függ, például a végpontok közötti késéstől, valamint az alkalmazás által megnyitott adatforgalom számától. A táblázatban szereplő számok azt a felső korlátot jelentik, amelyet az alkalmazás elméletileg érhet el ideális környezetben.
>
>

## <a name="gwsub"></a>Átjáró-alhálózat

ExpressRoute-átjáró létrehozása előtt létre kell hoznia egy átjáró-alhálózatot. Az átjáró-alhálózat tartalmazza azokat az IP-címeket, amelyeket a Virtual Network Gateway-beli virtuális gépek és szolgáltatások használnak. A virtuális hálózati átjáró létrehozásakor az átjáróként működő virtuális gépek üzembe helyezése az átjáró alhálózatán történik, és a szükséges ExpressRoute-átjáró beállításaival van konfigurálva. Soha ne helyezzen üzembe semmilyen mást (például további virtuális gépeket) az átjáró-alhálózathoz. A megfelelő működéshez az átjáró alhálózatának "GatewaySubnet" nevűnek kell lennie. A "GatewaySubnet" átjáró alhálózatának elnevezése lehetővé teszi, hogy az Azure tudja, hogy ez az alhálózat a virtuális hálózati átjárók és a szolgáltatások üzembe helyezéséhez.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. Az átjáró-alhálózat IP-címei az átjáró virtuális gépei és az átjáró szolgáltatások számára vannak lefoglalva. Egyes konfigurációknak a többinél nagyobb számú IP-címre van szükségük. 

Az átjáró-alhálózat méretének tervezésekor tekintse meg a létrehozni kívánt konfiguráció dokumentációját. A ExpressRoute/VPN Gateway egyazon konfiguráció például nagyobb átjáró-alhálózatot igényel, mint a legtöbb más konfiguráció. Emellett érdemes lehet gondoskodni arról, hogy az átjáró-alhálózat elegendő IP-címet tartalmazzon a lehetséges jövőbeli további konfigurációkhoz. Míg az átjáró-alhálózatot kisebb as/29-ként is létrehozhatja, javasoljuk, hogy hozzon létre egy/27-ös vagy nagyobb átjáró-alhálózatot (/27,/26 stb.), ha a rendelkezésre álló címtartomány erre van szüksége. Ez a legtöbb konfigurációt kielégíti.

A következő Resource Manager PowerShell-példa egy GatewaySubnet nevű átjáró-alhálózatot mutat be. Láthatja, hogy a CIDR jelölése egy/27, amely elegendő IP-címet biztosít a jelenleg létező konfigurációkhoz.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zrgw"></a>Zóna – redundáns átjárók SKU-i

A ExpressRoute-átjárókat Azure Availability Zones is üzembe helyezheti. Ez fizikailag és logikailag elkülöníti őket különböző Availability Zones, így biztosítva a helyszíni hálózati kapcsolatot az Azure-hoz a zóna szintű hibák miatt.

![Zóna – redundáns ExpressRoute-átjáró](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zóna – a redundáns átjárók a ExpressRoute-átjáróhoz megadott új Gateway SKU-ket használnak.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Az új átjárói SKU-ket más üzembe helyezési lehetőségek is támogatják az igényeinek leginkább megfelelő módon. Ha az új átjárók használatával hoz létre virtuális hálózati átjárót, lehetősége van arra is, hogy az átjárót egy adott zónába telepítse. Ezt egy zóna-átjárónak nevezzük. Ha egy zóna-átjárót helyez üzembe, az átjáró összes példánya ugyanabban a rendelkezésre állási zónában lesz telepítve.

## <a name="fastpath"></a>FastPath
A ExpressRoute virtuális hálózati átjáró a hálózati útvonalak cseréjére és a hálózati forgalom irányítására szolgál. A FastPath úgy lett kialakítva, hogy javítsa a helyszíni hálózat és a virtuális hálózat adatelérési útjainak teljesítményét. Ha engedélyezve van, a FastPath közvetlenül a virtuális hálózatban lévő virtuális gépekre küldi a hálózati forgalmat, megkerülve az átjárót. 

A FastPath az összes ExpressRoute-áramkörön elérhető. Továbbra is szükség van egy virtuális hálózati átjáró létrehozására a virtuális hálózat és a helyszíni hálózat közötti útvonalak cseréjéhez. A virtuális hálózati átjárónak Ultra Performance vagy ErGw3AZ értékűnek kell lennie.

A FastPath nem támogatja a következő funkciókat:
* UDR az átjáró alhálózatán: Ha UDR alkalmaz a virtuális hálózat átjáró-alhálózatára, a helyszíni hálózatról érkező hálózati forgalmat a rendszer továbbra is elküldi a virtuális hálózati átjárónak.
* VNet-társítás: Ha más virtuális hálózatokkal is rendelkezik, amelyekkel a rendszer csatlakoztatja a helyi hálózatról a másik virtuális hálózatra irányuló hálózati forgalmat (azaz az úgynevezett "küllős" virtuális hálózatok), a rendszer továbbra is a virtuális hálózatra küldi a ExpressRoute. átjáró. A megkerülő megoldás az összes virtuális hálózat közvetlen összekapcsolása a ExpressRoute-áramkörrel.
* Alapszintű Load Balancer: Ha alapszintű belső terheléselosztó üzembe helyezését végzi a virtuális hálózatban, vagy a virtuális hálózatban üzembe helyezett Azure Pásti szolgáltatás alapszintű belső terheléselosztó, a helyszíni hálózatról a hálózati forgalom a (z) rendszerben üzemeltetett virtuális IP-címekre A rendszer az alapszintű Load balancert a virtuális hálózati átjárónak küldi el. A megoldás az alapszintű Load Balancer [standard Load balancerre](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview)való frissítése.
* Privát hivatkozás: Ha a virtuális hálózatban lévő [privát végponthoz](../private-link/private-link-overview.md) csatlakozik a helyszíni hálózatról, a kapcsolat a virtuális hálózati átjárón keresztül fog haladni.
 
## <a name="resources"></a>REST API-k és PowerShell-parancsmagok
A REST API-k és a virtuális hálózati átjárók konfigurációinak PowerShell-parancsmagok használata esetén a következő lapokon talál további technikai forrásokat és konkrét szintaxisi követelményeket:

| **Klasszikus** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Következő lépések
A rendelkezésre álló [ExpressRoute](expressroute-introduction.md) kapcsolatos további információkért tekintse meg a további tudnivalókat ismertető cikket.

A ExpressRoute-átjárók létrehozásával kapcsolatos további információkért tekintse meg [a virtuális hálózati átjáró létrehozása a ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) című témakört.

További információ a zóna – redundáns átjárók konfigurálásáról: [create a Zone-redundáns Virtual Network Gateway](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) .

A FastPath engedélyezésével kapcsolatos további információkért tekintse meg a [virtuális hálózat ExpressRoute való csatolását](expressroute-howto-linkvnet-arm.md) ismertető témakört. 
