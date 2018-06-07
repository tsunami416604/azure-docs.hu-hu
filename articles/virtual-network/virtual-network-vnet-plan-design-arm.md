---
title: Tervezze meg az Azure virtuális hálózatok |} Microsoft Docs
description: Ismerje meg a elkülönítési, a kapcsolat és a helyre vonatkozó követelmények alapján virtuális hálózatok tervezését.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2018
ms.author: jdial
ms.openlocfilehash: fd290420c2c755e07f6949750e3a88bcb64682f3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656907"
---
# <a name="plan-virtual-networks"></a>Virtuális hálózatok tervezése

Kísérletezhet virtuális hálózat létrehozása elég egyszerűen, de, a szervezet a termelési igényeinek támogatására idővel több virtuális hálózat fog üzembe helyezni. Néhány tervezéssel fogja tudni telepíteni a virtuális hálózatok, és csatlakozzon a hatékonyabb szükséges erőforrások. A cikkben szereplő információkat akkor hasznos, ha már jártas a virtuális hálózatok, és rendelkezik némi tapasztalattal hozzájuk. Ha nem ismeri a virtuális hálózatok, javasolt elolvasni [virtuális hálózat áttekintése](virtual-networks-overview.md).

## <a name="naming"></a>Elnevezés

Az összes Azure-erőforrások nevet adni. A név, amelyek minden erőforrástípus változhatnak hatókörön belül egyedinek kell lennie. Például egy virtuális hálózat nevét belül egyedinek kell lennie egy [erőforráscsoport](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), de belül megkettőzhetők egy [előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) vagy Azure [régió](https://azure.microsoft.com/regions/#services). Egy elnevezési konvenciója, használhat következetesen erőforrások elnevezésekor meghatározása akkor hasznos, ha az adott idő alatt több hálózati erőforrások kezelése. A javaslatok, lásd: [elnevezési konvenciói](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#networking).

## <a name="regions"></a>Régiók

Egy Azure-régió, és az előfizetés az összes Azure-erőforrások jönnek létre. Egy erőforrás csak egy virtuális hálózat már szerepel a azonos régióban, és az előfizetés erőforrásként hozhatók létre. Ugyanakkor különböző előfizetésekhez és régiókban létező virtuális hálózatok csatlakoztatása. További információkért lásd: [kapcsolat](#connectivity). Mely régió(k) esetében az erőforrások telepítése meghatározásakor vegye figyelembe az erőforrások fogyasztói fizikailag hol:

- Erőforrások fogyasztói általában érdemes a legkisebb hálózati késést erőforrásaik. Egy megadott helyre és az Azure-régiók közötti relatív késések megállapításához lásd: [relatív késések megtekintése](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Rezidens, közös joghatóság alá, megfelelőségi vagy rugalmassági követelményeinek van? Ha igen, a régiót, amelyben a követelményeinek igazítja kiválasztása fontos. További információkért lásd: [Azure földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/).
- Szükség van-e rugalmassági Azure rendelkezésre állási zónák között az erőforrások telepítése Azure régión belül? Erőforrások, például virtuális gépek (VM) telepíthet másik rendelkezésre állási zónákhoz belül az azonos virtuális hálózatban. Nem minden Azure-régiók azonban támogatja a rendelkezésre állási zónák. Rendelkezésre állási zónák és az őket támogató régiók kapcsolatos további információkért lásd: [rendelkezésre állási zónák](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Előfizetések

Legfeljebb annyi virtuális hálózatok belül minden előfizetés szükség szerint telepítheti a [korlát](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Egyes vállalatoknak van például különböző előfizetések különböző részlegek számára. További információért és megfontolásokért előfizetések körül, lásd: [előfizetés irányítás](/azure/architecture/cloud-adoption-guide/subscription-governance#define-your-hierarchy).

## <a name="segmentation"></a>Szegmentálás

Több virtuális hálózat előfizetésenként és régiónként hozhat létre. A virtuális hálózaton belül több alhálózatra is létrehozhat. Az az alábbi szempontokat segítségével meghatározhatja, hány virtuális hálózatok és alhálózatok van szüksége:

### <a name="virtual-networks"></a>Virtuális hálózatok

A virtuális hálózat az Azure nyilvános hálózat virtuális, elkülönített része. Minden egyes virtuális hálózati van rendelve az előfizetéshez. Megfontolandó szempontok, ha eldöntötte, hogy a előfizetés egy virtuális hálózatot, vagy több virtuális hálózat létrehozásához:

- A forgalom elkülönítése külön virtuális hálózatba léteznek bármely szervezeti biztonsági követelmények? Ha szeretné-e csatlakozni a virtuális hálózatok. Ha virtuális hálózatok, a hálózati virtuális készülék, például egy tűzfal, a virtuális hálózatok közötti forgalom üzenetáramlásának szabályozására is létrehozható. További információkért lásd: [biztonsági](#security) és [kapcsolat](#connectivity).
- Virtuális hálózatok elkülönítésére különálló bármely szervezeti követelmények léteznek [előfizetések](#subscriptions) vagy [régiók](#regions)?
- A [hálózati illesztő](virtual-network-network-interface.md) lehetővé teszi, hogy a virtuális gépek más erőforrások folytatott kommunikációhoz. Mindegyik hálózati interfész egy vagy több privát IP-cím van hozzárendelve. Hány hálózati adapterek és [magánhálózati IP-címek](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) szükség van-e a virtuális hálózatot? Nincsenek [korlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) hálózati felületek és privát IP-címek, amelyek a virtuális hálózaton belül lehet számával.
- Szeretné a virtuális hálózat egy másik virtuális hálózathoz vagy a helyszíni hálózatában? Néhány virtuális hálózatok csatlakozni egymáshoz vagy a helyszíni hálózatokban, de nem mások dönthet. További információkért lásd: [kapcsolat](#connectivity). Minden más virtuális hálózathoz csatlakozó virtuális hálózati vagy a helyszíni hálózat, rendelkeznie kell egy egyedi címtartományt. Minden egyes virtuális hálózati egy vagy több nyilvános vagy privát címtartományai a címterület rendelve van. Címtartomány classless internetes tartomány útválasztási (CIDR) formátumban, például 10.0.0.0/16 van megadva. További információ [-címtartományok](manage-virtual-network.md#add-or-remove-an-address-range) virtuális hálózatok.
- Rendelkezik a különböző virtuális hálózatokon vonatkozó erőforrások szervezeti felügyeleti követelmények? Ha igen, akkor előfordulhat, hogy külön erőforrások egyszerűbbé teheti a különálló virtuális hálózati [engedély-hozzárendelést](#permissions) az alkalmazottaknak a szervezet vagy, hogy különböző [házirendek](#policies) különböző virtuális hálózatok.
- Amikor bizonyos Azure szolgáltatás erőforrások virtuális hálózatba telepít, azok saját virtuális hálózat létrehozása. Annak megállapításához, hogy az Azure-szolgáltatások a saját virtuális hálózatot hoz létre, információ az egyes [is telepíthető egy virtuális hálózatot az Azure-szolgáltatások](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Alhálózatok

Egy virtuális hálózatot is lehet szegmentált be egy vagy több alhálózatból akár a [korlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Megfontolandó szempontok, ha eldöntötte, hogy a előfizetés egy alhálózatot, vagy több virtuális hálózat létrehozásához:

- Az egyes alhálózatokon rendelkeznie kell egy egyedi-címtartományt, a virtuális hálózat a címtér CIDR formátumban megadott. A címtartomány a virtuális hálózatban lévő más alhálózatok nem lehet átfedésben.
- Egyes Azure-szolgáltatások erőforrásokat üzembe helyezés virtuális hálózatot szeretne, ha azok előfordulhat, hogy igényel, vagy hozzon létre, a saját alhálózatot, tehát nincs elegendő szabad kell lennie szóköz, hogy ehhez. Annak megállapításához, hogy az Azure-szolgáltatások a saját IP-alhálózatot hoz-e, tekintse meg az adatok [is telepíthető egy virtuális hálózatot az Azure-szolgáltatások](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Például ha egy virtuális hálózatot az Azure VPN Gateway használatával a helyszíni hálózathoz csatlakoztat, a virtuális hálózati átjáró dedikált alhálózat kell rendelkeznie. További információ [átjáró alhálózatok](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Az Azure útvonalak közötti hálózati forgalom egy virtuális hálózatot, alapértelmezés szerint az összes alhálózatot. Ha szeretné felülbírálni az Azure alapértelmezett útválasztási megelőzése Azure útválasztás alhálózatok között, vagy egy hálózati virtuális készüléken keresztül alhálózatok közötti forgalom irányítására például. Ha szükséges, hogy az adott virtuális hálózati adatfolyam keresztül a hálózati virtuális készülék (NVA) az erőforrások közötti forgalom, telepítheti az erőforrásokat a más alhálózatokon. További információ: [biztonsági](#security).
- Korlátozhatja például egy Azure storage-fiók vagy az Azure SQL-adatbázis, a virtuális hálózati szolgáltatásvégpont alhálózatok Azure-erőforrások eléréséhez. További megtagadhatja a hozzáférést az erőforrásokhoz az internetről. Előfordulhat, hogy a hozzon létre több alhálózat működik, és az egyes alhálózatok, de nem mások szolgáltatásvégpont engedélyezése. További információ [szolgáltatás végpontjait](virtual-network-service-endpoints-overview.md), és az Azure-erőforrások engedélyezheti őket.
- Nulla vagy egy hálózati biztonsági csoportot minden egyes alhálózatnak egy virtuális hálózathoz társíthatja. Társítsa ugyanazt vagy egy másik, a hálózati biztonsági csoportra, és az egyes alhálózatokon. Minden egyes hálózati biztonsági csoport tartalmazza a szabályok, amelyek források és célok érkező vagy oda irányuló adatforgalom engedélyezéséhez vagy letiltásához. További információ [hálózati biztonsági csoportok](#traffic-filtering).

## <a name="security"></a>Biztonság

Szűrheti a hálózati forgalmat hálózati biztonsági csoportok és a hálózati virtuális készülékek használata a virtuális hálózatán lévő erőforrásokat. Szabályozhatja, hogyan Azure irányítja a forgalmat alhálózatok. Dolgozó a szervezet képes virtuális hálózatok erőforrásokat is korlátozható.

### <a name="traffic-filtering"></a>Forgalomszűrés

- A hálózati biztonsági csoport, a hálózati forgalom vagy mindkettő szűrő NVA használatával egy virtuális hálózatán lévő erőforrásokat közötti hálózati forgalom jeleníthetők meg. NVA, például egy tűzfal telepíteni a hálózati forgalom szűrésére, tekintse meg a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). NVA használatakor is létrehozhat egyéni útvonalakat a az NVA alhálózatok-forgalom irányításához. További információ [a forgalom útválasztásához](#traffic-routing).
- Hálózati biztonsági csoport, amely engedélyezi vagy megtagadja a bejövő és kimenő forgalmat a források több alapértelmezett biztonsági szabályokat tartalmazza. Lehet, hogy a hálózati biztonsági csoport társítva a hálózati adaptert, az alhálózatot a hálózati adaptert, vagy mindkettőt. Egyszerűbbé teheti a biztonsági szabályok kezelését, javasoljuk, hogy társít egy hálózati biztonsági csoport az egyes alhálózatokhoz ahelyett, hogy az egyes hálózati adapterek a alhálózatban, amikor csak lehetséges.
- Egy alhálózaton belül különböző virtuális gépek különböző biztonsági szabály van szükség, ha a hálózati illesztő a virtuális gép egy vagy több alkalmazás biztonsági csoportokra lehet társítani. A szabály az alkalmazás biztonsági csoport meghatározhatja a forrás és cél tartozhatnak. Ez a szabály majd csak érvényes a hálózati adapterek, amelyek az alkalmazás biztonsági csoport tagjai. További információ [hálózati biztonsági csoportok](security-overview.md) és [biztonsági csoportok](security-overview.md#application-security-groups).
- Azure néhány alapértelmezett biztonsági szabályokat az egyes hálózati biztonsági csoportok hoz létre. Egy alapértelmezett szabály lehetővé teszi, hogy a virtuális hálózat összes erőforrása közötti összes forgalmat. Bírálja felül ezt a viselkedést, használja a hálózati biztonsági csoportok, egyéni irányíthatja a forgalmat a NVA, vagy mindkét történő továbbítást. Javasoljuk, hogy Ismerkedjen meg az összes Azure [alapértelmezett biztonsági szabályok](security-overview.md#default-security-rules) és megérteni, hogyan erőforrás hálózati biztonsági csoport szabályok érvényesek.

Megtekintheti a minta tervek közötti Azure és az internet használatával DMZ végrehajtásához egy [NVA](/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) vagy [hálózati biztonsági csoportok](virtual-networks-dmz-nsg.md).

### <a name="traffic-routing"></a>a forgalom útválasztásához

Azure több alapértelmezett útvonalakat a kimenő forgalom alhálózatot hoz létre. Ha szeretné felülbírálni az Azure alapértelmezett útválasztási hoz létre egy útválasztási táblázatot, és alhálózathoz való társítás. Gyakori okai a figyelőkével Azure alapértelmezett útválasztási vannak:
- Mivel NVA áramlása érdekében alhálózatok közötti forgalmat. További információt hogyan [NVA forgalmát kényszerítheti az útvonaltáblák konfigurálása](tutorial-create-route-table-portal.md).
- Mivel az összes internetes-forgalmat az NVA vagy a helyszínen, az Azure VPN-átjárón keresztül kényszeríteni kívánja. Internetes forgalmat a helyszínen a vizsgálathoz újraindítás és a naplózást gyakran hivatkoznak, a kényszerített bújtatást. További információk konfigurálása [kényszerített bújtatás](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Ha egyéni útválasztást van szüksége, javasoljuk, hogy Ön feltérképezése [az Azure útválasztási](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Kapcsolatok

Kapcsolható a virtuális hálózati más virtuális hálózatok használatával a virtuális hálózati társviszony-létesítést, vagy a helyszíni hálózat az Azure VPN gateway használatával.

### <a name="peering"></a>Társviszony-létesítés

Használata esetén [virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md), a virtuális hálózatok lehetnek azonos, vagy másik, a támogatott Azure-régiók. A virtuális hálózatok ugyanazon vagy másik Azure-előfizetések, lehet, amíg az azonos Azure Active Directory-bérlőhöz hozzárendelt mindkét előfizetéshez. Mielőtt létrehozna egy társviszony-létesítést, javasoljuk, hogy Ön Ismerkedjen meg az összes társviszony [követelményeket és korlátokat](virtual-network-manage-peering.md#requirements-and-constraints). Erőforrások között lévő virtuális hálózatok sávszélesség társviszonyban ugyanabban a régióban van ugyanaz, mintha az erőforrásokat ugyanabban a virtuális hálózatban.

### <a name="vpn-gateway"></a>VPN-átjáró

Használhatja az Azure [VPN-átjáró](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) egy virtuális hálózathoz csatlakozni a helyszíni hálózat használja egy [telephelyek közötti VPN](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json), vagy egy dedikált kapcsolat használatával az Azure-ral [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Kombinálhatja a társviszony-létesítést és a VPN-átjáró létrehozásához [küllős hálózatok](/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), ahol küllős virtuális hálózatok központi virtuális hálózathoz csatlakozzon, és a központ például csatlakozik egy helyszíni hálózat.

### <a name="name-resolution"></a>Névfeloldás

Egy virtuális hálózatán lévő erőforrásokat nem oldható fel az erőforrások az Azure peered virtuális hálózat nevének [beépített DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md). Egy peered virtuális hálózati nevek feloldása [a saját DNS-kiszolgáló telepítése](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server), vagy használja az Azure DNS [titkos tartományok](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Névfeloldás virtuális hálózatán lévő erőforrásokat és a helyszíni hálózat között is meg kell a saját DNS-kiszolgáló központi telepítése.

## <a name="permissions"></a>Engedélyek

Használja az Azure [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) erőforrásokhoz. Jogosultságokat hozzá kell rendelni egy [hatókör](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) a következő hierarchia: előfizetés, a felügyeleti csoport, a erőforráscsoport és az egyéni erőforrás. A hierarchia kapcsolatos további információkért lásd: [az erőforrások rendszerezése](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Az Azure virtuális hálózatok és a kapcsolódó funkciók, például a társviszony-létesítést, a hálózati biztonsági csoportok, a végpontok és a útvonaltáblák dolgozni, hozzárendelheti a szervezet tagjaira a beépített [tulajdonos](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [Közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor), vagy [hálózat közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörök, és ezután rendelje hozzá a szerepkört a megfelelő hatókörben. Ha szeretne hozzárendelni a virtuális hálózati funkciók egy részéhez meghatározott engedélyek, hozzon létre egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , és rendelje hozzá a szükséges engedélyeket [virtuális hálózatok](manage-virtual-network.md#permissions), [ alhálózatok és a végpontok](virtual-network-manage-subnet.md#permissions), [hálózati illesztőt](virtual-network-network-interface.md#permissions), [társviszony-létesítés](virtual-network-manage-peering.md#permissions), [hálózati és az alkalmazás biztonsági csoportok](manage-network-security-group.md#permissions), vagy [útvonaltáblát](manage-route-table.md#permissions) a szerepkörhöz.

## <a name="policy"></a>Szabályzat

Az Azure házirend lehetővé teszi létrehozása, hozzárendelése és házirend-definíciók kezelése. Házirend-definíciók érvényesíti azokat a különböző szabályokat keresztül az erőforrásokat, így az erőforrások megfeleljenek a vállalati szabványoknak és a szolgáltatásszint-szerződések maradnak. Azure házirend futtatja az erőforrások, az erőforrások, amely nem kompatibilis a van a házirend-definíciók keresése értékelését. Például adja meg, és egy létrehozását, amely lehetővé teszi a csak egy adott erőforráscsoportban vagy a régió virtuális hálózatok létrehozása. Egy másik házirend megkövetelheti, hogy minden alhálózat rendelkezik-e a hálózati biztonsági csoport társítva. A házirendek létrehozása és frissítése erőforrások majd kiértékelése.

Házirendek vonatkoznak a következő hierarchia: előfizetés, a felügyeleti csoport és az erőforráscsoportot. További információ [Azure házirend](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , vagy telepítse az egyes virtuális hálózati [Házirendsablon](policy-samples.md) minták.

## <a name="next-steps"></a>További lépések

További tudnivalók az összes feladatok, beállítások és a beállítások megadása a [virtuális hálózati](manage-virtual-network.md), [alhálózat és a szolgáltatási végpont](virtual-network-manage-subnet.md), [hálózati illesztő](virtual-network-network-interface.md), [társviszony-létesítési](virtual-network-manage-peering.md), [alkalmazás és a hálózati biztonsági csoport](manage-network-security-group.md), vagy [útvonaltábla](manage-route-table.md).
