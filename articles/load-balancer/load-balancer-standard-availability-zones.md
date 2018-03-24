---
title: Az Azure szabványos terheléselosztó és a rendelkezésre állási zónák |} Microsoft Docs
description: Standard terheléselosztó és a rendelkezésre állási zónák
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: 70b39b854a3b7cb28716d3cb290998690dbeb549
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="standard-load-balancer-and-availability-zones"></a>Standard terheléselosztó és a rendelkezésre állási zónák

Támogatja az Azure Load Balancer Standard Termékváltozat [rendelkezésre állási zónák](../availability-zones/az-overview.md) forgatókönyvek. Néhány új fogalom érhetők el Standard terheléselosztással, amelyek lehetővé teszik a végpont forgatókönyv a rendelkezésre állási optimalizálhatja a zónák erőforrások igazítása, valamint a szét azokat a zónákat.  Felülvizsgálati [rendelkezésre állási zónák](../availability-zones/az-overview.md) régiók jelenleg támogatja a rendelkezésre állási zónák és más Mik azok a rendelkezésre állási zónák útmutatást, fogalmak és a termékek kapcsolatos. Rendelkezésre állási zónák együtt szabványos terheléselosztással egy kiterjedtnek és rugalmas készlet, amely számos különböző alkalmazási helyzetek hozhat létre.  Tekintse át a jelen dokumentum értenek [fogalmak](#concepts) és alapvető forgatókönyv [tervezési útmutató](#design).

>[!NOTE]
> A Load Balancer Standard Termékváltozat jelenleg előzetes verzió. Előzetes a szolgáltatás esetleg nincs azonos szintű rendelkezésre állást és megbízhatóságot, szolgáltatások, amelyek általában a rendelkezésre állási kiadási. További részletekért lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Használja a nyilvánosan elérhető [Load Balancer alapszintű Termékváltozat](load-balancer-overview.md) a termelés számára. Használandó [rendelkezésre állási zónák előzetes](https://aka.ms/availabilityzones) ebben az előzetes szükséges egy [előfizetési külön](https://aka.ms/availabilityzones), mellett regisztrál a Load Balancer [szabványos előzetes](#preview-sign-up).

## <a name="concepts"></a> Terheléselosztó alkalmazott rendelkezésre állási zónák fogalmak

Nincs terheléselosztó erőforrások és a tényleges infrastruktúra; közötti közvetlen kapcsolat Terheléselosztó létrehozása példánya nem hozható létre. A terheléselosztó erőforrások objektumai belül, amely akkor is express hogyan Azure kell programot, az előre elkészített több-bérlős-infrastruktúrát a létrehozni kívánt forgatókönyvhöz.  Ez a rendelkezésre állási zónák környezetében jelentős, mert egyetlen terheléselosztó erőforrást szabályozhatja az infrastruktúra több rendelkezésre állási zóna programozás során egy ügyfél szempontból egy erőforrást egy zónaredundáns szolgáltatás jelenik meg.

A Load Balancer erőforrás funkciók egész időtúllépést, egy szabályt, a állapotmintáihoz és a háttér-készlet definíciójának beolvasása.

A rendelkezésre állási zónák a környezetben a viselkedést és a terheléselosztó erőforrás tulajdonságainak leírt zónaredundáns vagy zonal.  Zónaredundáns és zonal ismertetik a zonality tulajdonság.  A Load Balancer környezetében zónaredundáns mindig azt jelenti, hogy *minden zóna* és zonal azt jelenti, hogy a szolgáltatás biztosítása a *ugyanabban a zónában*.

A nyilvános és a belső terheléselosztó zónaredundáns és zonal forgatókönyvek támogatása, és a is közvetlen forgalom zónák között szükség szerint (*határokon zóna terheléselosztás*).

A Load Balancer erőforrás maga regionális és soha nem zonal.  És egy VNet és alhálózat mindig regionális és soha nem zonal.

### <a name="frontend"></a>Előtér

A terheléselosztó előtérbeli egy előtér-IP-konfiguráció hivatkozik egy nyilvános IP-cím erőforrás vagy a magánhálózati IP-címet a virtuális hálózati erőforrás alhálózatban.  Ha a szolgáltatás fel van fedve az elosztott terhelésű végpont ez képezi.

A Load Balancer erőforrás egyidejűleg zonal és a zónaredundáns frontends is tartalmazhat.

Egy nyilvános IP-erőforrás nincs garanciát zónához, a zonality (vagy annak hiányát) esetén nem változtatható.  Ha szeretne módosítani, vagy hagyja el a nyilvános IP-időtúllépést zonality, akkor hozza létre újra a megfelelő zónához a nyilvános IP-Címmel.  

Módosíthatja a egy belső terheléselosztó időtúllépést zonality eltávolítása és az előtérbeli újbóli létrehozása, módosítása vagy a zonality kihagyásával.

Ha több frontends használ, tekintse át a [terheléselosztóhoz több frontends](load-balancer-multivip-overview.md) további fontos tudnivalók találhatók.

#### <a name="zone-redundant-by-default"></a>Alapértelmezés szerint zónaredundáns

A rendelkezésre állási zónák régióban egy szabványos terheléselosztó előtér zónaredundáns alapértelmezés szerint.  Egyetlen előtérbeli IP-címnek hibatűrését zóna hibát, és a zóna függetlenül minden háttér a készlet tagjainak eléréséhez használható. Ez nem jelenti a hitless elérési útja, de bármilyen újrapróbálkozások vagy reestablishment sikeres lesz. DNS-redundancia rendszerek esetén nincs szükség. Az előtér-egyetlen IP-cím független infrastruktúráknál minden rendelkezésre állási zónában egyidejűleg szolgál ki.  Zónaredundáns azt jelenti, hogy az összes bejövő vagy kimenő forgalom által egyidejűleg használatával egyetlen IP-címnek régióban minden rendelkezésre állási zóna rendelkezésre.

Egy vagy több rendelkezésre állási zónák sikertelen lehet, és az elérési útja, amíg az a régió marad egy zónát survives kifogástalan. Zónaredundáns konfigurációt az alapértelmezett, és nem kell további műveleteket igényel.  Ha egy régiót a rendelkezésre állási zónák támogatására képesek rendelkezik, egy meglévő előtér automatikusan zónaredundáns válik.

A következő parancsfájl segítségével hozzon létre egy zónaredundáns nyilvános IP-címet a szabványos belső terheléselosztóhoz. Ha meglévő Resource Manager-sablonok a konfigurációt használ, adja hozzá a **sku** szakasz ezeket a sablonokat.

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

A következő parancsfájl segítségével hozzon létre egy zónaredundáns front-end IP-címet a szabványos belső terheléselosztóhoz. Ha meglévő Resource Manager-sablonok a konfigurációt használ, adja hozzá a **sku** szakasz ezeket a sablonokat.

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

#### <a name="optional-zone-guarantee"></a>Nem kötelező zóna garancia

Dönthet úgy, hogy csak egy zónához garantált időtúllépést néven ismert egy *zonal előtér*.  Ez azt jelenti, hogy a bejövő vagy kimenő folyamat ugyanabban a régióban zónában által kiszolgált.  Az előtér sorsáról osztanak meg a zóna állapotát.  Az elérési útja nem kívül ahol garantált volt sikertelen. Zonal frontends segítségével teszi közzé a rendelkezésre állási zónánkénti IP-címet.  Emellett zonal frontends közvetlenül felhasználását, de ha tartalmaz. az előtérbeli nyilvános IP-címek, integrálja ezeket a DNS-terheléselosztási például a termék [Traffic Manager](../traffic-manager/traffic-manager-overview.md) és egy egyetlen DNS-nevet, amely egy ügyfél megoldja a használata több zonal IP-címet.  Is használhatja a zóna elosztott terhelésű végpont külön-külön figyelheti az egyes zónákkal / tenni.  Ha szeretne ezekről a fogalmakról (zónaredundáns és azonos háttérkiszolgáló zonal) keverése, tekintse át a [az Azure Load Balancer több frontends](/load-balancer-multivip-overview.md).

Egy nyilvános terheléselosztó előtér hozzáadása egy *zónák* paramétert az előtérbeli IP-konfigurációja által hivatkozott nyilvános IP-cím.  

Egy belső terheléselosztó előtér hozzáadása egy *zónák* paramétert a belső terheléselosztó előtérbeli IP-konfigurációja. A zonal előtér azt eredményezi, a terheléselosztó IP-cím az alhálózat a megadott zónában való biztosításához.

A következő parancsfájl segítségével hozzon létre egy zonal szabványos nyilvános IP-címet a rendelkezésre állási zóna 1. Ha meglévő Resource Manager-sablonok a konfigurációt használ, adja hozzá a **sku** szakasz ezeket a sablonokat.

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

A következő parancsfájl segítségével hozzon létre egy belső szabványos terheléselosztó előtér rendelkezésre állási zóna 1.

Ha meglévő Resource Manager-sablonok a konfigurációt használ, adja hozzá a **sku** szakasz ezeket a sablonokat. Továbbá adja meg a **zónák** tulajdonság az előtérbeli IP-konfigurációban a gyermek-erőforrás.

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

### <a name="cross-zone-load-balancing"></a>Kereszt-zóna terheléselosztás

Kereszt-zóna terheléselosztás terheléselosztó képessége, egy háttér-végpont minden zónában eléréséhez, és független az előtér- és a zonality.

Ha szeretne igazítása és a központi telepítés egy zóna belül garantálni, igazítása zonal előtér- és zonal háttér-erőforrások ugyanabban a zónában. Nincs szükség semmilyen további műveletre.

### <a name="backend"></a>Háttérszolgáltatás

Terheléselosztó működik együtt a virtuális gépek.  A virtuális gép egyetlen virtuális függetlenül-e azt egy zóna garantált volt, vagy mely zóna garantáltan háttérkészlet része lehet.

Ha szeretne igazítása és garantálja az előtér- és egy zóna háttérrendszeréhez, csak helyezze virtuális gépeken belül az azonos zónába megfelelő háttérkészlet.

Ha szeretne virtuális gépek cím több zóna között, egyszerűen helyezze virtuális gépek több zónákból azonos háttérkészlet.  Virtuálisgép-méretezési használatával állítja be, amikor egy vagy több virtuálisgép-méretezési csoportok elhelyezhet egy háttér-készlethez.  És a virtuálisgép-méretezési csoportok mindegyikének egyetlen vagy több zóna lehet.

### <a name="outbound-connections"></a>Kimenő kapcsolatok

[Kimenő kapcsolatok](load-balancer-outbound-connections.md) minden zóna által szolgáltatott, és automatikusan zónaredundáns egy területen a rendelkezésre állási zónák társítva van egy nyilvános terheléselosztó és zónaredundáns időtúllépést a virtuális gépek esetén.  Kimenő kapcsolat SNAT port lefoglalását zóna hibák képesek túlélni.  

Ha a virtuális Géphez társítva egy nyilvános terheléselosztó és zonal időtúllépést, kimenő kapcsolatok garantáltan egy zóna tudja megjeleníteni.  Kimenő kapcsolatok sorsáról a megfelelő zónaállapot megosztani.

A SNAT port előzetes lefoglalás és algoritmus megegyezik vagy zónák nélkül.

### <a name="health-probes"></a>Állapotminták

A meglévő állapotfigyelő mintavételi definíciók maradnak, amíg azok rendelkezésre állási zónák nélkül.  De azt kibővítette a rendszerállapot-modell infrastruktúra szinten. 

Zónaredundáns használatakor frontends, Load Balancer bővíti a belső állapotmodell egymástól függetlenül a virtuális gépek minden egyes rendelkezésre állási zónából reachability mintavételi, és leállíthatja a elérési utak között zóna listáját, amelyet nem sikerült felhasználói beavatkozás nélkül.  Ha a megadott elérési út nem érhető el a terheléselosztó infrastruktúrától egy zóna egy virtuális gép egy másik zónában, a terheléselosztó képesek észlelni és a hiba. Más zónákban, akik képes elérni a virtuális gép továbbra is a virtuális Gépet a megfelelő frontends szolgálnak.  Ennek eredményeképpen akkor lehetséges, hogy sikertelen események minden zóna lehetnek némileg eltérő folyamat terjesztéseket közben védelmet a végpont szolgáltatás általános állapotát.

## <a name="design"></a> Kialakítási szempontok

Terheléselosztó szándékosan rugalmas rendelkezésre állási zónák környezetében. Dönthet úgy, hogy zónák, vagy dönthet úgy, hogy zónaredundáns.  Nagyobb összetettségi áron mértékű rendelkezésre állást származhatnak, és alakítson ki kell az optimális teljesítmény érdekében rendelkezésre állásra.  Vessen egy pillantást fontos tervezési szempontokat.

### <a name="automatic-zone-redundancy"></a>Automatikus zóna-redundancia

Terheléselosztó egyszerűen rendelkezik zónaredundáns időtúllépést, egyetlen IP-cím lehet. Zónaredundáns IP-címnek biztonságosan ki tud szolgálni minden zónában zonal erőforrás, és mindaddig, amíg egy zóna kifogástalan a régión belül egy vagy több zóna hibák hibatűrését. Ezzel ellentétben zonal időtúllépést a szolgáltatás egyetlen zóna és a megosztások sorsáról a megfelelő zónával csökkentését.

Zóna-redundancia nem feltétlenül jelenti azt, hitless datapath vagy vezérlő vezérlősík;  akkor kifejezetten adatok vezérlősík. Zónaredundáns adatfolyamok zónák használható, és az ügyfél adatfolyamok fogja használni a megfelelő zónák mindegyikében régióban. Zóna meghiúsulása forgalom megfelelő zónák használata ezen a ponton a ideje nem érintettek.  A zóna használata a zóna hibát időpontjában forgalom negatív hatással lehet, de alkalmazások helyre tudja állítani, és ezek a folyamatok újraküldési vagy reestablishment régión belül a fennmaradó megfelelő zónák továbbra is, amennyiben az Azure a zóna hibát körül egy konvergált.

### <a name="xzonedesign"></a> Kereszt-zóna határok

Fontos tudni, hogy bármikor végpont szolgáltatásnak áthalad zónák, sorsáról azt megosztása nem egy zónát, de a potenciálisan több zóna.  Ennek eredményeképpen a végpont szolgáltatás előfordulhat, hogy nem rendelkezik szerzett a rendelkezésre állási keresztül nem zonal központi telepítések.

Ne vezet be, amely fog rendelkezésre állási növekedését érvényteleníti a rendelkezésre állási zónák használata esetén nem kívánt kereszt-zóna-függőségek.  Az alkalmazás több összetevőből áll, és a zóna hiba rugalmasak lehetnek kívánja, a rendszer zóna sikertelen esetén elegendő kritikus fontosságú összetevők maradását kell gondoskodunk.  Például az alkalmazáshoz kritikus adott összetevőt befolyásolhatja a teljes alkalmazás, ha csak egy zónában, a fennmaradó zóna nem létezik.  Emellett figyelembe venni a zóna visszaállítását, valamint azt, hogyan lesz összevonva, az alkalmazás. Most tekintse át az egyes kulcs mutat, és használja őket lehetőségekre kérdéseket tesz fel amennyire véleménye szerint keresztül az adott forgatókönyv.

- Ha az alkalmazás például IP-cím és a felügyelt lemezes virtuális gép két részből áll, és azok által garantált zónában 1 és zóna 2, ha az 1 zóna nem sikerül a végpont szolgáltatás nem lesz újraindítását követően sem ha zóna 1 sikertelen.  Zónák nem kereszt, kivéve, ha megértette, hogy a potenciálisan veszélyes hibaállapotba hoz létre.

- Ha az alkalmazás két részből áll, mint az IP-cím és a virtuális gép és felügyelete lemez, és azok garantáltan zónaredundáns és 1 rendre zónát, a végpont szolgáltatás lesznek képesek túlélni zóna hiba zóna 2, zónát 3 vagy mindkettő kivéve, ha a zóna 1 sikertelen volt.  Azonban elvesznek néhány lehetőséget a szolgáltatás állapotával kapcsolatos ok az összes azt követően, a frontend reachability esetén.  Érdemes lehet egy szélesebb körű állapotát és a kapacitás modell.  Előfordulhat, hogy a zónaredundáns és zonal fogalmak együtt is bontsa ki a betekintést és kezelhetőségét.

- Ha az alkalmazás két összetevők, például egy zónaredundáns terheléselosztó előtér- és a kereszt-zóna virtuálisgép-méretezési csoport három zónákban, zónákban nincs hatással a hibát az erőforrások elérhetők lesznek, de a végpont szolgáltatás nem lesznek elérhetők a kapacitás zóna hiba során. Az infrastruktúra szempontjából a központi telepítés egy vagy több zóna hibák hibatűrését, és ezt a riasztást az alábbi kérdéseket:
  - Tegye tisztában hogyan ilyen hibák és csökkent kapacitás kapcsolatos okok miatt az alkalmazás?
  - Olyan biztonságos környezetet biztosítanak a kényszerítéséhez a szolgáltatás szükség esetén régió párból feladatátvételt kell?
  - Miként fogja figyelni, észleli, és csökkenthető az ilyen esetben? Előfordulhat, hogy a végpont szolgáltatás teljesítményének figyelemmel kísérése révén szabványos terheléselosztó diagnostics használatára. Fontolja meg az elérhető és mi bővítés szükséges átfogó képet.

- Zónák tehet hibák könnyebben megértése és tartalmazott.  Azonban zóna hibája helyzetet teremt, mint más hibák például időtúllépések, az újrapróbálkozások és leállítási algoritmusok fogalmakra ismét. Annak ellenére, hogy Azure Load Balancer zóna redundáns útvonalakat biztosít, és megpróbálja helyreállítani gyorsan, valós idejű, a csomag szintjén újraküldése vagy reestablishments történhet során jelezze a hibát, és fontos tudni, hogyan az alkalmazás a copes hibák. A terheléselosztási séma a rendszer után is megmaradnak, de meg kell terveznie a következőket:
  - A zóna nem sikerül, ha nem a végpont szolgáltatás megértéséhez, és ha az állapot nem vesztek el, hogyan fogja helyreállít?
  - Ha a zóna ad vissza, nem az alkalmazás megtudhatja, hogyan lesz biztonságosan?

### <a name="zonalityguidance"></a> Zónaredundáns és zonal

Zónaredundáns nyújthat a zóna-független, és a szolgáltatás-címen egyetlen IP-cím a megadott idő rugalmas beállítást.  Ezután csökkentheti összetettségét.  Zónaredundáns is rendelkezik mobilitási zónák között, és minden zónában erőforrások biztonságosan használható.  Azt is jövőbeli igazolása régiókban rendelkezésre állási zónák, ami korlátozhatja az szükséges, amennyiben az a régió kapnak a rendelkezésre állási zónák módosítások nélkül.  A zónaredundáns IP-cím vagy frontend konfigurációs szintaxisa sikeres bármely régióban, beleértve a rendelkezésre állási zónák nélkül.

Zonal biztosíthat egy explicit garancia zónához, sorsáról osztja meg a zóna állapotát. Társítása zonal IP cím vagy zonal terheléselosztó előtér lehet kívánatos vagy ésszerű attribútum különösen akkor, ha a csatolt erőforrás a zonal virtuális gépek ugyanabban a zónában.  Vagy lehet, hogy az alkalmazás által igényelt explicit Tudásbázis mely zónára erőforrás található, és az okból elérhetősége a különálló zónákra explicit módon kívánja.  Választhat egy végpontok közötti szolgáltatás zónák pontjain több zonal frontends tenni (Ez azt jelenti, hogy egy zóna zonal frontends a több zonal virtuálisgép-méretezési állítja be).  Ha a zonal frontends nyilvános IP-címek, a több zonal frontends használhatja a szolgáltatást, amely közzétételénél és [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Vagy több zonal frontends segítségével egy zóna állapotának és teljesítményének insights figyelési megoldásoknak harmadik féltől származó keresztül szerezhet, és tegye elérhetővé az általános zónaredundáns előtér-bA. Meg kell csak szolgáltat zonal erőforrásokat zonal frontends az azonos zónába igazodik a, és elkerülheti a potenciálisan káros kereszt-zóna forgatókönyv-zonal erőforrásokat.  Zonal erőforrások csak azokban a régiókban, ahol a rendelkezésre állási zónák létezik létezik.

Nincs, hogy még jobb megoldás, mint a másikra anélkül, hogy tudnák a service-architektúra általános útmutatás.

## <a name="limitations"></a>Korlátozások

- Miközben az adatok vezérlősík teljesen zónaredundáns (kivéve, ha a zonal garancia van megadva), a vezérlő vezérlősík műveletek még nem teljesen zónaredundáns.

## <a name="next-steps"></a>További lépések
- További információ [rendelkezésre állási zónák](../availability-zones/az-overview.md)
- További információ [szabványos terheléselosztó](load-balancer-standard-overview.md)
