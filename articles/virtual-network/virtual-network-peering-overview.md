---
title: "Társviszony kialakítása virtuális hálózatok között az Azure-ban | Microsoft Docs"
description: "Tudnivalók az Azure-beli virtuális hálózatok közötti társviszony-létesítésről"
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: narayan;anavin
ms.openlocfilehash: df1d316654bdfd282965000966f79543e0d5124c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között

A virtuális hálózatok közötti társviszony lehetővé teszi két Azure [virtuális hálózat](virtual-networks-overview.md) zökkenőmentes összekapcsolását. A társviszony kialakítását követően a virtuális hálózatok a csatlakozás szempontjából egyetlen hálózatnak látszanak. A virtuális társhálózatokba tartozó virtuális gépek közötti forgalmat lényegében ugyanúgy továbbítja a rendszer a Microsoft gerincinfrastruktúráján keresztül, ahogyan az azonos virtuális hálózatba tartozó virtuális gépek közötti forgalmat, *magánhálózati* IP-címeken keresztül. 

A virtuális társhálózatok használatának előnyei:

* A társított virtuális hálózatok közti hálózati adatforgalom nem nyilvános. A virtuális hálózatok közötti forgalom a Microsoft gerinchálózatán belül marad. A virtuális hálózatok közti forgalomhoz nincs szükség nyilvános internetre, átjárókra vagy titkosításra.
* Kis késésű, nagy sávszélességű kapcsolat jön létre eltérő virtuális hálózatokba tartozó erőforrások között.
* Az egyes virtuális hálózatokban lévő erőforrások a hálózatok társítását követően képesek más virtuális hálózatokban lévő erőforrásokkal kommunikálni.
* Adatok továbbíthatóak Azure-előfizetések, üzemi modellek és Azure-régiók között (előzetes verzió).
* Létrehozható társviszony az Azure Resource Managerrel létrehozott virtuális hálózatok között, vagy egy, a Resource Managerrel létrehozott virtuális hálózat és egy, a klasszikus üzemi modellel létrehozott virtuális hálózat között. Az Azure üzembehelyezési modellekkel kapcsolatos további információkért lásd: [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Ez nem okoz leállást egyik virtuális hálózat erőforrásaiban sem a társításkor és azt követően sem.

## <a name="requirements-constraints"></a>Követelmények és korlátozások

* Az azonos régiókban lévő virtuális hálózatok közötti társviszony kialakítása általánosan elérhető. Az eltérő régiókban lévő virtuális hálózatok közötti társviszony kialakítása jelenleg előzetes kiadásban érhető el az USA nyugati középső régiójában, a Közép-Kanada régióban és az USA 2. nyugati régiójában. Mielőtt különböző régiókban lévő virtuális hálózatok között alakítana ki társviszonyt, [regisztrálnia kell az előfizetését](virtual-network-create-peering.md#register) az előzetes verzióra. A különböző régióban lévő virtuális hálózatok közötti társviszony kialakításának kísérlete meghiúsul, ha még nem regisztrált az előzetes verzióra.
    > [!WARNING]
    > A régiók között létrehozott virtuális társhálózatok rendelkezésre állása és megbízhatósága eltérő lehet az általánosan elérhető kiadások társításaitól. Előfordulhat, hogy a virtuális társhálózatok korlátozott képességekkel rendelkeznek, vagy nem érhetőek el minden Azure-régióban. A szolgáltatás rendelkezésre állásával és állapotával kapcsolatos legfrissebb értesítésekért tekintse meg az [Azure virtuális hálózati frissítésekkel kapcsolatos](https://azure.microsoft.com/updates/?product=virtual-network) oldalát.

* A virtuális társhálózatok IP-címterei nem fedhetik egymást.
* Miután egy virtuális hálózat és egy másik virtuális hálózat között társviszony jött létre, nem adhatóak hozzá és nem törölhetőek címtartományok a címtérben. Ha címtartományokat kell hozzáadnia egy társított virtuális hálózat címteréhez, előbb el kell távolítania a társítást, ezután hozzáadhatja a címtartományt, majd végül újra kell létesítenie a társítást.
* A virtuális hálózati társviszony két virtuális hálózat között jön létre. A társviszonyok nem adódnak tovább tranzitív módon. Ha például az A virtuális hálózat társviszonyban áll a B virtuális hálózattal, és a B hálózat társviszonyban áll a C virtuális hálózattal, az *nem* jelenti azt, hogy az A virtuális hálózat társviszonyban van a C virtuális hálózattal.
* Társviszonyt létesíthet két különböző előfizetésben található virtuális hálózat között, ha a társviszony-létesítést mindkét előfizetés esetében engedélyezi egy rendszergazdai engedéllyel rendelkező felhasználó (lásd a [konkrét engedélyeket](create-peering-different-deployment-models-subscriptions.md#permissions)), és az előfizetések ugyanahhoz az Azure Active Directory-bérlőhöz vannak társítva. Különböző Active Directory-bérlőkhöz társított előfizetésekben található virtuális hálózatok között [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) segítségével létesíthető társviszony.
* A virtuális hálózatok társviszonyba állíthatók, ha mindkettő a Resource Manager-alapú üzemi modellel lett létrehozva, vagy ha az egyik virtuális hálózat a Resource Manager-alapú üzemi modellel, a másik pedig a klasszikus üzemi modellel lett létrehozva. Klasszikus üzemi modellel létrehozott virtuális hálózatok között azonban nem létesíthető társviszony. [VPN-átjáróval](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) összeköthetőek a klasszikus üzemi modellel létrehozott virtuális hálózatok.
* Bár a virtuális társhálózatokba tartozó virtuális gépek kommunikációjára nem vonatkoznak külön sávszélesség-korlátozások, a virtuális gépekre méretalapú sávszélességkorlát érvényes. Ha többet szeretne megtudni a különböző méretű virtuális gépek maximális hálózati sávszélességével kapcsolatban, olvassa el a [windowsos](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy a [linuxos](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek méretéről szóló cikkeket.

     ![Társviszony létesítése virtuális hálózatok között alapszinten](./media/virtual-networks-peering-overview/figure03.png)

## <a name="connectivity"></a>Kapcsolatok

Ha társviszony jött létre a virtuális hálózatok között, akkor a virtuális hálózatokba tartozó erőforrások közvetlenül tudnak kommunikálni a virtuális társhálózat erőforrásaival.

Az azonos régióban lévő virtuális társhálózaton belüli virtuális gépek közötti hálózati késés megegyezik az egyetlen virtuális hálózaton belüli hálózati késéssel. A hálózat átbocsátóképessége attól függ, hogy milyen, a virtuális gépek méretével arányos sávszélesség van engedélyezve. A társviszonyon belül más korlátozás nem vonatkozik a sávszélességre.

A virtuális társhálózatokon belüli virtuális gépek közötti forgalom közvetlenül a Microsoft gerincinfrastruktúráján halad át, nem pedig átjárón vagy a nyilvános interneten.

Az egyik virtuális hálózatba tartozó virtuális gépek elérhetik az azonos régióban lévő virtuális társhálózat belső terheléselosztóit. A belső terheléselosztók támogatása nem terjed ki a globális virtuális társhálózatokra (előzetes kiadás). A globális virtuális társhálózatok általánosan elérhető kiadásában a belső terheléselosztók is támogatottak lesznek.

Szükség esetén bármelyik virtuális hálózatban alkalmazhatók hálózati biztonsági csoportok a más virtuális hálózatok vagy alhálózatok elérésének blokkolására.
Virtuális hálózatok társviszonyának konfigurálásakor megnyithatja vagy lezárhatja a hálózati biztonsági csoportszabályokat a virtuális hálózatok között. Ha úgy dönt, hogy teljes körű kapcsolatra nyit lehetőséget a virtuális társhálózatok között (ez az alapértelmezett beállítás), akkor a hálózati biztonsági csoportok segítségével konkrét alhálózatokra vagy virtuális gépekre vonatkozóan blokkolhatja vagy megtagadhatja a megadott típusú hozzáférést. A hálózati biztonsági csoportokkal kapcsolatos további információk: [Hálózati biztonsági csoportok áttekintése](virtual-networks-nsg.md).

## <a name="service-chaining"></a>Szolgáltatásláncolás

Konfigurálhatók olyan felhasználó által megadott útvonalak, amelyekben a társviszonyban álló virtuális hálózatba tartozó virtuális gépek (a *következő ugrás* IP-címeként) vagy virtuális hálózati átjárók vannak megadva, így lehetővé válik a szolgáltatásláncolás. A szolgáltatásláncolással a forgalom a felhasználó által meghatározott útvonalon átirányítható egy virtuális hálózatból a virtuális társhálózatban működő virtuális berendezésre vagy virtuális hálózati átjáróra.

Üzembe helyezhet küllős topológiájú hálózatokat is, ahol a központi virtuális hálózatban infrastrukturális összetevők (például virtuális hálózati berendezések vagy VPN-átjárók) működhetnek. Az ágakon lévő virtuális hálózatok társhálózatai lehetnek a középponti hálózatnak. A középponti virtuális hálózatban az adatforgalom virtuális hálózati berendezéseken vagy VPN-átjárókon is áthaladhat. 

A virtuális hálózatok közötti társviszony-létesítéssel a felhasználó által definiált útvonalon egy virtuális társhálózat egyik virtuális gépének vagy egy VPN-átjárónak az IP-címe beállítható a következő ugrás IP-címeként. Ugyanakkor nem hozhat létre virtuális hálózatok közötti útvonalat olyan felhasználói útvonallal, amely egy ExpressRoute-átjárót határoz meg a következő ugrás típusaként. A felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd a [felhasználó által megadott útvonalak áttekintését](virtual-networks-udr-overview.md#user-defined). A küllős hálózati topológiák létrehozásával kapcsolatos további információkért lásd a [küllős hálózati topológiákat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering) ismertető cikket

## <a name="gateways-and-on-premises-connectivity"></a>Átjárók és kapcsolat helyszíni rendszerekkel

Minden virtuális hálózatnak – függetlenül attól, hogy társviszonyban áll-e más virtuális hálózattal – lehet saját átjárója, és használhatja egy helyi hálózathoz való csatlakozásra. A [virtuális hálózatok közötti kapcsolat](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) is konfigurálható átjárók segítségével, még abban az esetben is, ha a virtuális hálózatok társviszonyban állnak.

Ha a virtuális hálózatok mindkét összekapcsolási módszere konfigurálva van, a virtuális hálózatok közötti forgalom a társhálózati konfiguráción keresztül (vagyis az Azure gerinchálózatán keresztül) zajlik.

Ha egyazon régióban lévő virtuális hálózatok között társhálózati viszony van, a virtuális társhálózatban működő átjáró is konfigurálható átviteli pontként a helyi hálózat felé. Ebben az esetben a távoli átjárót használó virtuális hálózatnak nem lehet saját átjáróra. Egy virtuális hálózatnak csak egy átjárója lehet. Az átjáró lehet helyi vagy távoli (virtuális társhálózatban működő), amint a következő képen látható:

![virtuális társhálózatok közötti átvitel](./media/virtual-networks-peering-overview/figure04.png)

Az átjáróval történő adatátvitel nem támogatott abban az esetben, ha olyan virtuális hálózatok állnak egymással társviszonyban, amelyek különböző üzemi modellel vagy eltérő régiókban lettek létrehozva. Ahhoz, hogy az átjárón át történő átvitel működjön, a társviszonyban álló virtuális hálózatokat a Resource Manager-alapú üzemi modellel kell létrehozni, és mindkettőnek ugyanabban a régióban kell lennie. A globális virtuális társhálózatok jelenleg nem támogatják az átjáróval történő adatátvitelt.

Ha két, ugyanazon az Azure ExpressRoute-kapcsolaton osztozó virtuális hálózatot állítanak társviszonyba, akkor a társhálózaton (vagyis az Azure gerinchálózatán) keresztül áramlik közöttük a forgalom. Az egyes virtuális hálózatok helyi átjárói ennek ellenére használhatók arra, hogy kapcsolatot létesítsenek a helyszíni kapcsolatcsoporttal. Közös átjárót is használhat, és átvitel konfigurálásával létesíthet kapcsolatot a helyszíni rendszerrel.

## <a name="permissions"></a>Engedélyek

A virtuális hálózatok közötti társviszony-létesítés jogosultsághoz van kötve. Külön funkció a VirtualNetworks névtérben. A felhasználók egy külön engedély megadásával jogosíthatók fel a társviszony-létesítésre. A virtuális hálózathoz írási-olvasási hozzáféréssel rendelkező felhasználó automatikusan örökli ezeket a jogokat.

Olyan felhasználó kezdeményezhet társviszony-létesítési műveletet egy másik virtuális hálózattal, aki rendszergazda, vagy fel van jogosítva a társviszony-létesítésre. A minimálisan szükséges jogosultság a Hálózati közreműködő. Ha a másik oldalon is van egy ennek megfelelő társviszony-létesítési kérelem, és ha teljesül a többi követelmény, létrejön a társviszony.

Ha például a myVirtualNetworkA és a myVirtualNetworkB nevű virtuális hálózatokat kapcsolja össze, a fióknak az alábbi minimális szintű szerepkörökkel vagy engedélyekkel kell rendelkeznie az egyes virtuális hálózatok esetében:

|Virtuális hálózat|Üzemi modell|Szerepkör|Engedélyek|
|---|---|---|---|
|myVirtualNetworkA|Resource Manager|[Hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klasszikus|[Klasszikus hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
|myVirtualNetworkB|Resource Manager|[Hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klasszikus|[Klasszikus hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

## <a name="monitor"></a>Figyelés

Két, Resource Managerrel létrehozott virtuális hálózat közötti társviszony létrehozása esetén mindkét érintett virtuális hálózathoz konfigurálni kell egy társítást. Nyomon követheti a társítási kapcsolat állapotát. A társítás állapota a következők állapotok egyike:

* **Kezdeményezve**: Ez az állapot jelenik meg, amikor létrehozza a társviszonyt az első virtuális hálózatról a második virtuális hálózat felé.
* **Csatlakoztatva**: Ez az állapot jelenik meg, amikor létrehozza a társviszonyt a második virtuális hálózatról az első virtuális hálózat felé. Az első virtuális hálózat társviszonyállapota *Kezdeményezve* értékről *Csatlakoztatva* értékre változott. A virtuális hálózatok társviszony-létesítése csak akkor történik meg sikeresen, ha mindkét virtuális hálózat társítási állapota *Csatlakoztatva* értékre változott.
* **Leválasztva**: Ez az állapot jelenik meg, ha töröl egy virtuális hálózatok közötti meglévő társviszony-létesítést.

## <a name="troubleshoot"></a>Hibaelhárítás

A meg kíván győződni a virtuális hálózatok közötti társviszony-létesítés sikeres végrehajtásáról, a virtuális hálózat bármely alhálózatában elvégezheti a hálózati adapter [érvényes útvonalainak ellenőrzését](virtual-network-routes-troubleshoot-portal.md). Ha létezik a virtuális hálózatok közötti társviszony, a virtuális hálózat összes alhálózata *Virtuális hálózatok közötti társviszony* következő ugrási típusú útvonalakkal rendelkezik minden virtuális társhálózat minden címterében.

A virtuális társhálózatokban lévő virtuális gépek kapcsolatának hibaelhárításához használhatja a Network Watcher [kapcsolat-ellenőrzési funkcióját](../network-watcher/network-watcher-connectivity-portal.md). A kapcsolat ellenőrzése során megtekintheti az adatforgalmat a forrás és a cél virtuális gépek hálózati adapterei között.

## <a name="limits"></a>Korlátok

Az egy virtuális hálózat esetében létesíthető társviszonyok mennyiségére korlátozás vonatkozik. További információkért lásd az [Azure hálózati korlátozásait](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="pricing"></a>Díjszabás

Egy névleges díj vonatkozik a társviszony-kapcsolatot használó bejövő és kimenő forgalomra. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/virtual-network) olvasható.

## <a name="next-steps"></a>Következő lépések

* Végezzen el egy oktatóanyagot a virtuális hálózatok közötti társviszony létesítéséhez. A virtuális hálózatok közötti társviszony az azonos vagy eltérő előfizetésekben lévő, azonos vagy eltérő üzembehelyezési modelleken keresztül létrehozott virtuális hálózatok között jön létre. Végezzen el egy oktatóanyagot a következő forgatókönyvek egyikéhez:

    |Azure üzembehelyezési modell  | Előfizetés  |
    |---------|---------|
    |Mindkét Resource Manager |[Ugyanaz](virtual-network-create-peering.md)|
    | |[Különböző](create-peering-different-subscriptions.md)|
    |Egy Resource Manager, egy klasszikus     |[Ugyanaz](create-peering-different-deployment-models.md)|
    | |[Különböző](create-peering-different-deployment-models-subscriptions.md)|

* További információ a [küllős hálózati topológiák](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering) létrehozásáról.
* További információ az összes [virtuális hálózati társviszony beállításról és azok módosításáról](virtual-network-manage-peering.md)
