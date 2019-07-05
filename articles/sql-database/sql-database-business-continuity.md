---
title: Felhőalapú üzletmenet-folytonosság – adatbázis-helyreállítás – SQL Database | Microsoft Docs
description: Ismerje meg, hogyan támogatja az Azure SQL Database a felhőalapú üzletmenet-folytonosságot és az adatbázis-helyreállítást, és hogyan segít az üzletmenet szempontjából kritikus fontosságú felhőalapú alkalmazások folyamatos működésének biztosításában.
keywords: üzletmenet-folytonosság,felhőalapú üzletmenet-folytonosság,adatbázis-vészhelyreállítás,adatbázis-helyreállítás
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 06/25/2019
ms.openlocfilehash: 2f3723e0a1b14edd6f516f3cc080501bea80d486
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442038"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése

**Üzletmenet-folytonossági** a mechanizmusok, szabályzatokat és eljárásokat, amelyek lehetővé teszik, hogy továbbra is fennállnak megszakadását, különösen a számítástechnikai infrastruktúra esetén hivatkozik az Azure SQL Database-ben. A legtöbb esetben az Azure SQL Database az azokat a káros eseményeket, amelyek a felhőalapú környezetben történik és tartsa az alkalmazások és üzleti folyamatok futtatása fogja kezelni. Vannak azonban bizonyos azokat a káros eseményeket, amelyek nem kell kezelnie az SQL Database automatikusan például:

- Felhasználó véletlenül törölt vagy frissített egy sort a táblában.
- Rosszindulatú támadó sikeresen törli az adatokat, vagy töröl egy adatbázist.
- Földrengés egy áramkimaradás és ideiglenes letiltott adatközpont-miatt.

Az áttekintés az Azure SQL Database az üzletmenet-folytonosság és a vészhelyreállítás biztosítására szolgáló funkcióit írja le. Ismerje meg a lehetőségeket, javaslatokat és oktatóanyagokat, amelyek adatvesztéshez vezethetnek vagy az adatbázis és az alkalmazást elérhetetlenné zavaró események utáni helyreállításhoz. Ismerje meg, mi a teendő, ha egy felhasználó vagy alkalmazás hiba hatással van az adatok integritásának megőrzése, egy Azure-régióban van kimaradás vagy az alkalmazás karbantartásra szorul.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Az SQL Database üzletmenet-folytonosságot biztosító funkciói

Az adatbázist négyféleképpen fő lehetséges megszakítása:

- Lemezmeghajtó hiba például az adatbázis-csomópontot befolyásoló helyi hardveres vagy szoftveres hibák.
- Adatsérülés vagy a törlés oka általában egy alkalmazás hibát vagy az emberi hibák. Az ilyen hibák az alkalmazás-specifikus, és általában nem észlelhető az adatbázis-szolgáltatás által.
- Adatközpont-meghibásodás után, oka valószínűleg egy természeti katasztrófa esetére. Ehhez a forgatókönyvhöz szükséges bizonyos szintű georedundáns tárolás az alkalmazás feladatátvételt egy másodlagos adatközpontba.
- Frissítés és karbantartás hibákat nem várt során felmerülő problémák infrastruktúra tervezett karbantartások alkalmával kerül, vagy frissítések szükség lehet a gyors visszaállítás egy korábbi adatbázis állapotának.

A helyi hardver- és szoftver hibák elhárításához, az SQL Database szolgáltatás egy [magas rendelkezésre állású architektúra](sql-database-high-availability.md), amely garantálja, hogy ezek a hibák 99.995 % az automatikus helyreállítást rendelkezésre állási SLA.  

Adatvesztéssel szemben üzleti adatai védelméről, SQL Database automatikusan hoz létre teljes adatbázis biztonsági mentését hetente, különbözeti adatbázisok biztonsági mentése 12 óránként, és a tranzakciónapló biztonsági mentések minden 5 – 10 percet. A biztonsági mentések legalább 7 nappal minden szolgáltatási szint esetében az RA-GRS tároló vannak tárolva. Alapszintű kivételével az összes szolgáltatási szint támogatja a konfigurálható biztonsági másolat megőrzési idejének időponthoz visszaállításhoz, legfeljebb 35 napon belül. 

Az SQL Database számos üzletmenet-folytonossági funkciókat, csökkentése érdekében a nem tervezett különböző forgatókönyvekben amelyekkel is biztosít. 

- A [historikus táblák](sql-database-temporal-tables.md) segítségével a sorverziókat bármely időpontra visszaállíthatja.
- [Beépített automatikus biztonsági mentések](sql-database-automated-backups.md) és [időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore) lehetővé teszi, hogy a teljes adatbázis bizonyos ponton belül a konfigurált adatmegőrzési időszak megfelelő állapotba való visszaállítását 35 napra visszamenőleg.
- Is [törölt adatbázis visszaállítása](sql-database-recovery-using-backups.md#deleted-database-restore) a pont, ahol azt törölték, ha a **SQL Database-kiszolgáló nem lett törölve**.
- [Hosszú távú adatmegőrzés](sql-database-long-term-retention.md) lehetővé teszi, hogy a biztonsági mentések tartani 10 évre.
- [Aktív georeplikáció](sql-database-active-geo-replication.md) hozhatók létre olvasható replikát, és manuális feladatátvétel esetén a data center kimaradás vagy alkalmazás frissítése bármely replika.
- [Automatikus feladatátvételi csoport](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) lehetővé teszi, hogy az alkalmazás automatikusan Recovery esetén egy adatközpont-meghibásodás után.

## <a name="recover-a-database-within-the-same-azure-region"></a>Ugyanazon Azure-régióban adatbázis helyreállítása

Az adatbázis automatikus biztonsági mentések segítségével adatbázis visszaállítása egy időben történtek. Ezzel a módszerrel helyreállíthatja az emberi hibák által okozott adatsérülések. A pontja-in-time-visszaállítás lehetővé teszi, hogy hozzon létre egy új adatbázist ugyanazon a kiszolgálón, amely az adatokat a hibás esemény előtt állapotát jeleníti meg. A legtöbb adatbázis a visszaállítási műveleteket a kevesebb mint 12 óra vesz igénybe. Ez hosszabb időt vehet igénybe nagyon nagy vagy nagyon aktív adatbázis helyreállítása. Helyreállítási idő kapcsolatos további információkért lásd: [adatbázis-helyreállítás ideje](sql-database-recovery-using-backups.md#recovery-time). 

Ha a maximális támogatott biztonsági másolat megőrzési idejének időponthoz visszaállítás (PITR) nem az van elegendő az alkalmazáshoz, kibővítheti, egy az adatbázis(ok) hosszú távú adatmegőrzési (LTR) házirendjének konfigurálásával. További információkért lásd: [hosszú távú adatmegőrzés](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-azure-region"></a>Adatbázis helyreállítása egy másik Azure-régió az

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Leállás esetén az üzletmenet esetleg csak néhány percre, de akár több órára is megszakadhat.

- Az egyik lehetőség, hogy megvárja, amíg az adatközpont leállását követően az adatbázis újra elérhető lesz. Ez az olyan alkalmazások esetében működik, amelyek esetében megengedhető, hogy az adatbázis offline állapotú legyen. Például egy fejlesztési projekt vagy egy ingyenes próbaverzió esetében, amelyeken nem kell folyamatosan dolgoznia. Amikor egy adatközpontban szolgáltatáskimaradás következik, nem tudja, mennyi ideig a szolgáltatáskimaradás elhárítása után előfordulhat, hogy a legutóbbi, így ez a beállítás csak akkor működik, ha már nincs szüksége az adatbázis egy ideig.
- Egy másik lehetőség az, hogy egyetlen kiszolgálón, az minden olyan Azure-régió-adatbázis visszaállítása [georedundáns adatbázis biztonsági másolatait](sql-database-recovery-using-backups.md#geo-restore) (georedundáns visszaállítást). A GEO-visszaállítás georedundáns biztonsági másolat használja forrásként, és segítségével helyreállíthat egy adatbázist, akkor is, ha az adatbázis vagy az Adatközpont-leállás miatt elérhetetlenné.
- Végül állíthatja helyre a kimaradás utáni Ha konfigurálta a geo-secondary segítségével [aktív georeplikáció](sql-database-active-geo-replication.md) vagy egy [automatikus feladatátvételi csoport](sql-database-auto-failover-group.md) az adatbázis vagy -adatbázisok számára. Ezek a technológiák válaszaitól függően manuális vagy automatikus feladatátvételt is használhat. Feladatátvételi magát csupán néhány másodpercet vesz igénybe, amíg a szolgáltatás legalább egy órával az aktiválás vesz igénybe. Ez azért szükséges, győződjön meg arról, hogy a feladatátvétel indokolt-e a skála, a szolgáltatáskiesés megszüntetése után. Is a feladatátvétel aszinkrón replikáció természete miatt kis adatvesztést eredményezhet. 

Az üzletmenet-folytonossági terv kidolgozása során meg kell állapítania az alkalmazás a zavaró eseményeket követő teljes helyreállításának maximális elfogadható időkorlátját. Az alkalmazás teljes helyreállításához szükséges időt a helyreállítási időre vonatkozó célkitűzés (RTO) néven ismert. Emellett ismernie kell a leghosszabb Adatfrissítés (időintervallum) az alkalmazás működését elvesztése, ha egy nem tervezett zavaró események utáni helyreállításhoz. Az esetleges adatvesztés Helyreállításipont-célkitűzés (RPO) néven ismert.

Másik helyreállítási módszert RPO és RTO másik szintet nyújtanak. Egy adott helyreállítási módszer, vagy az alkalmazás teljes helyreállítási achicethe módszerek kombinációját használják. Az alábbi táblázat RPO és RTO helyreállítási hátrányait hasonlítja össze.

| Helyreállítási módszer | RTO | HELYREÁLLÍTÁSI IDŐKORLÁT |
| --- | --- | --- | 
| Georedundáns visszaállítás georeplikált biztonsági másolatokból | 12 óra | 1 óra |
| Automatikus feladatátvételi csoportok | 1 óra | 5 s |
| Manuális adatbázis-feladatátvétel | 30 s | 5 s |

> [!NOTE]
> *Manuális adatbázis feladatátvételi* hivatkozik a georeplikált másodlagos használatának egy önálló adatbázis feladatátvételét a [nem tervezett mód](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Lásd a táblázatot az Automatikus feladatátvétel RTO és RPO részleteket a cikk korábbi részében.


Automatikus feladatátvételi csoportok használata, ha az alkalmazás megfelel a következő feltételeknek:

- Az üzletmenet szempontjából kritikus fontosságú.
- Rendelkezik egy szolgáltatásiszint-szerződés (SLA), amely nem engedélyezi a 12 óra vagy több állásidőt.
- Állásidő pénzügyi kötelezettséget eredményezhet.
- Adatok nagy mértékű módosítása rendelkezik, és 1 órányi adat elvesztése nem fogadható el.
- Az aktív georeplikáció többletköltsége alacsonyabb, mint a potenciális pénzbeli kötelezettség és a kapcsolódó üzletvesztés összege.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Előfordulhat, hogy használni kívánja adatbázisok biztonsági mentése és az aktív georeplikáció attól függően, hogy az alkalmazás követelményeinek. Tervezési szempontjait önálló adatbázisok és ezek üzletmenet-folytonossági funkciókat használó rugalmas készletek számára, lásd: [tervezése felhőalapú vészhelyreállítással alkalmazás](sql-database-designing-cloud-solutions-for-disaster-recovery.md) és [rugalmas készletek vészhelyreállítási stratégiák](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

A következő szakaszok az adatbázis biztonsági mentése vagy az aktív georeplikáció használatával végzett helyreállítás lépéseinek áttekintését. Beleértve a tervezési követelményeket, helyreállítás utáni lépések és információ a leállások szimulálását a vészhelyreállítási próba végrehajtása részletes lépéseiért lásd: [SQL-adatbázis helyreállítása a kimaradás utáni](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Előkészületek leállás esetére

A használt üzletmenet-folytonossági funkciótól függetlenül végre kell hajtania a következőket:

- Azonosítsa és készítse elő a célkiszolgálót, beleértve a kiszolgálószintű IP-tűzfalszabályainak, a bejelentkezéseket és a főadatbázis szintű jogosultságokat.
- Határozza meg, hogyan legyenek átirányítva az ügyfelek és az ügyfélalkalmazások az új kiszolgálóra.
- Dokumentálja az egyéb függőségeket, mint a naplózási beállítások és a riasztások.

Ha nem megfelelően, így az alkalmazások, online, miután a feladatátvétel, vagy egy adatbázis-helyreállítás további időt igényel, és valószínűleg hibaelhérítást is igényel a stressz – rossz kombináció egyszerre.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Feladatátvételt egy georeplikált másodlagos adatbázisra

Ha tárreplikációval helyreállítási mechanizmusként aktív georeplikációt, vagy automatikus feladatátvételi csoportok használ, automatikus feladatátvételi szabályzat konfigurálása, vagy használjon [manuális nem tervezett feladatátvétel](sql-database-active-geo-replication-portal.md#initiate-a-failover). Követően a feladatátvételt a másodlagos az új elsődleges válnak, és készen áll rögzíteni az új tranzakciókat és válaszolni a lekérdezésekre – az adatok nem replikálta minimális adatvesztéssel járó hatására a. A feladatátvételi folyamat tervezésével kapcsolatos további információkért lásd: [tervezése felhőalapú vészhelyreállítással alkalmazás](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Az Adatközpont ismét online elérhető a régi eredményezi automatikusan csatlakozzon újra az új elsődleges, és a másodlagos adatbázisok válnak. Helyezze át az elsődleges vissza az eredeti régió van szüksége, ha manuálisan is kezdeményezhető a tervezett feladatátvétel (feladat-visszavétel).

### <a name="perform-a-geo-restore"></a>Georedundáns visszaállítás végrehajtása

Ha az automatikus biztonsági másolatokat használ georedundáns tárolás (alapértelmezés szerint engedélyezve van), akkor helyreállíthatja az adatbázist a használatával [geo-visszaállítás](sql-database-disaster-recovery.md#recover-using-geo-restore). Helyreállítási általában 12 óra alatt – akár egy óráig határozza meg, amikor a legutóbbi naplók biztonsági mentése volt hozott, és a replikált adatok elvesztése akkor kerül sor. A helyreállítás befejezéséig az adatbázis nem tudja rögzíteni a tranzakciókat, illetve nem tud válaszolni a lekérdezésekre. Vegye figyelembe, hogy a geo-visszaállítás csak visszaállítja az adatbázist a legutóbbi elérhető pont időben.

> [!NOTE]
> Az Adatközpont ismét online elérhető, mielőtt az alkalmazás a helyreállított adatbázisra, megszakíthatja a helyreállítást.

### <a name="perform-post-failover--recovery-tasks"></a>Feladatátvétel/helyreállítás utáni feladatok végrehajtása

A helyreállítási mechanizmusok végrehajtása után a következő további feladatokat kell végrehajtania a felhasználók és az alkalmazások újbóli üzembe helyezéséhez:

- Irányítsa át az ügyfeleket és az ügyfélalkalmazásokat az új kiszolgálóra és a helyreállított adatbázisra.
- Győződjön meg, hogy a megfelelő kiszolgálói szintű IP tűzfalszabályok vannak érvényben a felhasználók csatlakozni, vagy használjon [adatbázisszintű tűzfalak](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal) ahhoz, hogy megfelelő szabályokat.
- Biztosítsa a megfelelő bejelentkezési adatok és főadatbázis-szintű jogosultságok rendelkezésre állását (vagy használjon [tartalmazott felhasználókat](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Konfigurálja a naplózást, ha szükséges.
- Konfigurálja a riasztásokat, ha szükséges.

> [!NOTE]
> Ha egy feladatátvételi csoportot használ, és az adatbázisok, az olvasási és írási lstener használatával csatlakozhat, az átirányítás, a feladatátvételt követően történik meg automatikusan és transzparens módon az alkalmazáshoz.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Alkalmazások frissítése minimális állásidővel

Néha egy alkalmazást kell venni a kapcsolat nélküli tervezett karbantartások, például alkalmazásfrissítések miatt. [Alkalmazásfrissítések kezeléséről](sql-database-manage-application-rolling-upgrade.md) ismerteti, hogyan lehet aktív georeplikáció használatával engedélyezhető az frissítések közbeni állásidő minimálisra csökkentése, és adja meg a helyreállítási elérési út, ha valami probléma merül fel a felhőalapú alkalmazások működés közbeni frissítése.

## <a name="next-steps"></a>További lépések

Alkalmazások tervezési szempontjait önálló adatbázisok és rugalmas készletekhez, lásd: [tervezése felhőalapú vészhelyreállítással alkalmazás](sql-database-designing-cloud-solutions-for-disaster-recovery.md) és [rugalmas készletek vészhelyreállítási stratégiái](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
