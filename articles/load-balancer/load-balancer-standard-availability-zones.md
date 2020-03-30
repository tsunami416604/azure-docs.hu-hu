---
title: Az Azure standard terheléselosztó és rendelkezésre állási zónái
titleSuffix: Azure Load Balancer
description: Ezzel a tanulási útvonallal ismerkedje meg az Azure Standard Load Balancer és a Availability Zones használatával.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5a65982c5c13eb4e4273efcfd8d14910b0f35572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197147"
---
# <a name="standard-load-balancer-and-availability-zones"></a>A Standard Load Balancer és a rendelkezésre állási zónák

Az Azure Standard Load Balancer támogatja a [rendelkezésre állási zónák](../availability-zones/az-overview.md) forgatókönyvek. A Standard Load Balancer segítségével optimalizálhatja a rendelkezésre állást a végpontok közötti forgatókönyvben, ha az erőforrásokat zónákhoz igazítja, és zónák között osztja el őket.  Tekintse át [a rendelkezésre állási zónákat,](../availability-zones/az-overview.md) hogy útmutatást kap-e a rendelkezésre állási zónákról, mely régiók támogatják jelenleg a rendelkezésre állási zónákat, valamint más kapcsolódó fogalmakat és termékeket. A rendelkezésre állási zónák a Standard Load Balancer-rel kombinálva egy kiterjedt és rugalmas szolgáltatáskészlet, amely számos különböző forgatókönyvet hozhat létre.  Tekintse át ezt a dokumentumot, hogy megértse ezeket [a fogalmakat](#concepts) és az alapvető [forgatókönyv-tervezési útmutatót.](#design)

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a>A terheléselosztóra alkalmazott rendelkezésre állási zónák fogalmai

A terheléselosztó erőforrás maga regionális és soha nem zonal. A konfigurálható beállítások részletességét az előtér-, szabály- és háttérkészlet-definíció konkretizálása korlátozza.
A rendelkezésre állási zónák környezetében a terheléselosztó szabály viselkedését és tulajdonságait zónaredundánsként vagy zónaszintűként írják le.  Zóna-redundáns és zónabeli írja le a zonalitás egy ingatlan.  A terheléselosztó kontextusában a zónaredundáns mindig *több zónát* jelent, és a zónaszintű azt jelenti, hogy a szolgáltatást *egyetlen zónára*különítik el.
Mind a nyilvános, mind a belső terheléselosztó támogatja a zónaredundáns és zónaszintű forgatókönyveket, és mindkettő szükség szerint irányíthatja a forgalmat a zónák között (*zónaközi terheléselosztás).* 

### <a name="frontend"></a>Előtér

A terheléselosztó előtér egy előtér-IP-konfiguráció, amely egy nyilvános IP-címerőforrásra vagy egy virtuális hálózati erőforrás alhálózatán belüli privát IP-címre hivatkozik.  Ez képezi a terheléselosztási végpontot, ahol a szolgáltatás elérhetővé.
A terheléselosztó erőforrás egyszerre tartalmazhat zónaszintű és zónaredundáns előtétrendszerű szabályokat. Ha egy nyilvános IP-erőforrás vagy egy privát IP-cím garantált egy zónában, a zonalitás (vagy annak hiánya) nem módosítható.  Ha módosítani vagy kihagyni szeretné a nyilvános IP-cím vagy privát IP-cím előtér zónaitását, újra létre kell hoznia a nyilvános IP-címet a megfelelő zónában.  Rendelkezésre állási zónák nem módosítja a korlátozásokat több előtér, tekintse át [a több előtér a terheléselosztó](load-balancer-multivip-overview.md) a részleteket ehhez a képességhez.

#### <a name="zone-redundant"></a>Felesleges zóna 

A rendelkezésre állási zónákkal rendelkező régiókban a standard terheléselosztó előtér zónaredundáns lehet.  A zónaredundáns azt jelenti, hogy az összes bejövő vagy kimenő folyamatot több rendelkezésre állási zóna szolgálja ki egy régióban egyidejűleg, egyetlen IP-címet használva. Dns-redundanciaséma nem szükséges. Egyetlen előtér-IP-cím képes túlélni zóna hiba, és használható az összes (nem érintett) háttérkészlet-tagok, függetlenül a ttól, hogy a zóna. Egy vagy több rendelkezésre állási zóna sikertelen lehet, és az adatelérési út mindaddig fennmarad, amíg a régió egy zónája kifogástalan marad. Az előtér egyetlen IP-címét egyszerre több független infrastruktúra-telepítés szolgálja ki több rendelkezésre állási zónában.  Ez nem jelenti azt, hitless adatelérési út, de minden újrapróbálkozások vagy újbóli létrehozása sikeres lesz más zónákban nem érinti a zóna hiba.   

#### <a name="optional-zone-isolation"></a>Választható zónaelkülönítés

Választhat, hogy egy előtér garantált egyetlen zóna, amely az úgynevezett *zonal előtér.*  Ez azt jelenti, hogy a bejövő vagy kimenő folyamatot egy régió egyetlen zónája szolgálja ki.  A frontend megosztja a sorsot a zóna egészségével.  Az adatelérési utat nem érintik a hibák a más zónákban, ahol garantálták. A zónaszintű előtér-rendszerek használatával elérhetővé teheti az IP-címet a rendelkezésre állási zónánként.  

Emellett a zónaszintű előpontokat közvetlenül az egyes zónákon belüli terheléselosztási végpontokhoz használhatja. Ezzel zónaterhelés-elhatárolt végpontok egyenként figyelheti az egyes zónákat is.  Vagy nyilvános végpontok integrálhatja őket egy DNS-terheléselosztási termék, például [a Traffic Manager,](../traffic-manager/traffic-manager-overview.md) és egyetlen DNS-nevet. Az ügyfél ezután ezt a DNS-nevet több zónaszintű IP-címre oldja fel.  

Ha szeretné keverni ezeket a fogalmakat (zónaredundáns és zónaszintű ugyanazon háttérrendszerekesetében), tekintse át [az Azure Load Balancer több előtétes](load-balancer-multivip-overview.md)rendszereket.

Nyilvános terheléselosztó előtér esetén hozzá kell adnia egy *zónaparamétert* a megfelelő szabály által használt előtér-IP-konfiguráció által hivatkozott nyilvános IP-erőforráshoz.

Belső terheléselosztó előtér esetén adjon hozzá egy *zónaparamétert* a belső terheléselosztó előtér IP-konfigurációjához. A zónaszintű előtér hatására a terheléselosztó garantálja az IP-címet egy adott zónában.

### <a name="cross-zone-load-balancing"></a>Többzónás terheléselosztás

A zónaközi terheléselosztás a terheléselosztó azon képessége, hogy bármely zónában elérje a háttérvégpontot, és független az előtértől és annak zonalitásátótól.  Bármely terheléselosztási szabály bármely rendelkezésre állási zónában vagy regionális példányban megcélozhatja a háttérpéldányokat.

Meg kell, hogy vigyázzon, hogy a forgatókönyv olyan módon, amely kifejezett rendelkezésre állási zónák fogalmát. Például garantálnia kell a virtuális gép egyetlen zónán vagy több zónán belül történő üzembe helyezését, és a zónaszintű előtér- és zónaszintű háttérerőforrásokat ugyanahhoz a zónához kell igazítania.  Ha a rendelkezésre állási zónák at csak zónaszintű erőforrások, a forgatókönyv működni fog, de nem lehet egyértelmű hiba mód a rendelkezésre állási zónák tekintetében. 

### <a name="backend"></a>Háttér

A terheléselosztó virtuális géppéldányokkal működik.  Ezek lehetnek önálló, rendelkezésre állási csoportok vagy virtuálisgép-méretezési csoportok.  Egy virtuális gép példánya egy virtuális hálózatban lehet része a háttérkészlet, függetlenül attól, hogy garantált volt-e a zóna, vagy melyik zóna garantált.

Ha egyetlen zónával szeretné összehangolni és garantálni szeretné az előtér- és háttérrendszer-kezelőt, csak helyezzen el virtuális gépeket ugyanazon a zónán belül a megfelelő háttérkészletbe.

Ha több zónában szeretné megcímezni a virtuális gépeket, egyszerűen helyezzen több zónából származó virtuális gépeket ugyanabba a háttérkészletbe.  Virtuálisgép-méretezési készletek használatakor egy vagy több virtuálisgép-méretezési készletek et helyezhet ugyanabba a háttérkészletbe.  És ezek a virtuális gép méretezési csoportok lehet egy vagy több zónában.

### <a name="outbound-connections"></a>Kimenő kapcsolatok

Ugyanazok a zónaredundáns és zónaszintű tulajdonságok vonatkoznak a [kimenő kapcsolatokra](load-balancer-outbound-connections.md)is.  A kimenő kapcsolatokhoz használt zónaredundáns nyilvános IP-címet az összes zóna kiszolgálja. Az övezeti nyilvános IP-címet csak az a zóna szolgálja ki, amelyben garantált.  A kimenő kapcsolat SNAT-portfoglalásai túlélik a zónahibákat, és a forgatókönyv továbbra is kimenő SNAT-kapcsolatot biztosít, ha a zónahiba nem befolyásolja.  Ehhez szükség lehet az átvitelek vagy a kapcsolatok zónaredundáns forgatókönyvek esetén történő újbóli létrehozásához, ha egy érintett zóna egy folyamatot szolgált ki.  Az érintett zónáktól eltérő zónákban lévő áramlásokat ez nem érinti.

Az SNAT-port előfoglalási algoritmusa megegyezik a rendelkezésre állási zónákkal vagy azok nélkül.

### <a name="health-probes"></a>Állapotminták

A meglévő állapotminta-definíciók maradnak, mivel azok rendelkezésre állási zónák nélkül.  Azonban az állapotmodellt infrastruktúra szinten bővítettük. 

Zónaredundáns előhívók használatakor a terheléselosztó kibővíti belső állapotmodelljét, hogy önállóan vizsgálja meg a virtuális gépek elérhetőségét az egyes rendelkezésre állási zónákból, és állítsa le az olyan zónák közötti elérési utakat, amelyek ügyfél beavatkozása nélkül sikertelenek lehetnek.  Ha egy adott elérési út nem érhető el az egyik zóna terheléselosztó infrastruktúrájáról egy másik zónában lévő virtuális gépre, a terheléselosztó észleli és elkerüli ezt a hibát. Más zónák, akik elérhetik ezt a virtuális gép továbbra is szolgálja a virtuális gép a megfelelő előtér-rendszerek.  Ennek eredményeképpen előfordulhat, hogy a hibaesemények során minden zóna előfordulhat, hogy kissé eltérő terjesztések új folyamatok, miközben a teljes körű szolgáltatás általános állapotát védi.

## <a name="design-considerations"></a><a name="design"></a>Tervezési szempontok

A terheléselosztó szándékosan rugalmas a rendelkezésre állási zónák kontextusában. Választhatja a zónákhoz való igazítást, vagy választhatja azt is, hogy minden szabályhoz zónaredundáns legyen.  A megnövekedett rendelkezésre állás a megnövekedett összetettség árán történhet, és az optimális teljesítmény érdekében meg kell terveznie a rendelkezésre állást.  Vessünk egy pillantást néhány fontos tervezési szempontokat.

### <a name="automatic-zone-redundancy"></a>Automatikus zónaredundancia

A terheléselosztó egyszerűvé teszi, hogy egyetlen IP-cím legyen zónaredundáns előtér. A zónaredundáns IP-cím biztonságosan kiszolgálhat egy zónaszintű erőforrást bármely zónában, és túlélhet egy vagy több zónahibát mindaddig, amíg egy zóna kifogástalan állapotban marad a régióban. Ezzel szemben a zónaszintű előtér a szolgáltatás egyetlen zónára való csökkentése, és megosztja a sorsot az adott zónával.

A zónaredundancia nem jelent hitless adatutat vagy vezérlősíkot;  ez kifejezetten adatsík. A zónaredundáns folyamatok bármely zónát használhatnak, és az ügyfél folyamatai a régió összes kifogástalan zónáját használják. Zónahiba esetén az adott időpontban a kifogástalan zónákat használó forgalom nem érinti.  A zóna meghibásodásakor egy zónát használó forgalom hatással lehet, de az alkalmazások helyreállíthatók. Ezek a folyamatok folytatódhatnak a régión belüli fennmaradó kifogástalan zónákban újraküldés vagy újralétrehozás esetén, miután az Azure a zónahiba körül konvergált.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a>Zónák közötti határok

Fontos megérteni, hogy minden alkalommal, amikor egy végpontok közötti szolgáltatás keresztezi zónák, megosztja a sorsnem egy zóna, hanem potenciálisan több zónában.  Ennek eredményeképpen előfordulhat, hogy a végpontok között szolgáltatás nem szerzett rendelkezésre állást a nem zónaszintű telepítések felett.

Kerülje a nem kívánt zónaközi függőségek bevezetését, amelyek a rendelkezésre állási zónák használatakor érvénytelenítik a rendelkezésre állási nyereséget.  Ha az alkalmazás több összetevőből áll, és szeretné, hogy rugalmas zóna meghibásodása, ügyelnie kell arra, hogy a túlélés a megfelelő kritikus összetevők esetén a zóna meghibásodása.  Például az alkalmazás egyetlen kritikus összetevője hatással lehet a teljes alkalmazásra, ha az csak a túlélő zóna(k)tól eltérő zónában létezik.  Ezenkívül vegye figyelembe a zóna helyreállítását, és azt is, hogy az alkalmazás hogyan fog konvergálni. Meg kell értenie, hogy az alkalmazás okait tekintetében hibák részeit. Tekintsük át a legfontosabb pontokat, és használjuk őket inspirációként a konkrét forgatókönyv ön által imázsként.

- Ha az alkalmazás két összetevővel rendelkezik, például egy IP-címet és egy felügyelt lemezzel rendelkező virtuális gépet, és garantáltak az 1- es és a 2-es zónában, ha az 1.  Ne keresztezze a zónákat zónaszintű forgatókönyvekkel, hacsak nem érti meg teljes mértékben, hogy potenciálisan veszélyes hibamódot hoz létre.  Ez a forgatókönyv rugalmasságot biztosít.

- Ha az alkalmazás két összetevővel rendelkezik, például egy IP-címet és egy felügyelt lemezzel rendelkező virtuális gépet, és garantáltan zónaredundáns, illetve 1-es zóna, a végpontok közötti szolgáltatás túléli a 2-es zóna, a 3.  Azonban elveszíti bizonyos képességét, hogy oka a szolgáltatás állapotát, ha minden, amit megfigyel az elérhető a frontend.  Fontolja meg egy szélesebb körű egészségügyi és kapacitásmodell kidolgozását.  Előfordulhat, hogy zónaredundáns és zónaszintű fogalmak együttesen bővíteni betekintést és kezelhetőséget.

- Ha az alkalmazás két összetevővel rendelkezik, például egy zónaredundáns terheléselosztó előtétrendszerrel és egy három zónában beállított zónaközi virtuálisgép-méretezéssel, a meghibásodás által nem érintett zónákban lévő erőforrások elérhetők lesznek, de a végpontok közötti szervizkapacitás csökkenhet zónahiba esetén. Az infrastruktúra szempontjából a telepítés egy vagy több zónahibát is túlélhet, és ez a következő kérdéseket veti fel:
  - Tisztában van azzal, hogy az alkalmazás okaaz ilyen hibák és a leromlott kapacitás?
  - Szükség van a szolgáltatásban lévő biztosítékokkal, hogy szükség esetén egy régiópárra kényszerítse a feladatátvételt?
  - Hogyan fogja figyelni, észlelni és enyhíteni egy ilyen forgatókönyv? Előfordulhat, hogy a standard terheléselosztó diagnosztika segítségével növelheti a végpontok közötti szolgáltatás teljesítményének figyelését. Fontolja meg, mi áll rendelkezésre, és mi lehet szükség augmentation a teljes képet.

- A zónák könnyebben megérthetővé és megfékezhetővé tehetik a hibákat.  A zónahiba azonban nem különbözik a többi hibától, amikor olyan fogalmakról van szó, mint az időtúltöltések, az újrapróbálkozások és a visszalépési algoritmusok. Annak ellenére, hogy az Azure Load Balancer zónaredundáns elérési utakat biztosít, és megpróbálja gyorsan helyreállítani, csomagszinten valós időben, újraküldések vagy újralétrehozások fordulhatnak elő a hiba kezdete során, és fontos megérteni, hogy az alkalmazás hogyan birkózik meg a Kudarcok. A terheléselosztási séma fennmarad, de a következőkre kell terveznie:
  - Ha egy zóna meghibásodik, a végpontok között szolgáltatás megérti ezt, és ha az állapot elvész, hogyan fog helyreállítani?
  - Amikor egy zóna visszatér, az alkalmazás tisztában van a biztonságos konvergenciával?

Tekintse át az [Azure felhőtervezési mintákat,](https://docs.microsoft.com/azure/architecture/patterns/) hogy javítsa az alkalmazás hibaforgatókönyvekkel való ellenálló képességét.

## <a name="next-steps"></a>További lépések
- További információ a [rendelkezésre állási zónákról](../availability-zones/az-overview.md)
- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md)
- Ismerje meg, hogyan lehet [egy zónán belül kitölteni a virtuális gépeket egy zónaszintű előtérrel rendelkező standard terheléselosztó használatával](load-balancer-standard-public-zonal-cli.md)
- Ismerje meg, hogyan [lehet a virtuális gépek terhelését zónák között, zónaredundáns előtérrel rendelkező standard terheléselosztó használatával](load-balancer-standard-public-zone-redundant-cli.md)
- Ismerje meg az [Azure felhőalapú tervezési mintáit,](https://docs.microsoft.com/azure/architecture/patterns/) hogy javítsa az alkalmazás rugalmasságát a hibaforgatókönyvekhez.
