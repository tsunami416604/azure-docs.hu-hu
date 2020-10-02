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
ms.openlocfilehash: 61620a8497765c4d8f90a3d616bd2f4b932f8dcc
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629029"
---
# <a name="azure-load-balancer-algorithm"></a>Azure Load Balancer algoritmus

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

- Minden végpontot egy virtuális gép válaszol. A TCP-kézfogás például az ügyfél és a kijelölt háttérbeli virtuális gép között történik. Az előtérre irányuló kérelemre adott válasz egy háttérbeli virtuális gép által generált válasz. Amikor sikeresen érvényesíti a kapcsolatot az előtérrel, a kapcsolat érvényesítése legalább egy háttérbeli virtuális gépen megtörtént.
- Az alkalmazás hasznos adatai transzparensek a terheléselosztó számára. Bármely UDP-vagy TCP-alkalmazás támogatott lehet.
- Mivel a terheléselosztó nem kommunikál a TCP-adattartalommal, és TLS-kiszervezést biztosít, átfogó titkosított forgatókönyveket hozhat létre. A terheléselosztó használatával a TLS-alkalmazásokhoz nagy léptékű kibővíthető teljesítmény áll a virtuális gépen. Például a TLS-munkamenet beírási kapacitását csak a háttérbeli készlethez hozzáadott virtuális gépek típusa és száma korlátozza.

## <a name="limitations"></a><a name = "limitations"></a>Korlátozások

- Egy terheléselosztó-szabály nem terjedhet ki két virtuális hálózatra.  A frontendeknek és a háttérbeli példányoknak ugyanabban a virtuális hálózatban kell lenniük.  

- A Load Balancer terheléselosztást és port továbbítást biztosít bizonyos TCP-vagy UDP-protokollokhoz. A terheléselosztási szabályok és a bejövő NAT-szabályok támogatják a TCP és az UDP használatát, de nem más IP-protokollok, beleértve az ICMP-t is.

- A háttérbeli virtuális gép kimenő folyamata egy belső Load Balancer felületére sikertelen lesz.

- A továbbítási IP-töredékek nem támogatottak a terheléselosztási szabályokban. Az UDP-és TCP-csomagok IP-töredezettsége nem támogatott a terheléselosztási szabályokban. HA portok terheléselosztási szabályai használhatók a meglévő IP-töredékek továbbítására. További információ: [magas rendelkezésre állású portok – áttekintés](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>További lépések

- Lásd: [nyilvános standard Load Balancer létrehozása](quickstart-load-balancer-standard-public-portal.md) a Load Balancer használatának megkezdéséhez: hozzon létre egyet, hozzon létre virtuális gépeket egy egyéni IIS-bővítménnyel, és a virtuális gépek között a webalkalmazás terheléselosztását.
- További információ a [Azure Load Balancer kimenő kapcsolatokról](load-balancer-outbound-connections.md).
- További információ a [Azure Load Balancerról](load-balancer-overview.md).
- Tudnivalók az [állapot](load-balancer-custom-probe-overview.md)-mintavételekről.
- Ismerje meg a [standard Load Balancer diagnosztikát](load-balancer-standard-diagnostics.md).
- További információ a [hálózati biztonsági csoportokról](../virtual-network/security-overview.md).
