---
title: Az Azure virtuális hálózatainak tervezése | Microsoft dokumentumok
description: Megtudhatja, hogyan tervezhet virtuális hálózatokat az elkülönítési, kapcsolati és helyhez kapcsolódó követelményeknek megfelelően.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: kumud
ms.openlocfilehash: 95dd7be118e869aed02bb55918ab0cefa0d05d03
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998869"
---
# <a name="plan-virtual-networks"></a>Virtuális hálózatok tervezése

A kísérlethez szükséges virtuális hálózat létrehozása elég egyszerű, de nagy az esélye annak, hogy idővel több virtuális hálózatot telepít a szervezet termelési igényeinek támogatására. Némi tervezéssel virtuális hálózatokat telepíthet, és hatékonyabban csatlakoztathatja a szükséges erőforrásokat. Az ebben a cikkben található információk akkor a leghasznosabbak, ha már ismeri a virtuális hálózatokat, és van némi tapasztalata a velük való munkában. Ha nem ismeri a virtuális hálózatokat, ajánlott elolvasni [a Virtuális hálózat áttekintését.](virtual-networks-overview.md)

## <a name="naming"></a>Elnevezés

Minden Azure-erőforrásnak van neve. A névnek egyedinek kell lennie egy hatókörön belül, amely erőforrástípusonként eltérő lehet. Egy virtuális hálózat nevének például egyedinek kell lennie egy [erőforráscsoporton](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)belül, de egy [előfizetésen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) vagy az Azure-régióban duplikálható. [region](https://azure.microsoft.com/regions/#services) Több hálózati erőforrás idővel történő kezelése során az erőforrások elnevezésekénél konzisztensen használható elnevezési konvenció meghatározása. Javaslatokért [lásd: Elnevezési konvenciók](../azure-resource-manager/management/resource-name-rules.md#microsoftnetwork).

## <a name="regions"></a>Régiók

Az összes Azure-erőforrás egy Azure-régióban és előfizetésben jön létre. Erőforrás csak olyan virtuális hálózatban hozható létre, amely ugyanabban a régióban és előfizetésben létezik, mint az erőforrás. A különböző előfizetésekben és régiókban található virtuális hálózatokat azonban csatlakoztathatja. További információt a Kapcsolat című témakörben [talál.](#connectivity) Annak eldöntésekor, hogy melyik régió(ka)n kell(ek) erőforrásokat telepíteni, vegye figyelembe, hogy az erőforrások fogyasztói fizikailag hol találhatók:

- Az erőforrások fogyasztói általában szeretné a legalacsonyabb hálózati késés az erőforrásaikat. A megadott hely és az Azure-régiók közötti relatív késések meghatározásához olvassa el [a Relatív késések megtekintése.](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Rendelkezik adattárolási, szuverenitási, megfelelőségi vagy rugalmassági követelményekkel? Ha igen, a követelményekhez igazodó régió kiválasztása kritikus fontosságú. További információ: [Azure geographies](https://azure.microsoft.com/global-infrastructure/geographies/).
- Szüksége van rugalmasságra az Azure rendelkezésre állási zónái között ugyanazon az Azure-régión belül az üzembe helyezett erőforrásokhoz? Erőforrásokat, például virtuális gépeket (VM) helyezhet üzembe ugyanazon a virtuális hálózaton belül különböző rendelkezésre állási zónákba. Nem minden Azure-régió támogatja azonban a rendelkezésre állási zónákat. Ha többet szeretne tudni a rendelkezésre állási zónákról és az azokat támogató régiókról, olvassa el a [Rendelkezésre állási zónák című témakört.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="subscriptions"></a>Előfizetések

Az egyes előfizetéseken belül szükség szerint annyi virtuális hálózatot [telepíthet, akorig.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) Egyes szervezetek különböző előfizetésekkel rendelkeznek a különböző részlegek számára, például. Az előfizetésekkel kapcsolatos további információkért és szempontokért olvassa el az [Előfizetések cégirányítási témakörét.](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy)

## <a name="segmentation"></a>Szegmentálás

Előfizetésenként és régiónként több virtuális hálózatot is létrehozhat. Az egyes virtuális hálózatokon belül több alhálózatot is létrehozhat. Az alábbi szempontok segítenek meghatározni, hogy hány virtuális hálózatra és alhálózatra van szüksége:

### <a name="virtual-networks"></a>Virtuális hálózatok

A virtuális hálózat az Azure nyilvános hálózatának egy virtuális, elkülönített része. Minden virtuális hálózat az előfizetéshez van elkülönében. Figyelembe vehet dolgok, amikor eldönti, hogy egy virtuális hálózatot vagy több virtuális hálózatot hoz-e létre egy előfizetésben:

- Léteznek szervezeti biztonsági követelmények a forgalom külön virtuális hálózatokba való elkülönítéséhez? Választhat, hogy csatlakozik-e a virtuális hálózatokhoz, vagy sem. Ha virtuális hálózatokat csatlakoztat, a virtuális hálózatok közötti forgalom szabályozásához megvalósíthat egy hálózati virtuális berendezést, például egy tűzfalat. További információt a [Biztonság](#security) és a kapcsolat című témakörben [talál.](#connectivity)
- Léteznek olyan szervezeti követelmények a virtuális hálózatok külön [előfizetésekvagy](#subscriptions) régiók között történő [elkülönítéséhez?](#regions)
- A [hálózati adapter](virtual-network-network-interface.md) lehetővé teszi, hogy a virtuális gép más erőforrásokkal kommunikáljon. Minden hálózati adapterhez egy vagy több privát IP-cím van hozzárendelve. Hány hálózati adapterre és [privát IP-címre](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) van szüksége egy virtuális hálózatban? A virtuális hálózaton belül a hálózati adapterek és a magánhálózati IP-címek száma [korlátozott.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)
- Szeretné csatlakoztatni a virtuális hálózatot egy másik virtuális hálózathoz vagy a helyszíni hálózathoz? Dönthet úgy, hogy egyes virtuális hálózatokat egymáshoz vagy helyszíni hálózatokhoz csatlakoztat, másokat azonban nem. További információt a Kapcsolat című témakörben [talál.](#connectivity) Minden olyan virtuális hálózatnak, amely et egy másik virtuális hálózathoz vagy a helyszíni hálózathoz csatlakozik, egyedi címtérrel kell rendelkeznie. Minden virtuális hálózat hoz egy vagy több nyilvános vagy privát címtartományt a címterületéhez. A címtartomány osztály nélküli internetes tartomány-útválasztási (CIDR) formátumban van megadva, például 10.0.0.0/16. További információ a virtuális hálózatok [címtartományairól.](manage-virtual-network.md#add-or-remove-an-address-range)
- Rendelkezik a különböző virtuális hálózatok erőforrásaira vonatkozó szervezeti felügyeleti követelményekkel? Ha igen, az erőforrásokat külön virtuális hálózatra bonthatja, hogy egyszerűsítse az [engedélyhozzárendelést](#permissions) a szervezetben lévő egyének számára, vagy különböző házirendeket rendeljen a különböző virtuális hálózatokhoz.
- Ha üzembe helyez bizonyos Azure-szolgáltatási erőforrásokat egy virtuális hálózatba, saját virtuális hálózatot hoznak létre. Annak megállapításához, hogy egy Azure-szolgáltatás saját virtuális hálózatot hoz-e létre, tekintse meg az egyes [Azure-szolgáltatások adatait, amelyek virtuális hálózatba telepíthetők.](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)

### <a name="subnets"></a>Alhálózatok

A virtuális hálózatok egy vagy több alhálózatra oszthatók a [korlátokig.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) Figyelembe vehet dolgok, amikor eldönti, hogy egy alhálózatot vagy több virtuális hálózatot hoz-e létre egy előfizetésben:

- Minden alhálózatnak rendelkeznie kell egy CIDR formátumban megadott egyedi címtartománysal a virtuális hálózat címterében. A címtartomány nem fedheti át a virtuális hálózat más alhálózataival.
- Ha azt tervezi, hogy üzembe helyez bizonyos Azure-szolgáltatás erőforrásait egy virtuális hálózatba, előfordulhat, hogy saját alhálózatot igényelnek, vagy létrehozzák őket, ezért elegendő szabad helynek kell lennie ahhoz, hogy ezt megtehessék. Annak megállapításához, hogy egy Azure-szolgáltatás saját alhálózatot hoz-e létre, tekintse meg az egyes [Azure-szolgáltatások adatait, amelyek virtuális hálózatba telepíthetők.](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) Ha például egy virtuális hálózatot csatlakoztat egy helyszíni hálózathoz egy Azure VPN-átjáró használatával, a virtuális hálózatnak rendelkeznie kell egy dedikált alhálózattal az átjáróhoz. További információ az [átjáróalhálózatokról.](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)
- Az Azure alapértelmezés szerint a virtuális hálózat összes alhálózata közötti hálózati forgalmat irányítja. Felülbírálhatja az Azure alapértelmezett útválasztását az alhálózatok közötti Azure-útválasztás megakadályozása érdekében, vagy például az alhálózatok közötti forgalmat egy hálózati virtuális berendezésen keresztül. Ha az azonos virtuális hálózat erőforrásai közötti forgalom egy hálózati virtuális berendezésen (NVA) keresztül áramlik, telepítse az erőforrásokat különböző alhálózatokra. További információ a [biztonságról](#security).
- Az Azure-erőforrásokhoz, például egy Azure-tárfiókhoz vagy az Azure SQL-adatbázishoz való hozzáférést a virtuális hálózati szolgáltatásvégpontot tartalmazó adott alhálózatokra korlátozhatja. Továbbá megtagadhatja az erőforrásokhoz való hozzáférést az internetről. Létrehozhat több alhálózatot, és engedélyezheti a szolgáltatásvégpontot egyes alhálózatokhoz, de másokhoz nem. További információ a [szolgáltatásvégpontokról](virtual-network-service-endpoints-overview.md)és az Azure-erőforrásokról, amelyekhez engedélyezheti őket.
- A virtuális hálózat minden alhálózatához nulla vagy egy hálózati biztonsági csoportot társíthat. Az egyes alhálózatokhoz társíthatja ugyanazt vagy egy másik hálózati biztonsági csoportot. Minden hálózati biztonsági csoport olyan szabályokat tartalmaz, amelyek engedélyezik vagy megtagadják a forrásokba és célhelyekről érkező és onnan érkező forgalmat. További információ a [hálózati biztonsági csoportokról.](#traffic-filtering)

## <a name="security"></a>Biztonság

A hálózati biztonsági csoportok és a hálózati virtuális készülékek segítségével szűrheti a virtuális hálózat erőforrásaiba és erőforrásaiból érkező hálózati forgalmat. Szabályozhatja, hogy az Azure hogyan irányítja az alhálózatokból érkező forgalmat. Azt is korlátozhatja, hogy a szervezetben ki dolgozhat a virtuális hálózatok erőforrásaival.

### <a name="traffic-filtering"></a>Forgalomszűrés

- A virtuális hálózat erőforrásai közötti hálózati forgalmat hálózati biztonsági csoport, a hálózati forgalmat szűrő NVA vagy mindkettő segítségével szűrheti. Ha nva-t, például tűzfalat szeretne telepíteni a hálózati forgalom szűréséhez, tekintse meg az [Azure Marketplace-t.](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1) NVA használata esetén egyéni útvonalakat is létrehozhat az alhálózatokból az NVA-ba irányuló forgalom irányításához. További információ a [forgalomútválasztásról.](#traffic-routing)
- A hálózati biztonsági csoport számos alapértelmezett biztonsági szabályt tartalmaz, amelyek engedélyezik vagy megtagadják az erőforrásokba irányuló vagy onnan érkező forgalmat. Hálózati biztonsági csoport társítható egy hálózati adapterhez, ahhoz az alhálózathoz, amelyben a hálózati csatoló található, vagy mindkettőhöz. A biztonsági szabályok kezelésének egyszerűsítése érdekében javasoljuk, hogy amikor csak lehetséges, az alhálózaton belüli egyes hálózati adapterek helyett az egyes alhálózatokhoz társítson egy hálózati biztonsági csoportot.
- Ha egy alhálózaton belül különböző virtuális gépeknek különböző biztonsági szabályokat kell alkalmaznirájuk, a virtuális gép hálózati adapterét egy vagy több alkalmazásbiztonsági csoporthoz társíthatja. A biztonsági szabályok megadhatnak egy alkalmazásbiztonsági csoportot a forrásukban, a célban vagy mindkettőben. Ez a szabály csak azokra a hálózati adapterekre vonatkozik, amelyek az alkalmazásbiztonsági csoport tagjai. További információ a [hálózati biztonsági csoportokról](security-overview.md) és [az alkalmazásbiztonsági csoportokról.](security-overview.md#application-security-groups)
- Az Azure számos alapértelmezett biztonsági szabályt hoz létre az egyes hálózati biztonsági csoportokon belül. Egy alapértelmezett szabály lehetővé teszi, hogy a virtuális hálózat összes erőforrása között minden forgalom folyjon. A viselkedés felülbírálásához használjon hálózati biztonsági csoportokat, egyéni útválasztást a forgalom nva-hoz vagy mindkettőhöz történő irányításához. Javasoljuk, hogy ismerkedjen meg az Azure [összes alapértelmezett biztonsági szabályával,](security-overview.md#default-security-rules) és ismerje meg, hogyan vonatkozik a hálózati biztonsági csoport szabályok egy erőforrásra.

Az Azure és az internet közötti peremhálózat (más néven DMZ) megvalósításához használható mintatervek [et nva](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)használatával tekintheti meg.

### <a name="traffic-routing"></a>Forgalom útválasztása

Az Azure számos alapértelmezett útvonalat hoz létre az alhálózatból érkező kimenő forgalomhoz. Felülbírálhatja az Azure alapértelmezett útválasztását egy útvonaltábla létrehozásával és alhálózathoz társításával. Az Azure alapértelmezett útválasztásának felülbírálásának gyakori okai a következők:
- Mert azt szeretné, hogy az alhálózatok közötti forgalom egy NVA-n keresztül folyjon. Ha többet szeretne megtudni arról, hogyan állíthatja be az [útvonaltáblákat az NVA-n keresztüli forgalom kényszerítésére.](tutorial-create-route-table-portal.md)
- Mert azt szeretné, hogy az összes internet-kötött forgalmat egy NVA, vagy a helyszíni, egy Azure VPN-átjárón keresztül. Az internetes forgalom helyszíni kényszerítése ellenőrzésre és naplózásra gyakran nevezik kényszerített bújtatásnak. További információ a [kényszerített bújtatás konfigurálásáról.](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)

Ha egyéni útválasztást kell megvalósítania, ajánlott megismerkedni az [útválasztással az Azure-ban.](virtual-networks-udr-overview.md)

## <a name="connectivity"></a>Kapcsolatok

Virtuális hálózatot csatlakoztathat más virtuális hálózatokhoz virtuális hálózati társviszony-létesítés vagy a helyszíni hálózat segítségével egy Azure VPN-átjáró használatával.

### <a name="peering"></a>Társviszony-létesítés

Virtuális [hálózati társviszony-létesítés](virtual-network-peering-overview.md)használata esetén a virtuális hálózatok lehetnek ugyanabban a vagy különböző támogatott Azure-régiókban. A virtuális hálózatok lehetnek azonos vagy különböző Azure-előfizetések (akár előfizetések tartozó különböző Azure Active Directory-bérlők). Társviszony-létesítés létrehozása előtt ajánlott megismerkedni az összes társviszony-létesítési [követelmények kel és korlátokkal.](virtual-network-manage-peering.md#requirements-and-constraints) Az ugyanabban a régióban társviszonyban lévő virtuális hálózatok erőforrásai közötti sávszélesség megegyezik, mintha az erőforrások ugyanabban a virtuális hálózatban lennének.

### <a name="vpn-gateway"></a>VPN-átjáró

Az Azure [VPN-átjáró](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) segítségével csatlakozhat virtuális hálózathoz a helyszíni hálózathoz egy helyek közötti [VPN](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json)használatával, vagy az Azure [ExpressRoute-tal](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)dedikált kapcsolathasználatával.

A társviszony-létesítés és a VPN-átjáró kombinálásával [hub- és küllőhálózatokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)hozhat létre, ahol a küllős virtuális hálózatok egy központi virtuális hálózathoz csatlakoznak, és például a hub egy helyszíni hálózathoz csatlakozik.

### <a name="name-resolution"></a>Névfeloldás

Az egy virtuális hálózat erőforrásai nem oldhatók fel a társviszony-létesített virtuális hálózat erőforrásainak nevei az Azure [beépített DNS-ével.](virtual-networks-name-resolution-for-vms-and-role-instances.md) A névfeloldástársi virtuális hálózatban telepítse [saját DNS-kiszolgálóját,](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)vagy használja az Azure [DNS-es magántartományokat.](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) A virtuális hálózat és a helyszíni hálózatok erőforrásai közötti nevek feloldásához saját DNS-kiszolgáló telepítéséhez is szükség van.

## <a name="permissions"></a>Engedélyek

Az Azure [szerepköralapú hozzáférés-vezérlést](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) használ az erőforrásokhoz. Az engedélyek a következő hierarchia [hatóköréhez](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) vannak rendelve: felügyeleti csoport, előfizetés, erőforráscsoport és egyes erőforrások. Ha többet szeretne megtudni a hierarchiáról, olvassa [el az Erőforrások rendezése](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört. Az Azure virtuális hálózataival és az összes kapcsolódó képességükkel, például a társviszony-létesítéssel, a hálózati biztonsági csoportokkal, a szolgáltatásvégpontokkal és az útvonaltáblákkal való munkához hozzárendelheti a szervezet tagjait a beépített [tulajdonosi,](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner) [közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)vagy [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörökhöz, majd hozzárendelheti a szerepkört a megfelelő hatókörhöz. Ha a virtuális hálózati képességek egy részhalmazához speciális engedélyeket szeretne rendelni, hozzon létre egy [egyéni szerepkört,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és rendelje hozzá a [virtuális hálózatokhoz,](manage-virtual-network.md#permissions) [alhálózatokhoz és szolgáltatásvégpontokhoz,](virtual-network-manage-subnet.md#permissions)hálózati [csatolókhoz,](virtual-network-network-interface.md#permissions) [társviszony-létesítéshez,](virtual-network-manage-peering.md#permissions) [hálózati és alkalmazásbiztonsági csoportokhoz](manage-network-security-group.md#permissions)vagy a [szerepkörhöz irányított táblákhoz](manage-route-table.md#permissions) szükséges speciális engedélyeket.

## <a name="policy"></a>Szabályzat

Az Azure Policy lehetővé teszi a szabályzatdefiníciók létrehozását, hozzárendelését és kezelését. A szabályzatdefiníciók különböző szabályokat kényszerítenek ki az erőforrásokra, így az erőforrások megfelelnek a szervezeti szabványoknak és a szolgáltatásiszint-szerződéseknek. Az Azure Policy kiértékeli az erőforrásokat, és olyan erőforrásokat keres, amelyek nem felelnek meg a szabályzatdefinícióinak. Definiálhat például olyan házirendet, amely lehetővé teszi a virtuális hálózatok létrehozását csak egy adott erőforráscsoportban vagy régióban. Egy másik házirend megkövetelheti, hogy minden alhálózathoz legyen egy hálózati biztonsági csoport társítva. A házirendek kiértékelése az erőforrások létrehozásakor és frissítésekor történik.

A házirendek a következő hierarchiára vonatkoznak: felügyeleti csoport, előfizetés és erőforráscsoport. További információ az [Azure-szabályzatról,](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy üzembe helyezése néhány virtuális hálózati [házirendsablon-minták.](policy-samples.md)

## <a name="next-steps"></a>További lépések

Ismerje meg a [virtuális hálózat,](manage-virtual-network.md) [az alhálózat és a szolgáltatásvégpont, a](virtual-network-manage-subnet.md)hálózati [illesztő,](virtual-network-network-interface.md) [a társviszony-létesítés](virtual-network-manage-peering.md), [a hálózat- és alkalmazásbiztonsági csoport](manage-network-security-group.md)vagy az útvonaltábla összes feladatát, beállítását és [beállítását.](manage-route-table.md)
