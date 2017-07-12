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
ms.date: 06/06/2017
ms.author: narayan
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: bfba85160cd02baa337881653dbe6582d10ba073
ms.contentlocale: hu-hu
ms.lasthandoff: 06/28/2017


---
<a id="virtual-network-peering" class="xliff"></a>

# Társviszony létesítése virtuális hálózatok között
A virtuális hálózatok közötti társviszony lehetővé teszi, hogy az Azure gerinchálózatán keresztül összekapcsoljon két – azonos régión belüli – virtuális hálózatot. A társviszony kialakítását követően a két virtuális hálózat a csatlakozás szempontjából egyetlen hálózatnak látszik. A két virtuális hálózat kezelése továbbra is külön erőforrásként történik, de a virtuális társhálózatokba tartozó virtuális gépek közvetlenül, magánhálózati IP-címekkel kommunikálhatnak egymással.

A virtuális társhálózatokba tartozó virtuális gépek közötti forgalmat lényegében ugyanúgy továbbítja a rendszer az Azure infrastruktúráján keresztül, mint az azonos virtuális hálózatba tartozó virtuális gépek között. A virtuális társhálózatok előnyei egyebek között a következők:

* Kis késésű, nagy sávszélességű kapcsolat jön létre eltérő virtuális hálózatokba tartozó erőforrások között.
* A virtuális társhálózatokban lévő erőforrások (például hálózati berendezések és VPN-átjárók) átviteli pontként használhatók.
* Két virtuális hálózat közötti társviszony létrehozása az Azure Resource Manager-alapú üzemi modellel vagy egy, a Resource Managerrel létrehozott virtuális hálózat és egy, a klasszikus üzemi modellel létrehozott virtuális hálózat társviszonyba állítása. A két Azure üzembehelyezési modell közti különbségekkel kapcsolatos további információkért olvassa el [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című cikket.

A virtuális hálózatok közötti társviszony-létesítés követelményei és fontos szempontjai:

* A virtuális társhálózatoknak ugyanabban az Azure-régióban kell lenniük. Különböző Azure-régióban lévő virtuális hálózatok [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) használatával kapcsolhatók össze.
* A virtuális társhálózatok IP-címterei nem fedhetik egymást.
* Miután egy virtuális hálózat társviszonyba lépett egy másik virtuális hálózattal, nem adható hozzá és nem törölhető belőle címtér.
* A virtuális hálózati társviszony két virtuális hálózat között jön létre. A társviszonyok nem adódnak tovább tranzitív módon. Ha például virtualisHalozatA társviszonyban áll virtualisHalozatB-vel, virtualisHalozatB pedig virtualisHalozatC-vel, ebből *nem* következik, hogy virtualisHalozatA társviszonyban áll virtualisHalozatC-vel.
* Társviszonyt létesíthet két különböző előfizetésben található virtuális hálózat között, ha a társviszony-létesítést mindkét előfizetés esetében engedélyezi egy rendszergazdai engedéllyel rendelkező felhasználó, és az előfizetések ugyanahhoz az Azure Active Directory-bérlőhöz vannak társítva. Különböző Active Directory-bérlőkhöz társított előfizetésekben található virtuális hálózatok között [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) segítségével létesíthető társviszony.
* Két virtuális hálózat társviszonyba állítható, ha mindkettő a Resource Manager-alapú üzemi modellel lett létrehozva, vagy ha az egyik a Resource Manager-alapú üzemi modellel, a másik pedig a klasszikus üzemi modellel lett létrehozva. Két, a klasszikus üzembehelyezési modellel létrehozott virtuális hálózat azonban nem hozható egymással társviszonyba. Ha különböző üzembehelyezési modellekkel létrehozott virtuális hálózatok között szeretne társviszonyt létesíteni, akkor a virtuális hálózatoknak *ugyanabban* az előfizetésben kell lenniük. A *különböző* előfizetésekhez tartozó, különböző üzembehelyezési modellel létrehozott virtuális hálózatok társviszonyba állítása **előzetes** verzióban érhető el. További részleteket a [Társviszony-létesítés virtuális hálózatok között](virtual-network-create-peering.md#different-subscriptions-different-deployment-models) című részben talál.
* Bár a virtuális társhálózatokba tartozó virtuális gépek kommunikációjára nem vonatkoznak külön sávszélesség-korlátozások, a virtuális gépekre méretalapú sávszélességkorlát érvényes. Ha többet szeretne megtudni a különböző méretű virtuális gépek maximális hálózati sávszélességével kapcsolatban, olvassa el a [windowsos](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy a [linuxos](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek méretéről szóló cikkeket.

![Társviszony létesítése virtuális hálózatok között alapszinten](./media/virtual-networks-peering-overview/figure01.png)

<a id="connectivity" class="xliff"></a>

## Kapcsolatok
Ha társviszony jött létre két virtuális hálózat között, akkor a virtuális hálózatba tartozó virtuális gépek vagy Cloud Services-szerepkörök közvetlenül tudnak kommunikálni a virtuális társhálózatba tartozó többi erőforrással. A két virtuális hálózat között teljes körű IP-szintű kapcsolat áll fenn.

A virtuális társhálózaton belüli két virtuális közötti adatváltási késés megegyezik az egyetlen virtuális hálózaton belüli adatváltási késéssel. A hálózat átbocsátóképessége attól függ, hogy milyen, a virtuális gépek méretével arányos sávszélesség van engedélyezve. A társviszonyon belül más korlátozás nem vonatkozik a sávszélességre.

A virtuális társhálózatok közötti forgalom közvetlenül az Azure háttér-infrastruktúráján halad át, nem pedig átjárón.

Virtuális hálózathoz csatlakozó virtuális gépek elérhetik a virtuális társhálózat belső terheléselosztásos végpontjait. Szükség esetén bármelyik virtuális hálózatban alkalmazhatók hálózati biztonsági csoportok a más virtuális hálózatok vagy alhálózatok elérésének blokkolására.

Virtuális hálózatok társviszonyának konfigurálásakor megnyithatja vagy lezárhatja a hálózati biztonsági csoportszabályokat a virtuális hálózatok között. Ha úgy dönt, hogy teljes körű kapcsolatra nyit lehetőséget a virtuális társhálózatok között (ez az alapértelmezett beállítás), akkor a hálózati biztonsági csoportok segítségével konkrét alhálózatokra vagy virtuális gépekre vonatkozóan blokkolhatja vagy megtagadhatja a megadott típusú hozzáférést. A hálózati biztonsági csoportokról további információt tartalmaz a [Hálózati biztonsági csoportok áttekintése](virtual-networks-nsg.md) című cikk.

Az Azure által a virtuális gépekre vonatkozóan biztosított belső DNS-névfeloldás nem működik két társviszonyban álló virtuális hálózat között. A virtuális gépeknek olyan belső DNS-nevük van, amelyet csak a helyi virtuális hálózaton belül lehet feloldani. Konfigurálhatók azonban olyan virtuális gépek is, amelyek virtuális hálózati DNS-kiszolgálóként kapcsolódnak a társkapcsolatban álló virtuális hálózatokhoz. További részleteket a [Névfeloldás saját DNS-kiszolgáló használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) című cikkben talál.

<a id="service-chaining" class="xliff"></a>

## Szolgáltatásláncolás
Konfigurálhatók olyan útvonalak, amelyekben a társviszonyban álló virtuális hálózatba tartozó virtuális gépek vannak megadva a „következő ugrás” IP-címeként, lehetővé téve a szolgáltatásláncolást. A szolgáltatásláncolás lehetővé teszi a forgalomnak a felhasználó által meghatározott útvonalon történő átirányítását egy virtuális hálózatból a virtuális társhálózatban működő virtuális berendezésre.

Jó eredménnyel alakíthat ki csillagpontos típusú környezetet is, amelyben a csillag középponti hálózatában infrastrukturális összetevők (például virtuális hálózati berendezések) működhetnek. Az ágakon lévő virtuális hálózatok társhálózatai lehetnek a középponti hálózatnak. Az adatforgalom áthaladhat a középponti virtuális hálózatban futó virtuális hálózati berendezéseken. A virtuális hálózatok közötti társhálózati viszonnyal lehetőség nyílik arra, hogy a felhasználó által definiált útvonalon egy virtuális társhálózat egyik virtuális gépének IP-címe legyen a következő ugrás IP-címe. A felhasználó által megadott útvonalakkal kapcsolatos további információkat talál a [felhasználó által megadott útvonalakat áttekintő](virtual-networks-udr-overview.md) cikkben.

<a id="gateways-and-on-premises-connectivity" class="xliff"></a>

## Átjárók és kapcsolat helyszíni rendszerekkel
Minden virtuális hálózatnak – függetlenül attól, hogy társviszonyban áll-e más virtuális hálózattal – lehet saját átjárója, és használhatja egy helyi hálózathoz való csatlakozásra. [Két virtuális hálózat közötti kapcsolat](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) is konfigurálható átjárók segítségével, még abban az esetben is, ha a virtuális hálózatok társviszonyban állnak.

Ha a virtuális hálózatok mindkét összekapcsolási módszere konfigurálva van, a virtuális hálózatok közötti forgalom a társhálózati konfiguráción keresztül (vagyis az Azure gerinchálózatán keresztül) zajlik.

Ha két virtuális hálózat között társhálózati viszony van, a virtuális társhálózatban működő átjáró is konfigurálható átviteli pontként a helyi hálózat felé. Ebben az esetben a távoli átjárót használó virtuális hálózatnak nem lehet saját átjáróra. Egy virtuális hálózatnak csak egy átjárója lehet. Az átjáró lehet helyi vagy távoli (virtuális társhálózatban működő), amint a következő képen látható:

![Átvitel virtuális társhálózat esetén](./media/virtual-networks-peering-overview/figure02.png)

Az átjáróval történő adatátvitel nem támogatott abban az esetben, ha olyan virtuális hálózatok állnak egymással társviszonyban, amelyek különböző üzembehelyezési modellel lettek létrehozva. Ahhoz, hogy az átjárón át történő átvitel működjön, a társviszonyban álló virtuális hálózatok közül mindkettőt a Resource Manager-alapú üzemi modellel kell létrehozni.

Ha két, ugyanazon az Azure ExpressRoute-kapcsolaton osztozó virtuális hálózatot állítanak társviszonyba, akkor a társhálózaton (vagyis az Azure gerinchálózatán) keresztül áramlik közöttük a forgalom. Az egyes virtuális hálózatok helyi átjárói ennek ellenére használhatók arra, hogy kapcsolatot létesítsenek a helyszíni kapcsolatcsoporttal. Közös átjárót is használhat, és átvitel konfigurálásával létesíthet kapcsolatot a helyszíni rendszerrel.

<a id="provisioning" class="xliff"></a>

## Kiépítés
A virtuális hálózatok közötti társviszony-létesítés jogosultsághoz van kötve. Külön funkció a VirtualNetworks névtérben. A felhasználók egy külön engedély megadásával jogosíthatók fel a társviszony-létesítésre. A virtuális hálózathoz írási-olvasási hozzáféréssel rendelkező felhasználó automatikusan örökli ezeket a jogokat.

Olyan felhasználó kezdeményezhet társviszony-létesítési műveletet egy másik virtuális hálózattal, aki rendszergazda, vagy fel van jogosítva a társviszony-létesítésre. Ha a másik oldalon is van egy ennek megfelelő társviszony-létesítési kérelem, és ha teljesül a többi követelmény, létrejön a társviszony.

<a id="limits" class="xliff"></a>

## Korlátok
Az egy virtuális hálózat esetében létesíthető társviszonyok mennyiségére korlátozás vonatkozik. Erről az [Azure hálózati korlátozásait](../azure-subscription-service-limits.md#networking-limits) ismertető cikk nyújt részletesebb tájékoztatást.

<a id="pricing" class="xliff"></a>

## Díjszabás
Egy névleges díj vonatkozik a társhálózati viszonyt használó bejövő és kimenő forgalomra. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/virtual-network) olvasható.

## <a name="next-steps"></a>Következő lépések

* A [Társviszony kialakítása virtuális hálózatok között](virtual-network-create-peering.md) oktatóanyag befejezése
* További tudnivalók a [virtuális társhálózatok beállításairól és azok megváltoztatásáról](virtual-network-manage-peering.md).

