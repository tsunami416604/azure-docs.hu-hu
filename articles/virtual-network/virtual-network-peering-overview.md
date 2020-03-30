---
title: Az Azure virtuális hálózat társviszony-létesítése
titlesuffix: Azure Virtual Network
description: Tudnivalók az Azure-beli virtuális hálózatok közötti társviszony-létesítésről
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: anavin
ms.openlocfilehash: 5fb54e812e72b9393ffdf632085d0f32ab8b1988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279545"
---
# <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között

A virtuális hálózati társviszony-létesítés lehetővé teszi a hálózatok zökkenőmentes csatlakoztatását az [Azure virtuális hálózatban.](virtual-networks-overview.md) A virtuális hálózatok kapcsolódási célokra egyként jelennek meg. A virtuális gépek közötti forgalom a Microsoft gerinchálózati infrastruktúráját használja. Az ugyanazon a hálózaton lévő virtuális gépek közötti forgalomhoz hasonlóan a forgalom csak a Microsoft *magánhálózatán* keresztül történik.

Az Azure a következő típusú társviszony-létesítést támogatja:

* Virtuális hálózati társviszony-létesítés: Virtuális hálózatok csatlakoztatása ugyanabban az Azure-régióban.
* Globális virtuális hálózati társviszony-létesítés: Virtuális hálózatok összekapcsolása az Azure-régiók között.

A virtuális társhálózatok akár helyi, akár globális létesítésének előnyei:

* Kis késésű, nagy sávszélességű kapcsolat jön létre eltérő virtuális hálózatokba tartozó erőforrások között.
* Az egyetlen virtuális hálózat erőforrásainak lehetősége, hogy kommunikáljanak egy másik virtuális hálózat erőforrásaival.
* A virtuális hálózatok közötti adatátvitel lehetősége az Azure-előfizetések, az Azure Active Directory-bérlők, a telepítési modellek és az Azure-régiók között.
* Az Azure Resource Manager en keresztül létrehozott virtuális hálózatok társviszony-létesítése.
* Az a képesség, hogy az Erőforrás-kezelőn keresztül létrehozott virtuális hálózatot a klasszikus üzembe helyezési modellen keresztül létrehozott virtuális hálózatra létesítsen. Az Azure üzembehelyezési modellekkel kapcsolatos további információkért lásd: [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Ez nem okoz leállást egyik virtuális hálózat erőforrásaiban sem a társításkor és azt követően sem.

A társított virtuális hálózatok közti hálózati adatforgalom nem nyilvános. A virtuális hálózatok közötti forgalom a Microsoft gerinchálózatán belül marad. A virtuális hálózatok közti forgalomhoz nincs szükség nyilvános internetre, átjárókra vagy titkosításra.

## <a name="connectivity"></a>Kapcsolatok

Társviszony-létesített virtuális hálózatok esetén a virtuális hálózat erőforrásai közvetlenül kapcsolódhatnak a társviszonyba adott virtuális hálózat erőforrásaihoz.

Az azonos régióban lévő virtuális társhálózaton belüli virtuális gépek közötti hálózati késés megegyezik az egyetlen virtuális hálózaton belüli hálózati késéssel. A hálózat átbocsátóképessége attól függ, hogy milyen, a virtuális gépek méretével arányos sávszélesség van engedélyezve. A társviszonyon belül más korlátozás nem vonatkozik a sávszélességre.

A virtuális társhálózatokon belüli virtuális gépek közötti forgalom közvetlenül a Microsoft gerincinfrastruktúráján halad át, nem pedig átjárón vagy a nyilvános interneten.

A virtuális hálózatban hálózati biztonsági csoportokat alkalmazhat más virtuális hálózatokhoz vagy alhálózatokhoz való hozzáférés letiltására.
A virtuális hálózati társviszony-létesítés konfigurálásakor nyissa meg vagy zárja be a hálózati biztonsági csoport szabályait a virtuális hálózatok között. Ha teljes kapcsolatot nyit meg a társviszony-létesített virtuális hálózatok között, hálózati biztonsági csoportokat alkalmazhat adott hozzáférés letiltására vagy megtagadására. A teljes kapcsolat az alapértelmezett beállítás. A hálózati biztonsági csoportokról a [Biztonsági csoportok](security-overview.md)ról olvashat bővebben.

## <a name="service-chaining"></a>Szolgáltatásláncolás

A szolgáltatásláncolás lehetővé teszi, hogy a felhasználó által meghatározott útvonalakon keresztül irányítsa a forgalmat egy virtuális hálózatról egy virtuális berendezésre vagy átjáróra egy társviszony-létesített hálózaton keresztül.

A szolgáltatásláncolás engedélyezéséhez konfigurálja a felhasználó által definiált útvonalakat, amelyek a társviszony-létesített virtuális hálózatokban lévő virtuális gépekre mutatnak a *következő ugrási* IP-címként. A felhasználó által definiált útvonalak a virtuális hálózati átjárókra is mutathatnak a szolgáltatásláncolás engedélyezéséhez.

Központi és küllős hálózatokat telepíthet, ahol a központi virtuális hálózat infrastruktúra-összetevőket, például hálózati virtuális berendezést vagy VPN-átjárót üzemeltet. Az ágakon lévő virtuális hálózatok társhálózatai lehetnek a középponti hálózatnak. A forgalom a központi virtuális hálózat hálózati virtuális készülékein vagy VPN-átjáróin keresztül áramlik.

A virtuális hálózatok közötti társviszony-létesítéssel a felhasználó által definiált útvonalon egy virtuális társhálózat egyik virtuális gépének vagy egy VPN-átjárónak az IP-címe beállítható a következő ugrás IP-címeként. Nem lehet olyan virtuális hálózatok között, amelyekben egy felhasználó által definiált útvonal, amely megadja az Azure ExpressRoute-átjárót a következő ugrástípusként. A felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd a [felhasználó által megadott útvonalak áttekintését](virtual-networks-udr-overview.md#user-defined). Ha tudni szeretné, hogyan hozhat létre központi és küllős hálózati topológiát, olvassa el [a Hub spoke hálózati topológiáját az Azure-ban.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="gateways-and-on-premises-connectivity"></a>Átjárók és kapcsolat helyszíni rendszerekkel

Minden virtuális hálózat, beleértve a társviszony-létesített virtuális hálózat, saját átjáróval rendelkezhet. A virtuális hálózat az átjáró segítségével csatlakozhat a helyszíni hálózathoz. A virtuális [hálózat és a virtuális hálózatok](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) között is konfigurálhat átjárókat, még társviszony-létesített virtuális hálózatok esetén is.

Ha a virtuális hálózatok összekapcsolhatóságának mindkét beállítását konfigurálja, a virtuális hálózatok közötti forgalom a társviszony-létesítési konfiguráción keresztül áramlik. A forgalom az Azure gerinchálózatát használja.

Az átjáróa társviszony-létesített virtuális hálózatban is konfigurálható a helyszíni hálózat tranzitpontjaként. Ebben az esetben a távoli átjárót használó virtuális hálózat nem rendelkezhet saját átjáróval. A virtuális hálózatnak csak egy átjárója van. Az átjáró a társviszony-létesített virtuális hálózat helyi vagy távoli átjárója, ahogy az az alábbi ábrán látható:

![virtuális társhálózatok közötti átvitel](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Virtuális hálózati társviszony-létesítés és a globális virtuális hálózati társviszony-létesítés i átjáró átviteli támogatása.

A különböző telepítési modelleken keresztül létrehozott virtuális hálózatok közötti átjáró-átvitel támogatott. Az átjárónak az Erőforrás-kezelő modell ben lévő virtuális hálózatban kell lennie. További információ az átjárók adatátvitelre való használatáról: [VPN-átjáró konfigurálása adatátvitelhez virtuális hálózatok közötti társviszony-létesítésben](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ha egyetlen Azure ExpressRoute-kapcsolaton osztozó virtuális hálózatokat létesít, a köztük lévő forgalom a társviszony-létesítési kapcsolaton megy keresztül. Ez a forgalom az Azure gerinchálózatát használja. Az egyes virtuális hálózatok helyi átjárói ennek ellenére használhatók arra, hogy kapcsolatot létesítsenek a helyszíni kapcsolatcsoporttal. Ellenkező esetben használhatja a megosztott átjárót, és konfigurálhatja az átvitelt a helyszíni kapcsolathoz.

## <a name="troubleshoot"></a>Hibaelhárítás

Annak ellenőrzéséhez, hogy a virtuális hálózatok társviszonyban vannak-e, ellenőrizheti a hatékony útvonalakat. Ellenőrizze, hogy a virtuális hálózat bármely alhálózatában található-e hálózati illesztőútvonal. Ha létezik a virtuális hálózatok közötti társviszony, a virtuális hálózat összes alhálózata *Virtuális hálózatok közötti társviszony* következő ugrási típusú útvonalakkal rendelkezik minden virtuális társhálózat minden címterében. További információt a [Virtuálisgép-útválasztási probléma diagnosztizálása](diagnose-network-routing-problem.md)című témakörben talál.

Az Azure Network Watcher használatával a társviszony-létesített virtuális hálózat ban lévő virtuális gépekkel való kapcsolódás hibaelhárítása is elháríthatja a kapcsolatot. A kapcsolatellenőrzése lehetővé teszi, hogy lássa, hogyan történik a forgalom a forrásvirtuális gép hálózati illesztője és a célvirtuális gép hálózati illesztője között. További információt az [Azure Network Watcher rel az Azure Portal használatával kapcsolatos kapcsolatok elhárítása című témakörben](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine)talál.

Próbálkozhat a [Virtuális hálózati társviszony-létesítési problémák elhárítása is.](virtual-network-troubleshoot-peering-issues.md)

## <a name="constraints-for-peered-virtual-networks"></a>Társviszony-létesített virtuális hálózatok korlátai<a name="requirements-and-constraints"></a>

A következő megkötések csak akkor érvényesek, ha a virtuális hálózatok globális társviszonyban vannak:

* Egy virtuális hálózat erőforrásai nem tudnak kommunikálni egy globálisan társviszonyba helyezett virtuális hálózat ban lévő alapszintű belső terheléselosztó (ILB) előtér-IP-címével.
* Egyes, alapszintű terheléselosztót használó szolgáltatások nem működnek a globális virtuális hálózati társviszony-létesítésen. További információ: [Mik a globális virtuális társviszony-létesítési és terheléselosztók kapcsolatos korlátozások.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). .

További információ: [Követelmények és korlátozások](virtual-network-manage-peering.md#requirements-and-constraints). A társviszony-létesítések támogatott számáról a [Hálózati korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)című témakörben olvashat bővebben.

## <a name="permissions"></a>Engedélyek

A virtuális hálózati társviszony-létesítés hez szükséges engedélyekről az [Engedélyek című témakörben olvashat.](virtual-network-manage-peering.md#permissions)

## <a name="pricing"></a>Díjszabás

A virtuális hálózati társviszony-létesítési kapcsolatot használó kimenő és kimenő forgalom névleges díja van. További információ: [Virtual Network pricing](https://azure.microsoft.com/pricing/details/virtual-network).

A Gateway Transit egy társviszony-létesítési tulajdonság, amely lehetővé teszi, hogy a virtuális hálózat VPN/ExpressRoute átjárót használjon egy társviszonyt létesített virtuális hálózatban. Az átjáró-átvitel mind a több helyiség, mind a hálózat és a hálózat közötti kapcsolat esetén működik. Az átjáró (bejövő vagy kimenő) a társviszony-létesített virtuális hálózat virtuális hálózati forgalom merül fel a küllővirtuális hálózat (vagy nem átjáró virtuális hálózat) virtuális hálózati társviszony-létesítési díjakat. További információ: [VPN-átjáró díjszabása](https://azure.microsoft.com/pricing/details/vpn-gateway/) a VPN-átjáró díjaihoz és az ExpressRoute-átjáró díjai az ExpressRoute-átjáró díjaihoz.

>[!NOTE]
> A dokumentum egy korábbi verziója azt állította, hogy a virtuális hálózati társviszony-létesítési díjak nem vonatkoznak a küllővirtuális hálózatra (vagy nem átjáró virtuális hálózatra) a Gateway Transit használatával. Most tükrözi a pontos árképzési egy az árképzési oldalon.

## <a name="next-steps"></a>További lépések

* Két virtuális hálózat között társviszony-létesítést hozhat létre. A hálózatok ugyanahhoz az előfizetéshez, különböző üzembe helyezési modellekhez vagy különböző előfizetésekhez tartozhatnak. Végezzen el egy oktatóanyagot a következő forgatókönyvek egyikéhez:

    |Azure üzembehelyezési modell             | Előfizetés  |
    |---------                          |---------|
    |Mindkét Resource Manager              |[Ugyanaz](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Különböző](create-peering-different-subscriptions.md)|
    |Egy Resource Manager, egy klasszikus  |[Ugyanaz](create-peering-different-deployment-models.md)|
    |                                   |[Különböző](create-peering-different-deployment-models-subscriptions.md)|

* Ha tudni szeretné, hogyan hozhat létre központi és küllős hálózati topológiát, olvassa el [a Hub spoke hálózati topológiáját az Azure-ban.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
* A virtuális hálózati társviszony-létesítési beállításokról a [Virtuális hálózati társviszony-létesítés létrehozása, módosítása és törlése](virtual-network-manage-peering.md)című témakörben olvashat.
* A virtuális hálózati társviszony-létesítésre és a globális virtuális hálózati társviszony-létesítésre vonatkozó gyakori kérdésekre a [VNet-társviszony-létesítés](virtual-networks-faq.md#vnet-peering)i.
