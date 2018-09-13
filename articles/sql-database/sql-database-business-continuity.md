---
title: Felhőalapú üzletmenet-folytonosság – adatbázis-helyreállítás – SQL Database | Microsoft Docs
description: Ismerje meg, hogyan támogatja az Azure SQL Database a felhőalapú üzletmenet-folytonosságot és az adatbázis-helyreállítást, és hogyan segít az üzletmenet szempontjából kritikus fontosságú felhőalapú alkalmazások folyamatos működésének biztosításában.
keywords: üzletmenet-folytonosság,felhőalapú üzletmenet-folytonosság,adatbázis-vészhelyreállítás,adatbázis-helyreállítás
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.workload: On Demand
ms.date: 07/25/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: c653f1f5366e7dac43f51d5daf1f0b13d93674ce
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44721987"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése

Az Azure SQL Database az SQL Server adatbázismotor konfigurált és optimalizált Azure felhőalapú környezetet biztosít, a legújabb stabil megvalósítását [magas rendelkezésre állású](sql-database-high-availability.md) és a rugalmasság, a hibákat, amelyek hatással lehetnek a üzleti folyamatok. **Üzletmenet-folytonossági** a mechanizmusok, szabályzatokat és eljárásokat, amelyek lehetővé teszik egy üzleti megszakadását, különösen a számítástechnikai infrastruktúra esetén is hivatkozik az Azure SQL Database-ben.  A legtöbb esetben az Azure SQL Database fogja kezelni az azokat a káros eseményeket, előfordulhat, hogy a felhőalapú környezetben történik, és folyamatosan futó üzleti folyamatait. Vannak azonban bizonyos azokat a káros eseményeket, amelyek például nem kezeli az SQL Database:
 - Felhasználó véletlenül törölt vagy frissített egy sort a táblában.
 - Rosszindulatú támadó sikeresen törli az adatokat, vagy töröl egy adatbázist.
 - Földrengés egy áramkimaradás és ideiglenes letiltott adatközpont-miatt.
 
Ezekben az esetekben nem határozza meg az Azure SQL Database, így az üzletmenet-folytonossági funkciókat használja, amely lehetővé teszi az adatok helyreállításához, és tartsa működésben alkalmazásait az SQL Database-ben kell.

Az áttekintés az Azure SQL Database az üzletmenet-folytonosság és a vészhelyreállítás biztosítására szolgáló funkcióit írja le. Ismerje meg a lehetőségeket, javaslatokat és oktatóanyagokat, amelyek adatvesztéshez vezethetnek vagy az adatbázis és az alkalmazást elérhetetlenné zavaró események utáni helyreállításhoz. Ismerje meg, mi a teendő, ha egy felhasználó vagy alkalmazás hiba hatással van az adatok integritásának megőrzése, egy Azure-régióban van kimaradás vagy az alkalmazás karbantartásra szorul.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Az SQL Database üzletmenet-folytonosságot biztosító funkciói

Az adatbázist négyféleképpen fő lehetséges megszakítása:
- **Helyi hardveres vagy szoftveres hibák** érintő lemezmeghajtó hiba például az adatbázis-csomópontot.
- **Adatsérülés vagy Törlés** – általában egy alkalmazás hibát vagy emberi hiba okozhatja.  Az ilyen hibák belsőleg alkalmazásspecifikus, és nem, egy szabály észlelt vagy az infrastruktúra által automatikusan problémák elhárításáról.
- **Adatközpont-meghibásodás után**, valószínűleg okozhatja egy természeti katasztrófa esetére.  Ehhez a forgatókönyvhöz szükséges bizonyos szintű georedundáns tárolás az alkalmazás feladatátvételt egy másodlagos adatközpontba.
- **Frissítés vagy a karbantartási hiba** – nem várt során felmerülő problémák tervezett frissítések vagy egy alkalmazás vagy az adatbázis karbantartása szükség lehet a gyors visszaállítás egy korábbi adatbázis állapotának.

Az SQL Database számos üzletmenet-folytonossági funkciókat, beleértve az automatikus biztonsági mentést és a választható adatbázis-replikációt, amely lehet mérséklésére ezekben a forgatókönyvekben biztosít. Először tudni, hogyan SQL Database [magas rendelkezésre állású architektúra](sql-database-high-availability.md) 99,99 %-os rendelkezésre állást és néhány azokat a káros eseményeket, amelyek hatással lehetnek az üzleti folyamat rugalmasságot biztosít.
Ezt követően megismerheti a további mechanizmusok, amelyek segítségével helyreállíthatja a azokat a káros eseményeket, amelyek nem kell kezelnie az SQL Database magas rendelkezésre állású architektúra, például:
 - [Historikus táblák](sql-database-temporal-tables.md) lehetővé teszi sor verziók visszaállítása bármely pontról időben.
 - [Beépített automatikus biztonsági mentések](sql-database-automated-backups.md) és [időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore) lehetővé teszi a teljes adatbázis visszaállítását néhány pontra az elmúlt 35 napon belül.
 - Is [törölt adatbázis visszaállítása](sql-database-recovery-using-backups.md#deleted-database-restore) a pont, ahol azt törölték, ha a **nem lett törölve a logikai kiszolgáló**.
 - [Hosszú távú adatmegőrzés](sql-database-long-term-retention.md) lehetővé teszi, hogy a biztonsági mentések tartani 10 évre.
 - [Georeplikáció](sql-database-geo-replication-overview.md) lehetővé teszi az alkalmazásnak, hogy gyors vészhelyreállítás esetén a méretezési csoport adatközponti üzemkimaradások.

Minden egyes funkció más paraméterekkel rendelkezik a becsült helyreállítási idő (ERT) és a legutóbbi tranzakciók során előforduló esetleges adatvesztés tekintetében. Miután megismerkedett ezekkel a lehetőségekkel, szabadon válogathat közülük, és egyes forgatókönyvekben együtt is alkalmazhatja őket. Az üzletmenet folytonosságát biztosító terve kidolgozásakor kell tudni, mielőtt az alkalmazás a zavaró eseményeket követő teljes helyreállításának maximális elfogadható idő. Az alkalmazás teljes helyreállításához szükséges időt a helyreállítási időre vonatkozó célkitűzés (RTO) néven ismert. Emellett ismernie kell a leghosszabb Adatfrissítés (időintervallum) az alkalmazás működését elvesztése a zavaró eseményeket követő helyreállítása során. Az adott időszakban, előfordulhat, hogy elfogadható frissítések helyreállításipont-célkitűzés (RPO) néven ismert.

A következő táblázat összehasonlítja a ERT és RPO esetében a három leggyakoribb forgatókönyv az minden szolgáltatási szint.

| Képesség | Alapszintű | Standard | Prémium  | Általános rendeltetés | Üzletileg kritikus
| --- | --- | --- | --- |--- |--- |
| Időponthoz kötött visszaállítás biztonsági másolatból |Bármely visszaállítási pont 7 napon belül |Bármely visszaállítási pont 35 napon belül |Bármely visszaállítási pont 35 napon belül |Bármely visszaállítási pont (legfeljebb 35 napra) konfigurált időtartamon belül|Bármely visszaállítási pont (legfeljebb 35 napra) konfigurált időtartamon belül|
| Georedundáns visszaállítás georeplikált biztonsági másolatokból |ERT < 12 óra, RPO < 1 óra |ERT < 12 óra, RPO < 1 óra |ERT < 12 óra, RPO < 1 óra |ERT < 12 óra, RPO < 1 óra|ERT < 12 óra, RPO < 1 óra|
| Állítsa vissza a SQL hosszú távú megőrzéséből |ERT < 12 óra, RPO < 1 hét |ERT < 12 óra, RPO < 1 hét |ERT < 12 óra, RPO < 1 hét |ERT < 12 óra, RPO < 1 hét|ERT < 12 óra, RPO < 1 hét|
| Aktív georeplikáció |ERT < 30 másodperc, RPO < 5 másodperc |ERT < 30 s, RPO < 5-s |ERT < 30 s, RPO < 5-s |ERT < 30 s, RPO < 5-s|ERT < 30 s, RPO < 5-s|

## <a name="recover-a-database-to-the-existing-server"></a>A meglévő kiszolgáló adatbázis helyreállítása

Az SQL Database automatikusan végrehajtja az adatbázis teljes biztonsági mentését hetente, különbözeti adatbázis biztonsági mentését óránként, és a tranzakciós jelentkezzen biztonsági mentések minden 5 – 10 percet üzleti adatai védelméről adatvesztéssel szemben. A biztonsági mentések 35 napig az összes szolgáltatási szint DTU alapszintű szolgáltatási szintek a biztonsági mentések tárolására 7 napig kivételével az RA-GRS tároló vannak tárolva. További információkért lásd: [adatbázis automatikus biztonsági mentések](sql-database-automated-backups.md). Visszaállíthatja egy létező adatbázis űrlapot az automatikus biztonsági másolatokat egy korábbi időpontra az Azure portal, PowerShell vagy a REST API használatával az egyazon logikai kiszolgálón található új adatbázisként időben. További információkért lásd: [-időponthoz visszaállítási](sql-database-recovery-using-backups.md#point-in-time-restore).

Ha a maximális támogatott-időponthoz állítja vissza (PITR) megőrzési időszak nem elegendő az alkalmazáshoz, ezt végrehajthatja a az adatbázis(ok) hosszú távú adatmegőrzési (LTR) házirendjének konfigurálásával. További információkért lásd: [hosszú távú adatmegőrzés](sql-database-long-term-retention.md).

Az adatbázis automatikus biztonsági másolataiból helyreállíthatja az adatbázist a különféle zavaró eseményeket követően, akár az adatközponton belül, akár egy másik adatközpontba. Az adatbázis automatikus biztonsági másolatai használatakor a becsült helyreállítási idő több tényezőtől függ, többek között attól, hogy hány adatbázis helyreállítása zajlik egyidejűleg a régióban, az adatbázis méretétől, a tranzakciós napló méretétől, és a hálózati sávszélességtől. A helyreállítási idő általában a kevesebb mint 12 óra. Ez hosszabb időt vehet igénybe nagyon nagy méretű vagy aktív adatbázis helyreállítása. Helyreállítási idő kapcsolatos további információkért lásd: [adatbázis-helyreállítás ideje](sql-database-recovery-using-backups.md#recovery-time). Ha egy másik adatrégióba végzi a helyreállítást, az esetleges adatvesztés legfeljebb 1 óra lehet az adatbázis óránkénti különbségi biztonsági másolatai georedundáns tárolásának köszönhetően.

Használja az automatikus biztonsági másolatokat és [időponthoz visszaállítási](sql-database-recovery-using-backups.md#point-in-time-restore) az üzleti folytonossági és helyreállítási mechanizmusként Ha alkalmazását:

* Nem tekinthető az üzletmenet szempontjából kritikus fontosságúnak.
* Nem rendelkezik egy kötés SLA - egy 24 órás állásidő, vagy már nem pénzügyi kötelezettséget eredményez.
* Kis mértékű adatmódosításra lehet számítani (alacsony óránkénti tranzakciószám), és egy órányi adatmódosítás elvesztése elfogadható.
* Költségérzékeny.

Ha gyorsabb helyreállítás szükséges, használja a [aktív georeplikáció](sql-database-geo-replication-overview.md) (lásd az alábbiakban). Ha szeretné tudni 35 napot meghaladó időszakból másolatból szeretne adatokat helyreállítani, használja a [hosszú távú megőrzés](sql-database-long-term-retention.md). 

## <a name="recover-a-database-to-another-region"></a>Adatbázis helyreállítása egy másik régióba az
<!-- Explain this scenario -->

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Leállás esetén az üzletmenet esetleg csak néhány percre, de akár több órára is megszakadhat.

* Az egyik lehetőség, hogy megvárja, amíg az adatközpont leállását követően az adatbázis újra elérhető lesz. Ez az olyan alkalmazások esetében működik, amelyek esetében megengedhető, hogy az adatbázis offline állapotú legyen. Például egy fejlesztési projekt vagy egy ingyenes próbaverzió esetében, amelyeken nem kell folyamatosan dolgoznia. Amikor egy adatközpontban szolgáltatáskimaradás következik, nem tudja, mennyi ideig a szolgáltatáskimaradás elhárítása után előfordulhat, hogy a legutóbbi, így ez a beállítás csak akkor működik, ha már nincs szüksége az adatbázis egy ideig.
* Egy másik lehetőség az, hogy egyetlen kiszolgálón, az minden olyan Azure-régió-adatbázis visszaállítása [georedundáns adatbázis biztonsági másolatait](sql-database-recovery-using-backups.md#geo-restore) (georedundáns visszaállítást). A GEO-visszaállítás georedundáns biztonsági másolat használja forrásként, és segítségével helyreállíthat egy adatbázist, akkor is, ha az adatbázis vagy az Adatközpont-leállás miatt elérhetetlenné.
* Végül gyorsan előléptethet egy másik adatrégióba elsődlegessé (ezt feladatátvételnek is nevezik), és konfigurálhatja az alkalmazásokat az előléptetett elsődleges csatlakozni, ha aktív georeplikációt használ, a másodlagos kiszolgálóra. Előfordulhat, hogy néhány kisebb mennyiségű adatvesztést aszinkrón replikáció természete miatt a legutóbbi tranzakciók esetében. Automatikus feladatátvételi csoportok testre szabhatja a feladatátvételi szabályzat az esetleges adatvesztés minimalizálása érdekében. A felhasználók minden esetben rövid állásidőt tapasztalnak, és újra kell csatlakozniuk. Feladatátvételi csupán néhány másodpercet vesz igénybe, amíg az adatbázis biztonsági másolatokból való helyreállítás órát vesz igénybe.

Annak érdekében, hogy a feladatátvételt egy másik régióba, használhatja a [aktív georeplikáció](sql-database-geo-replication-overview.md) konfigurálása az adatbázis rendelkezik a legfeljebb négy olvasható másodlagos adatbázissal tetszőleges régiókban. Ezeket a másodlagos adatbázisokat a rendszer egy aszinkrón replikációs mechanizmus használatával folyamatosan szinkronizálja az elsődleges adatbázissal. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>


> [!IMPORTANT]
> Aktív georeplikáció és automatikus feladatátvételi csoportok szeretné használni, kell lennie az előfizetés tulajdonosa vagy az SQL Server rendszergazdai jogosultságok. Konfigurálhatja, és átadja a feladatokat az Azure portal, PowerShell vagy a REST API használatával az Azure-előfizetés engedélyek vagy a Transact-SQL használatával az SQL Server engedélyei.
> 

Ez a funkció az üzletmenet megszakadása ellen védelmet biztosító adatközponti üzemkimaradások esetére, vagy egy alkalmazás frissítése során használatos. Ahhoz, hogy az automatikus és a transzparens feladatátvételt kell szervezni a georeplikált adatbázis használatával csoportokba a [automatikus feladatátvételi csoport](sql-database-geo-replication-overview.md) SQL adatbázis szolgáltatása. Aktív georeplikáció és automatikus feladatátvételi csoportok használata, ha az alkalmazás megfelel a következő feltételeknek:

* Az üzletmenet szempontjából kritikus fontosságú.
* Olyan szolgáltatói szerződés (SLA) vonatkozik rá, amely nem enged 24 órás vagy azt meghaladó állásidőt.
* Állásidő pénzügyi kötelezettséget eredményezhet.
* Nagy mértékű adatváltozásokra lehet számítani, és egy órányi adat elvesztése nem elfogadható.
* Az aktív georeplikáció többletköltsége alacsonyabb, mint a potenciális pénzbeli kötelezettség és a kapcsolódó üzletvesztés összege.

Amikor műveleteket végrehajtani, mennyi idő alatt helyreállíthatók, és mekkora adatvesztés díjak, attól függ, hogyan kívánja-e üzletmenet-folytonossági funkciókat használhatja az alkalmazásban. Sőt előfordulhat, hogy használni kívánja adatbázisok biztonsági mentése és az aktív georeplikáció attól függően, hogy az alkalmazás követelményeinek. Alkalmazások tervezési szempontjait önálló adatbázisok és ezek üzletmenet-folytonossági funkciókat használó rugalmas készletek számára, lásd: [tervezése felhőalapú vészhelyreállítással alkalmazás](sql-database-designing-cloud-solutions-for-disaster-recovery.md) és [rugalmas Vészhelyreállítási stratégiák tárolókészlet](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

A következő szakaszok az adatbázis biztonsági mentése vagy az aktív georeplikáció használatával végzett helyreállítás lépéseinek áttekintését. Beleértve a tervezési követelményeket, helyreállítás utáni lépések és információ a leállások szimulálását a vészhelyreállítási próba végrehajtása részletes lépéseiért lásd: [SQL-adatbázis helyreállítása a kimaradás utáni](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Előkészületek leállás esetére
A használt üzletmenet-folytonossági funkciótól függetlenül végre kell hajtania a következőket:

* Azonosítsa és készítse elő a célkiszolgálót, beleértve a kiszolgálószintű tűzfalszabályokat, a bejelentkezéseket és a főadatbázis szintű jogosultságokat.
* Határozza meg, hogyan legyenek átirányítva az ügyfelek és az ügyfélalkalmazások az új kiszolgálóra.
* Dokumentálja az egyéb függőségeket, mint a naplózási beállítások és a riasztások.

Ha nem megfelelően, így az alkalmazások, online, miután a feladatátvétel, vagy egy adatbázis-helyreállítás további időt igényel, és valószínűleg hibaelhérítást is igényel a stressz – rossz kombináció egyszerre.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Feladatátvételt egy georeplikált másodlagos adatbázisra
Ha tárreplikációval helyreállítási mechanizmusként aktív georeplikációt, és automatikus feladatátvételi csoportok használ, automatikus feladatátvételi szabályzat konfigurálása, vagy használjon [manuális feladatátvétel](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-server-in-the-failover-group). Követően a feladatátvételt a másodlagos az új elsődleges válnak, és készen áll rögzíteni az új tranzakciókat és válaszolni a lekérdezésekre – az adatok nem replikálta minimális adatvesztéssel járó hatására a. A feladatátvételi folyamat tervezésével kapcsolatos további információkért lásd: [tervezése felhőalapú vészhelyreállítással alkalmazás](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Az Adatközpont ismét online elérhető a régi eredményezi automatikusan csatlakozzon újra az új elsődleges, és a másodlagos adatbázisok válnak. Helyezze át az elsődleges vissza az eredeti régió van szüksége, ha manuálisan is kezdeményezhető a tervezett feladatátvétel (feladat-visszavétel). 
> 

### <a name="perform-a-geo-restore"></a>Georedundáns visszaállítás végrehajtása
Ha, automatikus biztonsági másolatokat használ georedundáns tárolás replikációval tárreplikációval helyreállítási mechanizmusként [kezdeményezheti egy adatbázis helyreállítását, georedundáns visszaállítással](sql-database-disaster-recovery.md#recover-using-geo-restore). A helyreállítás általában legfeljebb 12 óra alatt végrehajtható – legfeljebb egy órányi adatvesztéssel attól függően, hogy mikor történt a legutóbbi óránkénti különbségi biztonsági mentés és replikáció. A helyreállítás befejezéséig az adatbázis nem tudja rögzíteni a tranzakciókat, illetve nem tud válaszolni a lekérdezésekre. Bár ez visszaállít egy adatbázist a legutóbbi elérhető időponthoz kötött, a geo-secondary bármely időpontra való visszaállítást idő jelenleg nem támogatott.

> [!NOTE]
> Az Adatközpont ismét online elérhető, mielőtt az alkalmazás a helyreállított adatbázisra, megszakíthatja a helyreállítást.  
>
>

### <a name="perform-post-failover--recovery-tasks"></a>Feladatátvétel/helyreállítás utáni feladatok végrehajtása
A helyreállítási mechanizmusok végrehajtása után a következő további feladatokat kell végrehajtania a felhasználók és az alkalmazások újbóli üzembe helyezéséhez:

* Irányítsa át az ügyfeleket és az ügyfélalkalmazásokat az új kiszolgálóra és a helyreállított adatbázisra.
* Biztosítsa, hogy megfelelő kiszolgálószintű tűzfalszabályok vannak érvényben a felhasználók csatlakozásához (vagy használjon [adatbázisszintű tűzfalakat](sql-database-firewall-configure.md#creating-and-managing-firewall-rules)).
* Biztosítsa a megfelelő bejelentkezési adatok és főadatbázis-szintű jogosultságok rendelkezésre állását (vagy használjon [tartalmazott felhasználókat](https://msdn.microsoft.com/library/ff929188.aspx))
* Konfigurálja a naplózást, ha szükséges.
* Konfigurálja a riasztásokat, ha szükséges.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Alkalmazások frissítése minimális állásidővel
Néha egy alkalmazást kell venni a kapcsolat nélküli tervezett karbantartások, például alkalmazásfrissítések miatt. [Alkalmazásfrissítések kezeléséről](sql-database-manage-application-rolling-upgrade.md) ismerteti, hogyan lehet aktív georeplikáció használatával engedélyezhető az frissítések közbeni állásidő minimálisra csökkentése, és adja meg a helyreállítási elérési út, ha valami probléma merül fel a felhőalapú alkalmazások működés közbeni frissítése. 

## <a name="next-steps"></a>További lépések
Alkalmazások tervezési szempontjait önálló adatbázisok és rugalmas készletekhez, lásd: [tervezése felhőalapú vészhelyreállítással alkalmazás](sql-database-designing-cloud-solutions-for-disaster-recovery.md) és [rugalmas készletek vészhelyreállítási stratégiái](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
