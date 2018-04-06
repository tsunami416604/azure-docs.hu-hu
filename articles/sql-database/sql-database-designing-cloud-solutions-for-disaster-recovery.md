---
title: Azure SQL Database használata magas rendelkezésre állású szolgáltatás kialakítása |} Microsoft Docs
description: További tudnivalók az Azure SQL Database használata magas rendelkezésre állású szolgáltatások alkalmazás tervét.
keywords: a felhő vész-helyreállítási, a vész-helyreállítási megoldások, az alkalmazás az adatok biztonsági mentése, a georeplikáció, üzleti folytonossági tervezése
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: d19087743740799ec9972bed7a602073afea9f26
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>Azure SQL Database használata magas rendelkezésre állású szolgáltatások tervezése

Összeállításakor, és az Azure SQL Database-magas rendelkezésre állású szolgáltatások telepítése, használata [feladatátvételi csoportok és aktív georeplikáció](sql-database-geo-replication-overview.md) regionális kimaradások és a végzetes hibák rugalmasság biztosításához. Azt is lehetővé teszi, hogy a másodlagos adatbázisok gyors helyreállítás. Ez a cikk általános alkalmazás-minták összpontosít, és az előnyöket és az egyes lehetőségek kompromisszumot ismerteti. Aktív georeplikáció a rugalmas készletek kapcsolatos információkért lásd: [rugalmas készlet vész-helyreállítási stratégiák](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

> [!NOTE]
> Prémium szintű használata vagy a fontos üzleti (előzetes verzió) adatbázisok és rugalmas készletek, akkor is használhatja őket a területi rugalmas kimaradások konvertálja azokat zóna redundáns központi telepítés konfigurálása (jelenleg az előzetes verzió). Lásd: [zónaredundáns adatbázisok](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>1. példa: A két Azure-régiók az üzletmenet folytonosságának minimális állásidővel használata
Ebben a forgatókönyvben az alkalmazásokat az alábbi tulajdonságokkal rendelkeznek: 
*   Azure-régió, egy alkalmazás aktív
*   Minden adatbázis-munkamenetek szükséges olvasási és írási hozzáférést (RW) adatokhoz
*   Webes réteg és az adatszint a kell a késleltetés és a forgalom költségek csökkentése a közös elhelyezésű 
*   Alapvetően, leállás magasabb üzleti kockázatot jelent az alkalmazás az adatveszteség-nál

Ebben az esetben az alkalmazás üzembe helyezési topológia optimalizált regionális katasztrófák kezelése, ha az összes alkalmazás-összetevő feladatátvételi együtt. Az alábbi ábrán ez a topológia látható. A földrajzi redundancia céljából az alkalmazás-erőforrásokat A és b régió van telepítve A régió B erőforrások azonban nem használhatók mindaddig, amíg A régió nem sikerül. Egy feladatátvételi csoport van konfigurálva, az adatbázis-kapcsolat, a replikáció és a feladatátvétel két régiók között. A webszolgáltatás mindkét régiókban van konfigurálva, az olvasási és írási figyelő keresztül-adatbázisának eléréséhez  **&lt;feladatátvételi csoportnév&gt;. database.windows.net** (1). A TRAFFIC manager használatára van beállítva [prioritású virtuális gép útválasztási módszer](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Az Azure traffic manager](../traffic-manager/traffic-manager-overview.md) használja a rendszer keresztül ez a cikk csak illusztrációs célokat szolgálnak. Minden terheléselosztási megoldás, amely támogatja a prioritás útválasztási metódus is használhatja.    
>

Az alábbi ábrán látható az ebben a konfigurációban, nem tervezett kimaradás előtt:

![1. forgatókönyv. A leállás konfigurációját.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Az elsődleges régióban kimaradás után a az SQL Database szolgáltatás észleli, hogy az elsődleges adatbázis nem érhető el, és elindítja a feladatátvételt a másodlagos régióban, az Automatikus feladatátvétel házirend (1) a paraméterek alapján. Attól függően, hogy az alkalmazás SLA-t a türelmi időszak, amely a szolgáltatáskimaradás észlelését és magát a feladatátvétel között eltelő idő is konfigurálhat. Akkor lehet, hogy a traffic manager kezdeményezi a végpont a feladatátvételi csoport elindítja az adatbázis a feladatátvétel előtt. Ebben az esetben a webalkalmazás nem azonnal újra az adatbázishoz. De az ezt a lehetőséget akkor sikeres automatikusan, amint az adatbázis feladatátvétel befejezését. Amikor a sikertelen régió visszaállítása, és ismét online állapotban, a régi elsődleges automatikusan újracsatlakozik-e egy új másodlagosként. Az alábbi ábra szemlélteti a feladatátvételt követően a konfigurációt.
 
> [!NOTE]
> A feladatátvétel után végrehajtott összes tranzakciók során az újracsatlakozás elvesznek. A feladatátvétel befejezése után az alkalmazás B régióban csatlakozzon újra, és indítsa újra a felhasználói kérelmek feldolgozásához. A webes alkalmazás, mind az elsődleges adatbázis régióban B, illetve ugyanott maradnak. n>

![1. forgatókönyv. Feladatátvétel konfigurálása](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Ha kimaradás B régióban, a replikációs folyamatot az elsődleges és a másodlagos adatbázis közötti lekérdezi felfüggesztett, de a két hivatkozás marad változatlanok maradnak (1). Kezelt forgalom észleli, hogy régió B kapcsolata megszakad, és csökkentett teljesítményű (2) állapotúként jelöli meg a végpont webalkalmazás 2. Nincs hatása ebben az esetben az alkalmazás teljesítményére, de az adatbázis elérhetővé válik, és ezért az adatvesztés esetben nagyobb a veszélye A régió nem sikerül egymás után.

> [!NOTE]
> Vész-helyreállítási az alkalmazás telepítésének korlátozott konfiguráció két régió ajánlott. Ennek az az oka az Azure földrajzi többsége csak két régióban. Ez a konfiguráció nem nyújt védelmet az alkalmazás mindkét régió egyidejű katasztrofális hibája esetén. Ilyen hiba valószínű esetben helyreállíthatja az adatbázisokat egy harmadik régiót használva [georedundáns helyreállítás művelet](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 Miután a szolgáltatáskimaradás elhárítására, a másodlagos adatbázis automatikusan újraszinkronizálja az elsődleges. A szinkronizálás során az elsődleges is lehet a teljesítményre. Adatok mennyisége óta a feladatátvételt az új elsődleges függ az adott hatása. A következő ábra szemlélteti a másodlagos régióban kimaradás:

![1. forgatókönyv. A másodlagos régióban kimaradás után beállításokra.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

A kulcs **előnyeit** ebben a kialakítási mintában a rendszer:

* Azonos a webes alkalmazás mindkét régió régióspecifikus konfigurálása nélkül telepít, és nem igényel további logikát feladatátvételi kezeléséhez. 
* Alkalmazások teljesítménye nincs hatással a feladatátvétel, a webes alkalmazás és az adatbázis mindig közös elhelyezésű.

A fő **kompromisszumot** , hogy az alkalmazás-erőforrásokat, a régiót B kihasználva a legtöbbször ennek.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>2. forgatókönyv: Azure-régiók az üzletmenet folytonossága érdekében az adatok maximális megőrzése
Ez a beállítás akkor legmegfelelőbb alkalmazásokat az alábbi adatokkal:

* Az adatvesztés nagy üzleti áll. Az adatbázis feladatátvételi csak használható utolsó lehetőségként, ha a szolgáltatáskimaradás végzetes hiba okozta.
* Az alkalmazás műveletek csak olvasható és írható-olvasható módot támogat, és egy ideig működhet "csak olvasható módban".

Ebben a mintában az alkalmazás csak olvasható módra vált az olvasási és írási kapcsolatok az időtúllépési hibák első indításakor. A webes alkalmazás mindkét régió telepít, és az olvasási és írási figyelő végpont kapcsolat és a különböző kapcsolat a csak olvasható figyelő (1). A Traffic manager-profilt kell használnia [prioritású virtuális gép útválasztási](../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Végpont figyelési](../traffic-manager/traffic-manager-monitoring.md) engedélyezni kell az alkalmazás végpontjának minden régióban (2).

A következő diagram azt ábrázolja, ebben a konfigurációban, nem tervezett kimaradás előtt:

![2. forgatókönyv. A leállás konfigurációját.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Ha a traffic manager régiónak A kapcsolat hibát észlel, automatikusan vált felhasználói forgalom az alkalmazáspéldány régióban a b kiszolgálóra. Ebben a mintában fontos, hogy beállította a türelmi időszak adatvesztéssel egy elég magas értékre, például 24 óra. Biztosítja, hogy adatvesztés megakadályozta, ha a szolgáltatáskimaradás elhárítására adott időn belül. A webes alkalmazás régióban B aktiválásakor a az olvasási és írási műveletek meghiúsul. Ezen a ponton akkor át kell váltania a csak olvasható módra (1). Ebben a módban a rendszer kérést automatikusan átirányítja a másodlagos adatbázis. Ha a szolgáltatáskimaradás végzetes hiba okozta, nagy valószínűséggel azt nem szüntethető meg a türelmi időszak. Amikor lejár a feladatátvételi csoport eseményindítók a feladatátvételt. Miután az olvasási és írási figyelő elérhetővé válik, és a kapcsolatok leállítása sikertelen (2). A következő ábra szemlélteti a helyreállítási folyamat két lépésben.

> [!NOTE]
> Ha az elsődleges régióban a szolgáltatáskimaradás elhárítására a türelmi időszak, a traffic manager észleli a kapcsolat visszaállítása az elsődleges régióban, felhasználói forgalom vissza az alkalmazáspéldány régióban azonosítójához. Adott alkalmazáspéldány után folytatja működését, és az elsődleges adatbázis használata A szerint az előző ábrán szemléltetett régióban írható-olvasható módban működik.
>

![2. forgatókönyv. Vész-helyreállítási fázisból áll.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Ha kimaradás B régióban, a traffic manager észleli a webszolgáltatás-alkalmazás-2 végpont hibája régió B és az "csökkentett teljesítményű" (1) jelöli. A feladatátvételi csoport időközben vált, a csak olvasható figyelőt, hogy A régió (2). A leállás nem befolyásolja a végfelhasználói élmény, de az elsődleges adatbázis a szolgáltatáskimaradás elhárítása során van közzétéve. A következő ábra szemlélteti a másodlagos régióban hiba:

![2. forgatókönyv. A másodlagos régió szolgáltatáskimaradás.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

A leállás elhárítására, ha a másodlagos adatbázis azonnal szinkronizálja az elsődleges és a csak olvasható figyelő át lett váltva vissza a másodlagos adatbázis régióban a b kiszolgálóra. A szinkronizálás során az elsődleges teljesítményének sikerült némileg negatív hatással lehet, amelyet a szinkronizálható adatok mennyiségétől függően.

Ebben a kialakításban van több **előnyeit**:

* Adatvesztés Ezzel elkerülheti a átmeneti kimaradásainak során.
* Állásidő csak milyen gyorsan a traffic manager észleli a csatlakozási hiba, amely konfigurálható függ.

A **kompromisszumot** , hogy az alkalmazás képes csak olvasható módban kell lennie.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>3. forgatókönyv: Alkalmazás áthelyezheti egy másik geográfiai adatvesztés nélkül, és közel állásidő 
Ebben a forgatókönyvben az alkalmazás a következő jellemzőkkel rendelkezik: 
* A végfelhasználók számára a különböző földrajzi területeken hozzáférni az alkalmazáshoz
* Az alkalmazás tartalmazza a csak olvasható munkaterheléseknek, amelyek nem a teljes szinkronizálás a legújabb frissítésekkel
* Az írási hozzáférést támogatnia kell a felhasználók többsége az ugyanazon földrajzi 
* Olvasási késése fontos a végfelhasználói élmény 


Ahhoz, hogy megfelel a fenti követelményeknek, annak biztosítására, hogy a felhasználó eszközén kell **mindig** csatlakozik a ugyanazon a földrajzi hely, a csak olvasható műveletekhez, például a böngészési, a központilag telepített alkalmazás analytics stb. Mivel az OLTP műveletek dolgoznak fel az ugyanazon földrajzi **legtöbb esetben**. Például a nap időszak OLTP műveletek dolgoznak fel az ugyanazon földrajzi, de az kikapcsolt órában azok sikerült feldolgozni a különböző földrajzi. Ha a végfelhasználó tevékenység többnyire akkor fordul elő, a munkaidő alatt, a felhasználók többsége optimális teljesítményének garantálható legtöbb esetben. Az alábbi ábrán ez a topológia látható. 
 
Minden egyes földrajzi jelentős használati igény szerinti esetében az alkalmazás-erőforrásokat kell telepíteni. Például ha az alkalmazás által használt, az Amerikai Egyesült Államokban, Európai Unió és Délkelet-Ázsia az alkalmazást kell telepíteni az összes ezek földrajzi. Az elsődleges adatbázis kell lennie dinamikusan átváltott a egy geográfiai a következő munkaidőt végén. Ez a metódus neve "követi a sun". Az OLTP-munkaterhelés mindig kapcsolódik az adatbázishoz keresztül az írási és olvasási figyelő  **&lt;feladatátvételi csoportnév&gt;. database.windows.net** (1). A csak olvasható munkaterhelés csatlakozik a helyi adatbázist közvetlenül az adatbázis-kiszolgáló végpont  **&lt;kiszolgálónév&gt;. database.windows.net** (2). A TRAFFIC manager van beállítva a [teljesítmény útválasztási módszer](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Biztosítja, hogy a felhasználói eszköz csatlakoztatva van a webszolgáltatás a legközelebbi régióban. A TRAFFIC manager végpont figyelési engedélyezve az minden webes szolgáltatás végpontját (3) kell beállítani.

> [!NOTE]
> A feladatátvételi csoport konfigurációja határozza meg, melyik régióban feladatátvételi szolgál. Mivel az új elsődleges kiszolgáló különböző földrajzi hosszabb OLTP és a csak olvasható munkaterhelések késése feladatátvételt eredményez addig, amíg az érintett régió újra online állapotba kerül.
>

![3. forgatókönyv. USA keleti régiója az elsődleges-konfiguráció.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

(Például: 23 óra helyi idő) a nap végén az aktív adatbázisokat kell állítani a következő terület (Észak-Európa). Ez a feladat teljesen automatizált használatával [Azure szolgáltatás ütemezése](../scheduler/scheduler-intro.md).  A feladat a következő lépésekből áll:
* Váltás a feladatátvételi csoport elsődleges kiszolgáló rövid feladatátvevő (1) használatával Észak-Európa
* Távolítsa el a feladatátvételi csoport közötti USA keleti régiója és Észak-Európa
* Hozzon létre egy új feladatátvevő ugyanazzal a névvel, de Észak-Európában és Kelet-Ázsia (2) között. 
* Az elsődleges és másodlagos Kelet-Ázsiában, Észak-Európa a csoporthoz hozzáadni kívánt feladatátvevő (3).


A következő ábra szemlélteti a tervezett feladatátvétel után az új konfiguráció:

![3. forgatókönyv. Az elsődleges helyről Észak-Európa tér át.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Kimaradás Észak-Európában történik, például, ha az adatbázis automatikus feladatátvételt kezdeményez a feladatátvételi csoport, amely hatékonyan eredményez helyezze át az alkalmazást a következő terület ütemezett (1).  Ebben az esetben az USA keleti régiója nem az egyetlen fennmaradó másodlagos régióban, amíg az Észak-Európa újra online állapotba kerül. A többi két régió az ügyfelek összes három földrajzi szerepkörök váltásával osztja ki. Azure schedulerrel van, úgy kell módosítani. A fennmaradó régiók további felhasználói forgalom beszerezni Európa, mert az alkalmazás van a teljesítményre nem csak további késés korlátozza, hanem a felhasználói kapcsolatok számának által. Miután a szolgáltatáskimaradás elhárítására Észak-Európában, a másodlagos adatbázis azonnal szinkronizálja az aktuális elsődleges. A következő diagram azt ábrázolja, Észak-Európában kimaradás:

![3. forgatókönyv. Ezen kívül az Észak-Európa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Ha a végfelhasználói élmény Európában csökken, mert a hosszú várakozási idő csökkentése érdekében. Tegye, hogy kell proaktív központi telepítése egy alkalmazás másolatot, és hozzon létre a másodlagos adatbázis (oka) egy másik helyi régióban (Nyugat-Európában) Észak-Európában offline alkalmazáspéldány helyettesíti. Ha ez utóbbi ismét online eldöntheti, hogy továbbra is használja, Nyugat-Európa, vagy távolítsa el az alkalmazás másolatát, majd váltson vissza az használ az Észak-Európa
>

A kulcs **előnyei** a kialakításának vannak:
* A csak olvasható alkalmazás munkaterhelési adatok WC-k régióban mindig fér hozzá. 
* Az olvasási és írási alkalmazás munkaterhelési adatokhoz a legközelebbi régióban hozzáfér a legmagasabb tevékenység egyes földrajzi időszakban
* Az alkalmazás központi telepítése több területre, mert azt hibatűrését adatvesztést jelentős állásidő nélkül régiók egyikéhez sem. 

Van azonban néhány, de **mellékhatásokkal**:
* Regionális kimaradás hosszabb várakozási ideje negatív hatással lehet a geográfiai eredményez. Az alkalmazás különböző földrajzi által kiszolgált írható-olvasható és az írásvédett munkaterhelések. 
* A csak olvasható munkaterhelések csatlakoztatni kell egy másik végpont minden régióban. 


## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Üzleti folytonosság tervezési: Válasszon egy alkalmazás tervét felhő katasztrófa utáni helyreállítás
Egyes adott felhőalapú vész-helyreállítási stratégiát kombinálhatja, vagy az alkalmazás igényeinek leginkább megfelelő ezek a kialakítási minták kiterjesztése.  A korábban említett stratégia az SLA-t szeretne ajánlani az ügyfelek és az alkalmazás üzembe helyezési topológia alapul. A következő részekben talál a döntést, hogy az alábbi táblázat összehasonlítja a választási lehetőségek a helyreállítási időkorlát (RPO) és a becsült helyreállítási idő (Beszúrása) alapján.

| Mintázat | A HELYREÁLLÍTÁSI IDŐKORLÁT | ERT |
|:--- |:--- |:--- |
| Aktív-passzív telepítési közös elhelyezésű adatbázis-hozzáférést katasztrófa utáni helyreállítás |Olvasási és írási hozzáférése < 5 másodperc |Hiba észlelése időpontja + a DNS-élettartam |
| Aktív-aktív központi telepítés alkalmazás terheléselosztás |Olvasási és írási hozzáférése < 5 másodperc |Hiba észlelése időpontja + a DNS-élettartam |
| Aktív-passzív telepítési az adatok megőrzése |Csak olvasási hozzáféréssel < 5 másodperc | Csak olvasási hozzáféréssel = 0 |
||Olvasási és írási hozzáférése = nulla | Olvasási és írási hozzáférése = hiba észlelés ideje + adatvesztéssel türelmi időszak |
|||

## <a name="next-steps"></a>További lépések
* Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md)
* A georeplikáció és feladatátvételi csoportokkal kapcsolatos további tudnivalókért lásd: [aktív georeplikáció](sql-database-geo-replication-overview.md)  
* Aktív georeplikáció a rugalmas készletek kapcsolatos információkért lásd: [rugalmas készlet vész-helyreállítási stratégiák](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
