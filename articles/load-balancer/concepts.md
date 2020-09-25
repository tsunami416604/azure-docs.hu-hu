---
title: Azure Load Balancer fogalmak
description: Azure Load Balancer fogalmak áttekintése
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 9765f685f2fccc9332a2f07d907aac415aa2c57f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333919"
---
# <a name="azure-load-balancer-concepts"></a>Azure Load Balancer fogalmak

A Load Balancer számos funkciót biztosít mind az UDP-, mind a TCP-alkalmazásokhoz. 

## <a name="load-balancing-algorithm"></a>Terheléselosztási algoritmus
Létrehozhat egy terheléselosztási szabályt, amellyel a rendszer átirányítja a forgalmat a felületről a háttérbeli készletbe. A Azure Load Balancer kivonatoló algoritmust használ a bejövő folyamatok (nem bájt) elosztásához. A terheléselosztó átírja a folyamatok fejléceit a háttérbeli készlet példányaiba. A kiszolgálók új folyamatok fogadására használhatók, ha az állapot-mintavétel állapota Kifogástalan.

Alapértelmezés szerint a Load Balancer egy öt rekordos kivonatot használ.

A kivonat a következőket tartalmazza:

- **Forrás IP-címe**
- **Forrásport**
- **Cél IP-cím**
- **Célport**
- **IP-protokoll száma, amellyel a folyamatokat le lehet képezni a rendelkezésre álló kiszolgálókra**

A forrás IP-címhez való affinitás két vagy három rekordos kivonat használatával jön létre. Ugyanannak a folyamatnak a csomagjai ugyanarra a példányra érkeznek, mint az elosztott terhelésű előtér. 

A forrásport akkor változik, ha az ügyfél egy új folyamatot indít el ugyanabból a forrás IP-címről. Ennek eredményeképpen az öt rekordos kivonat azt eredményezheti, hogy a forgalom egy másik háttér-végpontra mutat.
További információ: [Azure Load Balancer elosztási módjának konfigurálása](./load-balancer-distribution-mode.md).

A következő kép a kivonatalapú elosztást szemlélteti:

![Kivonatalapú elosztás](./media/load-balancer-overview/load-balancer-distribution.png)

*Ábra: Kivonatalapú elosztás*

## <a name="application-independence-and-transparency"></a>Az alkalmazások függetlensége és átláthatósága

A Load Balancer közvetlenül nem kommunikál a TCP-vagy UDP-vagy az alkalmazási réteggel. Bármely TCP-vagy UDP-alkalmazás támogatott lehet. A Load Balancer nem zárul le, vagy folyamatokból származik, vagy együttműködik a folyamat hasznos adataival. A Load Balancer nem biztosít Application Layer Gateway-funkciókat. A protokoll-kézfogások mindig közvetlenül az ügyfél és a háttérbeli készlet példánya között történnek. Egy beérkező folyamatra mindig egy virtuális géptől érkezik a válasz. Amikor a folyamat megérkezik a virtuális gépre, az eredeti IP-cím is megmarad.

* Minden végpontot egy virtuális gép válaszol. A TCP-kézfogás például az ügyfél és a kijelölt háttérbeli virtuális gép között történik. Az előtérre irányuló kérelemre adott válasz egy háttérbeli virtuális gép által generált válasz. Amikor sikeresen érvényesíti a kapcsolatot az előtérrel, a kapcsolat érvényesítése legalább egy háttérbeli virtuális gépen megtörtént.
* Az alkalmazás hasznos adatai transzparensek a terheléselosztó számára. Bármely UDP-vagy TCP-alkalmazás támogatott lehet.
* Mivel a terheléselosztó nem kommunikál a TCP-adattartalommal, és TLS-kiszervezést biztosít, átfogó titkosított forgatókönyveket hozhat létre. A terheléselosztó használatával a TLS-alkalmazásokhoz nagy léptékű kibővíthető teljesítmény áll a virtuális gépen. Például a TLS-munkamenet beírási kapacitását csak a háttérbeli készlethez hozzáadott virtuális gépek típusa és száma korlátozza.

## <a name="outbound-connections"></a>Kimenő kapcsolatok 

A háttér-készletből a nyilvános IP-címekre irányuló folyamatok le vannak képezve a felületre. Az Azure a terheléselosztási Kimenő szabály segítségével fordítja le a nyilvános előtéri IP-címet a kimenő kapcsolatokra. Ez a konfiguráció az alábbi előnyökkel jár. A szolgáltatások egyszerű frissítése és vész-helyreállítása, mivel az előtér dinamikusan képezhető le a szolgáltatás egy másik példányára. Egyszerűbb hozzáférés-vezérlési lista (ACL) kezelése. Az előtérbeli IP-címekként kifejezett ACL-ek nem változnak a szolgáltatások vertikális felskálázása vagy újbóli üzembe helyezése esetén. A kimenő kapcsolatok kisebb számú IP-címhez való lefordítása, mint a gépek, csökkenti a biztonságos címzettek listáinak megvalósításának terheit. További információ a forrás hálózati címfordításról (SNAT) és a Azure Load Balancerról: [SNAT és Azure Load Balancer](load-balancer-outbound-connections.md).

## <a name="availability-zones"></a>Rendelkezésre állási zónák 

A standard Load Balancer támogatja a további képességeket azokban a régiókban, ahol elérhetők a Availability Zones. Availability Zones konfigurációk mindkét típusú standard Load Balancer számára elérhetők; nyilvános és belső. Egy zóna-redundáns frontend a zóna meghibásodását a dedikált infrastruktúra használatával, egyszerre az összes zónában megmaradja. Emellett egy adott zónához is garantálhatja a felületet. A zónákhoz tartozó előtérbeli felületet egyetlen zónában dedikált infrastruktúra szolgálja ki. A többzónás terheléselosztás elérhető a háttér-készlethez. Egy virtuális hálózat bármely virtuálisgép-erőforrása egy háttér-készlet része lehet. Az alapszintű Load Balancer nem támogatja a zónákat. További információkért tekintse át [Availability Zones kapcsolódó képességeinek részletes megvitatását](load-balancer-standard-availability-zones.md) és [Availability Zones áttekintését](../availability-zones/az-overview.md) .

## <a name="ha-ports"></a>HA portok

Konfigurálhatja a HA port terheléselosztási szabályait, hogy az alkalmazás méretezése és megbízhatósága megbízható legyen. Ezek a szabályok a belső Load Balancer előtér-IP-címének rövid élettartamú portjain áramlanak be terheléselosztással. A funkció akkor hasznos, ha nem praktikus vagy nemkívánatos az egyes portok megadására. A HA-portok szabály lehetővé teszi, hogy aktív-passzív vagy aktív-aktív n + 1 forgatókönyvet hozzon létre. Ezek a forgatókönyvek a hálózati virtuális berendezésekhez és minden olyan alkalmazáshoz szükségesek, amelyhez a bejövő portok nagy tartománya szükséges. Az állapot-mintavétel segítségével meghatározható, hogy mely háttérrendszer kapjon új folyamatokat.  A porttartomány-forgatókönyvek emulálása hálózati biztonsági csoporttal végezhető el. Az alapszintű Load Balancer nem támogatja a HA portokat. Tekintse át [a ha-portok részletes megvitatását](load-balancer-ha-ports-overview.md).

## <a name="multiple-frontends"></a>Több előtérrendszer 

A Load Balancer több előtérbeli felülettel rendelkező több szabályt is támogat.  Standard Load Balancer kibontja ezt a képességet a kimenő forgatókönyvek esetében. A kimenő szabályok egy bejövő szabály inverzét jelentik. A Kimenő szabály társítást hoz létre a kimenő kapcsolatokhoz. A standard Load Balancer egy terheléselosztási szabályon keresztül a virtuális gép erőforrásaihoz társított összes előtérbeli felületet használja. Emellett a terheléselosztási szabály egyik paramétere is lehetővé teszi egy terheléselosztási szabály letiltását a kimenő kapcsolatok esetében, ami lehetővé teszi az adott előtérbeli elemek (például a none) kijelölését. Összehasonlítás esetén az alapszintű Load Balancer véletlenszerűen választja ki egyetlen előtérbeli felületet. Nincs lehetőség arra, hogy melyik előtér-felületet választotta.

## <a name="floating-ip"></a>Nem fix IP-cím

Egyes alkalmazási forgatókönyvek előnyben részesítettek, vagy megkövetelik, hogy ugyanazt a portot használja több alkalmazás-példány a háttér-készletben lévő egyetlen virtuális gépen. A portok használatának gyakori példái közé tartozik a fürtözés a magas rendelkezésre álláshoz, a hálózati virtuális berendezések, valamint a több TLS-végpont ismételt titkosítás nélküli kimutatása. Ha több szabályban szeretné felhasználni a háttér-portot, akkor a szabály definíciójában engedélyeznie kell a lebegőpontos IP-címet.

A **lebegőpontos IP-cím** az Azure terminológiai része, amely a Direct Server Return (DSR) néven ismert részét képezi. A DSR két részből áll: 

- Folyamat topológiája
- IP-cím-hozzárendelési séma

A platform szintjén Azure Load Balancer mindig a DSR-folyamat topológiájában működik, függetlenül attól, hogy engedélyezve van-e a lebegőpontos IP-cím. Ez azt jelenti, hogy a folyamat kimenő részét mindig közvetlenül a forráshoz kell visszaírni a folyamatba.
A lebegő IP-címek nélkül az Azure egy hagyományos terheléselosztási IP-cím-hozzárendelési sémát tesz elérhetővé a könnyű használat érdekében (a virtuálisgép-példányok IP-címe). A lebegőpontos IP-cím engedélyezése a terheléselosztó előtérbeli IP-címére való leképezést a további rugalmasság érdekében módosítja. További információ [itt](load-balancer-multivip-overview.md).


## <a name="limitations"></a><a name = "limitations"></a>Korlátozások

- A belső terheléselosztási forgatókönyvek vagy a nyilvános terheléselosztási forgatókönyvek esetében jelenleg nem támogatott a lebegőpontos IP-címek használata a másodlagos IP-konfigurációk esetében.

- Egy terheléselosztó-szabály nem terjedhet ki két virtuális hálózatra.  A frontendeknek és a háttérbeli példányoknak ugyanabban a virtuális hálózatban kell lenniük.  

- A virtuális hálózat és más Microsoft-Platform nélküli webes feldolgozói szerepkörök csak a szabványos belső terheléselosztó mögötti példányok számára érhetők el. Ne támaszkodjon erre a kisegítő lehetőségre, mert a saját maga vagy az alapul szolgáló platform értesítés nélkül megváltozhat. Ha standard belső terheléselosztó használatakor kimenő kapcsolatra van szükség, konfigurálni kell a [kimenő kapcsolatot](load-balancer-outbound-connections.md) .

- A Load Balancer terheléselosztást és port továbbítást biztosít bizonyos TCP-vagy UDP-protokollokhoz. A terheléselosztási szabályok és a bejövő NAT-szabályok támogatják a TCP és az UDP használatát, de nem más IP-protokollok, beleértve az ICMP-t is.

- A háttérbeli virtuális gép kimenő folyamata egy belső Load Balancer felületére sikertelen lesz.

- A továbbítási IP-töredékek nem támogatottak a terheléselosztási szabályokban. Az UDP-és TCP-csomagok IP-töredezettsége nem támogatott a terheléselosztási szabályokban. HA portok terheléselosztási szabályai használhatók a meglévő IP-töredékek továbbítására. További információ: [magas rendelkezésre állású portok – áttekintés](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Következő lépések

- Lásd: [nyilvános standard Load Balancer létrehozása](quickstart-load-balancer-standard-public-portal.md) a Load Balancer használatának megkezdéséhez: hozzon létre egyet, hozzon létre virtuális gépeket egy egyéni IIS-bővítménnyel, és a virtuális gépek között a webalkalmazás terheléselosztását.
- További információ a [Azure Load Balancer kimenő kapcsolatokról](load-balancer-outbound-connections.md).
- További információ a [Azure Load Balancerról](load-balancer-overview.md).
- Tudnivalók az [állapot](load-balancer-custom-probe-overview.md)-mintavételekről.
- Ismerje meg a [standard Load Balancer diagnosztikát](load-balancer-standard-diagnostics.md).
- További információ a [hálózati biztonsági csoportokról](../virtual-network/security-overview.md).
