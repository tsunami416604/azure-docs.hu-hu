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
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: e7ddb548d8dad5bdcc3021941877903377af9318
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771478"
---
# <a name="standard-load-balancer-and-availability-zones"></a>A Standard Load Balancer és a rendelkezésre állási zónák

Az Azure standard Load Balancer támogatja a [rendelkezésre állási zónák](../availability-zones/az-overview.md) forgatókönyveit. Standard Load Balancer használatával optimalizálhatja a rendelkezésre állást a végpontok közötti forgatókönyvben az erőforrások zónákhoz igazításával és a zónák közötti elosztásával.  Tekintse át a rendelkezésre állási [zónákat](../availability-zones/az-overview.md) a rendelkezésre állási zónákra vonatkozó útmutatásért, mely régiókban jelenleg a rendelkezésre állási zónák és egyéb kapcsolódó fogalmak és termékek érhetők el. A rendelkezésre állási zónák és a standard Load Balancer együttes használata egy átfogó és rugalmas szolgáltatáskészlet, amely számos különböző forgatókönyvet képes létrehozni.  Tekintse át ezt a dokumentumot, hogy megértse ezeket a [fogalmakat](#concepts) és az alapvető forgatókönyv [kialakítására vonatkozó útmutatót](#design).

>[!IMPORTANT]
>Tekintse át [Availability Zones](../availability-zones/az-overview.md) kapcsolódó témaköröket, beleértve a régióra vonatkozó információkat is.

## <a name="concepts"></a>A Load Balancerra alkalmazott fogalmak Availability Zones

Nincs közvetlen kapcsolat a Load Balancer erőforrások és a tényleges infrastruktúra között; Load Balancer létrehozása nem hoz létre példányt. Load Balancer az erőforrások olyan objektumok, amelyeken belül kipróbálhatja, hogy az Azure hogyan állíthatja be az előre elkészített több-bérlős infrastruktúrát a létrehozni kívánt forgatókönyv eléréséhez.  Ez a rendelkezésre állási zónák kontextusában jelentős jelentőséggel bír, mivel egy Load Balancer erőforrás több rendelkezésre állási zónában képes szabályozni az infrastruktúra programozását, miközben egy zóna redundáns szolgáltatása egyetlen erőforrásként jelenik meg az ügyfél szemszögéből.  

Egy Load Balancer erőforrás maga a régió, és nem a zóna.  A VNet és az alhálózatok mindig regionális és soha nem zónák. A konfigurálni kívánt beállítások részletességét a frontend, a szabály és a háttérrendszer-készlet definíciójának minden konfigurációja korlátozza.

A rendelkezésre állási zónák kontextusában a Load Balanceri szabályok viselkedését és tulajdonságait a zóna-redundáns vagy a zonaer-ként írja le.  A zóna-redundáns és a zonay leírja egy tulajdonság zonality.  A Load Balancer kontextusában a zóna-redundáns mindig azt jelenti, hogy *több* zóna és a zóna is a szolgáltatás elkülönítését jelenti *egyetlen zónában*.

Mind a nyilvános, mind a belső Load Balancer támogatja a zóna-redundáns és a zónákra kiterjedő forgatókönyveket, és mindkettő képes a zónák közötti közvetlen forgalmat igény szerint (*több zóna terheléselosztása*). 

### <a name="frontend"></a>Előtér

A Load Balancer előtér olyan előtéri IP-konfiguráció, amely egy nyilvános IP-cím erőforrásra vagy egy virtuális hálózati erőforrás alhálózatán belüli magánhálózati IP-címére hivatkozik.  Ez az elosztott terhelésű végpontot képezi, ahol a szolgáltatás elérhető.

Egy Load Balancer erőforrás egyszerre tartalmazhatja a zónák és a zóna redundáns előtérben található szabályokat. 

Ha egy nyilvános IP-erőforrás vagy egy magánhálózati IP-cím garantált egy zónához, a zonality (vagy annak hiánya) nem változtatható meg.  Ha szeretné módosítani vagy kihagyni egy nyilvános IP-cím vagy magánhálózati IP-zonality, újra létre kell hoznia a nyilvános IP-címet a megfelelő zónában.  A rendelkezésre állási zónák nem módosítják a több előtérre vonatkozó korlátozásokat, a lehetőség részleteinek megtekintéséhez tekintse át [Load Balancer több](load-balancer-multivip-overview.md) előtérbeli felületet.

#### <a name="zone-redundant-by-default"></a>A zóna alapértelmezés szerint redundáns

A rendelkezésre állási zónákkal rendelkező régiókban alapértelmezés szerint egy standard Load Balancer előtér-terület is redundáns.  A felesleges zóna azt jelenti, hogy az összes bejövő vagy kimenő folyamat egy adott régióban egyszerre több rendelkezésre állási zónából áll, egyetlen IP-cím használatával. A DNS-redundancia-sémák nem szükségesek. Egyetlen előtéri IP-cím képes túlélni a zóna meghibásodását, és felhasználható az összes (nem érintett) háttérbeli készlet tagjainak elérésére a zónától függetlenül. Egy vagy több rendelkezésre állási zóna meghiúsulhat, és az adatelérési út addig marad, amíg a régió egy zónája Kifogástalan állapotba kerül. A frontend egyetlen IP-címét egyszerre több független infrastruktúra-telepítés is szolgálja, több rendelkezésre állási zónában.  Ez nem jelenti azt, hogy az adatelérési út hitless, de az újrapróbálkozások és az áttelepítés a zóna meghibásodása által nem érintett más zónákban is sikeres lesz.   

Az alábbi részlet azt szemlélteti, hogyan lehet nyilvános IP-címet definiálni egy zóna – redundáns nyilvános IP-cím, amelyet a nyilvános standard Load Balancer használhat. Ha a konfigurációban meglévő Resource Manager-sablonokat használ, adja hozzá az **SKU** szakaszt ezekhez a sablonokhoz.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Az alábbi részlet azt szemlélteti, hogyan határozható meg egy zóna – redundáns előtéri IP-cím a belső standard Load Balancer számára. Ha a konfigurációban meglévő Resource Manager-sablonokat használ, adja hozzá az **SKU** szakaszt ezekhez a sablonokhoz.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Az előző részletek nem teljesek, hanem a rendelkezésre állási zónák tulajdonságainak megjelenítésére szolgálnak.  Ezeket az utasításokat bele kell foglalni a sablonokba.

#### <a name="optional-zone-isolation"></a>Választható zónák elkülönítése

Dönthet úgy is, hogy egy olyan előtérben van, amely egyetlen zónában van, amely egy *zónákhoz*tartozik.  Ez azt jelenti, hogy bármely bejövő vagy kimenő folyamat egy adott régióban egyetlen zónában szolgál.  A frontend a zóna állapotával osztja meg a sorsot.  Az adatelérési útvonalat a rendszer nem érinti a nem az adott zónán kívüli zónák meghibásodása esetén. Az IP-cím rendelkezésre állási zónákban való megjelenítéséhez használhatja a zónákhoz tartozó előtérbeli felületet.  

Ezen kívül az egyes zónákon belüli elosztott terhelésű végpontokhoz közvetlenül is felhasználhatja a zónákhoz tartozó előtérbeli felületet. Ezt a zónát is használhatja a zónák elosztott terhelésű végpontok számára, hogy külön figyelje az egyes zónákat.  Vagy nyilvános végpontok esetén integrálhatja őket egy DNS terheléselosztási termékkel, például [Traffic Manager](../traffic-manager/traffic-manager-overview.md) , és egyetlen DNS-nevet használhat. Az ügyfél ezután ezt a DNS-nevet fogja feloldani a több zóna IP-címére.  

Ha szeretné összekeverni ezeket a fogalmakat (a zóna redundáns és az azonos háttérbeli névteret), tekintse át [Azure Load Balancer több](load-balancer-multivip-overview.md)előtérben is.

Nyilvános Load Balancer előtér esetén a megfelelő szabály által használt előtéri IP-konfiguráció által hivatkozott nyilvános IP-erőforráshoz hozzá kell adni egy *Zones* paramétert.

Belső Load Balancer frontend esetén adjon hozzá egy *Zones* paramétert a belső Load Balancer előtér-IP-konfigurációhoz. A zóna-előtérbeli felület hatására a Load Balancer egy adott zónához tartozó IP-címet garantál.

Az alábbi részlet azt szemlélteti, hogyan határozható meg a rendelkezésre állási 1. zóna a zóna szerinti szabványos nyilvános IP-cím. Ha a konfigurációban meglévő Resource Manager-sablonokat használ, adja hozzá az **SKU** szakaszt ezekhez a sablonokhoz.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Az alábbi részlet szemlélteti, hogyan határozható meg belső standard Load Balancer előtér a rendelkezésre állási 1. zónaban. Ha a konfigurációban meglévő Resource Manager-sablonokat használ, adja hozzá az **SKU** szakaszt ezekhez a sablonokhoz. Továbbá adja meg a **zónák** tulajdonságot a alárendelt erőforrás ELŐTÉRBELI IP-konfigurációjában.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

A bájtértékre-kivonatok nem végeznek teljes sablonokat, de a rendelkezésre állási zónák tulajdonságainak megjelenítésére szolgálnak.  Ezeket az utasításokat bele kell foglalni a sablonokba.

### <a name="cross-zone-load-balancing"></a>Zónák közötti terheléselosztás

A zónák közötti terheléselosztás az Load Balancer képessége, hogy bármely zónában elérje a háttérbeli végpontot, és független a frontendtől és a zonality.  Bármely terheléselosztási szabály bármely rendelkezésre állási zónában vagy regionális példányban megcélozhatja a backend-példányt.

Ügyelnie kell arra, hogy a forgatókönyvet olyan módon hozza létre, amely a rendelkezésre állási zónák fogalmát jelezte. Például garantálnia kell, hogy a virtuális gép üzembe helyezése egyetlen zónán vagy több zónán belül történjen, és a zóna-előtérben és a zónákhoz tartozó háttér-erőforrások ugyanazon zónához legyenek igazítva.  Ha a rendelkezésre állási zónákat csak a zónákhoz tartozó erőforrásokkal együtt szeretné megtekinteni, a forgatókönyv működni fog, de a rendelkezésre állási zónák tekintetében nem lehet tiszta meghibásodási mód. 

### <a name="backend"></a>Háttérszolgáltatás

A Load Balancer Virtual Machines-példányokkal működik.  Ezek lehetnek önálló, rendelkezésre állási csoportok vagy virtuálisgép-méretezési csoportok.  Az egyetlen virtuális hálózatban található virtuálisgép-példányok a háttér-készlet részét képezhetik, függetlenül attól, hogy az adott zóna számára garantált-e vagy sem, vagy hogy melyik zónában volt garantált.

Ha a felületet és a háttérrendszer egyetlen zónához való igazítását és biztosítását szeretné biztosítani, akkor a rendszer csak a megfelelő háttér-készletbe helyezi át a virtuális gépeket ugyanazon a zónán belül.

Ha több zónában szeretné kezelni a virtuális gépeket, egyszerűen helyezze a virtuális gépeket több zónából ugyanabba a háttérbeli készletbe.  A virtuálisgép-méretezési csoportok használatakor egy vagy több virtuálisgép-méretezési csoport is elhelyezhető ugyanabba a háttérbeli készletbe.  A virtuálisgép-méretezési csoportok mindegyike egyetlen vagy több zónában is lehet.

### <a name="outbound-connections"></a>Kimenő kapcsolatok

Ugyanez a zóna – a redundáns és a zónákra vonatkozó tulajdonságok a [kimenő kapcsolatokra](load-balancer-outbound-connections.md)vonatkoznak.  A kimenő kapcsolatokhoz használt zóna-redundáns nyilvános IP-címet az összes zóna kiszolgálja. A zónákhoz tartozó nyilvános IP-címeket csak az általa garantált zóna szolgáltatja.  A kimenő kapcsolatok SNAT a portok megmaradnak a zónákban, és a forgatókönyv továbbra is biztosítja a kimenő SNAT-kapcsolatot, ha a zóna meghibásodása nem érinti.  Ehhez szükség lehet az átvitelre vagy a kapcsolatok ismételt megadására, ha a folyamat egy érintett zóna által szolgált.  Az érintett zónáktól eltérő zónákban lévő folyamatokat a rendszer nem érinti.

A SNAT port előfoglalási algoritmusa ugyanaz, mint a rendelkezésre állási zónák vagy azok nélkül.

### <a name="health-probes"></a>Állapotminták

A meglévő állapot-mintavételi definíciók a rendelkezésre állási zónák nélkül maradnak.  Az egészségügyi modellt azonban infrastrukturális szinten bővítettük. 

Ha a zóna redundáns felületeit használja, a Load Balancer kibővíti a belső állapot modelljét, hogy egymástól függetlenül megállapítsa a virtuális gép elérhetőségét az egyes rendelkezésre állási zónákból, és leállítsa az útvonalakat az ügyfelek beavatkozása nélkül esetlegesen meghiúsult zónák között.  Ha egy adott elérési út nem érhető el az egyik zóna Load Balancer infrastruktúrájában egy másik zónában lévő virtuális gépre, Load Balancer képes érzékelni és elkerülni ezt a hibát. A virtuális gépet elérő más zónák továbbra is kiszolgálják a virtuális gépet a saját előtéri felületéről.  Ennek eredményeképpen előfordulhat, hogy a meghibásodási események során minden zónának kis mértékben eltérő az új folyamatok eloszlása, miközben a teljes körű állapotot védi.

## <a name="design"></a>Tervezési szempontok

Load Balancer a rendelkezésre állási zónák kontextusában rugalmas. Dönthet úgy is, hogy a zónákhoz való igazítást választja, vagy az egyes szabályok esetében kiválaszthatja, hogy a zóna redundáns legyen.  A nagyobb rendelkezésre állás a megnövekedett összetettség árán érhető el, és az optimális teljesítmény érdekében meg kell terveznie a rendelkezésre állást.  Vessünk egy pillantást a fontos tervezési szempontokra.

### <a name="automatic-zone-redundancy"></a>Automatikus zóna – redundancia

A Load Balancer egyszerűvé teszi, hogy egyetlen IP-cím legyen a zóna redundáns felülete. A Zone-redundáns IP-címek bármely zónában biztonságosan képesek kiszolgálni a zónabeli erőforrásokat, és egy vagy több zóna meghibásodása esetén is megmaradhatnak, amíg az egyik zóna állapota Kifogástalan marad a régión belül. Ezzel szemben a zóna-előtérbeli felület a szolgáltatás egyetlen zónába való csökkentése, és a sors megosztva a megfelelő zónával.

A zóna – a redundancia nem jelenti a hitless DataPath vagy vezérlési síkot.  kifejezetten adatsík. Zóna – a redundáns folyamatok bármilyen zónát használhatnak, és az ügyfél folyamatai az adott régióban található összes kifogástalan zónát fogják használni. A zóna meghibásodása esetén az adott időponthoz tartozó, egészséges zónákat használó forgalom nem lesz hatással.  A zóna meghibásodásakor egy zónát használó adatforgalom hatással lehet, de az alkalmazások helyreállíthatók. Ezek a folyamatok a régión belüli fennmaradó kifogástalan zónákban folytatódnak az Újraküldés vagy az újralétesítés után, ha az Azure a zóna meghibásodása körül konvergált.

### <a name="xzonedesign"></a>Zónák közötti határok

Fontos megérteni, hogy minden alkalommal, amikor egy végpontok közötti szolgáltatás keresztezi a zónákat, a sorsot nem egy zónával, hanem akár több zónával is megoszthatja.  Ennek eredményeképpen előfordulhat, hogy a végpontok közötti szolgáltatás nem biztosít semmilyen rendelkezésre állást a nem zónákra kiterjedő üzembe helyezések során.

Kerülje a nem kívánt, több zónába tartozó függőségek bevezetését, amely a rendelkezésre állási zónák használatakor érvényteleníti a rendelkezésre állási nyereséget.  Ha az alkalmazás több összetevőből áll, és nem kívánja rugalmasan megtartani a zónaadatokat, ügyelnie kell arra, hogy a zónák meghibásodása esetén a megfelelő kritikus összetevők túlélése ne legyen sikeres.  Például az alkalmazás egy kritikus összetevője hatással lehet a teljes alkalmazásra, ha az csak a túlélő zóná (k) tól eltérő zónában van.  Emellett vegye figyelembe a zónák visszaállítását, valamint azt is, hogy az alkalmazás hogyan konvergál. Tisztában kell lennie azzal, hogy az alkalmazás miért nem az egyes részeinek meghibásodását veszi figyelembe. Tekintse át az egyes kulcsfontosságú pontokat, és használja őket inspirációként a konkrét forgatókönyvek szerint.

- Ha az alkalmazásnak két összetevője van, például egy IP-cím és egy felügyelt lemezzel rendelkező virtuális gép, és az 1. és 2. zónában vannak kibiztosítva, akkor az 1. zóna meghibásodása esetén a végpontok közötti szolgáltatás nem fog fennmaradni, ha az 1. zóna meghibásodik.  Ne hozzon létre több zónát a zónákon, kivéve, ha teljesen tisztában van azzal, hogy potenciálisan veszélyes meghibásodási módot hoz létre.  Ez a forgatókönyv rugalmasságot biztosít.

- Ha az alkalmazásnak két összetevője van, például egy IP-cím és egy felügyelt lemezzel rendelkező virtuális gép, és a zóna redundáns és 1. zónába tartozik, a végpontok közötti szolgáltatás a 2. zóna, a 3. zóna vagy az 1. zóna meghibásodása esetén is túléli a zónát.  A szolgáltatás állapotával kapcsolatban azonban elvész némi lehetőség, ha az összes megfigyelő funkció elérhetővé teszi a felületet.  Érdemes lehet átfogóbb állapot-és kapacitási modellt kifejleszteni.  Az elemzések és a kezelhetőség kibővítéséhez használhatja a zóna-redundáns és a zónákhoz kapcsolódó fogalmakat is.

- Ha az alkalmazásnak két összetevője van, például egy redundáns Load Balancer előtér, valamint egy több zónába tartozó virtuálisgép-méretezési csoport három zónában, akkor a meghibásodás által nem érintett zónák erőforrásai lesznek elérhetők, de a végpontok közötti szolgáltatási kapacitás csökkenhet. a zóna meghibásodása során. Infrastruktúra szempontjából az üzemelő példány képes túlélni egy vagy több zóna meghibásodását, és ez a következő kérdéseket veti fel:
  - Tisztában van azzal, hogy az alkalmazás miért nem az ilyen hibákkal és a csökkentett kapacitással kapcsolatos?
  - Szükség van-e arra, hogy a szolgáltatáson belül a feladatátvételt kényszerítse a régiós párokra, ha szükséges?
  - Hogyan fogja figyelni, felderíteni és elhárítani ezt a forgatókönyvet? Lehetséges, hogy standard Load Balancer diagnosztikát használ a végpontok közötti szolgáltatási teljesítmény figyelésének bővítéséhez. Gondolja át, hogy mi érhető el, és mire lehet szükség a teljes kép kiegészítéséhez.

- A zónák könnyebben érthetővé és tárolják a hibákat.  Azonban a zóna meghibásodása nem különbözik más hibáktól, amikor olyan fogalmakat tartalmaz, mint az időtúllépések, az újrapróbálkozások és a leállítási algoritmusok. Annak ellenére, hogy Azure Load Balancer a zóna-redundáns elérési utakat biztosít, és gyors helyreállítást tesz lehetővé a csomagok szintjén, a hiba bekövetkezésekor újraküldést vagy újraépítést eredményezhet, és fontos megérteni, hogy az alkalmazás hogyan alakul hibák. A terheléselosztási séma megmarad, de a következőket kell megterveznie:
  - Ha egy zóna meghibásodik, a végpontok közötti szolgáltatás megérti ezt, és ha az állapot elveszett, hogyan történik a helyreállítás?
  - Egy zóna visszaadásakor az alkalmazás megérti, hogyan konvergál biztonságos módon?

Tekintse át az [Azure Cloud design-mintákat](https://docs.microsoft.com/azure/architecture/patterns/) az alkalmazás meghibásodási forgatókönyvek rugalmasságának javítása érdekében.

### <a name="zonalityguidance"></a>Zóna – redundáns és zónák közötti

A redundáns zóna egyszerűvé teheti az egyszerűséget, és a szolgáltatáshoz egyetlen IP-címmel rendelkező, rugalmasan használható lehetőséget biztosít.  Ezzel csökkentheti az összetettséget.  A felesleges zóna a zónák közötti mobilitást is magában foglalhatja, és biztonságosan használható bármely zónában lévő erőforrásokhoz.  Emellett a rendelkezésre állási zónák nélküli régiókban is látható, ami korlátozhatja a szükséges változtatásokat, ha egy adott régió rendelkezésre állási zónákat használ.  Egy zóna – redundáns IP-cím vagy előtér – konfigurációs szintaxisa minden régióban sikeres, beleértve a rendelkezésre állási zónák nélküli tartományokat is: egy zóna nincs megadva a zónák: az erőforrás tulajdonsága alatt.

A zónák explicit módon biztosítanak egy zónát, explicit módon megosztják a sorsot a zóna állapotával. Egy Load Balancer szabály létrehozása egy zónákra épülő IP-cím vagy a többhelyes belső Load Balancer előtérben lehet kívánatos, különösen, ha a csatlakoztatott erőforrás egy azonos zónában található, egy régióban lévő virtuális gép.  Vagy lehet, hogy az alkalmazásnak explicit módon kell megismernie, hogy az adott erőforrás melyik zónában van, és a kifejezetten külön zónákban lévő rendelkezésre állást kívánja indokolni.  Dönthet úgy, hogy több, zónákra osztott, végpontok közötti szolgáltatás számára elérhetővé teheti a többzónás felületet (ez a zóna zónákhoz tartozó, több Zona virtuálisgép-méretezési csoportokra vonatkozóan).  Ha pedig a zóna-előtérben nyilvános IP-címek vannak, akkor használhatja ezeket a több zóna-előtérbeli felületet a szolgáltatás [Traffic Manager](../traffic-manager/traffic-manager-overview.md)való kimutatása érdekében.  Vagy több, a zóna számára elérhetővé tett felületi felületet is használhat, amelyek külső figyelési megoldásokon keresztül érhetik el a zónák állapotát A zónákhoz csak az ugyanahhoz a zónához igazított, zónákra kiterjedő előtér-erőforrásokat kell kiszolgálni, és el kell kerülnie a zónákon belüli erőforrások potenciálisan káros, több zóna közötti forgatókönyveit.  Csak a rendelkezésre állási zónákat tartalmazó régiókban találhatók a zónákhoz tartozó erőforrások.

Nincs általános útmutatás arra, hogy az egyik a szolgáltatás architektúrájának ismerete nélkül jobb választás.  Tekintse át az [Azure Cloud design-mintákat](https://docs.microsoft.com/azure/architecture/patterns/) az alkalmazás meghibásodási forgatókönyvek rugalmasságának javítása érdekében.

## <a name="limitations"></a>Korlátozások

- Míg az adatsík teljesen redundáns zónában van (kivéve, ha meg van adva a zónákhoz való hozzáférés), a vezérlési sík műveletei nem teljesen redundáns zónában vannak.

## <a name="next-steps"></a>Következő lépések
- További információ a [Availability Zones](../availability-zones/az-overview.md)
- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md)
- Ismerje meg, hogyan oszthatja meg a [virtuális gépeket egy zónán belül egy olyan standard Load Balancer használatával,](load-balancer-standard-public-zonal-cli.md) amely egy zóna-előtérben található.
- Megtudhatja, hogyan oszthatja meg a [virtuális gépeket zónák között egy olyan standard Load Balancer használatával, amely zóna nélküli előtérbeli felületet használ](load-balancer-standard-public-zone-redundant-cli.md)
- Ismerje meg az [Azure Felhőbeli kialakítási mintáit](https://docs.microsoft.com/azure/architecture/patterns/) , amelyekkel javítható az alkalmazás meghibásodási forgatókönyvekhez való rugalmassága.
