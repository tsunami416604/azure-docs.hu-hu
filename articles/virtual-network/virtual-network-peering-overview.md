---
title: Társviszony kialakítása virtuális hálózatok között az Azure-ban | Microsoft Docs
description: Tudnivalók az Azure-beli virtuális hálózatok közötti társviszony-létesítésről
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: jdial
ms.openlocfilehash: 69264ac7ffe8124b964a10c11aacaca5c712fd63
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között

A virtuális hálózatok közötti társviszony lehetővé teszi a [virtuális hálózatok](virtual-networks-overview.md) zökkenőmentes összekapcsolását. A társviszony kialakítását követően a virtuális hálózatok a csatlakozás szempontjából egyetlen hálózatnak látszanak. A virtuális társhálózatokba tartozó virtuális gépek közötti forgalmat lényegében ugyanúgy továbbítja a rendszer a Microsoft gerincinfrastruktúráján keresztül, ahogyan az azonos virtuális hálózatba tartozó virtuális gépek közötti forgalmat, *magánhálózati* IP-címeken keresztül. 

A virtuális társhálózatok használatának előnyei:

* A társított virtuális hálózatok közti hálózati adatforgalom nem nyilvános. A virtuális hálózatok közötti forgalom a Microsoft gerinchálózatán belül marad. A virtuális hálózatok közti forgalomhoz nincs szükség nyilvános internetre, átjárókra vagy titkosításra.
* Kis késésű, nagy sávszélességű kapcsolat jön létre eltérő virtuális hálózatokba tartozó erőforrások között.
* Az egyes virtuális hálózatokban lévő erőforrások a hálózatok társítását követően képesek más virtuális hálózatokban lévő erőforrásokkal kommunikálni.
* Adatok továbbíthatók Azure-előfizetések, üzemi modellek és Azure-régiók között.
* Létrehozható társviszony az Azure Resource Managerrel létrehozott virtuális hálózatok között, vagy egy, a Resource Managerrel létrehozott virtuális hálózat és egy, a klasszikus üzemi modellel létrehozott virtuális hálózat között. Az Azure üzembehelyezési modellekkel kapcsolatos további információkért lásd: [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Ez nem okoz leállást egyik virtuális hálózat erőforrásaiban sem a társításkor és azt követően sem.

## <a name="connectivity"></a>Kapcsolatok

Ha társviszony jött létre a virtuális hálózatok között, akkor a virtuális hálózatokba tartozó erőforrások közvetlenül tudnak kommunikálni a virtuális társhálózat erőforrásaival.

Az azonos régióban lévő virtuális társhálózaton belüli virtuális gépek közötti hálózati késés megegyezik az egyetlen virtuális hálózaton belüli hálózati késéssel. A hálózat átbocsátóképessége attól függ, hogy milyen, a virtuális gépek méretével arányos sávszélesség van engedélyezve. A társviszonyon belül más korlátozás nem vonatkozik a sávszélességre.

A virtuális társhálózatokon belüli virtuális gépek közötti forgalom közvetlenül a Microsoft gerincinfrastruktúráján halad át, nem pedig átjárón vagy a nyilvános interneten.

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

Az átjáróval történő adatátvitel nem támogatott abban az esetben, ha olyan virtuális hálózatok állnak egymással társviszonyban, amelyek különböző üzemi modellel vagy eltérő régiókban lettek létrehozva. Ahhoz, hogy az átjárón át történő átvitel működjön, a társviszonyban álló virtuális hálózatokat a Resource Manager-alapú üzemi modellel kell létrehozni, és mindkettőnek ugyanabban a régióban kell lennie.

Ha két, ugyanazon az Azure ExpressRoute-kapcsolaton osztozó virtuális hálózatot állítanak társviszonyba, akkor a társhálózaton (vagyis az Azure gerinchálózatán) keresztül áramlik közöttük a forgalom. Az egyes virtuális hálózatok helyi átjárói ennek ellenére használhatók arra, hogy kapcsolatot létesítsenek a helyszíni kapcsolatcsoporttal. Közös átjárót is használhat, és átvitel konfigurálásával létesíthet kapcsolatot a helyszíni rendszerrel.

## <a name="troubleshoot"></a>Hibaelhárítás

A meg kíván győződni a virtuális hálózatok közötti társviszony-létesítés sikeres végrehajtásáról, a virtuális hálózat bármely alhálózatában elvégezheti a hálózati adapter [érvényes útvonalainak ellenőrzését](virtual-network-routes-troubleshoot-portal.md). Ha létezik a virtuális hálózatok közötti társviszony, a virtuális hálózat összes alhálózata *Virtuális hálózatok közötti társviszony* következő ugrási típusú útvonalakkal rendelkezik minden virtuális társhálózat minden címterében.

A virtuális társhálózatokban lévő virtuális gépek kapcsolatának hibaelhárításához használhatja a Network Watcher [kapcsolat-ellenőrzési funkcióját](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A kapcsolat ellenőrzése során megtekintheti az adatforgalmat a forrás és a cél virtuális gépek hálózati adapterei között.

## <a name="requirements-and-constraints"></a>Követelmények és korlátozások

További információ a követelményekről és a korlátozásokról: [A virtuális hálózatok közötti társviszony követelményei és korlátozásai](virtual-network-manage-peering.md#requirements-and-constraints). A virtuális hálózatok közötti társviszonyok számának korlátozásairól az [Azure hálózati korlátait](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) ismertető szakaszban talál további információt. 

## <a name="permissions"></a>Engedélyek

További információ a virtuális hálózatok közötti társviszonyok létrehozásához szükséges engedélyekről: [Virtuális hálózatok közötti társviszonyra vonatkozó engedélyek](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Díjszabás

Egy névleges díj vonatkozik a társviszony-kapcsolatot használó bejövő és kimenő forgalomra. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/virtual-network) olvasható.

## <a name="next-steps"></a>További lépések

* A virtuális hálózatok közötti társviszony az azonos vagy eltérő előfizetésekben lévő, azonos vagy eltérő üzembehelyezési modelleken keresztül létrehozott virtuális hálózatok között jön létre. Végezzen el egy oktatóanyagot a következő forgatókönyvek egyikéhez:

    |Azure üzembehelyezési modell             | Előfizetés  |
    |---------                          |---------|
    |Mindkét Resource Manager              |[Ugyanaz](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Különböző](create-peering-different-subscriptions.md)|
    |Egy Resource Manager, egy klasszikus  |[Ugyanaz](create-peering-different-deployment-models.md)|
    |                                   |[Különböző](create-peering-different-deployment-models-subscriptions.md)|

* További információ a [küllős hálózati topológiák létrehozásáról](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).
* További tudnivalók a [virtuális társhálózatok beállításairól és azok megváltoztatásáról](virtual-network-manage-peering.md).