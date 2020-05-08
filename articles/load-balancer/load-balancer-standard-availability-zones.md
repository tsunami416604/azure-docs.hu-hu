---
title: Azure standard Load Balancer és Availability Zones
titleSuffix: Azure Load Balancer
description: Ezzel a képzési útvonallal megismerheti az Azure standard Load Balancer és a Availability Zones.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 6deb5714a43d61f5ceb793757d49bd099f09f2b7
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977648"
---
# <a name="standard-load-balancer-and-availability-zones"></a>A Standard Load Balancer és a rendelkezésre állási zónák

Az Azure standard Load Balancer támogatja a rendelkezésre állási zónák forgatókönyveit. A standard Load Balancer használatával növelheti a rendelkezésre állást a forgatókönyvben az erőforrások és a zónák közötti elosztásával. A standard Load balancerrel együtt elérhető rendelkezésre állási zónák egy átfogó és rugalmas szolgáltatáskészlet, amely számos különböző forgatókönyvet hozhat létre.  Tekintse át ezt a dokumentumot, hogy megértse ezeket a [fogalmakat](#concepts) és az alapvető forgatókönyv [kialakítására vonatkozó útmutatót](#design).

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a>A Load Balancerra alkalmazott fogalmak Availability Zones

A terheléselosztó az összetevőitől örökli a zóna konfigurációját: 

* Előtér
* Szabályok
* Háttér-készlet definíciója

A rendelkezésre állási zónák kontextusában a terheléselosztó szabályának viselkedését és tulajdonságait a zóna-redundáns vagy a zónákra vonatkozó szabályok írják le.  A terheléselosztó kontextusában a zóna-redundáns mindig azt jelenti, hogy **több zóna** és a zóna is a szolgáltatás elkülönítését jelenti **egyetlen zónában**. Egy Azure Load Balancer kétféle, nyilvános és belső típusú. A terheléselosztó mindkét típusa támogatja a zónák redundanciát és a zónabeli üzembe helyezést.  A terheléselosztó mindkét típusa igény szerint képes a zónák közötti forgalom irányítására.

## <a name="frontend"></a>Előtér

A Load Balancer frontend egy nyilvános IP-címről vagy egy virtuális hálózat alhálózatán belüli magánhálózati IP-címről hivatkozó előtér-IP-konfiguráció. Ez az elosztott terhelésű végpontot képezi, ahol a szolgáltatás elérhető.

A terheléselosztó-erőforrások olyan szabályokat tartalmazhatnak, amelyek a zónák és a zónák redundáns előtérben találhatóak. Ha egy nyilvános vagy privát IP-cím garantáltan egy zónához tartozik, a zonality (vagy annak hiánya) nem módosítható. Nyilvános vagy magánhálózati IP-zonality módosításához vagy kihagyása érdekében hozza létre újra az IP-címet a megfelelő zónában. 

A rendelkezésre állási zónák nem változtatják meg több előtérbeli elem korlátozásait. További részletekért tekintse át [Load Balancer több](load-balancer-multivip-overview.md) előtérbeli felületet.

### <a name="zone-redundant"></a>Felesleges zóna 

A rendelkezésre állási zónákkal rendelkező régiókban a standard Load Balancer előtér-felület lehet redundáns zónában. Egy adott régióban több zóna is kiszolgálhatja a bejövő vagy kimenő forgalmat. Ezt a forgalmat egyetlen IP-cím szolgálja ki. A DNS-redundancia-sémák nem szükségesek. 

Egyetlen előtéri IP-cím fogja túlélni A zóna meghibásodását. Az előtérbeli IP-cím felhasználható az összes (nem érintett) háttérbeli készlet tagjainak elérésére a zónától függetlenül. Egy vagy több rendelkezésre állási zóna meghiúsulhat, és az adatelérési út addig marad, amíg a régió egy zónája Kifogástalan állapotba kerül. 

A frontend IP-címét egyszerre több független infrastruktúra-telepítés is szolgálja, több rendelkezésre állási zónában. Az újrapróbálkozások és az újralétesítések a zóna meghibásodása által nem érintett más zónákban is sikeresek lesznek. 

:::image type="content" source="./media/az-zonal/zone-redundant-lb-1.svg" alt-text="Felesleges zóna" border="true":::

*Ábra: a zóna redundáns Load Balancer*

### <a name="zonal"></a>Zónaszintű

Dönthet úgy is, hogy egy olyan előtérben van, amely egyetlen zónában van, amely egy *zónákhoz*tartozik.  Ez a forgatókönyv azt jelenti, hogy a bejövő vagy kimenő folyamatokat egy adott régió egyetlen zónája szolgálja ki.  A frontend a zóna állapotával osztja meg a sorsot.  Az adatelérési útvonalat a rendszer nem érinti a nem az adott zónán kívüli zónák meghibásodása esetén. Az IP-cím rendelkezésre állási zónákban való megjelenítéséhez használhatja a zónákhoz tartozó előtérbeli felületet.  

Ezen kívül az egyes zónákon belüli elosztott terhelésű végpontok esetén közvetlenül a zóna-előtérbeli környezetek használata is támogatott. Ez a konfiguráció lehetővé teszi, hogy zónán belüli elosztott terhelésű végpontok számára elérhetővé tegye a zónák egyedi figyelését. Nyilvános végpontok esetén integrálhatja őket egy DNS terheléselosztási termékkel, például [Traffic Manager](../traffic-manager/traffic-manager-overview.md) , és egyetlen DNS-nevet használhat.

:::image type="content" source="./media/az-zonal/zonal-lb-1.svg" alt-text="Felesleges zóna" border="true":::

Ha szeretné összekeverni ezeket a fogalmakat (a zóna redundáns és az azonos háttérbeli névteret), tekintse át [Azure Load Balancer több](load-balancer-multivip-overview.md)előtérben is.

Nyilvános Load Balancer-előtérbeli felület esetén a **zóna** paramétert a nyilvános IP-címhez adja hozzá. Ezt a nyilvános IP-címet a megfelelő szabály által használt előtéri IP-konfiguráció hivatkozik.

Belső terheléselosztó előtérbeli felületéhez adjon hozzá egy **Zones** paramétert a belső terheléselosztó ELŐTÉRBELI IP-konfigurációjához. A zónákhoz tartozó előtér-felület egy adott zónához tartozó IP-címet garantál egy adott alhálózaton.

## <a name="backend"></a>Háttérrendszer

Azure Load Balancer a virtuálisgép-példányokkal működik. Ezek a példányok lehetnek önálló, rendelkezésre állási csoportok vagy virtuálisgép-méretezési csoportok. Az egyetlen virtuális hálózatban lévő virtuális gépek a háttér-készlet részét képezik, a virtuális gép által garantált zónákat is.

Ha egyetlen zónához szeretné igazítani és garantálni a felületet és a hátteret, akkor a rendszer csak a megfelelő háttér-készletbe helyezi át a virtuális gépeket ugyanazon a zónán belül.

Ha több zónában szeretné kezelni a virtuális gépeket, helyezzen át több zónába tartozó virtuális gépeket ugyanabba a háttérbeli készletbe. 

A virtuálisgép-méretezési csoportok használatakor helyezzen egy vagy több virtuálisgép-méretezési csoportot ugyanabba a háttérbeli készletbe. A méretezési csoportok lehetnek egy vagy több zónában.

## <a name="outbound-connections"></a>Kimenő kapcsolatok

A zóna-redundáns és a zónák a [kimenő kapcsolatokra](load-balancer-outbound-connections.md)érvényesek. A kimenő kapcsolatokhoz használt zóna-redundáns nyilvános IP-címet az összes zóna kiszolgálja. A zónákhoz tartozó nyilvános IP-címeket csak a által garantált zónában kézbesítjük. 

A kimenő kapcsolati SNAT portok megmaradnak a zónák meghibásodásakor, és a forgatókönyv továbbra is biztosítja a kimenő SNAT-kapcsolatot, ha a zóna meghibásodása nem befolyásolja. A zónák meghibásodása esetén előfordulhat, hogy a kapcsolatok ismételt létrehozásához szükség van a zónák redundáns eseteire, ha a forgalmat egy érintett zóna szolgáltatja. Az érintett zónáktól eltérő zónákban lévő folyamatok nem érintettek.

A SNAT port előfoglalási algoritmusa ugyanaz, mint a rendelkezésre állási zónák vagy azok nélkül.

## <a name="health-probes"></a>Állapotminták

A meglévő állapot-mintavételi definíciók a rendelkezésre állási zónák nélkül maradnak. Az egészségügyi modellt azonban infrastrukturális szinten bővítettük. 

A zóna redundáns előtérbeli felületének használatakor a terheléselosztó kibővíti a belső állapot-ellenőrzést. A Load Balancer egymástól függetlenül megállapítja, hogy a virtuális gép rendelkezésre áll-e az egyes zónákból, és leállítja az útvonalakat a nem beavatkozás nélkül meghiúsult zónák között.  

A virtuális gépet elérő más zónák továbbra is kiszolgálják a virtuális gépet a saját előtéri felületéről. A meghibásodási események során az egyes zónák az új folyamatok különböző eloszlásával rendelkezhetnek, miközben védik a szolgáltatás általános állapotát.

## <a name="design-considerations"></a><a name="design"></a>Tervezési szempontok

A Load Balancer rugalmas a rendelkezésre állási zónák környezetében. Dönthet úgy is, hogy az egyes szabályokhoz igazítja a zónákat, vagy a zóna redundáns. A magasabb rendelkezésre állás a megnövekedett összetettség árán érhető el. Az optimális teljesítmény érdekében tervezze meg a rendelkezésre állást.

### <a name="automatic-zone-redundancy"></a>Automatikus zóna – redundancia

A Load Balancer egyszerűvé teszi, hogy egyetlen IP-cím legyen a zóna redundáns felülete. A Zone-redundáns IP-címek bármely zónában kiszolgálják a zónákhoz tartozó erőforrásokat.  Az IP-cím képes túlélni egy vagy több zóna meghibásodását, amíg az egyik zóna állapota Kifogástalan marad a régión belül.  Ehelyett a zóna-előtérbeli felület a szolgáltatás egyetlen zónába való csökkentése, és a sors megosztva a megfelelő zónával.

A zóna – a redundancia nem jelent hitless-DataPath vagy-vezérlési gépet. Ez az adatsík. Zóna – a redundáns folyamatok bármilyen zónát használhatnak, és az ügyfél folyamatai az adott régióban található összes kifogástalan zónát fogják használni. A zóna meghibásodása esetén az egészséges zónákat használó forgalmi folyamatok nem érintettek. 

A zóna meghibásodása esetén egy zónát használó adatforgalom befolyásolható, de az alkalmazások helyreállíthatók. A forgalom a régión belüli, kifogástalan állapotú zónákban folytatódik, amikor az Azure a zóna meghibásodása körül konvergált.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a>Zónák közötti határok

Fontos tisztában lenni azzal, hogy egy szolgáltatás több zónába kerül, és nem egy zónával, hanem akár több zónával is megoszthatja a sorsot. Ennek eredményeképpen előfordulhat, hogy a szolgáltatás nem rendelkezik a zóna nélküli üzemelő példányokkal való rendelkezésre állással.

Kerülje a nem kívánt, több zónába tartozó függőségek bevezetését, amely a rendelkezésre állási zónák használatakor érvényteleníti a rendelkezésre állási nyereséget. Ha az alkalmazás több kritikus összetevőt is tartalmaz, akkor ügyeljen arra, hogy a megmaradjon, ha egy zóna meghibásodik.

Az alkalmazás egyetlen kritikus összetevője hatással lehet a teljes alkalmazásra, ha az csak a túlélő zóná (k) tól eltérő zónában van. Gondolja át a zóna visszaállítását, és hogy az alkalmazás hogyan fog konvergálni. Ismerje meg, hogy az alkalmazás hogyan reagál az egyes részeinek hibáira. Tekintse át a legfontosabb pontokat, és használja azokat a kérdésekre, amelyeket az adott forgatókönyvnek megfelelően gondol.

- Ha az alkalmazás két összetevőből áll:

    * IP-cím
    * Felügyelt lemezzel rendelkező virtuális gép

Ezek az 1. és a 2. zónában vannak konfigurálva. Ha az 1. zóna meghibásodik, a szolgáltatás nem fog fennmaradni. Ne hozzon létre több zónát a zónákon, kivéve, ha teljesen tisztában van azzal, hogy potenciálisan veszélyes meghibásodási módot hoz létre. Ez a forgatókönyv rugalmasságot biztosít.

- Ha az alkalmazás két összetevőből áll:

    * IP-cím
    * Felügyelt lemezzel rendelkező virtuális gép

Úgy vannak konfigurálva, hogy zóna-redundáns és 1. zóna legyenek. A szolgáltatás a 2. zónában, a 3. zónában vagy mindkettőn kívül marad a zóna meghibásodása esetén, hacsak az 1. zóna nem sikerült. Azonban a szolgáltatás állapotával kapcsolatos okok miatt elveszítheti a szolgáltatást, ha az összes megfigyelése a frontend elérhetősége.  Érdemes lehet átfogóbb állapot-és kapacitási modellt kifejleszteni.  Az elemzések és a kezelhetőség kibővítéséhez használhatja a zóna-redundáns és a zónákhoz kapcsolódó fogalmakat is.

- Ha az alkalmazás két összetevőből áll:

    * Zóna – redundáns Load Balancer felület
    * Többzónás virtuálisgép-méretezési csoport három zónában

A sikertelenül nem érintett zónák erőforrásai elérhetők lesznek. A szolgáltatás kapacitása csökkenhet a meghibásodás során. Infrastruktúra szempontjából az üzemelő példány egy vagy több zóna meghibásodását képes túlélni. Ez a forgatókönyv a következő kérdéseket veti fel:

  - Tisztában van azzal, hogy az alkalmazás hogyan reagál a hibákra és a csökkentett teljesítményre?
  - Szükség van-e arra, hogy a szolgáltatáson belül a feladatátvételt kényszerítse a régiós párokra, ha szükséges?
  - Hogyan fogja figyelni, felderíteni és elhárítani ezt a forgatókönyvet? A standard Load Balancer diagnosztikát használhatja a szolgáltatás teljesítményének figyelésére. Gondolja át, hogy mi érhető el, és mi szükséges a bővítéshez.

- A zónák könnyebben érthetővé és tárolják a hibákat. A zóna meghibásodása nem különbözik más hibáktól, amikor időtúllépések, újrapróbálkozások és leállítási algoritmusok vannak. A Azure Load Balancer zóna-redundáns elérési utakat biztosít. A terheléselosztó gyorsan, valós időben próbál helyreállítani a csomagok szintjén. A hiba kialakulásakor a rendszer újraküldi vagy újraindítást eredményezhet. Fontos tisztában lennie azzal, hogy az alkalmazás milyen hibákat észlel. A terheléselosztási séma megmarad, de a következő kérdésekre kell megterveznie:

  - Ha egy zóna meghibásodik, a szolgáltatás megérti ezt a hibát, és ha az állapot elveszett, hogyan történik a helyreállítás?
  - Egy zóna visszaadásakor az alkalmazás megérti, hogyan konvergál biztonságos módon?

Tekintse át az [Azure Cloud design-mintákat](https://docs.microsoft.com/azure/architecture/patterns/) az alkalmazás meghibásodási forgatókönyvek rugalmasságának javítása érdekében.

## <a name="next-steps"></a>További lépések
- További információ a [Availability Zones](../availability-zones/az-overview.md)
- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md)
- Ismerje meg, hogyan oszthatja meg a [virtuális gépeket egy zónán belül egy olyan standard Load Balancer használatával,](load-balancer-standard-public-zonal-cli.md) amely egy zóna-előtérben található.
- Megtudhatja, hogyan oszthatja meg a [virtuális gépeket zónák között egy olyan standard Load Balancer használatával, amely zóna nélküli előtérbeli felületet használ](load-balancer-standard-public-zone-redundant-cli.md)
- Ismerje meg az [Azure Felhőbeli kialakítási mintáit](https://docs.microsoft.com/azure/architecture/patterns/) , amelyekkel javítható az alkalmazás meghibásodási forgatókönyvekhez való rugalmassága.
