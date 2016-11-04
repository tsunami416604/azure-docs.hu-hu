
---
title: Társviszony kialakítása virtuális hálózatok között az Azure-ban | Microsoft Docs
description: A cikk ismerteti, hogyan alakítható ki társviszony az Azure-beli virtuális hálózatok között.
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn

ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/28/2016
ms.author: narayan

---
# Társviszony létesítése virtuális hálózatok között
A virtuális hálózatok közötti társviszony olyan technikai lehetőség, amellyel az Azure gerinchálózatán keresztül összekapcsolható két – azonos régión belüli – virtuális hálózat. A társviszony kialakítását követően a két virtuális hálózat a csatlakozás minden szempontjából egyetlen hálózatnak látszik. A kezelésük továbbra is külön erőforrásként történik, de az ezekbe a virtuális hálózatokba tartozó virtuális gépek közvetlenül, magánhálózati IP-címekkel kommunikálhatnak egymással.

A virtuális társhálózatokba tartozó virtuális gépek közötti forgalmat lényegében ugyanúgy továbbítja a rendszer az Azure infrastruktúráján keresztül, mint az azonos virtuális hálózatba tartozó virtuális gépek között. A virtuális társhálózatok előnyei egyebek között a következők:

* Kis késésű, nagy sávszélességű kapcsolat jön létre eltérő virtuális hálózatokba tartozó erőforrások között.
* A virtuális társhálózatokban lévő erőforrások (például hálózati berendezések és VPN-átjárók) átviteli pontként használhatók.
* Az Azure Resource Manager modellre épülő virtuális hálózatok összekapcsolhatók a klasszikus üzemi modellre épülő virtuális hálózatokkal, ezáltal korlátozás nélküli kapcsolat alakítható ki a kétféle virtuális hálózat erőforrásai között.

A virtuális hálózatok közötti társviszony-létesítés követelményei és fontos szempontjai:

* A társviszonyba állítandó két virtuális hálózatnak ugyanabba az Azure-régióba kell tartoznia.
* A társviszonyba állítandó két virtuális hálózat IP-címtere nem fedheti át egymást.
* A virtuális hálózati társviszony két virtuális hálózat között jön létre, nem származik belőle tranzitív kapcsolat. Vagyis ha például az A virtuális hálózatot társviszonyba állítják B virtuális hálózattal, és a B hálózatot társviszonyba állítják a C virtuális hálózattal, az nem eredményezi azt, hogy társviszony jön létre az A virtuális hálózat és a C virtuális hálózat között.
* Társviszony két különböző előfizetésben található két virtuális hálózat között létesíthető, ha a társviszony-létesítést mindkét előfizetés esetén engedélyezi egy rendszergazdai engedéllyel rendelkező felhasználó, és az előfizetések ugyanahhoz az Active Directory-bérlőhöz vannak társítva. 
* Egy a Resource Manager modellre épülő virtuális hálózatok összekapcsolható egy ugyanilyen modellre épülő virtuális hálózattal, illetve egy a klasszikus üzembe helyezési modellre épülő virtuális hálózattal. Két, a klasszikus üzembe helyezési modellt használó virtuális hálózat azonban nem hozható egymással társviszonyba.
* A virtuális társhálózatokba tartozó gépek kommunikációjára ugyan nem vonatkoznak sávszélesség-korlátozások, a virtuális gépek méretén alapuló sávszélességhatár azonban érvényben marad.

![Alapszintű virtuális társhálózati viszony](./media/virtual-networks-peering-overview/figure01.png)

## Kapcsolatok
Ha társviszony jött létre két virtuális hálózat között, akkor a virtuális hálózatba tartozó (webes/feldolgozói szerepkörű) virtuális gépek közvetlenül tudnak kommunikálni a virtuális társhálózatba tartozó többi virtuális géppel. A két hálózat között teljes körű IP-szintű kapcsolat áll fenn.

A virtuális társhálózatok közötti körbejárási késés megegyezik a helyi virtuális hálózaton belüli körbejárási késéssel. A hálózat átbocsátóképessége attól függ, hogy milyen, a virtuális gépekkel arányos sávszélesség van engedélyezve. Más korlátozás nem vonatkozik a sávszélességre.

A virtuális társhálózatok közötti forgalom közvetlenül az Azure háttér-infrastruktúráján halad át, nem pedig átjárón.

Az egyik virtuális hálózatba tartozó virtuális gépek elérhetik a virtuális társhálózat belső terheléselosztásos (ILB-) végpontjait. Szükség esetén bármelyik virtuális hálózatban alkalmazhatók hálózati biztonsági csoportok (NSG) a más virtuális hálózatok vagy alhálózatok elérésének blokkolására.

A felhasználók a társviszony konfigurálásakor eldönthetik, hogy megnyitják vagy lezárják a két virtuális hálózat közötti NSG-szabályokat. Ha a felhasználó úgy dönt, hogy teljes körű kapcsolatra nyit lehetőséget a virtuális társhálózatok között (ez az alapértelmezett beállítás), akkor az NSG-k segítségével konkrét alhálózatokra vagy virtuális gépekre vonatkozóan blokkolhatja vagy megtagadhatja a megadott típusú hozzáférést.

Az Azure által a virtuális gépekre vonatkozóan biztosított belső DNS-névfeloldás nem működik két társviszonyban álló virtuális hálózat között. A virtuális gépeknek olyan belső DNS-nevük van, amelyet csak a helyi virtuális hálózaton belül lehet feloldani. A felhasználók azonban konfigurálhatnak olyan virtuális gépeket, amelyek virtuális hálózati DNS-kiszolgálóként működnek a virtuális társhálózatokon belül.

## Szolgáltatásláncolás
A felhasználók olyan útvonaltáblákat konfigurálhatnak, amelyekben a társviszonyban álló virtuális hálózatba tartozó virtuális gépek vannak megadva a „következő ugrás” IP-címeként, amint az a cikk későbbi részében szereplő diagramon látható. Ezáltal a felhasználók szolgáltatóláncolást érhetnek el, vagyis a felhasználó által meghatározott útvonaltáblák segítségével átirányíthatják az egyik virtuális hálózatból a forgalmat egy a virtuális társhálózatban működő virtuális berendezésre.

A felhasználók jó eredménnyel alakíthatnak ki csillagpontos típusú környezetet is, amelyben a csillag középponti hálózatában infrastrukturális összetevők (például virtuális hálózati berendezések) működhetnek. A csillag csúcsain lévő virtuális hálózatok ezt követően társhálózati viszonyba hozhatók a központi hálózattal, illetve a forgalom egy része átirányítható a központi virtuális hálózaton futó berendezésre. A virtuális hálózatok közötti társhálózati viszonnyal lehetőség nyílik arra, hogy a „felhasználó által definiált útvonaltáblában” egy virtuális társhálózat egyik virtuális gépének IP-címe legyen a következő ugrás IP-címe.

## Átjárók és kapcsolat helyszíni rendszerekkel
Minden virtuális hálózatnak – függetlenül attól, hogy társviszonyban áll-e más virtuális hálózattal – lehet saját átjárója, és használhatja helyszíni rendszerhez való csatlakozásra. A felhasználók [két virtuális hálózat közötti kapcsolatot](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) is konfigurálhatnak átjárók segítségével, még abban az esetben is, ha a virtuális hálózatok társviszonyban állnak.

Ha a virtuális hálózatok mindkét összekapcsolási módszere konfigurálva van, a virtuális hálózatok közötti forgalom a társhálózati konfiguráción keresztül (vagyis az Azure gerinchálózatán keresztül) zajlik.

Ha két virtuális hálózat között társhálózati viszony van, a felhasználók a virtuális társhálózatban működő átjárót is konfigurálhatnak átviteli pontként a helyszíni rendszerek felé. Ebben az esetben a távoli átjárót használó virtuális hálózatnak nem lehet saját átjáróra. Egy virtuális hálózatnak csak egy átjárója lehet. Ez lehet helyi átjáró vagy (virtuális társhálózatban működő) távoli átjáró, amint a következő képen látható.

Az átjáróval történő adatátvitel nem támogatott abban az esetben, ha olyan virtuális hálózatok állnak egymással társviszonyban, amelyek közül az egyik a Resource Manager-modellt használja, a másik pedig a klasszikus üzemi modellt. Ahhoz, hogy az átjárón át történő átvitel működjön, a társviszonyban álló virtuális hálózatok közül mindkettőnek a Resource Manager üzemi modellt kell használnia.

Ha két, ugyanazon az Azure ExpressRoute-kapcsolaton osztozó virtuális hálózatot állítanak társviszonyba, akkor a társhálózaton (vagyis az Azure gerinchálózatán) keresztül áramlik közöttük a forgalom. A felhasználók ennek ellenére használhatják az egyes virtuális hálózatok helyi átjáróit arra, hogy kapcsolatot létesítsenek a helyszíni kapcsolatcsoporttal. Közös átjárót is használhatnak, és átvitel konfigurálásával létesíthetnek kapcsolatot a helyszíni rendszerrel.

![Átvitel virtuális társhálózat esetén](./media/virtual-networks-peering-overview/figure02.png)

## Kiépítés
A virtuális hálózatok közötti társviszony-létesítés jogosultsághoz van kötve. Külön funkció a VirtualNetworks névtérben. A felhasználók egy külön engedély megadásával jogosíthatók fel a társviszony-létesítésre. A virtuális hálózathoz írási-olvasási hozzáféréssel rendelkező felhasználó automatikusan örökli ezeket a jogokat.

Olyan felhasználó kezdeményezhet társviszony-létesítési műveletet egy másik virtuális hálózattal, aki rendszergazda, vagy fel van jogosítva a társviszony-létesítésre. Ha a másik oldalon is van egy ennek megfelelő társviszony-létesítési kérelem, és ha teljesül a többi követelmény, létrejön a társviszony.

A „Következő lépések” című szakaszban található cikkek részletesebb tájékoztatást nyújtanak arról, hogy miképpen lehet virtuális társhálózati viszonyba hozni egymással két virtuális hálózatot.

## Korlátok
Az egy virtuális hálózat esetében létesíthető társviszonyok mennyiségére korlátozás vonatkozik. Erről az [Azure hálózati korlátozásait](../azure-subscription-service-limits.md#networking-limits) ismertető cikk nyújt részletesebb tájékoztatást.

## Díjszabás
Az előzetes verzió idején ingyen lehet virtuális társhálózati viszonyt kialakítani. Kiadását követően névleges díj fog vonatkozni a társhálózati viszonyt használó bejövő és kimenő forgalomra vonatkozóan. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/virtual-network) olvasható.

## Következő lépések
* [Alakítson ki társviszonyt két virtuális hálózat között](virtual-networks-create-vnetpeering-arm-portal.md).
* Olvassa el az [NSG-k](virtual-networks-nsg.md) ismertetését.
* Olvassa el a [felhasználó által megadott útvonalakról és az IP-továbbításról](virtual-networks-udr-overview.md) szóló leírást.

<!--HONumber=Sep16_HO4-->


