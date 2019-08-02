---
title: Azure standard Load Balancer és Availability Zones
titlesuffix: Azure Load Balancer
description: A Standard Load Balancer és a rendelkezésre állási zónák
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: allensu
ms.openlocfilehash: 5ef7de148d5ef4727602b8287164f2aff9ccf822
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274517"
---
# <a name="standard-load-balancer-and-availability-zones"></a>A Standard Load Balancer és a rendelkezésre állási zónák

A Azure Load Balancer standard SKU támogatja [Availability Zones](../availability-zones/az-overview.md) forgatókönyveket. A standard Load Balancer számos új koncepciót is elérhetővé tesznek, amelyek lehetővé teszik, hogy az erőforrások zónákhoz igazításával és a zónák közötti elosztásával optimalizálja a rendelkezésre állást a végpontok közötti forgatókönyvben.  Tekintse át [Availability Zones](../availability-zones/az-overview.md) útmutatást arról, hogy milyen Availability Zones, mely régiók támogatják a Availability Zones, valamint az egyéb kapcsolódó fogalmakat és termékeket. A Availability Zones standard Load Balancer egy átfogó és rugalmas szolgáltatáskészlet, amely számos különböző forgatókönyvet képes létrehozni.  Tekintse át ezt a dokumentumot [](#concepts) , hogy megértse ezeket a fogalmakat és az alapvető forgatókönyv kialakítására vonatkozó [útmutatót](#design).

>[!IMPORTANT]
>Tekintse át [Availability Zones](../availability-zones/az-overview.md) kapcsolódó témaköröket, beleértve a régióra vonatkozó információkat is.

## <a name="concepts"></a>A Load Balancerra alkalmazott fogalmak Availability Zones

Nincs közvetlen kapcsolat a Load Balancer erőforrások és a tényleges infrastruktúra között; Load Balancer létrehozása nem hoz létre példányt. Load Balancer az erőforrások olyan objektumok, amelyeken belül kipróbálhatja, hogy az Azure hogyan állíthatja be az előre elkészített több-bérlős infrastruktúrát a létrehozni kívánt forgatókönyv eléréséhez.  Ez a Availability Zones kontextusában jelentős jelentőséggel bír, mivel egy Load Balancer erőforrás több Availability Zonesn is vezérelheti az infrastruktúra programozását, miközben egy zóna redundáns szolgáltatása egyetlen erőforrásként jelenik meg az ügyfél szemszögéből.

A Load Balancer erőforrás funkciói a következők: frontend, szabály, állapot-mintavétel és háttér-készlet definíciója.

A Availability Zones kontextusában egy Load Balancer erőforrás viselkedését és tulajdonságait a zóna redundáns vagy többzónás jellege határozza meg.  A zóna-redundáns és a zonay leírja egy tulajdonság zonality.  A Load Balancer kontextusában a zóna-redundáns mindig azt jelenti, hogy *több* zóna és a zóna is a szolgáltatás elkülönítését jelenti *egyetlen zónában*.

Mind a nyilvános, mind a belső Load Balancer támogatja a zóna-redundáns és a zónákra kiterjedő forgatókönyveket, és mindkettő képes a zónák közötti közvetlen forgalmat igény szerint (*több zóna terheléselosztása*).

Egy Load Balancer erőforrás maga a régió, és nem a zóna.  A VNet és az alhálózatok mindig regionális és soha nem zónák.

### <a name="frontend"></a>Frontend

A Load Balancer előtér olyan előtéri IP-konfiguráció, amely egy nyilvános IP-cím erőforrásra vagy egy virtuális hálózati erőforrás alhálózatán belüli magánhálózati IP-címére hivatkozik.  Ez az elosztott terhelésű végpontot képezi, ahol a szolgáltatás elérhető.

Egy Load Balancer erőforrás egyszerre többek között a zónák és a zónák redundáns felületeit is tartalmazhatja. 

Ha egy nyilvános IP-erőforrás garantált egy zónához, a zonality (vagy annak hiánya) nem változtatható.  Ha módosítani szeretné vagy kihagyja a nyilvános IP-zonality, a megfelelő zónában újra létre kell hoznia a nyilvános IP-címet.  

A belső Load Balancer zonality megváltoztathatja a felület eltávolításával és újbóli létrehozásával, a zonality módosításával vagy kihagyásával.

Több előtérbeli felület használata esetén tekintse át a fontos szempontokat [Load Balancer több](load-balancer-multivip-overview.md) előtérbeli felületet.

#### <a name="zone-redundant-by-default"></a>A zóna alapértelmezés szerint redundáns

>[!IMPORTANT]
>Tekintse át [Availability Zones](../availability-zones/az-overview.md) kapcsolódó témaköröket, beleértve a régióra vonatkozó információkat is.

Egy Availability Zones nevű régióban a standard Load Balancer előtér alapértelmezés szerint zónában van.  Egyetlen előtéri IP-cím képes túlélni a zóna meghibásodását, és felhasználható a háttérbeli készlet összes tagjának elérésére a zónától függetlenül. Ez nem jelenti azt, hogy a hitless adatelérési útja, de az újrapróbálkozások és az áttelepítés sikeres lesz. A DNS-redundancia-sémák nem szükségesek. A frontend egyetlen IP-címét egyszerre több független infrastruktúra üzemelő példánya szolgálja ki több Availability Zonesban.  A felesleges zóna azt jelenti, hogy az összes bejövő vagy kimenő folyamatot egy adott régióban egyszerre több Availability Zones szolgálja ki egy adott IP-cím használatával.

Egy vagy több Availability Zones meghiúsulhat, és az adatelérési út addig marad, amíg a régió egy zónája Kifogástalan állapotba kerül. A zóna – redundáns konfiguráció az alapértelmezett, és nincs szükség további műveletekre.  

A következő szkripttel hozzon létre egy zóna-redundáns nyilvános IP-címet a belső standard Load Balancer számára. Ha a konfigurációban meglévő Resource Manager-sablonokat használ, adja hozzá az **SKU** szakaszt ezekhez a sablonokhoz.

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

A következő szkripttel hozzon létre egy zóna – redundáns előtéri IP-címet a belső standard Load Balancer számára. Ha a konfigurációban meglévő Resource Manager-sablonokat használ, adja hozzá az **SKU** szakaszt ezekhez a sablonokhoz.

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

#### <a name="optional-zone-isolation"></a>Választható zónák elkülönítése

Dönthet úgy is, hogy egy olyan előtérben van, amely egyetlen zónában van, amely ** egy zónákhoz tartozik.  Ez azt jelenti, hogy bármely bejövő vagy kimenő folyamat egy adott régióban egyetlen zónában szolgál.  A frontend a zóna állapotával osztja meg a sorsot.  Az adatelérési útvonalat a rendszer nem érinti a nem az adott zónán kívüli zónák meghibásodása esetén. Az IP-cím rendelkezésre állási zónákban való megjelenítéséhez használhatja a zónákhoz tartozó előtérbeli felületet.  Azt is megteheti, hogy közvetlenül vagy, ha a frontend nyilvános IP-címekből áll, integrálja azokat egy DNS terheléselosztási termékkel, például a [Traffic Manager](../traffic-manager/traffic-manager-overview.md) , és egyetlen DNS-nevet használ, amelyet az ügyfél több, a Zona IP-címekhez .  Ezt a zónát is használhatja a zónák elosztott terhelésű végpontok számára, hogy külön figyelje az egyes zónákat.  Ha szeretné összekeverni ezeket a fogalmakat (a zóna redundáns és az azonos háttérbeli névteret), tekintse át [Azure Load Balancer több](load-balancer-multivip-overview.md)előtérben is.

Nyilvános Load Balancer előtér esetén a rendszer egy *zóna* paramétert ad hozzá a ELŐTÉRI IP-konfiguráció által hivatkozott nyilvános IP-címhez.  

Belső Load Balancer frontend esetén adjon hozzá egy *Zones* paramétert a belső Load Balancer előtér-IP-konfigurációhoz. A zóna-előtérbeli felület hatására a Load Balancer egy adott zónához tartozó IP-címet garantál.

A következő szkripttel hozzon létre egy Zona standard nyilvános IP-címet a rendelkezésre állási 1. zónaban. Ha a konfigurációban meglévő Resource Manager-sablonokat használ, adja hozzá az **SKU** szakaszt ezekhez a sablonokhoz.

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

A következő parancsfájl használatával hozzon létre belső standard Load Balancer előtéri 1. zóna.

Ha a konfigurációban meglévő Resource Manager-sablonokat használ, adja hozzá az **SKU** szakaszt ezekhez a sablonokhoz. Továbbá adja meg a **zónák** tulajdonságot a alárendelt erőforrás ELŐTÉRBELI IP-konfigurációjában.

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

### <a name="cross-zone-load-balancing"></a>Zónák közötti terheléselosztás

A zónák közötti terheléselosztás az Load Balancer képessége, hogy bármely zónában elérje a háttérbeli végpontot, és független a frontendtől és a zonality.

Ha egyetlen zónán belül szeretné összehangolni és garantálni az üzembe helyezést, akkor az azonos zónához igazíthatja a zóna-és a zónákhoz tartozó háttérbeli erőforrásokat. Nincs szükség további műveletekre.

### <a name="backend"></a>Backend

A Load Balancer Virtual Machinesval működik.  Egyetlen VNet lévő virtuális gép a háttér-készlet része lehet, függetlenül attól, hogy az adott zóna számára garantált-e vagy sem, vagy hogy melyik zónában volt garantált.

Ha egyetlen zónával szeretné összehangolni és garantálni a felületet és a hátteret, akkor csak az azonos zónába tartozó virtuális gépeket helyezheti át a megfelelő háttér-készletbe.

Ha több zónában lévő virtuális gépeket szeretne kezelni, egyszerűen helyezzen több zónába tartozó virtuális gépeket ugyanabba a háttérbeli készletbe.  A virtuálisgép-méretezési csoportok használatakor egy vagy több virtuálisgép-méretezési csoport is elhelyezhető ugyanabba a háttérbeli készletbe.  A virtuálisgép-méretezési csoportok mindegyike egyetlen vagy több zónában is lehet.

### <a name="outbound-connections"></a>Kimenő kapcsolatok

A [kimenő kapcsolatokat](load-balancer-outbound-connections.md) az összes zóna kiszolgálja, és a rendszer automatikusan redundáns zónába kerül egy olyan régióban, ahol a Availability Zones, ha egy virtuális gép egy nyilvános Load Balancerhoz és egy zóna redundáns előtérbeli felülethez van társítva.  A kimenő kapcsolatok SNAT-portjának lefoglalásai megmaradnak a zónák meghibásodásakor.  

Ha pedig a virtuális gép egy nyilvános Load Balancerhoz és egy zóna-előtérben van társítva, a kimenő kapcsolatokat egyetlen zónában kell kiszolgálni.  A kimenő kapcsolatok megosztják a sorsot a megfelelő zóna állapotával.

A SNAT port előfoglalása és algoritmusa azonos a zónákkal vagy anélkül.

### <a name="health-probes"></a>Állapotminták

A meglévő állapot-mintavételi definíciók a Availability Zones nélkül maradnak.  Az egészségügyi modellt azonban infrastrukturális szinten bővítettük. 

Ha a zóna redundáns felületeit használja, a Load Balancer kibővíti a belső állapot modelljét, hogy egymástól függetlenül megállapítsa a virtuális gép elérhetőségét az egyes rendelkezésre állási zónáktól, és leállítja az útvonalakat az ügyfelek beavatkozása nélkül esetlegesen meghiúsult zónák között.  Ha egy adott elérési út nem érhető el az egyik zóna Load Balancer infrastruktúrájában egy másik zónában lévő virtuális géphez, Load Balancer képes érzékelni és elkerülni ezt a hibát. A virtuális gépet elérő más zónák továbbra is kiszolgálják a virtuális gépet a saját előtéri felületéről.  Ennek eredményeképpen előfordulhat, hogy a meghibásodási események során az egyes zónák némileg eltérő flow-eloszlással rendelkezhetnek, miközben a végpontok közötti szolgáltatás teljes állapotát védi.

## <a name="design"></a>Tervezési szempontok

A Load Balancer a Availability Zones kontextusában is szándékosan rugalmas. Dönthet úgy is, hogy a zónákhoz való igazítást választja, vagy megadhatja, hogy a zóna redundáns legyen.  A nagyobb rendelkezésre állás a megnövekedett összetettség árán érhető el, és az optimális teljesítmény érdekében meg kell terveznie a rendelkezésre állást.  Vessünk egy pillantást a fontos tervezési szempontokra.

### <a name="automatic-zone-redundancy"></a>Automatikus zóna – redundancia

A Load Balancer egyszerűvé teszi, hogy egyetlen IP-cím legyen a zóna redundáns felülete. A Zone-redundáns IP-címek bármely zónában biztonságosan képesek kiszolgálni a zónabeli erőforrásokat, és egy vagy több zóna meghibásodása esetén is megmaradhatnak, amíg az egyik zóna állapota Kifogástalan marad a régión belül. Ezzel szemben a zóna-előtérbeli felület a szolgáltatás egyetlen zónába való csökkentése, és a sors megosztva a megfelelő zónával.

A zóna – a redundancia nem jelenti a hitless DataPath vagy vezérlési síkot.  kifejezetten adatsík. Zóna – a redundáns folyamatok bármilyen zónát használhatnak, és az ügyfél folyamatai az adott régióban található összes kifogástalan zónát fogják használni. A zóna meghibásodása esetén az adott időponthoz tartozó, egészséges zónákat használó forgalom nem lesz hatással.  A zóna meghibásodásakor egy zónát használó adatforgalom hatással lehet, de az alkalmazások helyreállíthatók. Ezek a folyamatok a régión belüli fennmaradó kifogástalan zónákban folytatódnak az Újraküldés vagy az újralétesítés után, ha az Azure a zóna meghibásodása körül konvergált.

### <a name="xzonedesign"></a>Zónák közötti határok

Fontos megérteni, hogy minden alkalommal, amikor egy végpontok közötti szolgáltatás keresztezi a zónákat, a sorsot nem egy zónával, hanem akár több zónával is megoszthatja.  Ennek eredményeképpen előfordulhat, hogy a végpontok közötti szolgáltatás nem biztosít semmilyen rendelkezésre állást a nem zónákra kiterjedő üzembe helyezések során.

Kerülje a nem kívánt, több zónába tartozó függőségek bevezetését, ami érvényteleníti a rendelkezésre állási nyereséget Availability Zones használatakor.  Ha az alkalmazás több összetevőből áll, és nem kívánja rugalmasan megtartani a zónaadatokat, ügyelnie kell arra, hogy a zónák meghibásodása esetén a megfelelő kritikus összetevők túlélése ne legyen sikeres.  Például az alkalmazás egy kritikus összetevője hatással lehet a teljes alkalmazásra, ha az csak a túlélő zóná (k) tól eltérő zónában van.  Emellett vegye figyelembe a zónák visszaállítását, valamint azt is, hogy az alkalmazás hogyan konvergál. Tekintse át az egyes kulcsfontosságú pontokat, és használja őket inspirációként a konkrét forgatókönyvek szerint.

- Ha az alkalmazásnak két összetevője van, például egy IP-cím és egy felügyelt lemezzel rendelkező virtuális gép, és az 1. és 2. zónában is garantáltak, akkor az 1. zóna meghibásodása esetén a végpontok közötti szolgáltatás nem fog fennmaradni, ha az 1. zóna meghibásodik.  Ne hozzon létre több zónát, kivéve, ha teljesen tisztában van azzal, hogy potenciálisan veszélyes meghibásodási módot hoz létre.

- Ha az alkalmazásnak két összetevője van, például egy IP-cím és egy felügyelt lemezzel rendelkező virtuális gép, és a zóna redundáns és 1. zónába tartozik, a végpontok közötti szolgáltatás a 2., 3. zóna vagy az 1. zóna meghibásodása esetén is fennmarad.  A szolgáltatás állapotával kapcsolatban azonban elvész némi lehetőség, ha az összes megfigyelő funkció elérhetővé teszi a felületet.  Érdemes lehet átfogóbb állapot-és kapacitási modellt kifejleszteni.  Az elemzések és a kezelhetőség kibővítéséhez használhatja a zóna-redundáns és a zónákhoz kapcsolódó fogalmakat is.

- Ha az alkalmazásnak két összetevője van, például egy redundáns Load Balancer előtér, valamint egy több zónába tartozó virtuálisgép-méretezési csoport három zónában, akkor a meghibásodás által nem érintett zónák erőforrásai lesznek elérhetők, de a végpontok közötti szolgáltatási kapacitás csökkenhet. a zóna meghibásodása során. Infrastruktúra szempontjából az üzemelő példány képes túlélni egy vagy több zóna meghibásodását, és ez a következő kérdéseket veti fel:
  - Tisztában van azzal, hogy az alkalmazás miért nem az ilyen hibákkal és a csökkentett kapacitással kapcsolatos?
  - Szükség van-e arra, hogy a szolgáltatáson belül a feladatátvételt kényszerítse a régiós párokra, ha szükséges?
  - Hogyan fogja figyelni, felderíteni és elhárítani ezt a forgatókönyvet? Lehetséges, hogy standard Load Balancer diagnosztikát használ a végpontok közötti szolgáltatási teljesítmény figyelésének bővítéséhez. Gondolja át, hogy mi érhető el, és mire lehet szükség a teljes kép kiegészítéséhez.

- A zónák könnyebben érthetővé és tárolják a hibákat.  Azonban a zóna meghibásodása nem különbözik más hibáktól, amikor olyan fogalmakat tartalmaz, mint az időtúllépések, az újrapróbálkozások és a leállítási algoritmusok. Annak ellenére, hogy Azure Load Balancer a zóna-redundáns elérési utakat biztosít, és gyors helyreállítást tesz lehetővé a csomagok szintjén, a hiba bekövetkezésekor újraküldést vagy újraépítést eredményezhet, és fontos megérteni, hogy az alkalmazás hogyan alakul hibák. A terheléselosztási séma megmarad, de a következőket kell megterveznie:
  - Ha egy zóna meghibásodik, a végpontok közötti szolgáltatás megérti ezt, és ha az állapot elveszett, hogyan történik a helyreállítás?
  - Egy zóna visszaadásakor az alkalmazás megérti, hogyan konvergál biztonságos módon?

### <a name="zonalityguidance"></a>Zóna – redundáns és zónák közötti

>[!IMPORTANT]
>Tekintse át [Availability Zones](../availability-zones/az-overview.md) kapcsolódó témaköröket, beleértve a régióra vonatkozó információkat is.

A felesleges zóna a szolgáltatáshoz egyetlen IP-címmel rendelkező zónát biztosíthat, és ezzel egyidejűleg rugalmasan használhatja a megoldást.  Ezzel csökkentheti az összetettséget.  A felesleges zóna a zónák közötti mobilitást is magában foglalhatja, és biztonságosan használható bármely zónában lévő erőforrásokhoz.  Azt is megteheti, hogy a régiók Availability Zones nélküli régiókban is megtalálhatók, ami korlátozhatja a szükséges változtatásokat, ha egy adott régió Availability Zones.  Egy zóna – redundáns IP-cím vagy előtér – konfigurációs szintaxisa minden régióban sikeres, beleértve a Availability Zones nélkül.

A zónák explicit garanciát biztosítanak egy zónához, és megosztják a sorsot a zóna állapotával. A zónákhoz tartozó IP-címek és a zóna-Load Balanceri frontendek társítása kívánatos vagy ésszerű attribútum lehet, különösen akkor, ha a csatlakoztatott erőforrás egy azonos zónában található, többhelyes virtuális gép.  Vagy lehet, hogy az alkalmazásnak explicit módon kell megismernie, hogy az erőforrás melyik zónában található, és hogy kifejezetten külön zónákban szeretné megadni a rendelkezésre állást.  Dönthet úgy, hogy több, zónákra osztott, végpontok közötti szolgáltatás számára elérhetővé teheti a többzónás felületet (ez a zóna zónákhoz tartozó, több Zona virtuálisgép-méretezési csoportokra vonatkozóan).  Ha pedig a zóna-előtérben nyilvános IP-címek vannak, akkor használhatja ezeket a több zóna-előtérbeli felületet a szolgáltatás [Traffic Manager](../traffic-manager/traffic-manager-overview.md)való kimutatása érdekében.  Vagy több, a zóna számára elérhetővé tett felületi felületet is használhat, amelyek külső figyelési megoldásokon keresztül érhetik el a zónák állapotát A zónákhoz csak az ugyanahhoz a zónához igazított, zónákra kiterjedő előtér-erőforrásokat kell kiszolgálni, és el kell kerülnie a zónákon belüli erőforrások potenciálisan káros, több zóna közötti forgatókönyveit.  A zóna-erőforrások csak olyan régiókban léteznek, ahol Availability Zones léteznek.

Nincs általános útmutatás arra, hogy az egyik a szolgáltatás architektúrájának ismerete nélkül jobb választás.

## <a name="limitations"></a>Korlátozások

- Míg az adatsík teljesen redundáns zónában van (kivéve, ha meg van adva a zónákhoz való hozzáférés), a vezérlési sík műveletei nem teljesen redundáns zónában vannak.

## <a name="next-steps"></a>További lépések
- További információ a [Availability Zones](../availability-zones/az-overview.md)
- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md)
- Ismerje meg, hogyan oszthatja meg a virtuális gépeket egy zónán belül egy olyan [standard Load Balancer használatával,](load-balancer-standard-public-zonal-cli.md) amely egy zóna-előtérben található.
- Megtudhatja, hogyan oszthatja meg a [virtuális gépeket zónák között egy olyan standard Load Balancer használatával, amely zóna nélküli előtérbeli felületet használ](load-balancer-standard-public-zone-redundant-cli.md)
