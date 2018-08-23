---
title: Feladatátvételi csoportok és az aktív georeplikáció – Azure SQL Database |} A Microsoft Docs
description: Aktív georeplikációs automatikus feladatátvételi csoportok használata, és engedélyezze a autoomatic feladatátvételi meghibásodás esetére.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 08179ae21465a57161cc6f18c12a3d9a21449359
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2018
ms.locfileid: "42060981"
---
# <a name="overview-active-geo-replication-and-auto-failover-groups"></a>Áttekintés: Aktív georeplikációs és automatikus feladatátvételi csoportok

Aktív georeplikáció az Azure SQL Database szolgáltatás, amely lehetővé teszi az ugyanazon vagy másik adatközpontban (régió) olvasható replikát az adatbázis létrehozását.

![Georeplikáció](./media/sql-database-geo-replication-failover-portal/geo-replication.png )

Aktív georeplikáció üzleti folytonossági megoldás, amely lehetővé teszi az alkalmazásnak, hogy gyors vészhelyreállítás esetén a méretezési csoport adatközponti üzemkimaradások tervezték. Ha engedélyezve van a georeplikáció, az alkalmazás kezdeményezhet feladatátvételt egy másodlagos adatbázist egy másik Azure-régióban. Legfeljebb négy meghatározhatják az azonos vagy eltérő régiókban támogatottak, és a másodlagos példány hozható létre a csak olvasási hozzáféréssel lekérdezésekhez is használható. A feladatátvétel kell kezdeményeznie manuálisan az alkalmazás vagy a felhasználó. A feladatátvételt követően az új elsődleges rendelkezik egy másik kapcsolat végpontját. 

> [!NOTE]
> Aktív georeplikáció az összes adatbázis összes szolgáltatási szinten az összes régióban érhető el.
> Aktív georeplikáció által a felügyelt példány nem érhető el.
>  

Automatikus feladatátvételi csoportok az aktív georeplikáció a kiterjesztése. Ez a célja, hogy egy alkalmazás által kezdeményezett feladatátvételi több georeplikált adatbázis sumultaneously feladatátvételének kezelésében, vagy delegálása feladatátvétel hajtható végre az SQL Database szolgáltatás, a felhasználó által megadott feltételek szerint. Az utóbbi lehetővé teszi az automatikus helyreállításán egy másodlagos régióban több kapcsolódó adatbázisok egy Katasztrofális hiba vagy egyéb nem tervezett esemény, amely az SQL Database szolgáltatás rendelkezésre állása az elsődleges régióban teljes vagy részleges elvesztését eredményezi. Emellett használhatja az olvasható másodlagos adatbázis csak olvasható lekérdezési számítási feladatok kiszervezéséhez. Automatikus feladatátvételi csoportok több adatbázis között, mivel ezeknek az adatbázisoknak az elsődleges kiszolgálón kell konfigurálni. Az adatbázisok a feladatátvételi csoport elsődleges és másodlagos kiszolgálók ugyanabban az előfizetésben kell lennie. Automatikus feladatátvételi csoportok támogatja az összes adatbázis replikálása a csoportban csak egy másodlagos kiszolgáló egy másik régióban.

> [!NOTE]
> Aktív georeplikációt használja, ha több másodlagos példány hozható létre szükség.
>  

Ha aktív georeplikációt használ, és bármely okból az elsődleges adatbázis meghibásodik, vagy egyszerűen csak van szüksége, offline állapotba kell helyezni, a másodlagos adatbázisok valamelyik is kezdeményezhető feladatátvétel. Feladatátvétel a másodlagos adatbázisok egyik aktiválásakor összes másodlagos a rendszer automatikusan összekapcsolja az új elsődleges. Ha automatikus feladatátvételi csoportok adatbázis-helyreállítás és bármilyen kimaradásról kezelésére használja, amely hatással van egy vagy több, az adatbázisok az automatikus feladatátvételi csoport eredményez. Konfigurálhatja az automatikus feladatátvételi szabályzat az alkalmazás igényeinek leginkább megfelelő, vagy kikapcsolhatja az újat, és manuális-aktiválást használjon. Ezenkívül automatikus feladatátvételi csoportok adja meg az olvasási és írási, és a továbbra is csak olvasási figyelői végpontok feladatátvételek során változatlan marad. Akár manuális vagy automatikus feladatátvételi aktiválási, feladatátvételi minden másodlagos adatbázisát a csoport vált, amennyiben az elsődleges. Az adatbázis-feladatátvétel befejezése után a DNS-rekord automatikusan frissül a végpontok átirányítása az új régióban.

Replikáció és a egy önálló adatbázis feladatátvételét, vagy egy kiszolgálón vagy az aktív georeplikáció használatával rugalmas készlet adatbázisai is kezelheti. Megteheti, hogy az 

- Az [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell: Önálló adatbázis](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: A rugalmas készlet](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [PowerShell: A feladatátvételi csoport](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [A Transact-SQL: Önálló adatbázis és rugalmas készlet](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API-val: Önálló adatbázis](/rest/api/sql/replicationlinks/failover)
- [REST API-val: Feladatátvételi csoport](/rest/api/sql/failovergroups/failover). 
 
A feladatátvételt követően ellenőrizze a hitelesítési követelmények, a kiszolgáló és az adatbázis az új elsődleges. További információkért lásd: [vész-helyreállítása után az SQL Database biztonsági](sql-database-geo-replication-security-config.md). Ez egyaránt vonatkozik az aktív georeplikáció és automatikus feladatátvételi csoportok.

Aktív georeplikáció kihasználja a [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) technológia az SQL Server replikálása aszinkron módon véglegesített tranzakciók száma, az elsődleges adatbázison egy másodlagos adatbázis-pillanatkép-elkülönítés használatával. Automatikus feladatátvételi csoportok a csoport szemantikát felett aktív georeplikációt biztosít, de az azonos aszinkrón replikációs mechanizmus használatos. Közben lekérdezhet, a másodlagos adatbázis némileg lehet az elsődleges adatbázis mögött, a másodlagos adatok garantáltan soha többé nem kell a részleges tranzakciók. Régiók közötti redundancia lehetővé teszi az alkalmazások gyorsan helyreállíthatók a teljes adatközpont vagy az egy kínai adatközpont természeti katasztrófa, katasztrofális az emberi hibák vagy rosszindulatú jogszabályok által okozott részei végleges adatvesztést. Az adott helyreállítási Időkorlát adatokat tekinthet meg [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).

Az alábbi ábra példát mutat be egy aktív georeplikáció által beállított, az elsődleges régióban az USA északi középső Régiójában és a másodlagos régióban az USA déli középső Régiója.

![georeplikációs kapcsolatban](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Mivel a másodlagos adatbázisok olvasható, azokat a csak olvasható munkaterhelésekkel, például jelentéskészítéssel feladatok kiszervezése használható. Ha aktív georeplikációt használ, lehetséges, a másodlagos adatbázis létrehozásához az ugyanabban a régióban, az elsődleges, de nem növeli a katasztrofális hibák az alkalmazás rugalmasságát. Ha automatikus feladatátvételi csoportokat használ, a másodlagos adatbázis mindig létrejön egy másik régióban.

Mellett vész helyreállítási aktív georeplikáció a következő esetekben használhatók:

* **Adatbázis-migrálási**: aktív georeplikáció használatával adatbázis áttelepítése az egyik kiszolgálóról a másikra online, minimális állásidővel.
* **Alkalmazásfrissítések**: alkalmazás frissítése során sikertelen vissza másolatként egy extra másodlagos is létrehozhat.

Elérése érdekében a valódi üzleti folytonosság, adatbázis-redundancia adatközpontok között csak a megoldás része. Egy alkalmazás (szolgáltatás) – teljes körű helyreállítása, végzetes hiba van szükség a szolgáltatást alkotó összetevők és minden függő szolgáltatás helyreállítás után. Ezen összetevők közé tartoznak az ügyfélszoftver (például egy egyéni JavaScript böngésző), a webes előtérrendszerek, a storage és a DNS. Rendkívül fontos, hogy az összes összetevő ellenálljanak a azonos hibákat, és a helyreállítási idő célértéke (RTO) az alkalmazás belül elérhetővé válnak. Ezért kell azonosítsa az összes függő szolgáltatást, és megismerheti a garanciák és képességeket biztosítanak. Ezt követően végre kell hajtania megfelelő lépéseket annak érdekében, hogy a szolgáltatások, amelytől a feladatátvétel során a szolgáltatás függvények. Vész-helyreállítási megoldások tervezésével kapcsolatos további információkért lásd: [felhőalapú megoldások tervezéséhez a vész-helyreállítási használatával aktív georeplikáció](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Aktív georeplikáció képességek
Az aktív georeplikációs szolgáltatás az alábbi alapvető képességeket biztosítja:
* **Az automatikus aszinkron replikáció**: ad hozzá egy meglévő adatbázist hozhat létre egy másodlagos adatbázis csak. Minden Azure SQL Database Server a másodlagos hozható létre. Létrehozása után megjelenik a másodlagos adatbázis az adatokat másolja az elsődleges adatbázisból. Ez a folyamat van áttöltésként is ismert. Miután létrehozott és áttöltésekor másodlagos adatbázis, az elsődleges adatbázis frissítések aszinkron módon replikált a másodlagos adatbázis automatikusan. Aszinkron replikáció azt jelenti, hogy tranzakció véglegesítve az elsődleges adatbázison, mielőtt azok replikálódnak a másodlagos adatbázis. 
* **Olvasható másodlagos adatbázis**: az alkalmazás hozzáférhet egy másodlagos adatbázis csak olvasható műveletekhez az elsődleges adatbázis eléréséhez használt azonos vagy eltérő rendszerbiztonsági használatával. A másodlagos adatbázisok annak érdekében, hogy a frissítések az elsődleges (napló visszajátszását) replikációs nem késnek-e a másodlagos végrehajtott lekérdezések snapshot elkülönítési módban működik.

> [!NOTE]
> A napló visszajátszását a másodlagos adatbázis késik, ha az elsődleges sémafrissítések. Az utóbbi séma zárolva van a másodlagos adatbázis szükséges. 
> 

* **Több olvasható másodlagos példánnyal**: két vagy több másodlagos adatbázis növelni a redundanciát és az elsődleges adatbázis és az alkalmazás adatvédelmi szintet. Ha több másodlagos adatbázis létezik, az alkalmazás akkor is védett akkor is, ha egy másodlagos adatbázis nem sikerül. Ha csak egy másodlagos adatbázist, és ez nem sikerül, az alkalmazás van kitéve nagyobb eséllyel addig, amíg létrejön egy új másodlagos adatbázis.

> [!NOTE]
> Ha egy globálisan elosztott alkalmazás létrehozása, és meg kell adnia a csak olvasási hozzáféréssel az adatok több mint négy régióban aktív georeplikációt használ, másodlagos, egy másodlagos (láncolási néven ismert folyamat) hozhat létre. Ily módon érheti el adatbázis-replikáció gyakorlatilag korlátlan méretezhetőségét. Emellett láncolási csökkenti a replikáció az elsődleges adatbázisból. Kompromisszumot kötni a rendszer a megnövekedett replikációs késés a levél – a legtöbb másodlagos adatbázisokon. 
>

* **A rugalmas készlet adatbázisok támogatása**: minden egyes replikának külön-külön részt venni a rugalmas készlet, vagy egyáltalán nem tudja bármilyen rugalmas készletben. A készlet minden egyes replikának választás a különálló, és nem függ semmilyen más replika konfigurációját (akár a elsődleges vagy másodlagos). Minden rugalmas készletet szerepel egy adott régión belül, így az azonos topológia több replika soha nem oszthatnak meg a rugalmas készlet.
* **A másodlagos adatbázis teljesítményszintjének konfigurálható**: elsődleges és másodlagos adatbázisok ugyanazon a szolgáltatásszinten van szüksége. A másodlagos adatbázis jön létre az eredeti teljesítményszint (dtu-k vagy virtuális magok) az elsődleges is erősen ajánlott. Egy alacsonyabb teljesítményszintre másodlagost fennáll a kockázata, egy nagyobb replikációs késés, a másodlagos lehetséges elérhetetlensége van, és ezért fennáll a kockázata, jelentős adatvesztés a feladatátvétel után. A közzétett RPO eredményeként = 5 mp nem garantálható. A többi kockázati, hogy a feladatátvételt követően az alkalmazás teljesítménye csökkenhet az új elsődleges elegendő számítási kapacitás miatt egy magasabb teljesítményi szinthez frissítéséig. A frissítés idején az adatbázis méretétől függ. Ha úgy dönt, hogy a másodlagos alacsonyabb teljesítményszintre létrehozása, a napló i/o százalékos diagramra az Azure portal nyújt egy jó módszer a szükséges ahhoz, hogy a replikáció terhelés fenntartása másodlagos minimális teljesítményszintjét. Például, ha az elsődleges adatbázis P6 (1000 DTU), és i/o-százalék naplót a másodlagos kell lennie legalább 50 %-a P4 (500 DTU). Is lekérhet használatával i/o-naplóadatok [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vagy [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) adatbázis-nézeteket.  Az SQL Database teljesítményszintek további információkért lásd: [Mik az SQL Database szolgáltatási szinteken](sql-database-service-tiers.md). 
* **Felhasználó általi feladatátvétel és feladat-visszavétel**: egy másodlagos adatbázis explicit módon lehessen kapcsolni az elsődleges szerepre bármikor az alkalmazás vagy a felhasználó. Egy valódi kimaradás során az "tervezett" kell beállítással, amely azonnal elősegíti a másodlagos kell az elsődleges. A sikertelen elsődleges állítja helyre, és megint elérhetővé válik, amikor a rendszer automatikusan jelöli meg a helyreállított elsődleges, másodlagos, és naprakészen tartása az új elsődleges állapotba. Replikáció aszinkron jellege miatt kisebb mennyiségű adatot elvesznek, nem tervezett feladatátvételek során ha elsődleges meghibásodik, a legutóbbi módosítások a másodlagos replikálás előtt. Több másodlagos helyekkel elsődleges átadja a feladatokat, amikor a rendszer automatikusan újrakonfigurálja a replikációs kapcsolatok, és hivatkozásokat tartalmaz a fennmaradó másodlagos példány hozható létre az újonnan előléptetett elsődleges felhasználói beavatkozás nélkül. A szolgáltatáskimaradás, amely miatt a feladatátvétel után, az alkalmazás az elsődleges régióba visszaadandó kívánatos lehet. Ehhez a feladatátvétel parancs a "tervezett" lehetőséggel lehet meghívni. 
* **Hitelesítő adatok és -tűzfalszabályok szinkronban tartja**: javasoljuk a [adatbázis-tűzfalszabályok](sql-database-firewall-configure.md) adatbázisok georeplikált, így ezek a szabályok replikálhatók az adatbázissal, minden másodlagos adatbázis rendelkezésre áll a azonos tűzfalszabályok elsődlegesként. Ezzel a módszerrel nem kell az ügyfelek manuális konfigurálása és karbantartása, mind az elsődleges és másodlagos adatbázisok üzemeltetési kiszolgáló tűzfalszabályait. Ehhez hasonlóan használatával [tartalmazott adatbázis felhasználóit](sql-database-manage-logins.md) adatok hozzáférés biztosítja, mind az elsődleges, mind a másodlagos adatbázisok mindig azonos felhasználói hitelesítő adatokat, így a feladatátvétel során a nem a felhasználónevek és jelszavak eltérések miatt szolgáltatások. Igény szerinti hozzáadásával [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), ügyfelek kezelhetik a felhasználói hozzáférést az elsődleges és másodlagos adatbázisok, és szükségtelenné teszik a kezeléséhez hitelesítő adatait érvényesítette adatbázisokban.

## <a name="auto-failover-group-capabilities"></a>Automatikus feladatátvételi csoport képességek

Automatikus feladatátvételi csoportok funkció biztosítja, hogy az aktív georeplikáció egy hatékony absztrakciós szintű replikációs csoport és az Automatikus feladatátvétel támogatása. Ezenkívül eltávolítja az SQL-kapcsolati karakterlánc módosítása a feladatátvételt követően azáltal, hogy a további figyelő végpontok szükségességét. 

* **Feladatátvételi csoport**: egy vagy több, feladatátvételi csoportok hozható létre több különböző régióban (az elsődleges és másodlagos kiszolgálók) két kiszolgáló között. Minden csoport egy vagy több adatbázis helyreállított egységként abban az esetben az összes vagy néhány elsődleges adatbázissal az elsődleges régióban leállás miatt elérhetetlenné lehetnek.  
* **Elsődleges kiszolgáló**: egy kiszolgálót, amelyen az elsődleges adatbázisok a feladatátvételi csoportban.
* **Másodlagos kiszolgáló**: egy kiszolgálót, amelyen a másodlagos adatbázisok a feladatátvételi csoportban. A másodlagos kiszolgáló és az elsődleges kiszolgáló ugyanabban a régióban nem lehet.
* **Adatbázisok hozzáadása a feladatátvételi csoport**: ugyanabban a feladatátvételi csoportban is elhelyezhető egy kiszolgálón vagy a rugalmas készletben lévő adatbázisok. Ha egy önálló adatbázis hozzáadása a csoporthoz, automatikusan létrehoz egy másodlagos adatbázist, kiadást és teljesítményszintet. Ha az elsődleges adatbázis egy rugalmas készletben, a másodlagos automatikusan létrejön a rugalmas készlet ugyanazzal a névvel. Hozzáad egy adatbázist, amely már rendelkezik egy másodlagos adatbázist a másodlagos kiszolgálóra, ha, georeplikáció örökli a csoport.

> [!NOTE]
> Egy adatbázis, amely már rendelkezik egy másodlagos adatbázis egy kiszolgálót, amely nem része a feladatátvételi csoporthoz való hozzáadásakor, egy új másodlagos jön létre a másodlagos kiszolgálón. 
>

* **Feladatátvételi csoport olvasási és írási figyelőjének**: egy DNS CNAME-rekordot formázott  **&lt;feladatátvételi csoportnév&gt;. database.windows.net** , amely a jelenlegi elsődleges kiszolgáló URL-címre mutat. Lehetővé teszi az olvasási és írási SQL alkalmazásokat az elsődleges változásakor a feladatátvételt követően az elsődleges adatbázis transzparens módon csatlakoznak. 
* **Feladatátvételi csoport csak olvasási figyelői**: egy DNS CNAME-rekordot formázott  **&lt;feladatátvételi csoportnév&gt;. secondary.database.windows.net** , amely a másodlagos kiszolgáló URL-címre mutat. A csak olvasható SQL alkalmazások számára transzparens módon a másodlagos adatbázishoz a megadott terheléselosztási szabályok használatával lehetővé teszi. 
* **Az automatikus feladatátvételi szabályzat**: alapértelmezés szerint a feladatátvételi csoporthoz egy automatikus feladatátvételi szabályzat van konfigurálva. A rendszer elindítja a feladatátvétel után a hibát észleli, és a türelmi időszak lejárt. A rendszer ellenőriznie kell, hogy a szolgáltatáskimaradás elhárítása után már nem mérsékelhetők a méretezési csoport, az ütközés miatt az SQL Database szolgáltatás beépített magas rendelkezésre állás infrastruktúrája. Ha szeretné szabályozni a feladatátvételi munkafolyamat az alkalmazásból, kikapcsolhatja az automatikus feladatátvételt. 
* **Csak olvasási feladatátvételi szabályzat**: alapértelmezés szerint a feladatátvétel, a csak olvasható figyelő le van tiltva. Ez biztosítja, hogy az elsődleges teljesítményének nem változik, ha a másodlagos offline állapotban. Azonban azt is jelenti a csak olvasható munkamenetek nem lesz képes csatlakozni, amíg a másodlagos helyre van. Ha állásidő zavarják meg a csak olvasható munkamenetek és az OK gombra a átmenetileg egyaránt csak olvasható és írható-olvasható forgalomhoz a teljesítménycsökkenés az elsődleges rovására használja az elsődleges, a csak olvasási figyelői a feladatátvételi engedélyezheti. Ebben az esetben a csak olvasható forgalom automatikusan irányítja az elsődleges kiszolgáló, ha a másodlagos kiszolgáló nem érhető el.  
* **Manuális feladatátvétel**: feladatátvétel manuálisan is kezdeményezhető, tetszőleges időpontban, függetlenül attól, automatikus feladatátvételi konfigurációjának. Az automatikus feladatátvételi szabályzat nem történik meg, ha manuális feladatátvételt a feladatátvételi csoport az adatbázisok helyreállításához szükséges. (A teljes adatszinkronizálás) kényszerített vagy rövid feladatátvételt kezdeményezhet. Az utóbbi használható helyezze át az aktív kiszolgáló az elsődleges régióba. Feladatátvétel befejezése után a DNS-rekordok automatikusan frissülnek annak biztosítása érdekében a megfelelő kiszolgálóhoz való kapcsolódás.
* **Türelmi időszak adatvesztéssel**: az elsődleges és másodlagos adatbázis szinkronizálva van, az aszinkron replikáció használatával, mert a feladatátvétel az adatvesztést eredményezhet. Az automatikus feladatátvételi szabályzat adatvesztés funkciót biztosít az alkalmazás megfelelően testre szabhatja. Konfigurálásával **Leváltó**, szabályozhatja, hogy a rendszer mennyi ideig vár, amely nagy eséllyel eredmény adatvesztés a feladatátvétel kezdeményezése előtt. 

> [!NOTE]
> Amikor rendszer azt észleli, hogy még mindig online állapotban-e a csoport az adatbázisok (például a szolgáltatáskimaradás elhárítása után csak hatással a szolgáltatás vezérlősík), függetlenül az érték elérésekor azonnalaktiváljaafeladatátvétel,ateljesadatszinkronizálás(rövidfeladatátvétel) **Leváltó**. Ez a viselkedés garantálja, hogy nincs-e az adatvesztés a helyreállítás során. A türelmi időszak lép érvénybe, csak akkor, amikor egy rövid feladatátvételi ez nem lehetséges. Ha a szolgáltatáskimaradás elhárítása után teljesítményköltségeket csökkenti a türelmi időszak lejárta előtt, a feladatátvétel nincs aktiválva.
>

* **Több, feladatátvételi csoportok**: konfigurálhat több feladatátvételi csoportok ugyanarra a két kiszolgáló feladatátvételi teszteket beosztásának szabályozására. Minden csoport külön feladatait. A több-bérlős alkalmazás rugalmas készletek használja, ha ez a funkció használatával minden egyes készletben található elsődleges és másodlagos adatbázis vegyesen. Ezzel a módszerrel csökkenthető a leállások hatásának fele a bérlők a.

## <a name="best-practices-of-using-failover-groups-for-business-continuity"></a>Ajánlott eljárások a feladatátvételi csoportok használatának az üzletmenet folyamatosságához
Amikor egy olyan szolgáltatást tervez az üzletmenet-folytonossági szem előtt, kövesse ezeket az irányelveket:

- **Egy vagy több, feladatátvételi csoportok segítségével kezelheti a több adatbázis feladatátvételét**: egy vagy több, feladatátvételi csoportok hozható létre több különböző régióban (az elsődleges és másodlagos kiszolgálók) két kiszolgáló között. Minden csoport egy vagy több adatbázis helyreállított egységként abban az esetben az összes vagy néhány elsődleges adatbázissal az elsődleges régióban leállás miatt elérhetetlenné lehetnek. A feladatátvételi csoport geo-secondary adatbázisra hoz létre, az elsődleges azonos szolgáltatási célt. Ha a feladatátvételi csoporthoz ad hozzá egy meglévő georeplikációs kapcsolatban, ellenőrizze a geo-secondary van konfigurálva a azonos szolgáltatási szint célkitűzésének elsődlegesként.
- **Olvasási és írási figyelő OLTP-munkaterhelések esetében használjon**: OLTP műveletek végrehajtásakor használni  **&lt;feladatátvételi csoportnév&gt;. database.windows.net** mint a kiszolgáló URL-CÍMÉT és a kapcsolatok vannak automatikusan átirányítja az elsődleges. Az URL-cím nem változtatja meg a feladatátvételt követően. Vegye figyelembe, hogy a feladatátvétel magában foglalja a DNS-bejegyzést, így az ügyfél-kapcsolatokat a rendszer átirányítja az új elsődleges csak akkor, ha az ügyfél DNS-gyorsítótár nem frissítik frissítése.
- **Csak olvasható munkaterhelések esetében használjon csak olvasási figyelői**: Ha egy logikailag elkülönített csak olvasható számítási feladat, amely tűri az adatok bizonyos frissesség, használhatja az alkalmazás a másodlagos adatbázis. Csak olvasható munkamenetek használata  **&lt;feladatátvételi csoportnév&gt;. secondary.database.windows.net** mint a kiszolgáló URL-CÍMÉT és a kapcsolat automatikusan irányul, a másodlagos. Emellett ajánlott jelzik a kapcsolati karakterlánc használatával leképezés olvasása **ApplicationIntent = csak olvasható**. 
- **Elő kell készíteni a teljesítményoptimalizált teljesítménycsökkenés**: SQL feladatátvételi döntés független az alkalmazás vagy a használt egyéb szolgáltatások többi részétől. Az alkalmazás "összekeverhetők" egy adott régióban, és néhány, a másik néhány összetevőt. A probléma elkerülése érdekében győződjön meg, hogy a redundáns alkalmazástelepítés, a Vészhelyreállítás régióban található, és kövesse a cikkben. Megjegyzés: a DR régióban az alkalmazást nem kell egy másik kapcsolati karakterláncot használja.  
- **Adatvesztés előkészítése**: kimaradás észlelhető, ha az SQL automatikusan aktiválja a írási-olvasási feladatátvételi a legjobb, az ismeretek adatvesztés esetén. Ellenkező esetben megvárja az Ön által megadott időszakban **Leváltó**. Ha a megadott **Leváltó**, elő kell készíteni az adatvesztést a. Leállás, általában az Azure rendelkezésre állási periodicitásának. Ha az adatok elvesztése nem elfogadható, feltétlenül állítson be **Leváltó** elég nagy számú, például a 24 órán keresztül. 

> [!IMPORTANT]
> Rugalmas készletek 800 vagy annál kisebb dtu-k és több mint 250 adatbázisaihoz georeplikációs többek között már tervezett feladatátvételeket problémák merülhetnek fel, és a teljesítménycsökkenést.  Ezeket a problémákat nagyobb valószínűséggel írási igényű számítási feladatokhoz fordulhat elő, ha a georeplikáció végpontok széles körben külön földrajzi hely szerint, vagy több másodlagos végpont az egyes adatbázisok használatakor.  Ezek a problémák tüneteit jelzi, ha a georeplikáció lag idővel növekszik.  A lag segítségével követhetők [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Ha ezeket a problémákat, majd megoldások például készlet dtu-k számának növelésével vagy a georeplikált azonos egy készletben található adatbázisok számának csökkentését.

## <a name="failover-groups-and-network-security"></a>Feladatátvételi csoportok és hálózati biztonság 

Egyes alkalmazások, a biztonsági szabályok követelnek meg, hogy a hálózati hozzáférést az adatréteg korlátozott-e egy adott összetevőre vagy az összetevők, például egy virtuális Gépet a web service stb. Ez a követelmény az üzleti folytonossági terv áttekinthet néhány problémát, és a feladatátvételi csoportok használatát mutatja be. Az ilyen korlátozott hozzáférés végrehajtása során vegye figyelembe a következő beállításokat.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Feladatátvételi csoportok és a virtuális hálózati szabályok használata

Ha használ [virtuális hálózati Szolgáltatásvégpontok és szabályok](sql-database-vnet-service-endpoint-rule-overview.md) való hozzáférés korlátozása az SQL database, vegye figyelembe, hogy mindegyik virtuális hálózati szolgáltatásvégpont csak egy Azure-régióra vonatkozik. A végpont nem engedélyezi a más régiókban az alhálózat-kommunikációt fogad. Ezért csak az ugyanabban a régióban üzembe helyezett ügyfélalkalmazások az elsődleges adatbázishoz csatlakozhat. A feladatátvétel az SQL ügyfélmunkameneteket folyamatban van a kiszolgálóhoz, a másik (másodlagos) régióban átirányítva eredményez, mivel ezek a munkamenetek sikertelen lesz, ha egy adott régión kívül ügyfél származik. Éppen ezért az automatikus feladatátvételi szabályzat nem lehet engedélyezni, ha a virtuális hálózati szabályok a résztvevő kiszolgálókat tartalmazza. Manuális feladatátvétel támogatása érdekében kövesse az alábbi lépéseket:

1.  Az alkalmazás a másodlagos régió (webszolgáltatás, virtuális gépek stb.) az előtérbeli összetevőinek redundáns másolatait kiépítése
2.  Konfigurálja a [virtuális hálózati szabályok](sql-database-vnet-service-endpoint-rule-overview.md) külön-külön az elsődleges és másodlagos kiszolgáló
3.  Engedélyezze a [előtér-feladatátvétel a használatával egy Traffic manager konfigurációja](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4.  Manuális feladatátvétel kezdeményezése a szolgáltatáskimaradás elhárítása után észlelésekor

Ez a beállítás az alkalmazások konzisztens késésű az előtér- és az adatszint között van optimalizálva, és a helyreállítási támogatja, ha az előtérbeli, adatrétegbeli vagy mindkettő negatív hatással van a szolgáltatáskiesés megszüntetése után. 

> [!NOTE]
> Ha használja a **csak olvasási figyelői** terheléselosztásához egy csak olvasható munkaterhelés, ellenőrizze, hogy, hogy ilyen számítási feladatok végrehajtja a virtuális gép vagy más resorce a másodlagos régióban, így képes csatlakozni a másodlagos adatbázisra.
>

 ### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Feladatátvételi csoportok és az SQL database-tűzfalszabályok használatával

Ha az üzletmenet folytonosságát biztosító terve van szüksége a feladatátvételi csoportok használatával automatikus feladatátvétellel, hozzáférés az SQL Database, a hagyományos tűzfalszabályok használatával korlátozhatja.  Automatikus feladatátvétel támogatása érdekében kövesse az alábbi lépéseket:

1.  [Hozzon létre egy nyilvános IP-cím](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address) 
2.  [Nyilvános load balancer létrehozása](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) és rendelje hozzá a nyilvános IP-cím. 
3.  [Hozzon létre egy virtuális hálózat és a virtuális gépek](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) az előtér-összetevők 
4.  [Hálózati biztonsági csoport létrehozása](../virtual-network/security-overview.md) és konfigurálja a bejövő kapcsolatokat. 
5. Győződjön meg arról, hogy a kimenő kapcsolatok meg nyitva, az Azure SQL database "Sql" használatával [szolgáltatáscímke](../virtual-network/security-overview.md#service-tags). 
5.  Hozzon létre egy [SQL database-tűzfalszabályt](sql-database-firewall-configure.md) az 1. lépésben létrehozott nyilvános IP-címről érkező forgalom engedélyezéséhez. 

A kimenő hozzáférés konfigurálása és milyen IP-címet használja a tűzfalszabályok kapcsolatos további információkért lásd: [betölteni a terheléselosztó kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md).

A fenti konfigurációs biztosítja, hogy az Automatikus feladatátvétel nem blokkolja a kapcsolatokat az előtér-kiszolgálói összetevők, és feltételezi, hogy az alkalmazás működését a hosszabb késéssel az előtér- és az adatszint között.

> [!IMPORTANT]
> A regionális üzemkimaradások utáni helyreállításon üzleti folytonosság biztosítása érdekében biztosítania kell a földrajzi redundancia előtér-kiszolgálói összetevők mind az adatbázisokat. 
>

## <a name="upgrading-or-downgrading-a-primary-database"></a>A frissítés, vagy egy elsődleges adatbázis alacsonyabb szolgáltatásszintre
Frissítés vagy visszaminősítését méretez más teljesítményszintre (belül ugyanazon a szolgáltatásszinten) az elsődleges adatbázis bármely másodlagos adatbázisok leválasztása nélkül is. Amikor frissít, javasoljuk, hogy először frissítse a másodlagos adatbázist, és utána frissítse az elsődleges. Ha alacsonyabb szolgáltatásszintre, fordított sorrendben: először alacsonyabbra az elsődleges, majd a gyűjteményt majd a másodlagos. Frissítésekor vagy eltérő szolgáltatási réteg az adatbázisról, a javaslat lép érvénybe. 

> [!NOTE]
> Ha másodlagos adatbázis nem javasoljuk, hogy a másodlagos adatbázisról, a feladatátvételi csoport konfigurációjának részeként hozta létre. Ez azért szükséges az adatszint rendelkezzen elegendő kapacitással a normál számítási feladatok feldolgozásához a feladatátvétel aktiválása után. 
>

## <a name="preventing-the-loss-of-critical-data"></a>Megakadályozza a fontos adatok elvesztését
A nagy kiterjedésű hálózaton magas késést, mert a folyamatos másolás használ egy aszinkrón replikációs mechanizmus. Az aszinkron replikáció révén kis mértékű adatvesztést elkerülhetetlen, ha hiba történik. Egyes alkalmazások azonban szükség lehet adatvesztés nélkül. Ezek a kritikus frissítések védelme érdekében a alkalmazásfejlesztő meghívhatja a [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) közvetlenül a tranzakció véglegesítése után a rendszer eljárást. Hívó **sp_wait_for_database_copy_sync** blokkolja a hívó szálat, amíg a másodlagos adatbázisra továbbítva lett az utolsó véglegesített tranzakció. Azt azonban nem vár a továbbított játssza vissza és a másodlagos véglegesített tranzakciók. **az sp_wait_for_database_copy_sync** hivatkozás egy adott folyamatos másolása hatókörét. A kapcsolódási jogok az elsődleges adatbázissal rendelkező bármelyik felhasználó meghívhatja ezt az eljárást.

> [!NOTE]
> **az sp_wait_for_database_copy_sync** megakadályozza az adatvesztést a feladatátvételt követően, de nem garantálja a teljes szinkronizálás olvasási hozzáféréssel. A késleltetés által okozott egy **sp_wait_for_database_copy_sync** eljáráshívási is lehet, és a hívás idején a tranzakciós napló méretétől függ. 
> 

## <a name="programmatically-managing-failover-groups-and-active-geo-replication"></a>Szoftveres kezelése a feladatátvételi csoportok és az aktív georeplikáció
Ahogy korábban tárgyalt, automatikus feladatátvételi csoportok és az aktív georeplikáció is kezelhetők programozott módon az Azure PowerShell és a REST API használatával. Az alábbi táblázatok ismertetik az elérhető parancsok.

**Az Azure Resource Manager API és a szerepkör alapú biztonsági**: aktív georeplikáció tartalmaz egy, az Azure Resource Manager API-k Management, beleértve a [Azure SQL Database REST API-t](https://docs.microsoft.com/rest/api/sql/) és [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview). Ezekkel az API-erőforráscsoportok használatát, és támogatja a szerepköralapú biztonság (RBAC). Szerepkörök hozzáférést megvalósításával további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md).

## <a name="manage-sql-database-failover-using-transact-sql"></a>Transact-SQL használatával az SQL database feladatátvételi kezelése

| Parancs | Leírás |
| --- | --- |
| [Az ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |MÁSODLAGOS ON-kiszolgáló hozzáadása argumentum használatával egy meglévő adatbázist, és elindítja az adatreplikációt egy másodlagos adatbázis létrehozása |
| [Az ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |A FELADATÁTVÉTELI vagy FORCE_FAILOVER_ALLOW_DATA_LOSS használatával válthat egy másodlagos adatbázist elsődlegessé, hogy feladatátvételt kezdeményezzen |
| [Az ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Használja másodlagos ON SERVER eltávolítása egy SQL-adatbázis és a megadott másodlagos adatbázis közötti adatreplikáció leáll. |
| [sys.geo_replication_links (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Az egyes adatbázisok az összes meglévő replikációs hivatkozások adatait adja vissza az Azure SQL Database logikai kiszolgálón. |
| [sys.dm_geo_replication_link_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Lekéri egy adott SQL-adatbázis a legutóbbi replikációs utolsó replikációs késés és egyéb információ a replikációs hivatkozást. |
| [a sys.dm_operation_status (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Az összes adatbázis-műveletek, beleértve a replikációs hivatkozások állapotát állapotát jeleníti meg. |
| [sp_wait_for_database_copy_sync (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |hatására végrehajtott tranzakciók lapblobokban replikálja, és arra vonatkozik, az aktív másodlagos adatbázis várnia, hogy az alkalmazás. |
|  | |

## <a name="manage-sql-database-failover-using-powershell"></a>Az SQL database feladatátvétel a PowerShell használatával kezelheti.

| Parancsmag | Leírás |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Egy vagy több adatbázist kér le. |
| [New-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |Létrehoz egy másodlagos adatbázist egy meglévő adatbázishoz, és elkezdi az adatok replikálását. |
| [Set-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |Egy másodlagos adatbázist elsődlegessé tesz, hogy feladatátvételt kezdeményezzen. |
| [Remove-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |Leállítja az adatreplikációt egy SQL-adatbázis és a megadott másodlagos adatbázis között. |
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |Lekéri az Azure SQL Database és egy erőforráscsoport vagy SQL-kiszolgáló közötti georeplikációs hivatkozásokat. |
| [New-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt az elsődleges és másodlagos kiszolgálók|
| [Remove-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | A feladatátvételi csoport eltávolítása a kiszolgálóról, és törli az összes másodlagos adatbázist tartalmaz a csoport |
| [Get-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | A feladatátvételi csoport konfigurációjának beolvasása |
| [Set-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   A feladatátvételi csoport konfigurációjának módosítása |
| [Switch-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | A másodlagos kiszolgáló feladatátvételi csoport feladatátvétele eseményindítók |
|  | |

> [!IMPORTANT]
> Mintaszkriptek, lásd: [konfigurálása és a egy önálló adatbázis aktív georeplikációs feladatátvételi](scripts/sql-database-setup-geodr-and-failover-database-powershell.md), [konfigurálása és a egy készletezett adatbázis aktív georeplikációt használ feladatátvételi](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md), és [ Konfigurálja és feladatátvételi egy feladatátvételi csoport egyetlen adatbázishoz](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

## <a name="manage-sql-database-failover-using-the-rest-api"></a>A REST API használatával az SQL database feladatátvételi kezelése
| API | Leírás |
| --- | --- |
| [Hozzon létre vagy az adatbázis frissítése (createMode visszaállítási =)](/rest/api/sql/Databases/CreateOrUpdate) |Hoz létre, frissít, vagy visszaállítja az elsődleges vagy másodlagos adatbázis. |
| [Get létrehozása, vagy az adatbázis állapotának frissítése](/rest/api/sql/Databases/CreateOrUpdate) |A létrehozási művelet során állapotának visszaadása. |
| [Másodlagos adatbázist beállítani az elsődleges (tervezett feladatátvétel)](/rest/api/sql/replicationlinks/failover) |Beállítja, mely az adatbázis-replikát elsődleges által az aktuális elsődleges replika adatbázis feladatátvételét. |
| [Másodlagos adatbázist beállítani az elsődleges (nem tervezett feladatátvétel)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Beállítja, mely az adatbázis-replikát elsődleges által az aktuális elsődleges replika adatbázis feladatátvételét. Ez a művelet adatvesztést okozhat. |
| [Replikációs hivatkozás beszerzése](/rest/api/sql/replicationlinks/get) |Lekéri egy adott replikációs hivatkozás egy adott SQL-adatbázis georeplikációs partnerséget. Lekéri az adatokat a sys.geo_replication_links katalógusnézet látható. |
| [Replikációs hivatkozások - lista-adatbázis](/rest/api/sql/replicationlinks/listbydatabase) | Minden replikációs hivatkozásnak lekéri egy adott SQL-adatbázis georeplikációs partnerséget. Lekéri az adatokat a sys.geo_replication_links katalógusnézet látható. |
| [Replikációs hivatkozás törlése](/rest/api/sql/databases/delete) | Egy adatbázis-replikációs hivatkozás törlése. Nem hajtható végre feladatátvétel során. |
| [Hozzon létre vagy feladatátvételi csoport frissítése](/rest/api/sql/failovergroups/createorupdate) | Létrehoz vagy frissít egy feladatátvételi csoportot |
| [Feladatátvételi csoport törlése](/rest/api/sql/failovergroups/delete) | A feladatátvételi csoport eltávolítása a kiszolgálóról |
| [Feladatátvétel (tervezett)](/rest/api/sql/failovergroups/failover) | Átadja a feladatokat az aktuális elsődleges kiszolgálóról a kiszolgálóhoz. |
| [Kényszerített feladatátvétel adatvesztés engedélyezése](/rest/api/sql/failovergroups/forcefailoverallowdataloss) |ails keresztül az aktuális elsődleges kiszolgálóról a kiszolgálóhoz. Ez a művelet adatvesztést okozhat. |
| [Feladatátvételi csoport lekérése](/rest/api/sql/failovergroups/get) | A feladatátvételi csoport lekérése. |
| [Kiszolgáló feladatátvételi csoportok listázása](/rest/api/sql/failovergroups/listbyserver) | A kiszolgálón a feladatátvételi csoportok listája. |
| [Feladatátvételi csoport](/rest/api/sql/failovergroups/update) | Frissíti egy feladatátvételi csoportot. |
|  | |

## <a name="next-steps"></a>További lépések
* Mintaszkriptek lásd:
   - [Önálló adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
   - [Készletezett adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
   - [Önálló adatbázis feladatátvételi csoportjának konfigurálása és feladatainak átvétele](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
* Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md)
* További információ az Azure SQL Database automatikus biztonsági mentések, lásd: [SQL-adatbázis automatikus biztonsági mentések](sql-database-automated-backups.md).
* Az automatikus biztonsági másolatokból helyreállítási kapcsolatos további információkért lásd: [adatbázis visszaállítása a szolgáltatás által létrehozott biztonsági másolatokból](sql-database-recovery-using-backups.md).
* Hitelesítési követelmények, egy új elsődleges kiszolgáló és az adatbázis kapcsolatos további információkért lásd: [vész-helyreállítása után az SQL Database biztonsági](sql-database-geo-replication-security-config.md).

