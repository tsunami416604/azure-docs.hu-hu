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
ms.date: 06/27/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 18c162e03030fc4277fa0a7b3e953bf780574a21
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084960"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése

Az áttekintés az Azure SQL Database az üzletmenet-folytonosság és a vészhelyreállítás biztosítására szolgáló funkcióit írja le. Ismerje meg a beállítások, a javaslatok és az oktatóanyagok esetén a helyreállítás őket az eseményeket, amelyek nem adatvesztéshez vezethet, vagy az adatbázis és a már nem érhető el az alkalmazást. Ismerje meg, mi a teendő, ha egy felhasználó vagy alkalmazás hiba hatással van az adatok integritását, egy Azure-régióban van kimaradás vagy az alkalmazás által igényelt karbantartási.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Az SQL Database üzletmenet-folytonosságot biztosító funkciói

Az SQL Database számos üzletmenet-folytonossági funkciót nyújt, többek között az automatikus biztonsági mentést és a választható adatbázis-replikációt. Minden egyes funkció más paraméterekkel rendelkezik a becsült helyreállítási idő (ERT) és a legutóbbi tranzakciók során előforduló esetleges adatvesztés tekintetében. Miután megismerkedett ezekkel a lehetőségekkel, szabadon válogathat közülük, és egyes forgatókönyvekben együtt is alkalmazhatja őket. Az üzletmenet-folytonossági terv kidolgozása során meg kell állapítania az alkalmazás a zavaró eseményeket követő teljes helyreállításának maximális elfogadható időkorlátját – ez lesz ugyanis a helyreállítási időre vonatkozó célkitűzés (RTO). A legfrissebb adatok maximális mennyisége ismernie kell frissítések (alatt az időtartam alatt) az alkalmazás működését zavaró esemény után helyreállításakor elvesztését – Ez a helyreállítási időkorlát (RPO).

Az alábbi táblázat összehasonlítja a Beszúrása és a helyreállítási Időkorlát három leggyakoribb forgatókönyve az egyes szolgáltatási szinthez.

| Képesség | Alapszintű | Standard | Prémium  | Általános rendeltetés | Üzletileg kritikus
| --- | --- | --- | --- |--- |--- |
| Időponthoz kötött visszaállítás biztonsági másolatból |Bármely visszaállítási pont 7 napon belül |Bármely visszaállítási pont 35 napon belül |Bármely visszaállítási pont 35 napon belül |A visszaállítási pont (legfeljebb 35 napon) beállított időn belül|A visszaállítási pont (legfeljebb 35 napon) beállított időn belül|
| Georedundáns helyreállítás georeplikált biztonsági másolatból |ERT < 12 óra, RPO < 1 óra |ERT < 12 óra, RPO < 1 óra |ERT < 12 óra, RPO < 1 óra |ERT < 12 óra, RPO < 1 óra|ERT < 12 óra, RPO < 1 óra|
| Visszaállítás Azure Backup-tárolóból |ERT < 12 óra, RPO < 1 hét |ERT < 12 óra, RPO < 1 hét |ERT < 12 óra, RPO < 1 hét |ERT < 12 óra, RPO < 1 hét|ERT < 12 óra, RPO < 1 hét|
| Aktív georeplikáció |ERT < 30 másodperc, RPO < 5 másodperc |ERT < 30 másodperc, RPO < 5 másodperc |ERT < 30 másodperc, RPO < 5 másodperc |ERT < 30 másodperc, RPO < 5 másodperc|ERT < 30 másodperc, RPO < 5 másodperc|

### <a name="use-point-in-time-restore-to-recover-a-database"></a>Adatbázis helyreállítása pont időponthoz kötött visszaállítás segítségével

SQL-adatbázis automatikusan elvégzi az adatbázis teljes biztonsági mentés hetente kombinációja, a különbözeti adatbázis óránkénti, és a tranzakció jelentkezhetnek biztonsági mentések minden öt - tíz perc az üzleti adatvesztés elleni védelméhez. Használata esetén a [alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md), majd a biztonsági mentése az a Standard és prémium szolgáltatáscsomagja és az adatbázisok alapszintű rétegben 7 nap-adatbázisok 35 napon vannak tárolva RA-GRS-tároló. Ha a szolgáltatásszint megőrzési időszaka nem felel meg az üzleti igényeinek, a megőrzési időszak növelése érdekében [váltson szolgáltatásszintet](sql-database-single-database-scale.md). Használata esetén a [vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)](sql-database-service-tiers-vcore.md), a biztonsági mentések megőrzési konfigurálható mentése 35 nap az általános célú és a kritikus fontosságú üzleti rétegeihez. Az adatbázis teljes és különbségi biztonsági másolatai emellett replikálva vannak egy [párosított adatközpontba](../best-practices-availability-paired-regions.md) az adatközpont-leállás hatásaival szembeni védelem érdekében. További információkért lásd: [automatikus mentését](sql-database-automated-backups.md).

Ha a maximális támogatott pont időponthoz kötött visszaállítás (PITR) megőrzési időtartam nem elegendő az alkalmazáshoz, kiterjesztheti a hosszú távú megőrzési (LTR) házirendet az adatbázis(ok) konfigurálásával. További információkért lásd: [automatikus biztonsági mentés](sql-database-automated-backups.md) és [hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-retention.md).

Az adatbázis automatikus biztonsági másolataiból helyreállíthatja az adatbázist a különféle zavaró eseményeket követően, akár az adatközponton belül, akár egy másik adatközpontba. Az adatbázis automatikus biztonsági másolatai használatakor a becsült helyreállítási idő több tényezőtől függ, többek között attól, hogy hány adatbázis helyreállítása zajlik egyidejűleg a régióban, az adatbázis méretétől, a tranzakciós napló méretétől, és a hálózati sávszélességtől. A helyreállítási idő az általában 12 óránál kevesebb. Nagyon nagy méretű vagy aktív adatbázis helyreállítása hosszabb ideig is eltarthat. A helyreállítási idő kapcsolatos további tudnivalókért lásd: [adatbázis-helyreállítási idő](sql-database-recovery-using-backups.md#recovery-time). Ha egy másik adatrégióba végzi a helyreállítást, az esetleges adatvesztés legfeljebb 1 óra lehet az adatbázis óránkénti különbségi biztonsági másolatai georedundáns tárolásának köszönhetően.

> [!IMPORTANT]
> Az automatikus biztonsági másolatokból való helyreállításhoz az SQL Server közreműködői szerepköre tagjának vagy az előfizetés tulajdonosának kell lennie – lásd: [Beépített RBAC-szerepkörök](../role-based-access-control/built-in-roles.md). A helyreállítást az Azure Portal, a PowerShell vagy a REST API használatával végezheti. A Transact-SQL nem használható.
>

Akkor használja az automatikus biztonsági másolatokat üzletmenet-folytonossági és helyreállítási mechanizmusként, ha az alkalmazásra igazak a következők:

* Nem tekinthető az üzletmenet szempontjából kritikus fontosságúnak.
* Nem rendelkezik egy kötés SLA - egy 24 órás állásidő, vagy már nem eredményez pénzügyi cselekményre vonatkozó minden felelősséget.
* Kis mértékű adatmódosításra lehet számítani (alacsony óránkénti tranzakciószám), és egy órányi adatmódosítás elvesztése elfogadható.
* Költségérzékeny.

Ha a gyorsabb helyreállítás van szüksége, [aktív georeplikáció](sql-database-geo-replication-overview.md) (tárgyalt mellett). Ha szeretné tudni időtartamra 35 napnál régebbi adatokat helyreállítani, használja [hosszú távú megőrzési](sql-database-long-term-retention.md). 

### <a name="use-active-geo-replication-and-auto-failover-groups-in-preview-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Aktív georeplikáció és automatikus feladatátvételt csoportok (az előzetes verzió) segítségével a helyreállítási idő csökkentésére, valamint a társított helyreállítási adatvesztés korlátozása

Egy üzleti megszakítása esetén használata az adatbázis biztonsági mentését az adatbázis helyreállításához, mellett használhatja [aktív georeplikáció](sql-database-geo-replication-overview.md) konfigurálása egy adatbázist legfeljebb négy olvasható másodlagos adatbázisok a kiválasztott régióban. Ezeket a másodlagos adatbázisokat a rendszer egy aszinkrón replikációs mechanizmus használatával folyamatosan szinkronizálja az elsődleges adatbázissal. Ez a szolgáltatás használható üzleti megszűnésének elleni védelem érdekében, ha egy adatközpont-meghibásodás után következik be, vagy egy alkalmazás frissítése során. Aktív georeplikáció adja meg a csak olvasható lekérdezések teljesítményét lekérdezés földrajzilag elosztott felhasználóknak is használható.

Automatikus, és transzparens feladatátvétellel kell szervezni a georeplikált adatbázisok használatával csoportokba a [automatikus feladatátvételt csoport](sql-database-geo-replication-overview.md) szolgáltatás SQL-adatbázis (az előzetes verzió).

Ha az elsődleges adatbázis kikapcsol vagy kell venni a karbantartás tevékenységek offline, gyors előléptetheti az elsődleges (más néven a feladatátvétel), és konfigurálhatja az alkalmazásokat az előléptetett elsődleges csatlakozni egy másodlagos. Ha az alkalmazás az adatbázisokat a feladatátvételi csoport figyelőjének csatlakozik, nem módosítani szeretné az SQL kapcsolódási karakterlánc konfigurációs feladatátvételt követően. Tervezett feladatátvétel esetén nincs adatvesztés. Nem tervezett feladatátvétel esetén elképzelhető kismértékű adatvesztés a legutóbbi tranzakciók esetében az aszinkrón replikáció természete miatt. Automatikus feladatátvételt csoportok (az előzetes verzió) segítségével testre szabhatja a lehetséges adatvesztést minimalizálása érdekében a feladatátvételi házirendhez. A feladatátvételt később követheti feladat-visszavétel – akár tervezetten, akár amikor az adatközpont újra elérhetővé válik. A felhasználók minden esetben rövid állásidőt tapasztalnak, és újra kell csatlakozniuk.

> [!IMPORTANT]
> Aktív georeplikáció és automatikus feladatátvételt csoportok (az előzetes verzió) szeretné használni, kell lennie az előfizetés tulajdonosa vagy az SQL Server rendszergazdai jogosultságok. Konfigurálhatja, és a feladatátvétel használatával az Azure portál, PowerShell vagy a REST API-t Azure-előfizetés engedéllyel vagy SQL Server engedélyekkel Transact-SQL használatával.
> 

Aktív georeplikáció és automatikus feladatátvételt csoportokat használhatja (előzetes verzió) Ha az alkalmazás megfelel a fenti feltételeknek:

* Az üzletmenet szempontjából kritikus fontosságú.
* Olyan szolgáltatói szerződés (SLA) vonatkozik rá, amely nem enged 24 órás vagy azt meghaladó állásidőt.
* Állásidő pénzügyi felelőssége azt eredményezheti.
* Nagy mértékű adatváltozásokra lehet számítani, és egy órányi adat elvesztése nem elfogadható.
* Az aktív georeplikáció többletköltsége alacsonyabb, mint a potenciális pénzbeli kötelezettség és a kapcsolódó üzletvesztés összege.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

## <a name="recover-a-database-after-a-user-or-application-error"></a>Adatbázis helyreállítása felhasználói vagy alkalmazáshiba után

Senki tökéletes! Egy felhasználó véletlenül törölhet néhány adatot, egy fontos adattáblát, vagy akár a teljes adatbázist is. Vagy előfordulhat, hogy egy alkalmazás valamilyen alkalmazáshiba miatt felülírja a helyes adatokat rossz adatokkal.

Ebben a forgatókönyvben ezek közül a helyreállítási lehetőségek közül választhat.

### <a name="perform-a-point-in-time-restore"></a>Időponthoz kötött visszaállítás végrehajtása
Az automatikus biztonsági másolatok használatával helyreállíthatja az adatbázist annak egy megfelelő időpontban készült másolatára, feltéve, hogy az adott időpont a megőrzési időszakba esik. Az adatbázis visszaállítását követően lecserélheti az eredeti adatbázist a visszaállítottra, vagy átmásolhatja a szükséges adatokat a visszaállított adatbázisból az eredetibe. Ha az adatbázis aktív georeplikációt használ, azt javasoljuk a szükséges adatok másolását a visszaállított másolja át a az eredeti adatbázist. Ha az eredeti adatbázis cserélje a visszaállított adatbázis, kell konfigurálnia, majd szinkronizálja újra aktív georeplikáció (amely elég nagy adatbázis némi időbe telhet). Amíg ez a művelet visszaállítja egy adatbázis a legutóbbi elérhető pont idő, és időben történő visszaállításakor a földrajzi másodlagos bármelyik pontra jelenleg nem támogatott.

Adatbázisok az Azure Portalon vagy a PowerShell-lel végzett, adott időpontra való helyreállításával kapcsolatos további információkért lásd: [időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore). Nem végezhet helyreállítást a Transact-SQL használatával.

### <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása
Ha az adatbázis törölve lett, a logikai kiszolgáló azonban nem, a törölt adatbázist visszaállíthatja arra az időpontra, amikor törölve lett. Ez visszaállítja az adatbázis biztonsági másolatát arra a logikai SQL Serverre, amelyről törölve lett. Visszaállíthatja az eredeti nevén, vagy megadhat egy új nevet a visszaállított adatbázis számára.

A törölt adatbázisok az Azure Portalon vagy a PowerShell-lel végzett visszaállításával kapcsolatos további információkért lásd: [törölt adatbázis visszaállítása](sql-database-recovery-using-backups.md#deleted-database-restore). Nem végezhet visszaállítást a Transact-SQL használatával.

> [!IMPORTANT]
> Ha a logikai kiszolgáló törölve lett, a törölt adatbázis nem állítható helyre.


### <a name="restore-backups-from-long-term-retention"></a>Hosszú távú megőrzési alapján állítsa helyre a biztonsági másolatok

Ha az adatvesztés automatikus biztonsági mentés az aktuális megőrzési időszak kívül történt, és az adatbázis hosszú távú megőrzési van konfigurálva, visszaállíthatja a balról jobbra tárolóban egy teljes biztonsági másolatból egy új adatbázist. Ekkor lecserélheti az eredeti adatbázist a visszaállítottra, vagy átmásolhatja a szükséges adatokat a visszaállított adatbázisból az eredetibe. Ha beolvasni egy fő alkalmazás frissítés előtt az adatbázis egy régi verziója van szüksége, kérelem teljesítéséhez ellenőrök vagy a jogi sorrendet, létrehozhat egy adatbázist egy teljes biztonsági mentése az Azure biztonsági mentési tároló.  További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Adatbázis helyreállítása egy másik régióba az Azure regionális adatközpontjának leállása esetén
<!-- Explain this scenario -->

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Leállás esetén az üzletmenet esetleg csak néhány percre, de akár több órára is megszakadhat.

* Az egyik lehetőség, hogy megvárja, amíg az adatközpont leállását követően az adatbázis újra elérhető lesz. Ez az olyan alkalmazások esetében működik, amelyek esetében megengedhető, hogy az adatbázis offline állapotú legyen. Például egy fejlesztési projekt vagy egy ingyenes próbaverzió esetében, amelyeken nem kell folyamatosan dolgoznia. Ha egy adott adatközpont kimaradás, nem tudja mennyi ideig a szolgáltatáskimaradás előfordulhat, hogy utoljára, így ez a beállítás csak akkor működik, ha nincs szüksége az adatbázis egy ideig.
* Egy másik lehetőség egy csak vagy keresztül egy másik adatterület aktív georeplikáció vagy a helyreállítás használata egy adatbázist georedundáns adatbázis biztonsági mentése (georedundáns helyreállítás). Feladatátvevő csupán néhány másodpercet vesz igénybe, amíg az adatbázis helyreállítási biztonsági másolatból óráig tart.

Ha egy művelet elvégzése, mennyi idő alatt helyreállíthatók, és mennyi Ön tudomásával adatvesztés, attól függ, hogyan úgy dönt, hogy használja ezen üzleti folytonosságot biztosító szolgáltatásokat az alkalmazásban. Valóban választhatja az adatbázis biztonsági másolatait és aktív georeplikáció alkalmazás követelményektől függően egyfajta kombinációját alkalmazza. Az alkalmazások tervezési szempontjait önálló adatbázisok vagy az ezeket az üzletmenet-folytonossági funkciókat használó rugalmas készletek esetén lásd: [Alkalmazások tervezése felhőalapú vészhelyreállítással](sql-database-designing-cloud-solutions-for-disaster-recovery.md) és [Rugalmas készletek vészhelyreállítási stratégiái](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

A következő szakaszokban vagy az adatbázis biztonsági másolatait, vagy az aktív georeplikáció használatával állítsa helyre a lépések áttekintését. Beleértve a tervezési követelményeinek, feladás egy vagy több helyreállítási lépéseket és információ a nem tervezett kimaradás szimulálása hajtsa végre a vész-helyreállítási részletezési részletes lépéseiért lásd: [SQL-adatbázis helyreállítása a kimaradás](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Előkészületek leállás esetére
A használt üzletmenet-folytonossági funkciótól függetlenül végre kell hajtania a következőket:

* Azonosítsa és készítse elő a célkiszolgálót, beleértve a kiszolgálószintű tűzfalszabályokat, a bejelentkezéseket és a főadatbázis szintű jogosultságokat.
* Határozza meg, hogyan legyenek átirányítva az ügyfelek és az ügyfélalkalmazások az új kiszolgálóra.
* Dokumentálja az egyéb függőségeket, mint a naplózási beállítások és a riasztások.

Ha nem megfelelő, az alkalmazások helyeznek online, miután a feladatátvétel, vagy egy adatbázis helyreállítási további időt igényel, és valószínűleg is szükség lehet magas terhelés - rossz kombinációjából egyszerre hibaelhárítás.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Feladatok átadása a georeplikált másodlagos adatbázis
Aktív georeplikáció és automatikus feladatátvételt csoportok (az előzetes verzió) a helyreállítási mechanizmusként használ, ha az Automatikus feladatátvétel házirendek konfigurálásához, vagy használjon [kézi feladatátvételre](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-server-in-the-failover-group). Indítják el, ha a feladatátvétel hatására az új elsődleges, és jegyezze fel új tranzakciók és lekérdezések - minimális adatvesztéssel az adatok replikálása még nem kész a másodlagos. Információ a feladatátvételi folyamat tervezéséről: [egy alkalmazás felhő vész-helyreállítási](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Az Adatközpont ismét online elérhető lesz a régi eredményezi automatikusan kapcsolódjon újra az új elsődleges, és a másodlagos adatbázisok válnak. Helyezze át az elsődleges vissza az eredeti régió van szüksége, ha manuálisan is kezdeményezhető a tervezett feladatátvétel (feladat-visszavétel). 
> 

### <a name="perform-a-geo-restore"></a>Hajtsa végre a georedundáns helyreállítás
A helyreállítási mechanizmusként georedundáns tárolás replikáció automatikus biztonsági mentés rendszer használata esetén [kezdeményezni egy adatbázis helyreállítási georedundáns helyreállítás használatával](sql-database-disaster-recovery.md#recover-using-geo-restore). A helyreállítás általában legfeljebb 12 óra alatt végrehajtható – legfeljebb egy órányi adatvesztéssel attól függően, hogy mikor történt a legutóbbi óránkénti különbségi biztonsági mentés és replikáció. A helyreállítás befejezéséig az adatbázis nem tudja rögzíteni a tranzakciókat, illetve nem tud válaszolni a lekérdezésekre. Amíg ez a művelet visszaállítja egy adatbázis a legutóbbi elérhető pont idő, és időben történő visszaállításakor a földrajzi másodlagos bármelyik pontra jelenleg nem támogatott.

> [!NOTE]
> Az Adatközpont ismét online elérhető, mielőtt Váltás az alkalmazást a helyreállított adatbázis, megszakíthatja a helyreállítást.  
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
Egyes esetekben az alkalmazás offline állapotba kell helyezni az alkalmazásfrissítés például tervezett karbantartás miatt. [Alkalmazásfrissítések kezelése](sql-database-manage-application-rolling-upgrade.md) aktív georeplikáció ahhoz, hogy a felhő alkalmazás frissítéskor minimalizálják az állásidőt, és adja meg a helyreállítási elérési úton található, ha valamilyen hiba közbeni használata. 

## <a name="next-steps"></a>További lépések
Az alkalmazások tervezési szempontjait önálló adatbázisok és rugalmas készletek esetén lásd: [Alkalmazások tervezése felhőalapú vészhelyreállítással](sql-database-designing-cloud-solutions-for-disaster-recovery.md) és [Rugalmas készletek vészhelyreállítási stratégiái](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
