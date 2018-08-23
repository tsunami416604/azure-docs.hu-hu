---
title: Az Azure virtuális hálózatok tervezése |} A Microsoft Docs
description: Ismerje meg, hogyan tervezi a virtuális hálózatokban az elkülönítés, kapcsolat és helyre vonatkozó követelmények alapján.
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
ms.openlocfilehash: 1ec60300d179edf69e32dc07653b6c69c7cc8e52
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "42054480"
---
# <a name="plan-virtual-networks"></a>Virtuális hálózatok tervezése

Kísérletezhet a virtuális hálózat létrehozásának elég egyszerű, de valószínűleg több virtuális hálózat központilag telepíti az éles környezetben a szervezet igényeinek támogatásához idővel. Az egyes tervezési lesz a virtuális hálózatok üzembe helyezése, és csatlakozzon a hatékonyabban szükséges erőforrásokat. Ebben a cikkben szereplő információkat akkor hasznos, ha már ismeri a virtuális hálózatok, és rendelkezik tapasztalattal a velük végzett munkát. Ha nem ismeri a virtuális hálózatok, javasoljuk, hogy olvasási [virtuális hálózatok áttekintése](virtual-networks-overview.md).

## <a name="naming"></a>Elnevezés

Az összes Azure-erőforrások nevezni. A név egy hatókör, amely különbözhet az egyes erőforrástípusok egyedinek kell lennie. Például egy virtuális hálózat neve egyedinek kell lennie egy [erőforráscsoport](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), de belül is kettőzhető egy [előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) vagy az Azure [régió](https://azure.microsoft.com/regions/#services). Egy elnevezési konvenciója, amellyel folyamatosan erőforrások elnevezésekor meghatározása akkor hasznos, ha több hálózati erőforrások felügyelete a idővel. További javaslatok: [elnevezési konvenciók](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#networking).

## <a name="regions"></a>Régiók

Egy Azure-régióban és az előfizetés az összes Azure-erőforrások jönnek létre. Egy erőforrás csak hozható létre egy virtuális hálózatban, hogy az azonos régióban és előfizetésben, az erőforrás megtalálható. Azonban eltérő előfizetésben és régióban lévő virtuális hálózatok összekapcsolása. További információkért lásd: [kapcsolat](#connectivity). Erőforrásokat helyezhet üzembe vásárlásom meghatározásakor vegye figyelembe, ahol az erőforrások a fogyasztók találhatók fizikailag:

- Az erőforrások a felhasználók általában érdemes az erőforrásokat, a legalacsonyabb hálózati késéssel. Egy adott helyre, és az Azure-régiók közötti relatív késések megállapításához lásd: [relatív késések megtekintéséhez](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Adatok fizikai tárolási helye, szuverenitását, megfelelőségi vagy rugalmassági követelmények van? Ha igen, válassza a régióban, amely megfelel a követelményeknek, kritikus fontosságú. További információkért lásd: [Azure régiócsoportjairól](https://azure.microsoft.com/global-infrastructure/geographies/).
- Van szüksége rugalmasság az Azure rendelkezésre állási zónában üzembe erőforrásokat az Azure ugyanazon a régión belül? Erőforrások, például a virtuális gépek (VM) telepíthet másik rendelkezésre állási zónák ugyanazon virtuális hálózaton belül. Nem minden Azure-régiók rendelkezésre állási zónák azonban támogatják. A rendelkezésre állási zónák és az azokat támogató régiókban kapcsolatos további tudnivalókért lásd: [rendelkezésre állási zónák](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Előfizetések

Tetszőleges számú virtuális hálózatok előfizetésenként, szükség szerint akár telepítheti a [korlát](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Egyes szervezetek például rendelkezik a különböző részlegek számára különböző előfizetésekben. További információért és megfontolásokért előfizetések körül, lásd: [előfizetés-irányítás](/azure/architecture/cloud-adoption-guide/subscription-governance#define-your-hierarchy).

## <a name="segmentation"></a>Szegmentálás

Létrehozhat több virtuális hálózatok száma előfizetésenként és régiónként. Minden egyes virtuális hálózaton belüli több alhálózaton is létrehozhat. Az alábbi szempontok segítségével eldöntheti, hogy hány virtuális hálózatok és alhálózatok van szüksége:

### <a name="virtual-networks"></a>Virtuális hálózatok

Egy virtuális hálózat az Azure nyilvános hálózat egy virtuális, elkülönített része. Minden virtuális hálózatnak az előfizetéshez van kijelölve. Megfontolandó szempontok, amikor eldönti, hogy hozzon létre egy virtuális hálózatot, vagy több virtuális hálózat egy előfizetésben:

- A forgalom elkülönítése a különálló virtuális hálózatokra léteznek bármely szervezeti biztonsági követelmények? Ha szeretné-e csatlakozni a virtuális hálózatok. Ha csatlakoztatja a virtuális hálózatok, hálózati virtuális berendezés, például egy tűzfal, a virtuális hálózatok közötti adatforgalom szabályozásához is alkalmazható. További információkért lásd: [biztonsági](#security) és [kapcsolat](#connectivity).
- Virtuális hálózatok elkülönítésére külön bármely szervezeti követelmények léteznek [előfizetések](#subscriptions) vagy [régiók](#regions)?
- A [hálózati adapter](virtual-network-network-interface.md) lehetővé teszi, hogy a virtuális gép más erőforrásokkal kommunikálni. Minden hálózati illesztőnek egy vagy több magánhálózati IP-címek hozzárendelve. Hány hálózati adapterek és [magánhálózati IP-címek](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) szükség van egy virtuális hálózaton? Nincsenek [korlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) hálózati adapterek és a egy virtuális hálózaton belül lehet magánhálózati IP-címek száma.
- Szeretné a virtuális hálózat egy másik virtuális hálózathoz vagy a helyszíni hálózat csatlakoztatása? Néhány virtuális hálózatokhoz csatlakozni egymáshoz, vagy a helyi hálózatokat, de nem más választhatja. További információkért lásd: [kapcsolat](#connectivity). Minden egyes egy másik virtuális hálózathoz csatlakozó virtuális hálózati vagy a helyszíni hálózathoz, rendelkeznie kell egy egyedi címtér. Minden virtuális hálózat rendelkezik egy vagy több nyilvános vagy magánhálózati címtartomány címterében rendelve. Címtartomány classless internetes tartomány útválasztási (CIDR formátumban), mint a 10.0.0.0/16 van megadva. Tudjon meg többet [-címtartományok](manage-virtual-network.md#add-or-remove-an-address-range) virtuális hálózatok esetében.
- Rendelkezik különböző virtuális hálózatokban lévő erőforrások a szervezeti felügyeleti kapcsolatos követelmények? Ha tehát, előfordulhat, hogy különálló erőforrásokat külön virtuális hálózatban az egyszerűbb [engedélyek hozzárendelése](#permissions) egyéni felhasználóknak a szervezetben, vagy rendelje hozzá a különböző [házirendek](#policies) különböző virtuális hálózatok.
- Amikor bizonyos Azure-szolgáltatási erőforrások üzembe egy virtuális hálózaton, azok saját virtuális hálózat létrehozásához. Annak megállapításához, hogy az Azure-szolgáltatások hoz létre a saját virtuális hálózat, tekintse meg az információt az egyes [is telepíthető egy virtuális hálózatot az Azure-szolgáltatás](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Alhálózatok

Virtuális hálózat osztható egy vagy több alhálózatot akár a [korlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Megfontolandó szempontok, amikor eldönti, hogy hozzon létre egy alhálózatot, vagy több virtuális hálózat egy előfizetésben:

- Minden alhálózati rendelkeznie kell egy egyedi címtartományhoz, CIDR-formátumban, a virtuális hálózat címterében meghatározott. A címtartomány nem lehet átfedésben a többi alhálózathoz a virtuális hálózatban.
- Ha azt tervezi, az egyes Azure-szolgáltatási erőforrások üzembe egy virtuális hálózaton, azok előfordulhat, hogy van szüksége, vagy hozzon létre saját, így ott lehet, hogy elegendő szabad lemezterület a számukra, hogy ehhez. Annak megállapításához, hogy az Azure-szolgáltatások a saját IP-alhálózatot hoz, tekintse meg az információt az egyes [is telepíthető egy virtuális hálózatot az Azure-szolgáltatás](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Például ha csatlakoztat egy virtuális hálózatot a helyszíni hálózathoz az Azure VPN Gateway használatával, a virtuális hálózati átjáró kijelölt alhálózatot kell rendelkeznie. Tudjon meg többet [átjáróalhálózatok](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Az Azure hálózati forgalom egy virtuális hálózatot, alapértelmezés szerint az összes alhálózat között. Az Azure alapértelmezett útválasztás, hogy az Azure útválasztási alhálózatok között, vagy egy hálózati virtuális készüléken keresztül az alhálózatok közötti forgalom irányítására például felül lehet bírálni. Ha szükséges, hogy az adott virtuális hálózati adatfolyam egy hálózati virtuális készüléket (NVA) keresztül az erőforrások közötti adatforgalom, az erőforrásokat üzembe kívánja különböző alhálózatokhoz. További információ: [biztonsági](#security).
- Korlátozhatja például egy Azure storage-fiók vagy az Azure SQL database, a virtuális hálózati szolgáltatásvégpont adott alhálózatokra mutató Azure-erőforrásokhoz való hozzáférést. További megtagadhatja a hozzáférést az erőforrásokhoz az internetről. Előfordulhat, hogy létrehoz több alhálózat működik, és az egyes alhálózatok, de nem más szolgáltatásvégpont engedélyezése. Tudjon meg többet [szolgáltatásvégpontokat](virtual-network-service-endpoints-overview.md), és az Azure-erőforrások engedélyezheti őket.
- Nulla vagy egy hálózati biztonsági csoport egy virtuális hálózaton lévő egyes alhálózatokhoz is társíthatja. Társítsa ugyanazt vagy egy másik, a hálózati biztonsági csoportot, amely minden alhálózatban. Minden egyes hálózati biztonsági csoport tartalmaz szabályokat, amelyek engedélyezik vagy megtagadják a forgalmat forrásaként és céljaként. Tudjon meg többet [hálózati biztonsági csoportok](#traffic-filtering).

## <a name="security"></a>Biztonság

Szűrheti a hálózati forgalom és a egy hálózati biztonsági csoportok és a hálózati virtuális berendezések használata virtuális hálózatban lévő erőforrásokra. Szabályozhatja, hogyan irányítja az Azure alhálózat forgalmát. A vállalatnál ki működhet együtt a virtuális hálózatok erőforrások is korlátozhatja.

### <a name="traffic-filtering"></a>Forgalomszűrés

- Szűrhet egy hálózati biztonsági csoport és a egy nva-t, amely szűri a hálózati forgalom vagy mindkettő használatával virtuális hálózatban lévő erőforrások közötti hálózati forgalmat. Hálózati forgalom szűrésére egy nva-t, például a tűzfalak telepíteni, tekintse meg a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). NVA használatakor is létrehozhat egyéni útvonalak irányíthatja a forgalmat az alhálózatok az nva-n. Tudjon meg többet [forgalom-útválasztást](#traffic-routing).
- A hálózati biztonsági csoport néhány alapértelmezett biztonsági szabályok, amelyek engedélyezik vagy megtagadják a bejövő és kimenő forgalmat erőforrásokat tartalmaz. A hálózati biztonsági csoport egy hálózati adapter vagy az alhálózatot a hálózati adapter társíthatók. Biztonsági szabályok kezelésének egyszerűsítéséhez, javasoljuk, hogy társít egy hálózati biztonsági csoportot az egyes alhálózatokra ahelyett, hogy az egyes hálózati adapterek az alhálózatban, amikor csak lehetséges.
- Egy alhálózaton belül különböző virtuális gépek különböző biztonsági szabály van szükség, ha a hálózati adaptert a virtuális gépen, egy vagy több alkalmazásbiztonsági csoportokkal is hozzárendelhetők. Biztonsági szabály megadhatja egy alkalmazásbiztonsági csoportot a forrás, cél vagy mindkettőt. Ez a szabály majd csak érvényes a hálózati adaptereket, amelyek az alkalmazásbiztonsági csoport tagjai. Tudjon meg többet [hálózati biztonsági csoportok](security-overview.md) és [az alkalmazásbiztonsági csoportok](security-overview.md#application-security-groups).
- Az Azure számos alapértelmezett biztonsági szabályokat minden egyes hálózati biztonsági csoporton belül hoz létre. Egy alapértelmezett szabály lehetővé teszi, hogy a virtuális hálózatban lévő erőforrások között az összes bejövő forgalmat. Bírálja felül ezt a viselkedést, használja a hálózati biztonsági csoportok, egyéni útválasztás a forgalom irányítása NVA vagy mindkettőt. Javasoljuk, hogy, ismerkedjen meg az Azure összes [alapértelmezett biztonsági szabályokat](security-overview.md#default-security-rules) és megismerheti, hogyan erőforrás hálózati biztonsági csoport szabályai érvényesek.

Megtekintheti a minta tervek megvalósításához az Azure és a használatával az internet közötti DMZ- [NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) vagy [hálózati biztonsági csoportok](virtual-networks-dmz-nsg.md).

### <a name="traffic-routing"></a>Forgalom-Útválasztás

Az Azure egy alhálózatról kimenő forgalom több alapértelmezett útvonalakat hoz létre. Az Azure alapértelmezett útválasztási hoz létre egy útválasztási táblázatot, és a egy alhálózathoz való társítás felül lehet bírálni. Leggyakoribb okai a figyelőkével az Azure alapértelmezett útválasztási vannak:
- Mivel az nva-n keresztül az alhálózatok közötti adatforgalom. Megtudhatja, hogyan [konfigurálása kényszerített nva-n keresztül a forgalom útválasztási táblázatokat](tutorial-create-route-table-portal.md).
- Mivel az összes internetre irányuló forgalmat egy nva-t vagy a helyszínen, az Azure VPN-átjárón keresztül kényszeríteni kívánja. Kényszerít ki internetes forgalmat a helyszíni az ellenőrzés és naplózás gyakran hivatkoznak úgy, ahogy a kényszerített bújtatás. További tudnivalók a konfigurálása [kényszerített bújtatás](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Ha egyéni útválasztás megvalósításához van szüksége, javasoljuk, hogy, ismerje meg az [útválasztás az Azure-ban](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Kapcsolatok

Vagy is kapcsolódni egy virtuális hálózatot más virtuális hálózatokhoz való kapcsolódásának virtuális hálózatok közötti társviszony, a helyszíni hálózathoz az Azure VPN gateway használatával.

### <a name="peering"></a>Társviszony-létesítés

Használata esetén [virtuális hálózatok közötti társviszony](virtual-network-peering-overview.md), a virtuális hálózatok lehetnek azonos vagy eltérő, Azure-régióban támogatott. A virtuális hálózatok lehetnek azonos vagy eltérő Azure-előfizetések, a mindaddig, amíg mindkét előfizetéshez hozzárendelt ugyanahhoz az Azure Active Directory-bérlőhöz. Mielőtt létrehozná a társviszonyt, javasoljuk, hogy, ismerje meg az összes a társviszony-létesítés [-követelmények és korlátozások](virtual-network-manage-peering.md#requirements-and-constraints). Sávszélesség-erőforrások a virtuális hálózatok közötti társviszonyban, ugyanabban a régióban van ugyanaz, mintha az erőforrásokat ugyanabban a virtuális hálózatban.

### <a name="vpn-gateway"></a>VPN-átjáró

Használhatja az Azure-beli [VPN-átjáró](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , egy virtuális hálózat csatlakoztatása a helyszíni hálózat használatával egy [site-to-site VPN](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json), dedikált kapcsolat használata az Azure-ral, vagy [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Kombinálhatja társviszony-létesítés és a egy VPN-átjáró létrehozása [küllős topológiájú hálózatokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), amelyben küllő virtuális hálózatok csatlakoztatása hub virtuális hálózathoz, és a hubhoz például csatlakozik egy helyszíni hálózat.

### <a name="name-resolution"></a>Névfeloldás

Egy virtuális hálózatban lévő erőforrásokra nem oldható fel az Azure virtuális társhálózatokban lévő erőforrások nevei [beépített DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md). Egy virtuális Társhálózat nevek feloldása [a saját DNS-kiszolgáló üzembe helyezése](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server), vagy az Azure DNS használatához [saját tartományok](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A névfeloldás virtuális hálózatban lévő erőforrásokra és a helyszíni hálózatok között is megköveteli, hogy a saját DNS-kiszolgáló üzembe helyezése.

## <a name="permissions"></a>Engedélyek

Használja az Azure [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) az erőforrásokhoz. Engedélyek vannak rendelve egy [hatókör](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) a következő hierarchia: előfizetés, a felügyeleti csoport, erőforráscsoport és egyéni erőforrás. A hierarchia kapcsolatos további információkért lásd: [az erőforrások rendszerezéséhez](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Dolgozunk az Azure virtuális hálózatok és az összes a kapcsolódó képességek, például a társviszony-létesítés, hálózati biztonsági csoportok, végpontok és útválasztási táblázatokat, hozzárendelheti a szervezet tagjai a beépített [tulajdonosa](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [Közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor), vagy [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörök, és ezután rendelje hozzá a szerepkört a megfelelő hatókörben. Ha a virtuális hálózati képességeit egy részhalmaza számára konkrét engedélyeket hozzárendelni kívánt, hozzon létre egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és a szükséges konkrét engedélyeket [virtuális hálózatok](manage-virtual-network.md#permissions), [ alhálózatok és a Szolgáltatásvégpontok](virtual-network-manage-subnet.md#permissions), [hálózati adapterek](virtual-network-network-interface.md#permissions), [társviszony-létesítés](virtual-network-manage-peering.md#permissions), [alkalmazás és a hálózati biztonsági csoportok](manage-network-security-group.md#permissions), vagy [útválasztási táblázatok](manage-route-table.md#permissions) a szerepkörhöz.

## <a name="policy"></a>Szabályzat

Az Azure Policy lehetővé teszi, hogy létre, rendelhet hozzá és kezelhet szabályzatdefiníciókat. Szabályzatdefiníciók különböző szabályokat kényszerítenek ki az erőforrásokat, így az erőforrások megfeleljenek a szervezeti szabványoknak és szolgáltatói szerződéseknek maradjon. Az Azure Policy futtatja az értékelést az erőforrások, amelyek nem felelnek meg a szabályzatdefiníciók rendelkezik erőforrások keresése. Például határozza meg, és a egy szabályzatot, amely lehetővé teszi, hogy csak egy adott erőforráscsoporthoz vagy régióban lévő virtuális hálózatok létrehozása a alkalmazni. Egy másik szabályzat megkövetelheti, hogy rendelkezik-e minden alhálózat egy hálózati biztonsági csoport társítva van hozzá. A házirendek majd kiértékelése során az erőforrások létrehozása és frissítése.

Szabályzatok érvénybe lépnek a következő hierarchia: előfizetés, a felügyeleti csoport és az erőforráscsoportot. Tudjon meg többet [az Azure policy](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy üzembe helyezhet egy virtuális hálózat [Házirendsablon](policy-samples.md) mintákat.

## <a name="next-steps"></a>További lépések

Ismerje meg az összes feladatok, beállítások és a beállítások egy [virtuális hálózat](manage-virtual-network.md), [alhálózat és a szolgáltatási végpont](virtual-network-manage-subnet.md), [hálózati adapter](virtual-network-network-interface.md), [társviszony-létesítési](virtual-network-manage-peering.md), [alkalmazás és a hálózati biztonsági csoport](manage-network-security-group.md), vagy [útvonaltábla](manage-route-table.md).
