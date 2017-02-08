---
title: "Felhőalapú üzletmenet-folytonosság – adatbázis-helyreállítás – SQL Database | Microsoft Docs"
description: "Ismerje meg, hogyan támogatja az Azure SQL Database a felhőalapú üzletmenet-folytonosságot és az adatbázis-helyreállítást, és hogyan segít az üzletmenet szempontjából kritikus fontosságú felhőalapú alkalmazások folyamatos működésének biztosításában."
keywords: "üzletmenet-folytonosság,felhőalapú üzletmenet-folytonosság,adatbázis-vészhelyreállítás,adatbázis-helyreállítás"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 18e5d3f1-bfe5-4089-b6fd-76988ab29822
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2016
ms.author: sashan
translationtype: Human Translation
ms.sourcegitcommit: 187954f3ddafdbc17e341ce41f5b109cb95f8a24
ms.openlocfilehash: d04eb659bcf4df53c5f44c1a4b01763844476267


---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése
Az áttekintés az Azure SQL Database az üzletmenet-folytonosság és a vészhelyreállítás biztosítására szolgáló funkcióit írja le. Lehetőségeket, javaslatokat és oktatóanyagokat tartalmaz az olyan zavaró események utáni helyreállításhoz, amelyek adatvesztéshez vezethetnek, vagy az adatbázis és az alkalmazás elérhetetlenné válását okozhatják. A leírás arra is kitér, mi a teendő, ha egy felhasználói vagy alkalmazáshiba hatással van az adatok integritására, egy Azure-régióban leállás történik, vagy az alkalmazás karbantartásra szorul. 

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Az SQL Database üzletmenet-folytonosságot biztosító funkciói
Az SQL Database számos üzletmenet-folytonossági funkciót nyújt, többek között az automatikus biztonsági mentést és a választható adatbázis-replikációt. Minden egyes funkció más paraméterekkel rendelkezik a becsült helyreállítási idő (ERT) és a legutóbbi tranzakciók során előforduló esetleges adatvesztés tekintetében. Miután megismerkedett ezekkel a lehetőségekkel, szabadon válogathat közülük, és egyes forgatókönyvekben együtt is alkalmazhatja őket. Az üzletmenet-folytonossági terv kidolgozása során meg kell állapítania az alkalmazás a zavaró eseményeket követő teljes helyreállításának maximális elfogadható időkorlátját – ez lesz ugyanis a helyreállítási időre vonatkozó célkitűzés (RTO). Emellett azt is meg kell határoznia, hogy az alkalmazás a zavaró eseményeket követő helyreállítása során legfeljebb mekkora mennyiségű adatfrissítés elvesztése (időintervallum) engedhető meg – ez lesz a helyreállításipont-célkitűzés (RPO). 

Az alábbi táblázat a három leggyakoribb forgatókönyv ERT és RPO értékeit hasonlítja össze.

| Képesség | Alapszintű csomag | Standard csomag | Premium szintű csomag |
| --- | --- | --- | --- |
| Időponthoz kötött visszaállítás biztonsági másolatból |Bármely visszaállítási pont 7 napon belül |Bármely visszaállítási pont 35 napon belül |Bármely visszaállítási pont 35 napon belül |
| Georedundáns visszaállítás georeplikált biztonsági másolatokból |ERT < 12 óra, RPO < 1 óra |ERT < 12 óra, RPO < 1 óra |ERT < 12 óra, RPO < 1 óra |
| Visszaállítás Azure Backup-tárolóból |ERT < 12 óra, RPO < 1 hét |ERT < 12 óra, RPO < 1 hét |ERT < 12 óra, RPO < 1 hét |
| Aktív georeplikáció |ERT < 30 másodperc, RPO < 5 másodperc |ERT < 30 másodperc, RPO < 5 másodperc |ERT < 30 másodperc, RPO < 5 másodperc |

### <a name="use-database-backups-to-recover-a-database"></a>Adatbázis helyreállítása az adatbázis biztonsági másolataiból
Az SQL Database automatikusan végrehajtja az adatbázis teljes biztonsági mentését hetente, az adatbázis különbségi biztonsági mentését óránként, valamint a tranzakciós naplók biztonsági mentését öt percenként, hogy védelmet nyújtson vállalkozása számára az adatvesztéssel szemben. A biztonsági másolatokat a rendszer egy georedundáns tárolóban tárolja 35 napig a Standard és Prémium szolgáltatásszinten, és hét napig az Alapszintű szolgáltatásszinten lévő adatbázisok esetében. A szolgáltatásszintekkel kapcsolatos bővebb információkért lásd: [szolgáltatásszintek](sql-database-service-tiers.md). Ha a szolgáltatásszint megőrzési időszaka nem felel meg az üzleti igényeinek, a megőrzési időszak növelése érdekében [váltson szolgáltatásszintet](sql-database-scale-up.md). Az adatbázis teljes és különbségi biztonsági másolatai emellett replikálva vannak egy [párosított adatközpontba](../best-practices-availability-paired-regions.md) az adatközpont-leállás hatásaival szembeni védelem érdekében. További információkért lásd: [adatbázisok automatikus biztonsági mentése](sql-database-automated-backups.md).

Ha a beépített megőrzési időszak nem elegendő az alkalmazáshoz, az adatbázis(ok) hosszú távú adatmegőrzési házirendjének konfigurálásával meghosszabbíthatja ezt az időszakot. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md). 

Az adatbázis automatikus biztonsági másolataiból helyreállíthatja az adatbázist a különféle zavaró eseményeket követően, akár az adatközponton belül, akár egy másik adatközpontba. Az adatbázis automatikus biztonsági másolatai használatakor a becsült helyreállítási idő több tényezőtől függ, többek között attól, hogy hány adatbázis helyreállítása zajlik egyidejűleg a régióban, az adatbázis méretétől, a tranzakciós napló méretétől, és a hálózati sávszélességtől. A legtöbb esetben a helyreállítási idő kevesebb mint 12 óra. Ha egy másik adatrégióba végzi a helyreállítást, az esetleges adatvesztés legfeljebb 1 óra lehet az adatbázis óránkénti különbségi biztonsági másolatai georedundáns tárolásának köszönhetően. 

> [!IMPORTANT]
> Az automatikus biztonsági másolatokból való helyreállításhoz az SQL Server közreműködői szerepköre tagjának vagy az előfizetés tulajdonosának kell lennie – lásd: [Beépített RBAC-szerepkörök](../active-directory/role-based-access-built-in-roles.md). A helyreállítást az Azure Portal, a PowerShell vagy a REST API használatával végezheti. A Transact-SQL nem használható.
> 
> 

Akkor használja az automatikus biztonsági másolatokat üzletmenet-folytonossági és helyreállítási mechanizmusként, ha az alkalmazásra igazak a következők:

* Nem tekinthető az üzletmenet szempontjából kritikus fontosságúnak.
* Nincsen érvényben SLA, így a 24 órás vagy azt meghaladó állásidők nem eredményeznek pénzügyi kötelezettséget.
* Kis mértékű adatmódosításra lehet számítani (alacsony óránkénti tranzakciószám), és egy órányi adatmódosítás elvesztése elfogadható. 
* Költségérzékeny. 

Ha gyorsabb helyreállítás szükséges, használjon [aktív georeplikációt](sql-database-geo-replication-overview.md) (lásd az alábbiakban). Ha 35 napot meghaladó időszakból lehet szükség adatok helyreállítására, vegye fontolóra az adatbázis rendszeres archiválását egy BACPAC-fájlba (ez egy, az adatbázissémát és a társított adatokat tartalmazó tömörített fájl), amelyet egy Azure Blob Storage-ban vagy más tetszőleges helyen tárol. A tranzakciós szempontból konzisztens adatbázisok létrehozásával kapcsolatos további információkért lásd: [adatbázis-másolat létrehozása](sql-database-copy.md) és [az adatbázis-másolat exportálása](sql-database-export.md). 

### <a name="use-active-geo-replication-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>A helyreállítási idő csökkentése és a helyreállítással kapcsolatos adatvesztés korlátozása aktív georeplikáció használatával
Amellett, hogy az adatbázis biztonsági másolatait használja az adatbázis helyreállításához az üzletmenet megszakadása esetén, az [aktív georeplikáció](sql-database-geo-replication-overview.md) használatával konfigurálhatja úgy az adatbázist, hogy az rendelkezzen legfeljebb négy olvasható másodlagos adatbázissal tetszőleges régiókban. Ezeket a másodlagos adatbázisokat a rendszer egy aszinkrón replikációs mechanizmus használatával folyamatosan szinkronizálja az elsődleges adatbázissal. Ez a funkció az üzletmenet megszakadása ellen véd adatközpont-leállás esetén vagy az alkalmazásfrissítések során. Az aktív georeplikációval növelhető a lekérdezések teljesítménye a földrajzilag elosztott felhasználók csak olvasható lekérdezései esetén.

Ha az elsődleges adatbázis váratlanul offline állapotba kerül, vagy karbantartási tevékenységek miatt kell offline állapotba helyeznie, gyorsan előléptethet egy másodlagos adatbázist elsődlegessé (ezt feladatátvételnek is nevezik), és konfigurálhatja az alkalmazásokat az újonnan előléptetett elsődleges adatbázishoz való csatlakozásra. Tervezett feladatátvétel esetén nincs adatvesztés. Nem tervezett feladatátvétel esetén elképzelhető kismértékű adatvesztés a legutóbbi tranzakciók esetében az aszinkrón replikáció természete miatt. A feladatátvételt később követheti feladat-visszavétel – akár tervezetten, akár amikor az adatközpont újra elérhetővé válik. A felhasználók minden esetben rövid állásidőt tapasztalnak, és újra kell csatlakozniuk. 

> [!IMPORTANT]
> Az aktív georeplikáció használatához az előfizetés tulajdonosának kell lennie, vagy rendszergazdai jogosultsággal kell rendelkeznie az SQL Serveren. A konfigurálást és a feladatátvételt az Azure Portal, a PowerShell vagy a REST API használatával az előfizetésre vonatkozó jogosultságokkal, vagy a Transact-SQL használatával az SQL Serveren belüli jogosultságokkal végezheti.
> 
> 

Akkor alkalmazzon aktív georeplikációt, ha az alkalmazás megfelel a következő feltételeknek:

* Az üzletmenet szempontjából kritikus fontosságú.
* Olyan szolgáltatói szerződés (SLA) vonatkozik rá, amely nem enged 24 órás vagy azt meghaladó állásidőt.
* Az állásidő pénzügyi kötelezettséget eredményez.
* Nagy mértékű adatváltozásokra lehet számítani, és egy órányi adat elvesztése nem elfogadható.
* Az aktív georeplikáció többletköltsége alacsonyabb, mint a potenciális pénzbeli kötelezettség és a kapcsolódó üzletvesztés összege.

>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

## <a name="recover-a-database-after-a-user-or-application-error"></a>Adatbázis helyreállítása felhasználói vagy alkalmazáshiba után
*Senki sem tökéletes. Egy felhasználó véletlenül törölhet néhány adatot, egy fontos adattáblát, vagy akár a teljes adatbázist is. Vagy előfordulhat, hogy egy alkalmazás valamilyen alkalmazáshiba miatt felülírja a helyes adatokat rossz adatokkal. 

Ebben a forgatókönyvben ezek közül a helyreállítási lehetőségek közül választhat.

### <a name="perform-a-point-in-time-restore"></a>Időponthoz kötött visszaállítás végrehajtása
Az automatikus biztonsági másolatok használatával helyreállíthatja az adatbázist annak egy megfelelő időpontban készült másolatára, feltéve, hogy az adott időpont a megőrzési időszakba esik. Az adatbázis visszaállítását követően lecserélheti az eredeti adatbázist a visszaállítottra, vagy átmásolhatja a szükséges adatokat a visszaállított adatbázisból az eredetibe. Ha az adatbázis aktív georeplikációt alkalmaz, javasoljuk a szükséges adatok átmásolását a visszaállított másolatból az eredeti adatbázisba. Ha lecseréli az eredeti adatbázist a visszaállított adatbázisra, újra kell konfigurálnia és szinkronizálnia az aktív georeplikációt (ez nagy méretű adatbázisok esetében hosszabb időt is igénybe vehet). 

Adatbázisok az Azure Portalon vagy a PowerShell-lel végzett, adott időpontra való helyreállításával kapcsolatos további információkért lásd: [időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore). Nem végezhet helyreállítást a Transact-SQL használatával.

### <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása
Ha az adatbázis törölve lett, a logikai kiszolgáló azonban nem, a törölt adatbázist visszaállíthatja arra az időpontra, amikor törölve lett. Ez visszaállítja az adatbázis biztonsági másolatát arra a logikai SQL Serverre, amelyről törölve lett. Visszaállíthatja az eredeti nevén, vagy megadhat egy új nevet a visszaállított adatbázis számára.

A törölt adatbázisok az Azure Portalon vagy a PowerShell-lel végzett visszaállításával kapcsolatos további információkért lásd: [törölt adatbázis visszaállítása](sql-database-recovery-using-backups.md#deleted-database-restore). Nem végezhet visszaállítást a Transact-SQL használatával.

> [!IMPORTANT]
> Ha a logikai kiszolgáló törölve lett, a törölt adatbázis nem állítható helyre. 
> 
> 

### <a name="restore-from-azure-backup-vault"></a>Visszaállítás Azure Backup-tárolóból
Ha az adatvesztés az automatikus biztonsági másolatok aktuális megőrzési időszakán kívül történt, és az adatbázis konfigurálva van hosszú távú adatmegőrzésre, az adatokat az Azure Backup-tárolóban lévő heti biztonsági másolatok egyikéből állíthatja vissza egy új adatbázisba. Ekkor lecserélheti az eredeti adatbázist a visszaállítottra, vagy átmásolhatja a szükséges adatokat a visszaállított adatbázisból az eredetibe. Ha az adatbázis egy nagyobb alkalmazásfrissítés előtti, régebbi verzióját kell lekérnie, esetleg auditorok vagy egy jogi entitás kérésének kell megfelelnie, létrehozhat egy új adatbázist az Azure Backup-tárolóba mentett valamelyik teljes adatbázis-másolatból.  További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Adatbázis helyreállítása egy másik régióba az Azure regionális adatközpontjának leállása esetén
<!-- Explain this scenario -->

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Leállás esetén az üzletmenet esetleg csak néhány percre, de akár több órára is megszakadhat. 

* Az egyik lehetőség, hogy megvárja, amíg az adatközpont leállását követően az adatbázis újra elérhető lesz. Ez az olyan alkalmazások esetében működik, amelyek esetében megengedhető, hogy az adatbázis offline állapotú legyen. Például egy fejlesztési projekt vagy egy ingyenes próbaverzió esetében, amelyeken nem kell folyamatosan dolgoznia. Ha egy adatközpont leáll, nem tudhatja, hogy a leállás meddig tart, ezért ez a lehetőség csak akkor működik, ha az adatbázisra egy ideig nem lesz szüksége.
* Egy másik lehetőség, hogy végrehajt egy feladatátvételt egy másik adatrégióba, ha az aktív georeplikációt használja, vagy georedundáns adatbázis-másolatok használatával hajt végre helyreállítást (georedundáns visszaállítást). A feladatátvétel mindössze néhány másodpercet vesz igénybe, a biztonsági másolatokból való helyreállítás azonban órákig is tarthat.

Hogy mikor teszi meg a megfelelő lépéseket, mennyi időt vesz igénybe a helyreállítás, és mekkora adatvesztés következik be adatközpont-leállás esetén, attól függ, hogyan alkalmazza a fentiekben leírt üzletmenet-folytonossági funkciókat az alkalmazásban. Akár választhatja azt is, hogy az adatbázis biztonsági másolatainak és az aktív georeplikációnak valamilyen kombinációját alkalmazza az alkalmazás követelményeinek megfelelően. Az alkalmazások tervezési szempontjait önálló adatbázisok vagy az ezeket az üzletmenet-folytonossági funkciókat használó rugalmas készletek esetén lásd: [Alkalmazások tervezése felhőalapú vészhelyreállítással](sql-database-designing-cloud-solutions-for-disaster-recovery.md) és [Rugalmas készletek vészhelyreállítási stratégiái](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Az alábbi szakaszok az adatbázis-másolatok vagy az aktív georeplikáció használatával végzett helyreállítás lépéseinek áttekintését tartalmazzák. A részletes lépésekért (beleértve a tervezési követelményeket, a helyreállítás utáni lépéseket, valamint a leállások szimulálását a vészhelyreállítási gyakorlatokhoz) lásd: [SQL Database helyreállítása leállás esetén](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Előkészületek leállás esetére
A használt üzletmenet-folytonossági funkciótól függetlenül végre kell hajtania a következőket:

* Azonosítsa és készítse elő a célkiszolgálót, beleértve a kiszolgálószintű tűzfalszabályokat, a bejelentkezéseket és a főadatbázis szintű jogosultságokat.
* Határozza meg, hogyan legyenek átirányítva az ügyfelek és az ügyfélalkalmazások az új kiszolgálóra.
* Dokumentálja az egyéb függőségeket, mint a naplózási beállítások és a riasztások. 

Ha a tervezést és az előkészítést nem megfelelően végzi el, az alkalmazások a feladatátvételt vagy helyreállítást követő elérhetővé tétele több időt vesz majd igénybe, és valószínűleg hibaelhérítást is igényel egy amúgy is feszült időszakban – ami nagyon rossz kombináció.

### <a name="failover-to-a-geo-replicated-secondary-database"></a>Feladatátvétel georeplikált másodlagos adatbázisra
Ha helyreállítási mechanizmusként aktív georeplikációt alkalmaz, [kényszerítse ki a feladatátvételt egy georeplikált másodlagos adatbázisra](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database). A rendszer néhány másodperc alatt előlépteti az adott másodlagos adatbázist új elsődleges adatbázissá, és készen áll rögzíteni az új tranzakciókat és válaszolni a lekérdezésekre – közben pedig mindössze néhány másodpercnyi adat veszik el a még nem replikált adatokból. A feladatátvételi folyamat automatizálásával kapcsolatos további információkért lásd: [Alkalmazások tervezése felhőalapú vészhelyreállítással](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Amint az adatközpont ismét elérhetővé válik, végrehajthat egy feladat-visszavételt az eredeti elsődleges adatbázisra (nem kötelező).
> 
> 

### <a name="perform-a-geo-restore"></a>Georedundáns visszaállítás végrehajtása
Ha automatikus biztonsági másolatokat használ georedundáns tárreplikációval helyreállítási mechanizmusként, [a georedundáns visszaállítással kezdeményezheti egy adatbázis helyreállítását](sql-database-disaster-recovery.md#recover-using-geo-restore). A helyreállítás általában legfeljebb 12 óra alatt végrehajtható – legfeljebb egy órányi adatvesztéssel attól függően, hogy mikor történt a legutóbbi óránkénti különbségi biztonsági mentés és replikáció. A helyreállítás befejezéséig az adatbázis nem tudja rögzíteni a tranzakciókat, illetve nem tud válaszolni a lekérdezésekre. 

> [!NOTE]
> Ha az adatközpont azelőtt válik újra elérhetővé, mielőtt átkapcsolná az alkalmazást a helyreállított adatbázisra, egyszerűen megszakíthatja a helyreállítást.  
> 
> 

### <a name="perform-post-failover--recovery-tasks"></a>Feladatátvétel/helyreállítás utáni feladatok végrehajtása
A helyreállítási mechanizmusok végrehajtása után a következő további feladatokat kell végrehajtania a felhasználók és az alkalmazások újbóli üzembe helyezéséhez:

* Irányítsa át az ügyfeleket és az ügyfélalkalmazásokat az új kiszolgálóra és a helyreállított adatbázisra.
* Biztosítsa, hogy megfelelő kiszolgálószintű tűzfalszabályok vannak érvényben a felhasználók csatlakozásához (vagy használjon [adatbázisszintű tűzfalakat](sql-database-firewall-configure.md#creating-database-level-firewall-rules)).
* Biztosítsa a megfelelő bejelentkezési adatok és főadatbázis-szintű jogosultságok rendelkezésre állását (vagy használjon [tartalmazott felhasználókat](https://msdn.microsoft.com/library/ff929188.aspx))
* Konfigurálja a naplózást, ha szükséges.
* Konfigurálja a riasztásokat, ha szükséges.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Alkalmazások frissítése minimális állásidővel
A tervezett karbantartások, például alkalmazásfrissítések miatt időnként offline állapotba kell helyezni az alkalmazásokat. Az [alkalmazásfrissítések kezeléséről](sql-database-manage-application-rolling-upgrade.md) szóló cikk ismerteti, hogyan használható az aktív georeplikáció a felhőalapú alkalmazások működés közbeni frissítésére a frissítés közbeni állásidő minimálisra csökkentése, valamint egy helyreállítási útvonal biztosítása érdekében arra az esetre, ha valami hiba történne. Ez a cikk két eltérő módszert mutat be a frissítési folyamat lebonyolítására, továbbá ismerteti ezek előnyeit és hátrányait.

## <a name="next-steps"></a>Következő lépések
Az alkalmazások tervezési szempontjait önálló adatbázisok és rugalmas készletek esetén lásd: [Alkalmazások tervezése felhőalapú vészhelyreállítással](sql-database-designing-cloud-solutions-for-disaster-recovery.md) és [Rugalmas készletek vészhelyreállítási stratégiái](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).




<!--HONumber=Jan17_HO2-->


