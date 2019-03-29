---
title: Az Azure Standard Load Balancer és rendelkezésre állási zónák
titlesuffix: Azure Load Balancer
description: A Standard Load Balancer és a rendelkezésre állási zónák
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: kumud
ms.openlocfilehash: 6f33be6e418366f57d243f578035b5c87079c99e
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579359"
---
# <a name="standard-load-balancer-and-availability-zones"></a>A Standard Load Balancer és a rendelkezésre állási zónák

Az Azure Load Balancer Standard Termékváltozat támogatja [rendelkezésre állási zónák](../availability-zones/az-overview.md) forgatókönyveket. Számos új fogalmakat érhetők el a Standard Load Balancer, ez lehetővé teszi annak optimalizálhatja a végpontok közötti forgatókönyv a rendelkezésre állási zónák erőforrások igazítás és elosztás őket zónák között.  Felülvizsgálat [rendelkezésre állási zónák](../availability-zones/az-overview.md) Mik azok a rendelkezésre állási zónák útmutatást, mely régiók jelenleg támogatja a rendelkezésre állási zónák és más kapcsolódó fogalmak és termékek. Rendelkezésre állási zónában a Standard Load Balancer együtt egy időtakarékos és rugalmas funkciókészletet, amely számos különböző helyzetekben hozhat létre.  Tekintse át ezt a dokumentumot, ezek megértése [fogalmak](#concepts) és alapvető forgatókönyv [tervezési útmutató](#design).

>[!IMPORTANT]
>Felülvizsgálat [rendelkezésre állási zónák](../availability-zones/az-overview.md) vonatkozó témaköreit, beleértve a régió konkrét információkkal.

## <a name="concepts"></a> Load Balancer a alkalmazni a rendelkezésre állási zónák fogalmak

Nincs terheléselosztó-erőforrások és a tényleges infrastruktúra közötti közvetlen kapcsolat egy terheléselosztó-példány nem hoz létre. Load Balancer-erőforrások olyan objektumok, amelyen belül fejezhető ki hogyan Azure kell program, az előre összeállított több-bérlős-infrastruktúrát a létrehozni kívánt forgatókönyvhöz a.  Ez a rendelkezésre állási zónák kontextusában jelentős, mert egy terheléselosztó-erőforrást szabályozhatja a programozási több rendelkezésre állási zónában az infrastruktúra, amíg egy ügyfél szempontjából egy erőforrás egy zónaredundáns szolgáltatás jelenik meg.

A Load Balancer erőforrásfüggvények időtúllépést, egy szabályt, az állapotfigyelő mintavételező és a háttérkiszolgáló-készlet definíciója fejezik ki.

A rendelkezésre állási zónák kontextusában a viselkedését, és a egy terheléselosztó erőforrás tulajdonságainak zónaredundáns vagy zónaszintű ismerteti.  Zónaredundáns és zónaszintű ismertetik a zonality tulajdonság.  A Load Balancer kontextusában a zónaredundáns mindig azt jelenti, hogy *több zónában* és zónaszintű azt jelenti, hogy a szolgáltatás elkülönítése a *zónában*.

Nyilvános és a belső Load Balancer támogatja a zónaredundáns és zónaszintű forgatókönyvek és a is közvetlen forgalom zónákban igény szerint (*zónaközi terheléselosztás*).

A Load Balancer erőforrásán regionális és soha nem zónaszintű.  És egy virtuális hálózatot és alhálózatot mindig regionális és soha nem zónaszintű.

### <a name="frontend"></a>Előtér

A terheléselosztó előtérbeli egy előtérbeli IP-konfiguráció hivatkozik egy nyilvános IP-cím erőforrás vagy a virtuális hálózati erőforrás az alhálózaton belüli magánhálózati IP-cím.  Ha a szolgáltatás ki van téve az elosztott terhelésű végpontot, képezi.

A terheléselosztó erőforrás zónaszintű és zónaredundáns is előtérrendszer egyszerre is tartalmazhat. 

Zóna garantálta lett nyilvános IP-erőforrást, amikor zonality (vagy annak hiánya) nem módosítható.  Ha szeretné módosítani, vagy hagyja ki a zonality, egy nyilvános előtérbeli IP-Címmel, hozza létre újra a megfelelő zónához lévő nyilvános IP-címet szeretne.  

Módosíthatja a zonality, egy belső terheléselosztó időtúllépést eltávolításával és újbóli létrehozása az előtérbeli, módosítása vagy a zonality kihagyása.

Több előtérrendszer használata esetén tekintse át a [Load Balancer több előtérrendszer](load-balancer-multivip-overview.md) fontos szempontjait.

#### <a name="zone-redundant-by-default"></a>Alapértelmezés szerint zónaredundánsak

>[!IMPORTANT]
>Felülvizsgálat [rendelkezésre állási zónák](../availability-zones/az-overview.md) vonatkozó témaköreit, beleértve a régió konkrét információkkal.

A rendelkezésre állási zónák egy régióban egy Standard Load Balancer előtérbeli zónaredundáns alapértelmezés szerint.  Egy egyetlen előtérbeli IP-cím hibatűrését zóna hiba, és minden háttérkészlet-tagokra attól függetlenül, a zóna eléréséhez használható. Ez nem jelenti azt, hitless adatok elérési útja, de bármilyen újrapróbálkozások vagy reestablishment sikeres lesz. DNS-redundancia rendszerek nem szükséges. Egyetlen IP-címet az előtérbeli egyszerre több független infrastruktúra üzembe helyezés több rendelkezésre állási zónában által kiszolgált.  Zónaredundáns azt jelenti, hogy az összes bejövő vagy kimenő forgalom több rendelkezésre állási zónában egy régióban egyszerre az egyetlen IP-címet használó szolgálja.

Egy vagy több rendelkezésre állási zónák sikertelen lehet, és az adatok elérési útja egy zónát a régióban marad, amíg survives kifogástalan állapotú. Zónaredundáns konfigurációt az alapértelmezett, és nem kell további műveleteket kell.  

A következő szkript használatával a belső Standard Load Balancer létrehozása a zónaredundáns nyilvános IP-cím. Ha meglévő Resource Manager-sablonok a konfigurációban használja, vegye fel a **termékváltozat** szakasz ezeket a sablonokat.

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

A következő szkript használatával a belső Standard Load Balancer létrehozása a zónaredundáns előtérbeli IP-cím. Ha meglévő Resource Manager-sablonok a konfigurációban használja, vegye fel a **termékváltozat** szakasz ezeket a sablonokat.

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

#### <a name="optional-zone-isolation"></a>Nem kötelező zóna elkülönítés

Rendelkezik egy előtérbeli garantáltan zónában, más néven választhatja egy *zónaszintű előtérbeli*.  Ez azt jelenti, hogy az egy régióban egy zóna által kiszolgált bármelyik kimenő vagy bejövő folyamathoz.  Az előtérbeli sorsát osztanak meg a zóna állapotát.  Az adatok elérési útja nem zónákban, ahol garantálta eltérő hibák. Használhatja a zónaszintű előtérrendszer IP-cím, egy rendelkezésre állási zónában elérhetővé.  Emellett közvetlenül a zónaszintű előtérrendszer használata, de ha nyilvános IP-címeket tartalmaz. az előtérbeli integrálja ezeket a DNS terheléselosztási termék például [Traffic Manager](../traffic-manager/traffic-manager-overview.md) és a egy egyetlen DNS-név, amely feloldani egy ügyfél használja több zónaszintű IP-címet.  Emellett ezzel elérhetővé egy zóna terheléselosztásos végpontjait minden zóna külön-külön figyeléséhez.  Ha ezek a fogalmak (zónaredundáns és ugyanarra a háttérrendszerre vonatkozó zónaszintű) kombinálhatja, tekintse át a [az Azure Load Balancer több előtérrendszer](load-balancer-multivip-overview.md).

Nyilvános Load Balancer előtér, adjon hozzá egy *zónák* paraméter az előtérbeli IP-konfigurációja által hivatkozott nyilvános IP-címhez.  

Belső Load Balancer előtér, adjon hozzá egy *zónák* paramétert a belső terheléselosztó előtérbeli IP-konfigurációt. A zónaszintű előtérbeli IP-cím az alhálózat egy adott zóna garantálja, hogy a terheléselosztó okoz.

A következő parancsfájl használatával zónaszintű Standard nyilvános IP-cím létrehozása rendelkezésre állási 1. zónában. Ha meglévő Resource Manager-sablonok a konfigurációban használja, vegye fel a **termékváltozat** szakasz ezeket a sablonokat.

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

A következő parancsfájl használatával hozzon létre egy belső, a Standard Load Balancer előtér rendelkezésre állási 1. zónában.

Ha meglévő Resource Manager-sablonok a konfigurációban használja, vegye fel a **termékváltozat** szakasz ezeket a sablonokat. Emellett határozza meg a **zónák** tulajdonság frissítése az előtérbeli IP-konfigurációt a gyermek-erőforrás.

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

### <a name="cross-zone-load-balancing"></a>Cross-zone Load-Balancing

Zónaközi terheléselosztás rendszer azon képessége, Load Balancer egy háttérbeli végpont, minden zónában eléréséhez, és független az előtér- és a zonality.

Ha szeretné igazítani, és garantálja a az üzembe helyezés egyetlen zónán belül, igazítása a zónaszintű előtér- és zónaszintű háttérerőforrásokhoz való ugyanabban a zónában. Nincs szükség további műveletekre.

### <a name="backend"></a>Háttérszolgáltatás

Terheléselosztó működik a virtuális gépekkel.  Egyetlen virtuális hálózaton lévő összes virtuális gép a háttérkészlet, attól függetlenül, e garantálta a zónához vagy melyik zónába garantáltan része lehet.

Ha szeretné igazítani, és garantálja a és a egy zóna a háttérbeli, csak helyezzen virtuális gépeket ugyanabban a zónában lévő megfelelő háttérkészlet.

Cím virtuális gépek több zónában szeretne, ha egyszerűen virtuális gépeket helyezni a több zónában egy háttérkiszolgáló-készlethez.  Ha a virtuális gép méretezési csoportokat használ, elhelyezhet egy vagy több virtuálisgép-méretezési csoportok egy háttérkiszolgáló-készlethez.  És ezeket a virtuálisgép-méretezési csoportok mindegyike egy vagy több zónában is lehetnek.

### <a name="outbound-connections"></a>Kimenő kapcsolatok

[Kimenő kapcsolatok](load-balancer-outbound-connections.md) minden zóna szolgáltatja, és automatikusan zónaredundáns a rendelkezésre állási zónákkal az egy régióban társított nyilvános Load Balancer és a egy zónaredundáns előtérbeli virtuális gép esetén.  Kimenő kapcsolat SNAT port hozzárendelések stabilitást biztosít az zóna meghibásodások.  

Viszont ha a virtuális gép rendelve egy nyilvános Load Balancer és a egy zónaszintű előtérbeli, kimenő kapcsolatok garantáltan kiszolgálása az internetszolgáltatójuk által zónában.  Kimenő kapcsolatok megoszthatja a megfelelő zónaállapot sorsáról.

Az SNAT port előzetes lefoglalás és algoritmus megegyezik vagy zónák nélkül.

### <a name="health-probes"></a>Állapotminták

A meglévő állapot-mintavételi definíciók marad, mivel ezek a rendelkezésre állási zónák nélkül.  De kiterjesztettük a állapotmodell-infrastruktúra szintjén. 

Zónaredundáns használatakor az előtérrendszer, Load Balancer egymástól függetlenül a lemezekről a virtuális gépek minden rendelkezésre állási zónában a mintavételi és elérési utak leállítja a zónában, amely felhasználói beavatkozás nélkül nem sikerült a belső állapotmodell bontja ki.  Ha egy adott elérési út nem érhető el a Load Balancer infrastruktúráról egy zónában egy másik zónában lévő virtuális géphez, a Load Balancer észlelése és a hiba. Más zónákban, akik elérheti a virtuális gép továbbra is a virtuális Gépet a megfelelő előtérrendszer kiszolgálása érdekében.  Ennek eredményeképpen is lehet, hogy során hibaesemények, minden zónában lehetnek némileg eltérő folyamat disztribúciók a teljes körű szolgáltatás általános állapotának védelme során.

## <a name="design"></a> Kialakítási szempontok

Load Balancer szolgáltatás szándékosan rugalmasan rendelkezésre állási zónák kontextusában. Választhat, igazodva a zónák, vagy dönthet úgy, hogy a zónaredundáns lehet.  Megnövelt rendelkezésre állást bármikor visszatérhet az árat nagyobb összetettséget, és meg kell kialakítása a rendelkezésre állás érdekében az optimális teljesítmény érdekében.  Vessünk egy pillantást a fontos tervezési szempontokat.

### <a name="automatic-zone-redundancy"></a>Automatikus zóna redundancia

Load Balancer egyszerűen rendelkezik, a zónaredundáns előtérbeli egyetlen IP-cím lehet. Egy zónaredundáns IP-cím biztonságosan tudjon kiszolgálni, minden zónában zónaszintű erőforrás és mindaddig, amíg egy zóna kifogástalan állapotú a régión belül egy vagy több zóna hibák hibatűrését. Ellentétben a zónaszintű előtérbeli a szolgáltatást, hogy a megfelelő zónát egy egyetlen zóna és -megosztások sorsát csökkentését.

Zóna redundancia nem jelenti azt, hitless datapath vagy vezérlősík;  fontos kifejezetten az adatsík. Zónaredundáns folyamat használhatja a zónák, és egy ügyfél folyamatok fogja használni a kifogástalan állapotú zónakészleten egy régióban. Kifogástalan állapotú zónák használata ezen a ponton az idő a forgalom adatfolyamait zóna meghibásodása esetén nem változik.  Egy zónát használó zóna sikertelen időpontjában adatforgalmakat érintheti, de helyreállíthatja az alkalmazások, és ezek a folyamatok a fennmaradó kifogástalan zónák újraküldési vagy reestablishment régióban továbbra is, az Azure rendelkezik hiperkonvergens körül a zóna hiba után.

### <a name="xzonedesign"></a> Adatbázisközi zóna határok

Fontos tudni, hogy bármikor egy teljes körű szolgáltatás átlép zónák, sorsát megoszt egy zóna nem, de potenciálisan több zónában.  Ennek eredményeképpen a teljes körű szolgáltatás előfordulhat, hogy nem révén egy rendelkezésre állási nem zónaszintű központi telepítések során.

Kerülje a rendszer érvényteleníti javul a rendelkezésre állási, rendelkezésre állási zónák használata esetén, amelyek nem kívánt zónák közötti függőségek bevezetését.  Amikor az alkalmazás több összetevőből áll, és szeretné is ellenáll zóna hiba, a rendszer egy zóna meghibásodása esetén elegendő kritikus fontosságú összetevők maradását kell átadniuk.  Például az alkalmazás egy egyetlen kritikus fontosságú összetevőnek befolyásolhatja a teljes alkalmazás csak a fennmaradó van(nak) eltérő zónában létezik.  Emellett is érdemes lehet a zóna visszaállítását, és hogyan fog át is szervezheti alkalmazását. Nézzük tekintse át a néhány alapvető szempontokat, és eszközként használni őket készítéséhez a kérdésekhez, ahogyan Ön gondol, az adott forgatókönyv keresztül.

- Ha az alkalmazás két részből például IP-cím és a felügyelt lemezes virtuális gépek és azok az 1. zóna biztosak lehetünk és, ha az 1. zóna nem sikerül a teljes körű szolgáltatás 2. zóna nem fogja túlélni Ha 1. zóna sikertelen lesz.  Zónák nem adatbázisközi, ha nincs teljesen tisztában, hogy egy potenciálisan veszélyes hibaállapot hoz létre.

- Ha az alkalmazás két összetevőből áll, például IP-cím és a egy virtuális Gépet a felügyelt lemez, és garantáltan állnak zónaredundáns és 1. zóna jelölik, a teljes körű szolgáltatás ki fogja túlélni 2. zóna zóna meghibásodása, zónáját 3 vagy mindkettőt, ha 1. zóna nem sikerült.  Azonban elvesznek néhány lehetőség a szolgáltatás állapotának vonatkozó döntések meghozatalát Ha minden észlelt problémát az előtér-lemezekről.  Érdemes lehet egy szélesebb körű állapotát és a kapacitás modellt.  Segítségével lehet zónaszintű és zónaredundáns az fogalmak együtt bontsa ki a insight és kezelhetősége.

- Ha az alkalmazás két összetevőből, például egy zónaredundáns terheléselosztó előtérbeli és a egy zónák közötti virtuálisgép-méretezési csoportot a három zónát, az erőforrások nem befolyásolják a hiba zónában lesz elérhető, de a teljes körű szolgáltatáskapacitás teljesítménye csökkenhet során hiba történt a zónát. Az infrastruktúra szempontjából a központi telepítés egy vagy több zóna hibák hibatűrését, és Ez kiváltja az alábbi kérdésekre:
  - Ehhez ismernie az alkalmazás hogyan okok miatt az ilyen hibák és a csökkentett teljesítményű kapacitás?
  - Szükség van biztosítékok feladatátvételt kényszerít ki egy olyan régiópárra szükség esetén a szolgáltatás?
  - Hogyan fogja, figyelése, észlelése és ilyen esetben csökkentése? Előfordulhat, hogy a Standard Load Balancer diagnosztika használata, mivel megvédi a teljes körű szolgáltatás teljesítményének figyelését. Fontolja meg, mi érhető el, és mi lehet szükségük teljes képet kiegészítését.

- Zónák teheti, hogy könnyebben értelmezhető és tartalmazott hibák.  Azonban zóna hibája esetén, a fő fogalmaira, például időtúllépések, az újrapróbálkozások és leállítási algoritmusok nem más hibák nem egyezik. Annak ellenére, hogy az Azure Load Balancer zóna redundáns útvonalakat biztosít, és megpróbálja helyreállítani, valós idejű, a csomag szintjén újraküldése vagy reestablishments jelezze a hibát során esetlegesen jelentkező és fontos tudni, hogy az alkalmazás hogyan copes az hibák. A terheléselosztási rendszer ki fogja túlélni, de meg kell terveznie a következőket:
  - A zóna nem sikerül, ha nem a teljes körű szolgáltatás megértéséhez, és ha az állapot elveszett, hogyan fog Ön helyre?
  - A zóna adja vissza, ha nem az alkalmazás megtudhatja, hogyan biztonságosan átvételre?

### <a name="zonalityguidance"></a> Zónaredundáns zónaszintű és

>[!IMPORTANT]
>Felülvizsgálat [rendelkezésre állási zónák](../availability-zones/az-overview.md) vonatkozó témaköreit, beleértve a régió konkrét információkkal.

Zónaredundáns nyújthat egy zóna-független, és egyetlen IP-Címmel rendelkező megadott idő rugalmas beállítást, oldja meg a szolgáltatáshoz.  Ez csökkentheti az összetettséget viszont.  Zónaredundáns is rendelkezik a mobilitási zónák között, és minden zónában erőforrások biztonságosan használható.  Azt is jövőbeli koncepció rendelkezésre állási zónák, ami korlátozhatja a módosítások szükséges, ha egy régió kapjanak a rendelkezésre állási zónák nélküli régiókban.  Egy zónaredundáns IP-cím vagy frontend konfigurációs szintaxisának sikeres bármelyik régióban, beleértve a rendelkezésre állási zónák nélkül.

A zónaszintű is biztosítanak egy explicit a zónához sorsát osztanak meg a zóna állapota. Társítása a zónaszintű IP-cím vagy a zónaszintű terheléselosztó előtérbeli is lehet kívánatos vagy ésszerű attribútum különösen, ha a csatolt erőforrás zónaszintű virtuális gép ugyanabban a zónában.  Vagy esetleg az alkalmazás mely zónára egy erőforráscsoportban található explicit ismeretek szükségesek, és explicit módon okból különálló zónákra lévő rendelkezésre állásról szeretné.  Dönthet úgy, hogy egy teljes körű szolgáltatás zónák között elosztott több zónaszintű előtérrendszer elérhetővé (azt jelenti, egy zóna több zónaszintű virtuálisgép-méretezési csoport és a zónaszintű előtérrendszerek állítja).  És ha a zónaszintű és előtérrendszerek nyilvános IP-címek, ezek több zónaszintű előtérrendszer használható a szolgáltatás által a [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Vagy használhatja a zónaszintű több előtérrendszer zóna állapotának és teljesítményének juthat információkhoz harmadik féltől származó figyelési megoldások száma és az általános zónaredundáns előtér-szolgáltatás elérhetővé. Érdemes csak zónaszintű előtérrendszer ugyanabban a zónában igazodik a zónaszintű erőforrások kiszolgálása és elkerülése érdekében a potenciálisan káros zónák közötti forgatókönyvek zónaszintű erőforrások számára.  A zónaszintű erőforrások csak a régiók, ahol a rendelkezésre állási zónák létezik szerepel.

Nincs sem, hogy egy jobb megoldás, mint a többi anélkül, hogy a service-architektúra általános útmutatást.

## <a name="limitations"></a>Korlátozások

- Miközben az adatok adatsík teljes zónaredundáns (kivéve, ha a zónaszintű garancia lett megadva), a vezérlési síkjával végzett műveletek nem teljes mértékben zónaredundáns.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [rendelkezésre állási zónák](../availability-zones/az-overview.md)
- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md)
- Ismerje meg, hogyan [terheléselosztása virtuális gépek használata a Standard Load Balancer zónaszintű előtérbeli zónán belül](load-balancer-standard-public-zonal-cli.md)
- Ismerje meg, hogyan [terheléselosztása virtuális gépek zónákban a zónaredundáns előtérbeli Standard Load Balancer használatával](load-balancer-standard-public-zone-redundant-cli.md)
