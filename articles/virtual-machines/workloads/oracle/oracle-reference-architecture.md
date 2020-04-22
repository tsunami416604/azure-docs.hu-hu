---
title: Oracle-adatbázisok referenciaarchitektúrái az Azure-ban | Microsoft dokumentumok
description: Hivatkozik az Oracle Database Enterprise Edition adatbázisok Microsoft Azure virtuális gépeken való futtatására szolgáló architektúrákra.
services: virtual-machines-linux
author: BorisB2015
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2019
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: bbb6665299ce9b6521eeb8801d8621dfbdc17f4a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683491"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Referenciaarchitektúrák az Oracle Database Enterprise Edition azure-beli kiadásához

Ez az útmutató részletesen ismerteti a magas rendelkezésre állású Oracle-adatbázis Azure-beli üzembe helyezésével kapcsolatos információkat. Emellett ez az útmutató beleveti magát a vész-helyreállítási szempontokat. Ezek az architektúrák az ügyfelek központi telepítései alapján lettek létrehozva. Ez az útmutató csak az Oracle Database Enterprise Edition kiadásra vonatkozik.

Ha szeretne többet megtudni az Oracle-adatbázis teljesítményének maximalizálásáról, olvassa el [az Oracle DB megbírása című témakört.](oracle-design.md)

## <a name="assumptions"></a>Feltételezések

- Ismeri az Azure különböző fogalmait, például a [rendelkezésre állási zónákat](../../../availability-zones/az-overview.md)
- Oracle Database Enterprise Edition 12c vagy újabb verziót futtat
- Tisztában van a licencelési következményekkel, és elismeri a licencelés következményeit a cikkben ismertetett megoldások használatakor.

## <a name="high-availability-for-oracle-databases"></a>Magas rendelkezésre állás az Oracle adatbázisok számára

A felhőben való magas rendelkezésre állás elérése minden szervezet tervezésének és tervezésének fontos része. A Microsoft Azure [rendelkezésre állási zónákat](../../../availability-zones/az-overview.md) és rendelkezésre állási csoportokat kínál (olyan régiókban, ahol a rendelkezésre állási zónák nem érhetők el). További információ [a felhőbe tervezhetvirtuális gépek rendelkezésre állásának kezeléséről.](../../../virtual-machines/linux/manage-availability.md)

A felhőalapú natív eszközök és ajánlatok mellett az Oracle olyan magas rendelkezésre állású megoldásokat is kínál, mint az [Oracle Data Guard,](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7) [az FSFO-val ellátott Data Guard,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html) [a Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)és a [GoldenGate,](https://www.oracle.com/middleware/technologies/goldengate.html) amelyek az Azure-ban állíthatók be. Ez az útmutató az egyes megoldások referenciaarchitektúráit ismerteti.

Végül a felhőhöz való alkalmazások áttelepítésekor vagy létrehozásakor fontos, hogy az alkalmazáskód finomhangolásával olyan natív felhőalapú mintákat adjon hozzá, mint az [újrapróbálkozási minta](https://docs.microsoft.com/azure/architecture/patterns/retry) és az [áramkör-megszakító minta.](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) A [Felhőtervezési minták útmutatóban](https://docs.microsoft.com/azure/architecture/patterns/) meghatározott további minták segíthetnek az alkalmazás rugalmasabb.

### <a name="oracle-rac-in-the-cloud"></a>Oracle RAC a felhőben

Az Oracle Real Application Cluster (RAC) az Oracle egyik megoldása, amely segít az ügyfeleknek a magas átviteli eredmények elérésében azáltal, hogy számos példány fér hozzá egy adatbázis-tárolóhoz (közös architektúraminta). Bár az Oracle RAC a helyszíni magas rendelkezésre állásérdekében is használható, az Oracle RAC önmagában nem használható a felhőben való magas rendelkezésre állásra, mivel csak a példányszintű hibák ellen nyújt védelmet, és nem rackszintű vagy adatközponti szintű hibák ellen. Ezért az Oracle azt javasolja, hogy az Oracle Data Guard-ot használja az adatbázisával (legyen az egy példány vagy RAC) a magas rendelkezésre állás érdekében. Az ügyfelekáltalában magas SLA-t igényelnek a kritikus fontosságú alkalmazások futtatásához. Az Oracle RAC jelenleg nem rendelkezik az Oracle által az Azure-on tanúsított vagy támogatott minősítéssel. Az Azure azonban olyan funkciókat kínál, mint például az Azure rendelkezésre állási zónákat és tervezett karbantartási időszakokat a példányszintű hibák elleni védelem érdekében. Ezenkívül az ügyfelek olyan technológiákat is használhatnak, mint az Oracle Data Guard, az Oracle GoldenGate és az Oracle Sharding a nagy teljesítmény és az újraéledés érdekében, mivel megvédik adatbázisaikat az állványszintű, valamint az adatközpont-szintű és a geopolitikai hibáktól.

Ha az Oracle Database-t több [rendelkezésre állási zónán](https://docs.microsoft.com/azure/availability-zones/az-overview) keresztül, valamint az Oracle Data Guard vagy a GoldenGate területén futtatja, az ügyfelek 99,99%-os uptime SLA-t kaphatnak. Azokban az Azure-régiókban, ahol a rendelkezésre állási zónák még nincsenek jelen, az ügyfelek [használhatják a rendelkezésre állási csoportokat,](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) és 99,95%-os rendelkezésre állási SLA-t érhetnek el.

>MEGJEGYZÉS: Lehet egy rendelkezésre állási cél, amely sokkal magasabb, mint a Microsoft által biztosított rendelkezésre állási SLA.

## <a name="disaster-recovery-for-oracle-databases"></a>Vészhelyreállítás Oracle-adatbázisokhoz

Amikor a legfontosabb alkalmazásokat a felhőben üzemelteti, fontos, hogy tervezzen a magas rendelkezésre állás és a vészhelyreállítás érdekében.

Az Oracle Database Enterprise Edition esetében az Oracle Data Guard hasznos funkció a vészhelyreállításhoz. Beállíthat egy készenléti adatbázis-példányt egy [párosított Azure-régióban,](/azure/best-practices-availability-paired-regions) és beállíthat data guard feladatátvételt a vész-helyreállítási. A nulla adatvesztés érdekében ajánlott az Active Data Guard mellett egy Oracle Data Guard Far Sync példányt is telepíteni. 

Fontolja meg a Data Guard Far Sync példány beállítását az Oracle elsődleges adatbázisától eltérő rendelkezésre állási zónában, ha az alkalmazás engedélyezi a késést (alapos tesztelésre van szükség). A **Maximális rendelkezésre állás** mód használatával beállíthatja a redo fájlok szinkron átvitelét a Far Sync példányba. Ezeket a fájlokat ezután aszinkron módon átviszi a készenléti adatbázisba. 

Ha az alkalmazás nem teszi lehetővé a teljesítménycsökkenést, amikor a Far Sync példányt egy másik rendelkezésre állási zónában állítja be **maximális rendelkezésre állási** módban (szinkron), beállíthat egy Far Sync példányt az elsődleges adatbázissal azonos rendelkezésre állási zónában. A további rendelkezésre állás érdekében fontolja meg több Far Sync-példány beállítását az elsődleges adatbázis közelében, és legalább egy példányt a készenléti adatbázis közelében (ha a szerepkör áttűnik). Tudjon meg többet az Oracle Data Guard Far Sync ebben [az Oracle Active Data Guard Far Sync tanulmány](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf).

Az Oracle Standard Edition adatbázisok használatakor vannak olyan ISV-megoldások, mint például a DBVisit Standby, amely lehetővé teszi a magas rendelkezésre állás és a vészhelyreállítás beállítását.

## <a name="reference-architectures"></a>Referenciaarchitektúrák

### <a name="oracle-data-guard"></a>Oracle Data Guard

Az Oracle Data Guard magas rendelkezésre állást, adatvédelmet és vészhelyreállítást biztosít a vállalati adatok számára. A Data Guard a készenléti adatbázisokat az elsődleges adatbázis tranzakciós konzisztens másolataként tartja fenn. Az elsődleges és másodlagos adatbázisok közötti távolságtól és az alkalmazás késéstűrési toleranciájától függően szinkron vagy aszinkron replikációt állíthat be. Ezt követően, ha az elsődleges adatbázis nem érhető el egy tervezett vagy nem tervezett kimaradás miatt, a Data Guard bármely készenléti adatbázist átválthat az elsődleges szerepkörre, minimalizálva az állásidőt. 

Az Oracle Data Guard használatakor a másodlagos adatbázist írásvédett célokra is megnyithatja. Ezt a konfigurációt Aktív adatvédőnek hívják. Az Oracle Database 12c bemutatta a Data Guard Far Sync Instance nevű funkciót. Ez a példány lehetővé teszi, hogy az Oracle-adatbázis nulla adatvesztési konfigurációját a teljesítmény veszélyeztetése nélkül állítsa be.

> [!NOTE]
> Az Active Data Guard további licencelést igényel. Ez a licenc a Far Sync funkció használatához is szükséges. Kérjük, lépjen kapcsolatba az Oracle képviselőjével, hogy megvitassák a licencelés következményeit.

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle Data Guard az FSFO-val
Az Oracle Data Guard gyors indítási feladatátvételsel (FSFO) további rugalmasságot biztosít a bróker külön gépen való beállításával. A Data Guard broker és a másodlagos adatbázis egyaránt futtatja a megfigyelőt, és figyelje meg az elsődleges adatbázist az állásidőre. Ez lehetővé teszi a redundanciát a Data Guard megfigyelőbeállításában is. 

Az Oracle Database 12.2-es vagy újabb verziójával több megfigyelő tanusítható egyetlen Oracle Data Guard brókerkonfigurációval. Ez a beállítás további rendelkezésre állást biztosít, ha egy megfigyelő és a másodlagos adatbázis állásidő. Data Guard Broker könnyű, és egy viszonylag kis virtuális gépen üzemeltethető. Ha többet szeretne megtudni a Data Guard Brokerről és annak előnyeiről, látogasson el az [Oracle dokumentációjára](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) ebben a témában.

Az alábbi ábra egy ajánlott architektúra az Oracle Data Guard az Azure-ban a rendelkezésre állási zónák használata. Ez az architektúra lehetővé teszi, hogy a virtuális gép 99,99%-os uptime SLA-ját.

![Oracle Database a rendelkezésre állási zónák használatával a Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

Az előző ábrán az ügyfélrendszer az Oracle háttérrendszerrel az interneten keresztül fér hozzá egy egyéni alkalmazáshoz. A webes előtér terheléselosztóban van konfigurálva. A webes előtér felhívja a megfelelő alkalmazáskiszolgálót a munka kezeléséhez. Az alkalmazáskiszolgáló lekérdezi az elsődleges Oracle-adatbázist. Az Oracle adatbázis egy hiperszálas [memóriaoptimalizált virtuális gép](../../../virtual-machines/windows/sizes-memory.md) pelenka, korlátozott mag [vCPU-k,](../../../virtual-machines/windows/constrained-vcpu.md) hogy mentse a licencelési költségeket, és maximalizálja a teljesítményt. A teljesítmény és a magas rendelkezésre állás érdekében több prémium díjas vagy ultralemez (felügyelt lemezek) is használható.

Az Oracle adatbázisok több rendelkezésre állási zónába kerülnek a magas rendelkezésre állás érdekében. Minden zóna egy vagy több, független áramellátással, hűtéssel és hálózattal felszerelt adatközpontból áll. A rugalmasság biztosítása érdekében minden engedélyezett régióban legalább három külön zóna van beállítva. A régión belüli rendelkezésre állási zónák fizikai elkülönítése megvédi az adatokat az adatközponti hibáktól. Emellett két FSFO-megfigyelő két rendelkezésre állási zónában van beállítva, hogy kezdeményezze és feladatátvételt az adatbázis a másodlagos, ha kimaradás történik. 

Az előző architektúrában látható zónától eltérő zónában további megfigyelőket és/vagy készenléti adatbázisokat is beállíthat (AZ 1, ebben az esetben). Végül az Oracle-adatbázisokat az Oracle Enterprise Manager (OEM) figyeli az állásidő és a teljesítmény érdekében. OEM is lehetővé teszi, hogy különböző teljesítmény-és használati jelentések.

Azokban a régiókban, ahol a rendelkezésre állási zónák nem támogatottak, a rendelkezésre állási csoportok segítségével telepítheti az Oracle Database magas rendelkezésre állású módon. A rendelkezésre állási készletek lehetővé teszik, hogy a virtuális gép 99,95%-os rendelkezésre állását érje el. A következő ábra a használat referenciaarchitektúrája:

![Oracle Database a Data Guard Broker készleteivel - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * Az Oracle Enterprise Manager virtuális gép nem kell elhelyezni a rendelkezésre állási csoport, mivel csak egy példánya oem telepítése.
> * Az ultralemezek jelenleg nem támogatottak a rendelkezésre állási konfigurációban.

#### <a name="oracle-data-guard-far-sync"></a>Oracle Data Guard Távol Sync

Az Oracle Data Guard Far Sync adatveszteség-védelmi képességet biztosít az Oracle adatbázisok számára. Ez a funkció lehetővé teszi az adatvesztés elleni védelmet, ha az adatbázis-gép meghibásodik. Az Oracle Data Guard Far Sync-et külön virtuális gépre kell telepíteni. A Far Sync egy könnyű Oracle példány, amely csak vezérlőfájllal, jelszófájllal, spfile-fájllal és készenléti naplóval rendelkezik. Nincsenek adatfájlok vagy rego naplófájlok. 

A nulla adatvesztés elleni védelem érdekében szinkron kommunikációnak kell lennie az elsődleges adatbázis és a Far Sync példány között. A Far Sync példány szinkron módon fogadja az újraműveleteket az elsődleges példánytól, és azonnal továbbítja azt az összes készenléti adatbázisnak aszinkron módon. Ez a beállítás csökkenti az elsődleges adatbázis terhelését is, mivel csak a távol-szinkronizálási példánynak kell elküldenie az ismétlést, nem pedig az összes készenléti adatbázisnak. Ha egy Távoli szinkronizálási példány meghibásodik, a Data Guard automatikusan aszinkron átvitelt használ a másodlagos adatbázisba az elsődleges adatbázisból a közel nulla adatvesztés elleni védelem fenntartásához. A további rugalmasság érdekében az ügyfelek adatbázispéldányonként (elsődleges és másodlagos példányok) több Távoli szinkronizálási példányt is telepíthetnek.

Az alábbi ábra az Oracle Data Guard Far Sync használatával magas rendelkezésre állású architektúrát ábrázol:

![Oracle adatbázis a rendelkezésre állási zónák at Data Guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

Az előző architektúrában van egy Távoli szinkronizálási példány telepítve ugyanabban a rendelkezésre állási zónában, mint az adatbázis-példány, hogy csökkentse a késést a kettő között. Azokban az esetekben, ahol az alkalmazás késés érzékeny, fontolja meg az adatbázis és a Far Sync példány vagy példányok egy [közelség elhelyezési csoportban.](../../../virtual-machines/linux/proximity-placement-groups.md)

Az alábbi ábra az Oracle Data Guard FSFO és a Far Sync architektúráját felhasználva valósítja meg a magas rendelkezésre állás és a vészhelyreállítás elérését:

![Oracle Database a rendelkezésre állási zónák at katasztrófa utáni helyreállítás data guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

A GoldenGate lehetővé teszi az adatok cseréjét és manipulálását a tranzakció szintjén több, heterogén platform között a vállalaton belül. A véglegesített tranzakciókat tranzakcióintegritással és minimális terheléssel helyezi át a meglévő infrastruktúrára. Moduláris felépítése rugalmasságot biztosít a kiválasztott adatrekordok, tranzakciós változások és DDL -módosítások (adatdefiníciós nyelv) kinyeréséhez és replikálásához a különböző topológiák között.

Az Oracle GoldenGate lehetővé teszi az adatbázis konfigurálását a magas rendelkezésre állásérdekében kétirányú replikáció biztosításával. Ez lehetővé teszi **többfős** vagy **aktív-aktív konfiguráció**beállítását. Az alábbi ábra az Oracle GoldenGate aktív-aktív beállítás ajánlott architektúrája az Azure-ban. A következő architektúrában az Oracle-adatbázis egy hiperszálas [memóriaoptimalizált virtuális gép pelenka,](../../../virtual-machines/windows/sizes-memory.md) [korlátozott magvCPU-kkal](../../../virtual-machines/windows/constrained-vcpu.md) van konfigurálva a licencelési költségek megtakarítása és a teljesítmény maximalizálása érdekében. A teljesítmény és a rendelkezésre állás érdekében több prémium díjas vagy ultralemez (felügyelt lemez) használható.

![Oracle Database a rendelkezésre állási zónák használatával a Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Hasonló architektúra lehet beállítani a rendelkezésre állási csoportok olyan régiókban, ahol a rendelkezésre állási zónák jelenleg nem érhető el.

Az Oracle GoldenGate olyan folyamatokkal rendelkezik, mint a Kinyerés, a Szivattyú és a Replikát, amelyek segítenek az adatok aszinkron replikálásában az egyik Oracle adatbázis-kiszolgálóról a másikra. Ezek a folyamatok lehetővé teszik, hogy kétirányú replikációt állítson be az adatbázis magas rendelkezésre állásának biztosítása érdekében, ha rendelkezésre áll a zónaszintű állásidő. Az előző ábrán a kibontási folyamat ugyanazon a kiszolgálón fut, mint az Oracle-adatbázis, míg az adatszivattyú és a replikát folyamatok egy külön kiszolgálón futnak ugyanabban a rendelkezésre állási zónában. A Replikátat folyamat segítségével adatokat fogad az adatbázisból a másik rendelkezésre állási zónában, és véglegesíti az adatokat az Oracle adatbázis a rendelkezésre állási zónában. Hasonlóképpen az adatszivattyú folyamat a kibontási folyamat által kinyert adatokat küld a replikát folyamat a másik rendelkezésre állási zónában. 

Míg az előző architektúradiagram egy külön kiszolgálón konfigurált adatszivattyú- és replikátai folyamatot mutat be, az oracle GoldenGate folyamatokat ugyanazon a kiszolgálón állíthatja be a kiszolgáló kapacitása és használata alapján. Mindig tekintse meg az AWR-jelentést és az Azure-beli mutatókat a kiszolgáló használati mintájának megértéséhez.

Az Oracle GoldenGate kétirányú replikációjának beállításakor a különböző rendelkezésre állási zónákban vagy különböző régiókban fontos, hogy a különböző összetevők közötti késés elfogadható legyen az alkalmazás számára. A rendelkezésre állási zónák és a régiók közötti késés változhat, és több tényezőtől függ. Javasoljuk, hogy állítson be teljesítményteszteket az alkalmazásszint és az adatbázisszint között különböző rendelkezésre állási zónákban és/vagy régiókban, hogy ellenőrizze, hogy megfelel-e az alkalmazás teljesítménykövetelményeinek.

Az alkalmazásszint saját alhálózatában állítható be, és az adatbázisszint a saját alhálózatára bontható. Ha lehetséges, fontolja meg az [Azure Application Gateway](../../../application-gateway/overview.md) használatával az alkalmazáskiszolgálók közötti terheléselosztási forgalmat. Az Azure Application Gateway egy robusztus webes terheléselosztó. Cookie-alapú munkamenet-affinitást biztosít, amely a felhasználói munkamenetet ugyanazon a kiszolgálón tartja, így minimalizálja az adatbázis ütközéseit. Az Application Gateway alternatívái az [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) és az [Azure Traffic Manager.](../../../traffic-manager/traffic-manager-overview.md)

### <a name="oracle-sharding"></a>Oracle Sharding

A szilánkok egy adatréteg-minta, amelyet az Oracle 12.2-ben vezettek be. Lehetővé teszi az adatok horizontális particionálást és méretezését független adatbázisok között. Ez egy megosztás-semmit architektúra, ahol minden adatbázis egy dedikált virtuális gépen található, amely lehetővé teszi a magas olvasási és írási átviteli feszültséget a rugalmasság és a megnövekedett rendelkezésre állás mellett. Ez a minta kiküszöböli az egyes meghibásodási pontokat, biztosítja a hibaelkülönítést, és lehetővé teszi a működés közbeni frissítéseket állásidő nélkül. Egy shard vagy egy adatközpont-szintű hiba állásideje nincs hatással a többi szegmensek teljesítményére vagy rendelkezésre állására más adatközpontokban. 

A nagy átviteli sebességű OLTP-alkalmazásokhoz, amelyek nem engedhetik meg maguknak az állásidőt, nagy átviteli sebességű. Minden sor azonos szilánkos kulccsal mindig garantáltan ugyanazon a szegmensen, így a teljesítmény növelése a magas konzisztencia. A skálázást használó alkalmazásoknak jól definiált adatmodell- és adatterjesztési stratégiával kell rendelkezniük (konzisztens kivonattal, tartománysal, listával vagy összetettsel), amely elsősorban egy skálázási kulcs (például *customerId* vagy *accountNum)* használatával fér hozzá az adatokhoz. A szilánkolás lehetővé teszi bizonyos adathalmazok tárolását a végfelhasználókhoz közelebb, így segítve a teljesítmény- és megfelelőségi követelmények teljesítését.

Ajánlott replikálni a szegmensek a magas rendelkezésre állás és a vészhelyreállítás. Ez a beállítás oracle technológiákkal, például Oracle Data Guard vagy Oracle GoldenGate alkalmazással végezhető el. A replikáció shard, egy szegmens része vagy egy szegmensek csoportja lehet. A szilánkos adatbázis rendelkezésre állását nem befolyásolja egy vagy több szegmensek kimaradása vagy lassulása. A magas rendelkezésre állás érdekében a készenléti szilánkok lehet helyezni ugyanabban a rendelkezésre állási zónában, ahol az elsődleges szilánkok kerülnek. Vész-helyreállítási a készenléti szilánkok egy másik régióban található. A szegmensek több régióban is üzembe helyezhetik a forgalmat ezekben a régiókban. További információ a szilánkos adatbázis magas rendelkezésre állásának és replikálásának konfigurálásáról az [Oracle Sharding dokumentációjában.](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html)

Az Oracle Sharding elsősorban a következő összetevőkből áll. Ezekről az összetevőkről további információ az [Oracle Sharding dokumentációjában](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)található:

- **Shard katalógus** – Speciális célú Oracle-adatbázis, amely a Shard-adatbázis összes konfigurációs adatának állandó tárolója. Minden konfigurációs módosítások, például hozzáadása vagy eltávolítása szegmensek, az adatok leképezése és a DDL-ek egy szegmens adatbázisban kezdeményezi a szegmens katalógusban. A szegmenskatalógus az SDB összes duplikált táblájának fő példányát is tartalmazza. 

  A szegmenskatalógus materializált nézeteket használ, hogy automatikusan replikálja a módosításokat az összes szegmensben lévő duplikált táblákra. A szegmenskatalógus-adatbázis lekérdezéskoordinátorként is működik, amely több szegmensből álló lekérdezéseket és olyan lekérdezéseket dolgoz fel, amelyek nem adnak meg szilánkos kulcsot. 
  
  Az Oracle Data Guard használata a rendelkezésre állási zónákkal vagy a shard katalógus magas rendelkezésre állásához rendelkezésre álló készletekkel együtt ajánlott ajánlott. A szegmenskatalógus rendelkezésre állása nincs hatással a szilánkos adatbázis rendelkezésre állására. A szegmenskatalógusban lévő állásidő csak a karbantartási műveleteket és a többmerevlemezes lekérdezéseket érinti az adatőr feladatátvétel befejezésének rövid időszakában. Az online tranzakciókat továbbra is az SDB irányítja és hajtja végre, és a katalóguskimaradás nem érinti őket.

- **Shard igazgatók** – Könnyű szolgáltatások, amelyeket telepíteni kell minden régióban/rendelkezésre állási zónában, ahol a szilánkok található. A shard igazgatók az Oracle Sharding keretében telepített globális szolgáltatáskezelők. Magas rendelkezésre állás érdekében ajánlott, hogy üzembe helyezlegalább egy shard igazgató minden rendelkezésre állási zónában, amelyben a szegmensek léteznek. 

  Az adatbázishoz való első csatlakozáskor az útválasztási információkat egy szegmensigazgató állítja be, és a rendszer gyorsítótárba helyezi a későbbi kérelmekhez, megkerülve a szegmensigazgatót. Miután a munkamenet létrejött egy shard, az összes SQL-lekérdezések és DML-ek támogatottak és végrehajtottak a hatókörben az adott szegmens. Ez az útválasztás gyors, és minden OLYAN OLTP-számítási feladathoz használatos, amely shard-tranzakciókat hajt végre. Javasoljuk, hogy közvetlen útválasztást használjon minden OLTP számítási feladathoz, amely a legmagasabb teljesítményt és rendelkezésre állást igényel. Az útválasztási gyorsítótár automatikusan frissül, ha egy szegmens elérhetetlenné válik, vagy a szilánkos topológiában változások következnek be. 
  
  Nagy teljesítményű, adatfüggő útválasztás esetén az Oracle azt javasolja, hogy a szilánkos adatbázisban lévő adatok elérésekor használjon kapcsolatkészletet. Az Oracle kapcsolatkészletei, nyelvspecifikus könyvtárai és illesztőprogramjai támogatják az Oracle Sharding-ot. További részleteket az [Oracle Sharding dokumentációjában](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9) talál.

- **Globális szolgáltatás** – A globális szolgáltatás hasonló a normál adatbázis-szolgáltatáshoz. Az adatbázis-szolgáltatások összes tulajdonsága mellett egy globális szolgáltatás olyan parharded adatbázisok tulajdonságaival rendelkezik, mint például az ügyfelek és a szegmensek közötti régióaffinitás és a replikációs késés tűréshatár. Csak egy globális szolgáltatást kell létrehozni az adatok olvasásához/írásához egy szilánkos adatbázisba. Az Active Data Guard használatakor és a szegmensek csak olvasható replikák beállításakor létrehozhat egy másik gGobal szolgáltatást az írásvédett számítási feladatokhoz. Az ügyfél ezekkel a globális szolgáltatásokkal csatlakozhat az adatbázishoz.

- **Shard adatbázisok** - Shard adatbázisok az Oracle adatbázisok. Minden adatbázis replikálása az Oracle Data Guard használatával történik egy Broker konfigurációban, ha a Fast-Start feladatátvétel (FSFO) engedélyezve van. Nem kell beállítani a Data Guard feladatátvétel és a replikáció minden szegmensen. Ez automatikusan konfigurálva van, és a megosztott adatbázis létrehozásakor települ. Ha egy adott szegmens meghibásodik, az Oracle Sharing automatikusan átadja az adatbázis-kapcsolatokat az elsődleges és a készenléti rendszer.

Az Oracle sharded adatbázisok két felülettel telepíthető és kezelhető: Oracle Enterprise `GDSCTL` Manager Cloud Control GUI és/vagy a parancssori segédprogram. A felhővezérlés használatával a különböző szegmensek rendelkezésre állását és teljesítményét is figyelheti. A `GDSCTL DEPLOY` parancs automatikusan létrehozza a szegmensek és a megfelelő figyelők. Ezenkívül ez a parancs automatikusan telepíti a rendszergazda által megadott shard szintű magas rendelkezésre álláshoz használt replikációs konfigurációt.

Az adatbázisok at többféleképpen is szilánkokkal lehet elsülíteni:

* Rendszer által felügyelt szilánkok – automatikusan elosztja a szilánkok particionálás használatával
* Felhasználó által definiált szilánkok – Lehetővé teszi az adatok leképezése a szegmensek, ami jól működik, ha vannak szabályozási vagy adatlokalizációs követelmények)
* Összetett szilánkok – a rendszer által felügyelt és a felhasználó által definiált szilánkok kombinációja a különböző _shardspaces_
* Tábla alpartíciók - Hasonló a rendszeres particionált tábla.

További információ a különböző [szilánkok módszer](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) az Oracle dokumentációjában.

Míg a szilánkos adatbázis úgy tűnhet, mint egy adatbázis az alkalmazások és a fejlesztők számára, amikor egy nem szilánkos adatbázisból egy szilánkos adatbázisba való áttelepítése során gondos tervezésszükséges annak meghatározásához, hogy mely táblák ismétlődnek, szemben a szilánkos. 

A duplikált táblák az összes szilánkon tárolódnak, míg a szilánkos táblák különböző szegmensek között vannak elosztva. A javaslat az, hogy a kis és méretbeli táblák duplikálása és a ténytáblák terjesztése/szilánkja. Az adatok betölthetők a szilánkos adatbázisba a szegmenskatalógus központi koordinátorként vagy adatszivattyú futtatásával minden szegmensen. További információ az [adatok szilánkos adatbázisba való áttelepítéséről](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) az Oracle dokumentációjában.

#### <a name="oracle-sharding-with-data-guard"></a>Oracle sharding a Data Guard

Oracle Data Guard rendszer által felügyelt, felhasználó által definiált és összetett szilánkolási módszerekkel használható.

Az alábbi ábra az Oracle Sharding referenciaarchitektúrája az Oracle Data Guard segítségével, amely az egyes szegmensek magas rendelkezésre állásához használatos. Az architektúradiagram egy _összetett szilánkolási módszert_jelenít meg. Az architektúradiagram valószínűleg eltérő lesz az olyan alkalmazások esetében, amelyek különböző követelményekkel rendelkező adatok hely, terheléselosztás, magas rendelkezésre állás, vész-helyreállítási, stb, és eltérő módszert használhat a szilánkok. Oracle horizontális lehetővé teszi, hogy megfeleljen ezeknek a követelményeknek, és a skála vízszintesen és hatékonyan azáltal, hogy ezeket a lehetőségeket. Hasonló architektúra is telepíthető az Oracle GoldenGate használatával.

![Oracle Database Sharding a rendelkezésre állási zónák használata a Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Míg a rendszer által felügyelt szilánkok a legkönnyebben konfigurálható és kezelhető, a felhasználó által definiált szilánkok vagy összetett szilánkok jól illeszkedik a forgatókönyvek, ahol az adatok és az alkalmazás földrajzilag elosztott, vagy olyan esetekben, ahol az egyes szegmensek replikációjának szabályozása szükséges. 

Az előző architektúrában az összetett horizontális skálázás az adatok földrajzi terjesztésére és horizontálisan horizontálisan horizontálisan az alkalmazásszintek horizontális rakoncátora. Összetett szilánkok a rendszer által kezelt és a felhasználó által meghatározott szilánkok kombinációja, és így mindkét módszer előnyeit biztosítja. Az előző forgatókönyvben az adatok először szilánkos több régió által elválasztott shardspaces között. Ezután az adatok at tovább particionálva konzisztens kivonat a shardspace több szegmensek között. Minden shardspace több shardgroups tartalmaz. Minden shardgroup rendelkezik több szegmensek és egy "egység" a replikáció, ebben az esetben. Minden shardgroup tartalmazza az összes adatot a shardspace. Az A1 és b1 shardcsoportok elsődleges szilánkcsoportok, míg az A2 és B2 shardcsoportok készenléti állapotok. Dönthet úgy, hogy az egyes szegmensek a replikáció egysége, nem pedig egy shardgroup.

Az előző architektúrában egy GSM/shard-igazgató van telepítve minden rendelkezésre állási zónában a magas rendelkezésre állás érdekében. A javaslat az, hogy adatközpontonként/régiónként legalább egy GSM/shard-igazgató üzembe helyezése. Emellett az alkalmazáskiszolgáló egy példánya minden rendelkezésre állási zónában telepítve van, amely egy shardgroup-ot tartalmaz. Ez a beállítás lehetővé teszi, hogy az alkalmazás alacsonyan tartsa a késést az alkalmazáskiszolgáló és az adatbázis/shardgroup között. Ha egy adatbázis meghibásodik, a készenléti adatbázissal azonos zónában lévő alkalmazáskiszolgáló képes kezelni a kérelmeket, amint az adatbázis-szerepkör váltása megtörténik. Az Azure Application Gateway és a szegmensigazgatója ennek megfelelően nyomon követheti a kérés- és válaszkésésés útvonalkéréseket.

Alkalmazásszempontból az ügyfélrendszer kérelmet nyújt be az Azure Application Gateway (vagy más terheléselosztási technológiák az Azure-ban), amely átirányítja a kérelmet a régió legközelebb az ügyfélhez. Az Azure Application Gateway is támogatja a ragadós munkamenetek, így az azonos ügyféltől érkező kérelmek ugyanarra az alkalmazáskiszolgálóra. Az alkalmazáskiszolgáló kapcsolatkészletezést használ az adatelérési illesztőprogramok között. Ez a funkció olyan illesztőprogramokban érhető el, mint a JDBC, ODP.NET, OCI stb. Az illesztőprogramok felismerik a kérelem részeként megadott szilánkos kulcsokat. A JDBC-ügyfelek hez használható [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) lehetővé teheti, hogy a nem Oracle alkalmazásügyfelek, például az Apache Tomcat és az IIS együttműködjenek az Oracle Sharding szolgáltatással. 

Az első kérelem során az alkalmazáskiszolgáló csatlakozik a shard igazgató a régióban, hogy útválasztási információkat a szegmens, amely a kérelmet kell irányítani. Az átadott szilánkos kulcs alapján a rendező az alkalmazáskiszolgálót a megfelelő szegmenshez irányítja. Az alkalmazáskiszolgáló gyorsítótárazza ezt az információt egy térkép létrehozásával, és a későbbi kérelmek esetében megkerüli a szegmensigazgatót, és a kérelmeket egyenesen a szegmensbe irányítja.

#### <a name="oracle-sharding-with-goldengate"></a>Oracle Sharding a GoldenGate

Az alábbi ábra az Oracle Sharding és az Oracle GoldenGate referenciaarchitektúrája az egyes szegmensek régión belül magas rendelkezésre állásához. Az előző architektúrával ellentétben ez az architektúra csak egy Azure-régión (több rendelkezésre állási zónán) belül magas rendelkezésre állású. Az Oracle GoldenGate használatával telepíthet egy többrégiós, magas rendelkezésre állású szilánkos adatbázist (az előző példához hasonlóan).

![Oracle Database Sharding a goldengate-i rendelkezésre állási zónák használatával](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

Az előző referencia architektúra a _rendszer által felügyelt_ szilánkos az adatokat. Mivel az Oracle GoldenGate replikáció adattömbszintjén történik, az egyik szegmensre replikált adatok fele replikálható egy másik szegmensbe. A másik fele replikálható egy másik szegmensbe. 

Az adatok replikálásának módja a replikációs tényezőtől függ. 2-es replikációs tényezővel minden adattömböt a shardgroup három szegmensében kap. Hasonlóképpen a shardgroup 3 és három szilánkok replikációs tényezője, az egyes szegmensekben lévő összes adat replikálódik a shardgroup minden más szegmensre. A shardgroup minden shardcsoportban lehet egy másik replikációs tényező. Ez a beállítás segít a magas rendelkezésre állás és a vész-helyreállítási tervezés hatékony meghatározásában egy shardgroup és több shardgroups között.

Az előző architektúrában az A shardgroup és a B shardgroup is ugyanazokat az adatokat tartalmazza, de különböző rendelkezésre állási zónákban található. Ha mind az A shardgroup A és a B shardgroup ugyanaz t3 azonos replikációs tényezővel rendelkezik, a szilánkos tábla minden egyes sora/darabja 6-szor replikálódik a két shardgroups között. Ha az A shardgroup replikációs tényezője 3, a B shardgroup replikációs tényezője pedig 2, akkor minden sor/adattömb 5-ször replikálódik a két shardgroups között.

Ez a beállítás megakadályozza az adatvesztést, ha példányszintű vagy rendelkezésre állási zónaszintű hiba lép fel. Az alkalmazásréteg képes olvasni és írni az egyes szegmensek. Az ütközések minimalizálása érdekében az Oracle Sharding egy "fő adattömböt" jelöl a kivonatértékek minden tartományához. Ez a funkció biztosítja, hogy egy adott adattömb írási kérelmek a megfelelő adattömbhöz irányulnak. Ezenkívül az Oracle GoldenGate automatikus ütközésészlelést és -feloldást is biztosít az esetlegesen felmerülő konfliktusok kezelésére. A GoldenGate oracle sharding-mal való megvalósításának további információi és korlátai az Oracle GoldenGate egy [szilánkos adatbázissal való használatáról](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72)szóló dokumentációban találhatók.

Az előző architektúrában egy GSM/shard-igazgató van telepítve minden rendelkezésre állási zónában a magas rendelkezésre állás érdekében. A javaslat az, hogy adatközpontonként vagy régiónként legalább egy GSM/shard-igazgató üzembe helyezése. Emellett az alkalmazáskiszolgáló egy példánya minden rendelkezésre állási zónában telepítve van, amely egy shardgroup-ot tartalmaz. Ez a beállítás lehetővé teszi, hogy az alkalmazás alacsonyan tartsa a késést az alkalmazáskiszolgáló és az adatbázis/shardgroup között. Ha egy adatbázis meghibásodik, a készenléti adatbázissal azonos zónában lévő alkalmazáskiszolgáló képes kezelni a kérelmeket, amint az adatbázisszerepkör áttűnik. Az Azure Application Gateway és a szegmensigazgatója ennek megfelelően nyomon követheti a kérés- és válaszkésésés útvonalkéréseket.

Alkalmazásszempontból az ügyfélrendszer kérelmet nyújt be az Azure Application Gateway (vagy más terheléselosztási technológiák az Azure-ban), amely átirányítja a kérelmet a régió legközelebb az ügyfélhez. Az Azure Application Gateway is támogatja a ragadós munkamenetek, így az azonos ügyféltől érkező kérelmek ugyanarra az alkalmazáskiszolgálóra. Az alkalmazáskiszolgáló kapcsolatkészletezést használ az adatelérési illesztőprogramok között. Ez a funkció olyan illesztőprogramokban érhető el, mint a JDBC, ODP.NET, OCI stb. Az illesztőprogramok felismerik a kérelem részeként megadott szilánkos kulcsokat. A JDBC-ügyfelek hez használható [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) lehetővé teheti, hogy a nem Oracle alkalmazásügyfelek, például az Apache Tomcat és az IIS együttműködjenek az Oracle Sharding szolgáltatással. 

Az első kérelem során az alkalmazáskiszolgáló csatlakozik a shard igazgató a régióban, hogy útválasztási információkat a szegmens, amely a kérelmet kell irányítani. Az átadott szilánkos kulcs alapján a rendező az alkalmazáskiszolgálót a megfelelő szegmenshez irányítja. Az alkalmazáskiszolgáló gyorsítótárazza ezt az információt egy térkép létrehozásával, és a későbbi kérelmek esetében megkerüli a szegmensigazgatót, és a kérelmeket egyenesen a szegmensbe irányítja.

## <a name="patching-and-maintenance"></a>Javítás és karbantartás

Az Oracle-számítási feladatok Azure-ba történő üzembe helyezésekor a Microsoft gondoskodik az összes gazdaoperációs rendszerszintű javításról. A tervezett operációsrendszer-szintű karbantartást előre közlik az ügyfelekkel, hogy az ügyfél számára lehetővé tegyék a tervezett karbantartást. Két különböző rendelkezésre állási zóna két kiszolgálója soha nem lesz egyszerre javítva. A virtuális gépek karbantartásával és javításával kapcsolatos további részletekért [olvassa el a virtuális gépek rendelkezésre állásának kezelése](../../../virtual-machines/linux/manage-availability.md) című témakört. 

A virtuálisgép-operációs rendszer javítása az [Azure Automation](../../../automation/automation-tutorial-update-management.md)segítségével automatizálható. Az Oracle-adatbázis javítása és karbantartása automatizálható és ütemezhető az [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops) vagy az [Azure Automation](../../../automation/automation-tutorial-update-management.md) használatával az állásidő minimalizálása érdekében. Tekintse meg a folyamatos kézbesítés és a [kék/zöld központi telepítések című témakört,](/azure/devops/learn/what-is-continuous-delivery) hogy megtudhatja, hogyan használható az Oracle-adatbázisok kontextusában.

## <a name="architecture-and-design-considerations"></a>Építészeti és tervezési szempontok

- Fontolja meg a hyperthreaded [memória optimalizált virtuális gép](../../../virtual-machines/windows/sizes-memory.md) korlátozott mag [vCPU-k](../../../virtual-machines/windows/constrained-vcpu.md) az Oracle Database VM menteni a licencelési költségeket, és maximalizálja a teljesítményt. A teljesítmény és a rendelkezésre állás érdekében használjon több prémium díjas vagy ultralemezt (felügyelt lemezeket).
- Felügyelt lemezek használata esetén a lemez/eszköz neve újraindul. Javasoljuk, hogy a név helyett az uuid eszközt használja annak biztosítására, hogy a csatlakoztatások továbbra is fennálljanak az újraindítások során. További információ [itt](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab)található .
- A rendelkezésre állási zónák használatával magas rendelkezésre állás érhető el a régióban.
- Fontolja meg az Oracle-adatbázis ultralemezeit (ha rendelkezésre áll) vagy prémium szintű lemezek használatát.
- Fontolja meg egy készenléti Oracle-adatbázis beállítását egy másik Azure-régióban az Oracle Data Guard használatával.
- Fontolja meg [a közelségelhelyezési csoportok](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) használatát az alkalmazás és az adatbázisszint közötti késés csökkentése érdekében.
- Állítsa be az [Oracle Enterprise Managert](https://docs.oracle.com/en/enterprise-manager/) felügyeletre, figyelésre és naplózásra.
- Fontolja meg az Oracle Automatic Storage Management (ASM) használatát az adatbázis egyszerűbb tárolásfelügyeletéhez.
- Az [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) használatával állásidő nélkül kezelheti az adatbázis-javításokat és frissítéseket.
- Az alkalmazáskód finomhangolására felhőalapú mintákat, például [újrapróbálkozási mintát,](/azure/architecture/patterns/retry) [megszakítómintát](/azure/architecture/patterns/circuit-breaker)és a [Cloud Design Patterns útmutatóban](/azure/architecture/patterns/) meghatározott egyéb mintákat adhat hozzá, amelyek segíthetnek az alkalmazás rugalmasabb kialakításában.

## <a name="next-steps"></a>További lépések

Tekintse át az alábbi Oracle-referenciacikkeket, amelyek az Ön forgatókönyvére vonatkoznak.

- [Bevezetés az Oracle Data Guard szolgáltatásba](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Oracle Data Guard Broker fogalmak](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Az Oracle GoldenGate konfigurálása aktív-aktív magas rendelkezésre álláshoz](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Az Oracle Sharding áttekintése](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Active Data Guard Far Sync Nulla adatveszteség bármilyen távolságból](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
