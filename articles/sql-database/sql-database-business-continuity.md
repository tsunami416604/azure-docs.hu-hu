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
ms.openlocfilehash: 46ab4a177cc7d86e5d967ff8e219dae96f82a0dc
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263146"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése

Az áttekintés az Azure SQL Database az üzletmenet-folytonosság és a vészhelyreállítás biztosítására szolgáló funkcióit írja le. Ismerje meg a lehetőségeket, javaslatokat és oktatóanyagokat, amelyek adatvesztéshez vezethetnek vagy az adatbázis és az alkalmazást elérhetetlenné zavaró események utáni helyreállításhoz. Ismerje meg, mi a teendő, ha egy felhasználó vagy alkalmazás hiba hatással van az adatok integritásának megőrzése, egy Azure-régióban van kimaradás vagy az alkalmazás karbantartásra szorul.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Az SQL Database üzletmenet-folytonosságot biztosító funkciói

Az SQL Database számos üzletmenet-folytonossági funkciót nyújt, többek között az automatikus biztonsági mentést és a választható adatbázis-replikációt. Minden egyes funkció más paraméterekkel rendelkezik a becsült helyreállítási idő (ERT) és a legutóbbi tranzakciók során előforduló esetleges adatvesztés tekintetében. Miután megismerkedett ezekkel a lehetőségekkel, szabadon válogathat közülük, és egyes forgatókönyvekben együtt is alkalmazhatja őket. Az üzletmenet-folytonossági terv kidolgozása során meg kell állapítania az alkalmazás a zavaró eseményeket követő teljes helyreállításának maximális elfogadható időkorlátját – ez lesz ugyanis a helyreállítási időre vonatkozó célkitűzés (RTO). Emellett ismernie kell a legújabb adatok maximális mérete (időintervallum) frissítéseket az alkalmazás működését a zavaró eseményeket követő helyreállítása során elvesztése – Ez a helyreállításipont-célkitűzés (RPO).

A következő táblázat összehasonlítja a ERT és RPO esetében a három leggyakoribb forgatókönyv az minden szolgáltatási szint.

| Képesség | Alapszintű | Standard | Prémium  | Általános rendeltetés | Üzletileg kritikus
| --- | --- | --- | --- |--- |--- |
| Időponthoz kötött visszaállítás biztonsági másolatból |Bármely visszaállítási pont 7 napon belül |Bármely visszaállítási pont 35 napon belül |Bármely visszaállítási pont 35 napon belül |Bármely visszaállítási pont (legfeljebb 35 napra) konfigurált időtartamon belül|Bármely visszaállítási pont (legfeljebb 35 napra) konfigurált időtartamon belül|
| Georedundáns visszaállítás georeplikált biztonsági másolatokból |ERT < 12 óra, RPO < 1 óra |ERT < 12 óra, RPO < 1 óra |ERT < 12 óra, RPO < 1 óra |ERT < 12 óra, RPO < 1 óra|ERT < 12 óra, RPO < 1 óra|
| Állítsa vissza a SQL hosszú távú megőrzéséből |ERT < 12 óra, RPO < 1 hét |ERT < 12 óra, RPO < 1 hét |ERT < 12 óra, RPO < 1 hét |ERT < 12 óra, RPO < 1 hét|ERT < 12 óra, RPO < 1 hét|
| Aktív georeplikáció |ERT < 30 másodperc, RPO < 5 másodperc |ERT < 30 másodperc, RPO < 5 másodperc |ERT < 30 másodperc, RPO < 5 másodperc |ERT < 30 másodperc, RPO < 5 másodperc|ERT < 30 másodperc, RPO < 5 másodperc|

### <a name="use-point-in-time-restore-to-recover-a-database"></a>Adatbázis helyreállítása időponthoz visszaállítás használatával

Az SQL Database automatikusan végrehajtja az adatbázis teljes biztonsági mentését hetente, különbözeti adatbázis biztonsági mentését óránként, és a tranzakciós jelentkezzen biztonsági mentések minden öt - tíz percet üzleti adatai védelméről adatvesztéssel szemben. Ha használja a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md), akkor ezeket a biztonsági másolatokat az RA-GRS storage tárolja 35 napig a Standard és prémium szolgáltatásszinten, és 7 nap az alapszintű szolgáltatásszinten lévő adatbázisok esetében adatbázis. Ha a szolgáltatásszint megőrzési időszaka nem felel meg az üzleti igényeinek, a megőrzési időszak növelése érdekében [váltson szolgáltatásszintet](sql-database-single-database-scale.md). Ha használja a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md), az általános célú és a kritikus fontosságú üzleti szintet a biztonsági mentések megőrzési konfigurálható mentése 35 napon belül. Az adatbázis teljes és különbségi biztonsági másolatai emellett replikálva vannak egy [párosított adatközpontba](../best-practices-availability-paired-regions.md) az adatközpont-leállás hatásaival szembeni védelem érdekében. További információkért lásd: [adatbázis automatikus biztonsági mentések](sql-database-automated-backups.md).

Ha a maximális támogatott-időponthoz állítja vissza (PITR) megőrzési időszak nem elegendő az alkalmazáshoz, ezt végrehajthatja a az adatbázis(ok) hosszú távú adatmegőrzési (LTR) házirendjének konfigurálásával. További információkért lásd: [automatizált biztonsági mentések](sql-database-automated-backups.md) és [hosszú távú adatmegőrzés](sql-database-long-term-retention.md).

Az adatbázis automatikus biztonsági másolataiból helyreállíthatja az adatbázist a különféle zavaró eseményeket követően, akár az adatközponton belül, akár egy másik adatközpontba. Az adatbázis automatikus biztonsági másolatai használatakor a becsült helyreállítási idő több tényezőtől függ, többek között attól, hogy hány adatbázis helyreállítása zajlik egyidejűleg a régióban, az adatbázis méretétől, a tranzakciós napló méretétől, és a hálózati sávszélességtől. A helyreállítási idő általában a kevesebb mint 12 óra. Ez hosszabb időt vehet igénybe nagyon nagy méretű vagy aktív adatbázis helyreállítása. Helyreállítási idő kapcsolatos további információkért lásd: [adatbázis-helyreállítás ideje](sql-database-recovery-using-backups.md#recovery-time). Ha egy másik adatrégióba végzi a helyreállítást, az esetleges adatvesztés legfeljebb 1 óra lehet az adatbázis óránkénti különbségi biztonsági másolatai georedundáns tárolásának köszönhetően.

> [!IMPORTANT]
> Az automatikus biztonsági másolatokból való helyreállításhoz az SQL Server közreműködői szerepköre tagjának vagy az előfizetés tulajdonosának kell lennie – lásd: [Beépített RBAC-szerepkörök](../role-based-access-control/built-in-roles.md). A helyreállítást az Azure Portal, a PowerShell vagy a REST API használatával végezheti. A Transact-SQL nem használható.
>

Akkor használja az automatikus biztonsági másolatokat üzletmenet-folytonossági és helyreállítási mechanizmusként, ha az alkalmazásra igazak a következők:

* Nem tekinthető az üzletmenet szempontjából kritikus fontosságúnak.
* Nem rendelkezik egy kötés SLA - egy 24 órás állásidő, vagy már nem pénzügyi kötelezettséget eredményez.
* Kis mértékű adatmódosításra lehet számítani (alacsony óránkénti tranzakciószám), és egy órányi adatmódosítás elvesztése elfogadható.
* Költségérzékeny.

Ha gyorsabb helyreállítás szükséges, használja a [aktív georeplikáció](sql-database-geo-replication-overview.md) (lásd az alábbiakban). Ha szeretné tudni 35 napot meghaladó időszakból másolatból szeretne adatokat helyreállítani, használja a [hosszú távú megőrzés](sql-database-long-term-retention.md). 

### <a name="use-active-geo-replication-and-auto-failover-groups-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Helyreállítási idő csökkentése és a helyreállítással kapcsolatos adatvesztés korlátozása aktív georeplikáció és automatikus feladatátvételi csoportok használatával

Adatbázis biztonsági másolatait használja az adatbázis helyreállításához üzletmenet megszakadása esetén, mellett használhatja [aktív georeplikáció](sql-database-geo-replication-overview.md) konfigurálása az adatbázis rendelkezik a legfeljebb négy olvasható másodlagos adatbázissal tetszőleges régiókban. Ezeket a másodlagos adatbázisokat a rendszer egy aszinkrón replikációs mechanizmus használatával folyamatosan szinkronizálja az elsődleges adatbázissal. Ez a funkció az üzletmenet megszakadása ellen védelmet biztosító adatközponti üzemkimaradások esetére, vagy egy alkalmazás frissítése során használatos. Aktív georeplikáció is használható földrajzilag elosztott felhasználók csak olvasható lekérdezései jobb lekérdezési teljesítményt biztosít.

Ahhoz, hogy az automatikus és a transzparens feladatátvételt kell szervezni a georeplikált adatbázis használatával csoportokba a [automatikus feladatátvételi csoport](sql-database-geo-replication-overview.md) SQL adatbázis szolgáltatása.

Ha az elsődleges adatbázis kikapcsol vagy állapotúra karbantartási tevékenységek miatt kell, gyorsan előléptethet egy másodlagos elsődlegessé (ezt feladatátvételnek is nevezik), és konfigurálhatja az alkalmazásokat az előléptetett elsődleges csatlakozni. Ha az alkalmazás csatlakozik az adatbázisokat a feladatátvételi csoport figyelőjét, nem kell módosítani az SQL kapcsolati karakterláncok konfigurálása a feladatátvételt követően. Tervezett feladatátvétel esetén nincs adatvesztés. Nem tervezett feladatátvétel esetén elképzelhető kismértékű adatvesztés a legutóbbi tranzakciók esetében az aszinkrón replikáció természete miatt. Automatikus feladatátvételi csoportok testre szabhatja a feladatátvételi szabályzat az esetleges adatvesztés minimalizálása érdekében. A feladatátvételt később követheti feladat-visszavétel – akár tervezetten, akár amikor az adatközpont újra elérhetővé válik. A felhasználók minden esetben rövid állásidőt tapasztalnak, és újra kell csatlakozniuk.

> [!IMPORTANT]
> Aktív georeplikáció és automatikus feladatátvételi csoportok szeretné használni, kell lennie az előfizetés tulajdonosa vagy az SQL Server rendszergazdai jogosultságok. Konfigurálhatja, és átadja a feladatokat az Azure portal, PowerShell vagy a REST API használatával az Azure-előfizetés engedélyek vagy a Transact-SQL használatával az SQL Server engedélyei.
> 

Aktív georeplikáció és automatikus feladatátvételi csoportok használata, ha az alkalmazás megfelel a következő feltételeknek:

* Az üzletmenet szempontjából kritikus fontosságú.
* Olyan szolgáltatói szerződés (SLA) vonatkozik rá, amely nem enged 24 órás vagy azt meghaladó állásidőt.
* Állásidő pénzügyi kötelezettséget eredményezhet.
* Nagy mértékű adatváltozásokra lehet számítani, és egy órányi adat elvesztése nem elfogadható.
* Az aktív georeplikáció többletköltsége alacsonyabb, mint a potenciális pénzbeli kötelezettség és a kapcsolódó üzletvesztés összege.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

## <a name="recover-a-database-after-a-user-or-application-error"></a>Adatbázis helyreállítása felhasználói vagy alkalmazáshiba után

Senki sem tökéletes. Egy felhasználó véletlenül törölhet néhány adatot, egy fontos adattáblát, vagy akár a teljes adatbázist is. Vagy előfordulhat, hogy egy alkalmazás valamilyen alkalmazáshiba miatt felülírja a helyes adatokat rossz adatokkal.

Ebben a forgatókönyvben ezek közül a helyreállítási lehetőségek közül választhat.

### <a name="perform-a-point-in-time-restore"></a>Időponthoz kötött visszaállítás végrehajtása
Az automatikus biztonsági másolatok használatával helyreállíthatja az adatbázist annak egy megfelelő időpontban készült másolatára, feltéve, hogy az adott időpont a megőrzési időszakba esik. Az adatbázis visszaállítását követően lecserélheti az eredeti adatbázist a visszaállítottra, vagy átmásolhatja a szükséges adatokat a visszaállított adatbázisból az eredetibe. Ha az adatbázis aktív georeplikációt használ, javasoljuk a szükséges adatok másolása a visszaállított másolatból az eredeti adatbázisba. Ha lecseréli az eredeti adatbázist a visszaállítottra, meg kell konfigurálnia és szinkronizálnia az aktív georeplikációt (Ez meglehetősen hosszabb ideig is eltarthat a nagy méretű adatbázisok). Bár ez visszaállít egy adatbázist a legutóbbi elérhető időponthoz kötött, a geo-secondary bármely időpontra való visszaállítást idő jelenleg nem támogatott.

Adatbázisok az Azure Portalon vagy a PowerShell-lel végzett, adott időpontra való helyreállításával kapcsolatos további információkért lásd: [időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore). Nem végezhet helyreállítást a Transact-SQL használatával.

### <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása
Ha az adatbázis törölve lett, a logikai kiszolgáló azonban nem, a törölt adatbázist visszaállíthatja arra az időpontra, amikor törölve lett. Ez visszaállítja az adatbázis biztonsági másolatát arra a logikai SQL Serverre, amelyről törölve lett. Visszaállíthatja az eredeti nevén, vagy megadhat egy új nevet a visszaállított adatbázis számára.

A törölt adatbázisok az Azure Portalon vagy a PowerShell-lel végzett visszaállításával kapcsolatos további információkért lásd: [törölt adatbázis visszaállítása](sql-database-recovery-using-backups.md#deleted-database-restore). Nem végezhet visszaállítást a Transact-SQL használatával.

> [!IMPORTANT]
> Ha a logikai kiszolgáló törölve lett, a törölt adatbázis nem állítható helyre.


### <a name="restore-backups-from-long-term-retention"></a>Biztonsági másolatok visszaállításához a hosszú távú megőrzéséből

Ha az adatvesztés az automatikus biztonsági másolatok aktuális megőrzési időszakán kívül történt, és az adatbázis konfigurálva van az Azure blob storage-dzsal hosszú távú megőrzésének, visszaállíthatja egy teljes biztonsági másolatból az Azure blob Storage egy új adatbázisba. Ekkor lecserélheti az eredeti adatbázist a visszaállítottra, vagy átmásolhatja a szükséges adatokat a visszaállított adatbázisból az eredetibe. Szeretné beolvasni az adatbázis egy nagyobb alkalmazásfrissítés előtti, régebbi verzióját, ha kérelem teljesítéséhez auditorok vagy egy jogi sorrendben is létrehozhat egy adatbázist egy teljes biztonsági mentése az Azure blob storage-ban.  További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Adatbázis helyreállítása egy másik régióba az Azure regionális adatközpontjának leállása esetén
<!-- Explain this scenario -->

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Leállás esetén az üzletmenet esetleg csak néhány percre, de akár több órára is megszakadhat.

* Az egyik lehetőség, hogy megvárja, amíg az adatközpont leállását követően az adatbázis újra elérhető lesz. Ez az olyan alkalmazások esetében működik, amelyek esetében megengedhető, hogy az adatbázis offline állapotú legyen. Például egy fejlesztési projekt vagy egy ingyenes próbaverzió esetében, amelyeken nem kell folyamatosan dolgoznia. Amikor egy adatközpontban szolgáltatáskimaradás következik, nem tudja, mennyi ideig a szolgáltatáskimaradás elhárítása után előfordulhat, hogy a legutóbbi, így ez a beállítás csak akkor működik, ha már nincs szüksége az adatbázis egy ideig.
* Egy másik lehetőség, hogy bármelyik sikertelen keresztül egy másik adatrégióba, ha az aktív georeplikáció vagy a helyreállítás egy adatbázis georedundáns adatbázis biztonsági másolatait (geo-visszaállítás) használja. Feladatátvételi csupán néhány másodpercet vesz igénybe, amíg az adatbázis biztonsági másolatokból való helyreállítás órát vesz igénybe.

Amikor műveleteket végrehajtani, mennyi idő alatt helyreállíthatók, és mekkora adatvesztés díjak, attól függ, hogyan kívánja-e üzletmenet-folytonossági funkciókat használhatja az alkalmazásban. Sőt előfordulhat, hogy használni kívánja adatbázisok biztonsági mentése és az aktív georeplikáció attól függően, hogy az alkalmazás követelményeinek. Az alkalmazások tervezési szempontjait önálló adatbázisok vagy az ezeket az üzletmenet-folytonossági funkciókat használó rugalmas készletek esetén lásd: [Alkalmazások tervezése felhőalapú vészhelyreállítással](sql-database-designing-cloud-solutions-for-disaster-recovery.md) és [Rugalmas készletek vészhelyreállítási stratégiái](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

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
Az alkalmazások tervezési szempontjait önálló adatbázisok és rugalmas készletek esetén lásd: [Alkalmazások tervezése felhőalapú vészhelyreállítással](sql-database-designing-cloud-solutions-for-disaster-recovery.md) és [Rugalmas készletek vészhelyreállítási stratégiái](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
