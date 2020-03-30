---
title: Globálisan elérhető szolgáltatások tervezése
description: Ismerje meg az Azure SQL Database használatával elérhető, magas rendelkezésre állású szolgáltatások alkalmazástervezését.
keywords: felhővész-helyreállítás,vészhelyreállítási megoldások,alkalmazásadatok biztonsági mentése,georeplikáció,üzletmenet-folytonossági tervezés
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 348bd2b92801217a5aea2ef4d1426c020085e4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269067"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Globálisan elérhető szolgáltatások tervezése az Azure SQL Database használatával

Amikor felhőszolgáltatásokat hoz létre és telepít az Azure SQL Database [használatával, aktív georeplikációs](sql-database-active-geo-replication.md) vagy [automatikus feladatátvételi csoportokat](sql-database-auto-failover-group.md) használ a regionális kimaradásokkal és a katasztrofális hibákkal szembeni ellenálló képesség biztosításához. Ugyanez a funkció lehetővé teszi, hogy globálisan elosztott alkalmazásokat hozzon létre az adatok helyi elérésére optimalizálva. Ez a cikk ismerteti a gyakori alkalmazásmintákat, beleértve az egyes lehetőségek előnyeit és kompromisszumait.

> [!NOTE]
> Prémium szintű vagy üzleti legkritikusabb adatbázisok és rugalmas készletek használata esetén rugalmassá teheti őket a regionális kimaradásokkal szemben, ha zónaredundáns központi telepítési konfigurációvá alakítja őket. Lásd: [Zónaredundáns adatbázisok](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>1. forgatókönyv: Két Azure-régió használata az üzletmenet folytonossága érdekében minimális állásidő mellett

Ebben a forgatókönyvben az alkalmazások a következő jellemzőkkel rendelkeznek:

* Az alkalmazás egy Azure-régióban aktív
* Minden adatbázis-munkamenethez olvasási és írási hozzáférés (RW) szükséges az adatokhoz
* A késés és a forgalmi költségek csökkentése érdekében a webes réteget és az adatréteget egy bekell helyezni
* Alapvetően az állásidő nagyobb üzleti kockázatot jelent ezeknél az alkalmazásoknál, mint az

Ebben az esetben az alkalmazás központi telepítési topológia regionális katasztrófák kezelésére van optimalizálva, ha az összes alkalmazás-összetevőkell feladatátvételt együtt. Az alábbi ábra ezt a topológiát mutatja. Földrajzi redundancia esetén az alkalmazás erőforrásai az A és B régióba vannak telepítve. A B régió erőforrásait azonban nem használja a rendszer, amíg az A régió meg nem hiúsul. A feladatátvételi csoport a két régió között van konfigurálva az adatbázis-kapcsolat, a replikáció és a feladatátvétel kezelésére. A webszolgáltatás mindkét régióban úgy van konfigurálva, hogy az írás-olvasás figyelő ** &lt;feladatátvevő&gt;-csoportneve .database.windows.net** (1) keresztül férjen hozzá az adatbázishoz. A Traffic Manager [prioritás-útválasztási módszer](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2) használatára van beállítva.  

> [!NOTE]
> [Az Azure traffic manager](../traffic-manager/traffic-manager-overview.md) ebben a cikkben csak illusztrációként használható. Bármilyen terheléselosztási megoldást használhat, amely támogatja a prioritás-továbbítási módszert.

Az alábbi ábra ezt a konfigurációt mutatja be a kimaradás előtt:

![1. forgatókönyv. A szolgáltatáskimaradás előtti konfiguráció.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Az elsődleges régióban egy kimaradás után az SQL Database szolgáltatás észleli, hogy az elsődleges adatbázis nem érhető el, és az automatikus feladatátvételi házirend (1) paraméterei alapján elindítja a feladatátvételt a másodlagos régióba. Az alkalmazás SLA-tól függően konfigurálhat egy türelmi időszakot, amely szabályozza a kimaradás és a feladatátvétel közötti időt. Lehetséges, hogy a traffic manager kezdeményezi a végpont feladatátvétel, mielőtt a feladatátvételi csoport elindítja az adatbázis feladatátvétel. Ebben az esetben a webalkalmazás nem tud azonnal újra csatlakozni az adatbázishoz. Az újracsatlakozások azonban automatikusan sikeresek lesznek, amint az adatbázis feladatátvétele befejeződik. A sikertelen terület visszaállításakor és online állapotba kerülése után a régi elsődleges automatikusan újracsatlakozik új másodlagosként. Az alábbi ábra a feladatátvétel utáni konfigurációt mutatja be.

> [!NOTE]
> A feladatátvétel után véglegesített összes tranzakció elvész az újracsatlakozás során. A feladatátvétel befejezése után a B régióban lévő alkalmazás képes újracsatlakozni, és újra tudja indítani a felhasználói kérelmek feldolgozását. Mind a webalkalmazás, mind az elsődleges adatbázis a B régióban van, és továbbra is közös helyen található.

![1. forgatókönyv. Konfiguráció feladatátvétel után](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Ha a B régióban kimaradás történik, az elsődleges és a másodlagos adatbázis közötti replikációs folyamat felfüggesztésre kerül, de a kettő közötti kapcsolat érintetlen marad (1). Felügyelt forgalom észleli, hogy a B régió kapcsolat megszakadt, és megjelöli a végpont webapp 2 degraded (2). Ebben az esetben az alkalmazás teljesítményét nem befolyásolja, de az adatbázis elérhetővé válik, és ezért nagyobb az adatvesztés kockázata abban az esetben, ha az A régió egymás után meghibásodik.

> [!NOTE]
> Vész-helyreállítási, azt javasoljuk, hogy a konfiguráció alkalmazás telepítése csak két régióban. Ennek az az oka, hogy az Azure-földrajzi területek többsége csak két régióval rendelkezik. Ez a konfiguráció nem védi meg az alkalmazást a két régió egyidejű katasztrofális meghibásodása. Nem valószínű, hogy ilyen hiba lenne, [geo-visszaállítási művelettel](sql-database-disaster-recovery.md#recover-using-geo-restore)helyreállíthatja az adatbázisokat egy harmadik régióban.
>

 A kimaradás mérséklése után a másodlagos adatbázis automatikusan újraszinkronizálja az elsődleges. A szinkronizálás során hatással lehet az elsődleges teljesítményére. A konkrét hatás a feladatátvétel óta beszerzett új elsődleges adatok mennyiségétől függ. Az alábbi ábra egy kimaradást mutat be a másodlagos régióban:

![1. forgatókönyv. Konfigurálás a másodlagos régióban való kimaradás után.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

A fő **előnye** ennek a tervezési minta a következők:

* Ugyanaz a webalkalmazás mindkét régióban régióspecifikus konfiguráció nélkül van telepítve, és nem igényel további logikát a feladatátvétel kezeléséhez.
* Az alkalmazás teljesítményét nem befolyásolja a feladatátvétel, mivel a webalkalmazás és az adatbázis mindig közös helyen található.

A fő **kompromisszumot** az, hogy az alkalmazás erőforrásait a B régióban az idő nagy részében kihasználatlan.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>2. forgatókönyv: Azure-régiók az üzletmenet folytonosságához maximális adatmegőrzéssel

Ez a beállítás a következő jellemzőkkel rendelkező alkalmazásokhoz a legmegfelelőbb:

* Minden adatvesztés magas üzleti kockázat. Az adatbázis feladatátvételcsak akkor használható végső megoldásként, ha a kimaradás t katasztrofális hiba okozza.
* Az alkalmazás támogatja a csak olvasható és írás-olvasási üzemmódok műveletek és működhet "csak olvasható módban" egy ideig.

Ebben a mintában az alkalmazás csak olvasható módba vált, amikor az írási és írási kapcsolatok idő-kikapcsolási hibákat kezdenek beolvasni. A webalkalmazás mindkét régióban telepítve van, és tartalmazza a kapcsolatot az írás-olvasási figyelő végpont és a különböző kapcsolatot az írásvédett figyelő végpont (1). A Traffic Manager-profilnak [prioritásos útválasztást kell használnia.](../traffic-manager/traffic-manager-configure-priority-routing-method.md) [A végpontfigyelést](../traffic-manager/traffic-manager-monitoring.md) minden régióban engedélyezni kell az alkalmazás végpontjára (2).

Az alábbi ábra ezt a konfigurációt mutatja be a kimaradás előtt:

![2. forgatókönyv. A szolgáltatáskimaradás előtti konfiguráció.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Ha a traffic manager az A régióban kapcsolati hibát észlel, automatikusan átkapcsolja a felhasználói forgalmat az alkalmazáspéldányra a B régióban. Ezzel a mintával fontos, hogy állítsa be a türelmi idő adatvesztés sel-veszteséggel egy kellően magas értéket, például 24 óra. Ez biztosítja, hogy az adatvesztés megelőzhető, ha a kimaradás mérséklése ezen idő alatt. Amikor a B régióban lévő webalkalmazás aktiválva van, az írási és olvasási műveletek elindulnak. Ezen a ponton át kell váltania írásvédett módba (1). Ebben az üzemmódban a kérelmek automatikusan átkerülnek a másodlagos adatbázisba. Ha a kimaradást katasztrofális hiba okozza, valószínűleg nem lehet enyhíteni a türelmi időszakon belül. Amikor lejár, a feladatátvételi csoport elindítja a feladatátvételt. Ezt követően elérhetővé válik az írás-olvasásfigyelő, és a hozzá fűződő kapcsolatok leállnak (2). Az alábbi ábra a helyreállítási folyamat két szakaszát mutatja be.

> [!NOTE]
> Ha az elsődleges régióban a szolgáltatáskimaradás a türelmi időszakon belül mérséklődik, a traffic manager észleli a kapcsolat helyreállítása az elsődleges régióban, és átkapcsolja a felhasználói forgalmat vissza az alkalmazáspéldány az A régióban. Az alkalmazáspéldány újraindul, és olvasási és írási módban működik az A régió elsődleges adatbázisának használatával, ahogy azt az előző diagram szemlélteti.

![2. forgatókönyv. Vészhelyreállítási fázisok.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Ha a B régióban kimaradás történik, a traffic manager észleli a hiba a végpont web-app-2 a B régióban, és jelzi, hogy leromlott (1). Időközben a feladatátvételi csoport átváltja az írásvédett figyelőt az A régióra (2). Ez a kimaradás nincs hatással a végfelhasználói élményre, de az elsődleges adatbázis a kimaradás során elérhetővé. Az alábbi ábra egy hibát mutat be a másodlagos régióban:

![2. forgatókönyv. A másodlagos régió kimaradása.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

A kimaradás mérséklése után a másodlagos adatbázis azonnal szinkronizálódik az elsődleges és az írásvédett figyelő visszavált a másodlagos adatbázis a B régióban. A szinkronizálás során az elsődleges teljesítmény kissé hatással lehet a szinkronizálandó adatok mennyiségétől függően.

Ennek a mintamintának számos **előnye**van:

* Elkerüli az adatvesztést az ideiglenes leállások során.
* Az állásidő csak attól függ, hogy a Traffic Manager milyen gyorsan észleli a kapcsolódási hibát, amely konfigurálható.

A **kompromisszum** az, hogy az alkalmazásnak képesnek kell lennie arra, hogy csak olvasható módban működjön.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>3. forgatókönyv: Az alkalmazások áthelyezése egy másik földrajzi helyre adatvesztés és közel nulla állásidő nélkül

Ebben az esetben az alkalmazás a következő jellemzőkkel rendelkezik:

* A végfelhasználók különböző földrajzi területekről érik el az alkalmazást
* Az alkalmazás csak olvasható számítási feladatokat tartalmaz, amelyek nem függnek a legújabb frissítésekteljes szinkronizálásától
* Az adatokhoz való írási hozzáférést a felhasználók többsége számára azonos földrajzi helyen kell támogatni.
* Az olvasási késés kritikus fontosságú a végfelhasználói élmény szempontjából

Annak érdekében, hogy ezek a követelmények, garantálnia kell, hogy a felhasználói eszköz **mindig** csatlakozik az alkalmazás telepítve az azonos földrajzi helyen az írásvédett műveletek, például a böngészési adatok, elemzések stb. Mivel az OLTP műveletek feldolgozása az idő nagy részében ugyanabban a földrajzi **területen kerül feldolgozásra.** Például a nap folyamán OLTP műveletek feldolgozása ugyanabban a földrajzi helyen, de a munkaidőn kívül lehet feldolgozni egy másik földrajzi. Ha a végfelhasználói tevékenység többnyire munkaidőben történik, az idő nagy részében a legtöbb felhasználó számára garantálhatja az optimális teljesítményt. Az alábbi ábrán ez a topológia látható.

Az alkalmazás erőforrásait kell telepíteni minden földrajzi, ahol jelentős használati igény. Ha például az alkalmazást aktívan használják az Egyesült Államokban, az Európai Unióban és Délkelet-Ázsiában, az alkalmazást az összes ilyen földrajzi területen kell telepíteni. Az elsődleges adatbázist dinamikusan át kell állítani az egyik földrajzi helyről a másikra a munkaidő végén. Ez a módszer az úgynevezett "kövesse a nap". Az OLTP-munkaterhelés mindig az írás-olvasásfigyelő ** &lt;feladatátvételi&gt;-csoportnév .database.windows.net** (1) keresztül csatlakozik az adatbázishoz. Az írásvédett munkaterhelés közvetlenül kapcsolódik a helyi adatbázishoz az adatbázisok ** &lt;kiszolgálóvégpont-kiszolgálóneve&gt;(database.windows.net 2)** használatával. A Traffic Manager a [teljesítmény-útválasztási módszerrel](../traffic-manager/traffic-manager-configure-performance-routing-method.md)van konfigurálva. Biztosítja, hogy a végfelhasználó eszköze a legközelebbi régióban csatlakozik a webszolgáltatáshoz. A Traffic Manager-t úgy kell beállítani, hogy a végpontfigyelés engedélyezve legyen minden webszolgáltatás végponthoz (3).

> [!NOTE]
> A feladatátvételi csoport konfigurációja határozza meg, hogy melyik régió használatos feladatátvételhez. Mivel az új elsődleges egy másik földrajzi helyen a feladatátvétel eredménye hosszabb késés mind OLTP és csak olvasható számítási feladatok, amíg az érintett régió újra online állapotba kerül.

![3. forgatókönyv. Konfiguráció az elsődleges az USA keleti részén.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

A nap végén, például helyi idő szerint 23:00 órakor az aktív adatbázisokat át kell állítani a következő régióra (Észak-Európa). Ez a feladat teljesen automatizált az [Azure Logic Apps](../logic-apps/logic-apps-overview.md)használatával. A feladat a következő lépéseket foglalja magában:

* Váltás az elsődleges kiszolgáló a feladatátvételi csoportban Észak-Európába barátságos feladatátvétel használatával (1)
* Az USA keleti része és Észak-Európa közötti feladatátvételi csoport eltávolítása
* Hozzon létre egy új feladatátvételi csoportot ugyanazzal a névvel, de Észak-Európa és Kelet-Ázsia között (2).
* Adja hozzá az elsődleges Észak-Európában és a másodlagos Kelet-Ázsiában, hogy ez a feladatátvételi csoport (3).

Az alábbi ábra a tervezett feladatátvétel utáni új konfigurációt mutatja be:

![3. forgatókönyv. Az előválasztás észak-európai átalakulása.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Ha például Észak-Európában kimaradás történik, az automatikus adatbázis-feladatátvételt a feladatátvételi csoport kezdeményezi, ami gyakorlatilag azt eredményezi, hogy az alkalmazást az ütemezés előtt áthelyezik a következő régióba (1).  Ebben az esetben az USA keleti része az egyetlen megmaradt másodlagos régió, amíg Észak-Európa újra online marad. A fennmaradó két régió mindhárom földrajzi területen szolgálja ki az ügyfeleket szerepkörváltással. Az Azure Logic Apps ennek megfelelően kell módosítani. Mivel a többi régió további felhasználói forgalmat kap Európából, az alkalmazás teljesítményét nem csak a további késés, hanem a végfelhasználói kapcsolatok megnövekedett száma is befolyásolja. Miután a kimaradás csökkentve Észak-Európában, a másodlagos adatbázis van azonnal szinkronizálva van a jelenlegi elsődleges. Az alábbi ábra egy észak-európai áramkimaradást szemléltet:

![3. forgatókönyv. Kimaradás Észak-Európában.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Csökkentheti azt az időt, amikor a végfelhasználó tapasztalata Európában a hosszú késés miatt csökken. Ehhez proaktív módon telepítenie kell egy alkalmazáspéldányt, és létre kell hoznia a másodlagos adatbázis(oka)t egy másik helyi régióban (Nyugat-Európa) az észak-európai offline alkalmazáspéldány cseréjeként. Amikor az utóbbi újra online, eldöntheti, hogy folytatja-e Nyugat-Európa használatát, vagy eltávolítja az alkalmazás másolatát, és visszavált Észak-Európa használatára.

Ennek a kialakításnak a legfontosabb **előnyei** a következők:

* Az írásvédett alkalmazás-munkaterhelés mindig hozzáfér a szekrények régióadataihoz.
* Az olvasási és írási alkalmazás számítási feladatok az egyes földrajzi területek legmagasabb tevékenységének időszakában a legközelebbi régióban lévő adatokhoz fér nek hozzá
* Mivel az alkalmazás több régióban van telepítve, az egyik régió jelentős leállás nélküli elvesztését is túlélheti.

De van néhány **kompromisszum:**

* A regionális kimaradás azt eredményezi, hogy a földrajzi hatással van a hosszabb késés. Az írási és írásvédett számítási feladatokat is egy másik földrajzi helyen szolgálja ki az alkalmazás.
* Az írásvédett számítási feladatoknak az egyes régiókban egy másik végponthoz kell csatlakozniuk.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Üzletmenet-folytonosság tervezése: Válasszon egy alkalmazástervet a felhőbeli vészhelyreállításhoz

Az adott felhővész-helyreállítási stratégia kombinálhatja vagy kiterjesztheti ezeket a tervezési mintákat, hogy a legjobban megfeleljen az alkalmazás igényeinek.  Ahogy korábban említettük, a választott stratégia az ügyfeleknek kínált SLA-n és az alkalmazás központi telepítési topológiáján alapul. A döntés irányításának elősegítése érdekében az alábbi táblázat a helyreállítási pont célkitűzésén (RPO) és a becsült helyreállítási időn (ERT) alapuló választási lehetőségeket hasonlítja össze.

| Mintázat | RPO | Ert |
|:--- |:--- |:--- |
| Aktív-passzív telepítés a vészhelyreállításhoz közös en-elhelyezkedő adatbázis-hozzáféréssel |Írási és olvasási hozzáférés < 5 mp |Hibaészlelési idő + DNS TTL |
| Aktív telepítés az alkalmazások terheléselosztásához |Írási és olvasási hozzáférés < 5 mp |Hibaészlelési idő + DNS TTL |
| Aktív-passzív telepítés az adatok megőrzéséhez |Írásvédett hozzáférés < 5 másodperc | Írásvédett hozzáférés = 0 |
||Olvasási és írási hozzáférés = nulla | Írási és írási hozzáférés = Hibaészlelési idő + türelmi idő adatvesztéssel |
|||

## <a name="next-steps"></a>További lépések

* Az üzletmenet folytonosságának áttekintését és forgatókönyveit az [Üzletmenet folytonosságának áttekintése című témakörben találja.](sql-database-business-continuity.md)
* Az aktív georeplikációról az [Aktív georeplikáció](sql-database-active-geo-replication.md)című témakörben olvashat.
* Az automatikus feladatátvételi csoportokról az [Automatikus feladatátvételi csoportok (Auto-feladatátvételi csoportok)](sql-database-auto-failover-group.md)témakörben olvashat.
* A rugalmas készletekkel rendelkező aktív georeplikációról az [Elastic Pool vész-helyreállítási stratégiái](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)című témakörben talál.
