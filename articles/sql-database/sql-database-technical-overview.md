---
title: Mi az Azure SQL Database szolgáltatás? | Microsoft Docs
description: 'Bevezetés az SQL Database-be: a Microsoft felhőalapú relációs adatbázis-kezelő rendszerének (RDBMS) technikai részletei és funkciói.'
keywords: bevezetés az sql-be,az sql bemutatása,mi az sql database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: f4b72a95c64467ce287d2cb762222d17334aad57
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755425"
---
# <a name="the-azure-sql-database-service"></a>Az Azure SQL Database szolgáltatásban

Az SQL Database általános célú, felügyelt relációsadatbázis-szolgáltatás a Microsoft Azure-ban, amely egyebek mellett relációs, JSON-, térbeli és XML-struktúrákat is támogat. Az SQL Database két különböző vásárlási modell belül dinamikusan méretezhető teljesítményt nyújt: a Virtuálismag-alapú vásárlási modell és a egy DTU-alapú vásárlási modell. Az SQL Database emellett olyan lehetőségeket kínál, mint az [oszlopcentrikus indexelés](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) a kivételes mélységű elemzéshez és jelentéskészítéshez, illetve a [memóriabeli OLTP](sql-database-in-memory.md) a rendkívül nagy teljesítményű tranzakciófeldolgozáshoz. A Microsoft zökkenőmentesen kezeli az SQL kódbázis karbantartását és frissítését, és teljesen átveszi az alapul szolgáló infrastruktúra kezelését.

> [!NOTE]
> Egy Azure SQL Database-ben szószedet, lásd: [SQL-adatbázis használati szószedet](sql-database-glossary-terms.md)

Az Azure SQL Database az alábbi lehetőségeket kínálja az Azure SQL-adatbázisok üzembe helyezésére:

- Mint egy [önálló adatbázis](sql-database-single-database.md) saját erőforráskészlettel felügyelt SQL Database-kiszolgálón keresztül. Önálló adatbázis hasonlít egy [tartalmazott adatbázisok](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) az SQL Server.
- Egy [rugalmas készlet](sql-database-elastic-pool.md), azaz egy SQL Database-kiszolgálón keresztül felügyelt közös erőforráskészlettel az adatbázis-gyűjtemény. Önálló adatbázisok áthelyezhetők, és a egy rugalmas készletből.
- [Felügyelt példány](sql-database-managed-instance.md), amely gyűjteménye, rendszer- és felhasználói adatbázisok közös erőforráskészlettel az. Felügyelt példány hasonlít egy példányát a [Microsoft SQL Server adatbázismotor](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation).

Az alábbi ábra ezt a három üzembehelyezési modellt mutatja be:

![deployment-options](./media/sql-database-technical-overview/deployment-options.png)

Az SQL Database kódbázisa közös a [Microsoft SQL Server adatbázismotorjáéval](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation). A Microsoft felhőt előnyben részesítő stratégiájának jegyében az SQL Server új lehetőségei először az SQL Database-ben jelentek meg, és csak aztán magában az SQL Serverben. Ennek a hozzáállásnak köszönhetően az SQL Server legújabb lehetőségei a karbantartás és frissítés terhei nélkül állnak az Ön rendelkezésére úgy, hogy az új funkciókat már több millió adatbázison tesztelték. A bejelentett új funkciókról az alábbi helyeken kaphat tájékoztatást:

- **[Az SQL Database az Azure ütemterve](https://azure.microsoft.com/roadmap/?category=databases)**:

  Ismerje meg, mi az új és újdonságokat helye.

- **[Az Azure SQL Database blog](https://azure.microsoft.com/blog/topics/database)**:

  Az a hely, ahol az SQL Server csapatának tagjai kapcsolatos SQL Database újdonságairól és funkcióiról.

> [!IMPORTANT]
> Az SQL Database és SQL Server közötti különbségek, valamint a különböző Azure SQL Database változatokhoz közötti különbségek ismertetése: [SQL-szolgáltatások](sql-database-features.md).

SQL Database kiszámítható teljesítményt több erőforrástípusok, a szolgáltatási szintek és a számítási méret, amely dinamikus méretezhetősége révén a nem állásidő, beépített intelligens optimalizálással, globális méretezhetőség és rendelkezésre állás és a speciális biztonsági biztosít beállítások – mindezt szinte Adminisztráció. Ezen képességek birtokában az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására összpontosíthat ahelyett, hogy értékes időt és erőforrásokat kellene lefoglalnia a virtuális gépek és infrastruktúra kezeléséhez. Az SQL Database szolgáltatás jelenleg a világ 38 adatközpontjában áll rendelkezésre, és újabb adatközpontok is rendszeresen lépnek működésbe, így Ön a saját adatbázisát egy közeli adatközponton futtathatja.

## <a name="scalable-performance-and-pools"></a>Méretezhető teljesítmény és készletek

- A önálló adatbázisokhoz minden egyes adatbázis elkülönül önálló, hordozható, és a saját számítási, memória és tárolási erőforrások garantált mennyiségét. Az SQL Database nyújt a különféle igényekre szabva - különböző számítási, memória és tárolási erőforrások és a rendszer képes dinamikusan [egyetlen adatbázis-erőforrások skálázása](sql-database-single-database-scale.md) felfelé és lefelé. A [nagy kapacitású szolgáltatásszint](sql-database-service-tier-hyperscale.md) (előzetes verzió) az egyetlen adatbázis lehetővé teszi, hogy a skálázás 100 TB-ig, a gyors biztonsági mentési és visszaállítási képességeket.
- A rugalmas készletekkel létrehozhat új adatbázisokat vagy önálló adatbázisok helyezhetik át maximalizálhatja az erőforrások használatát és pénzt - és képes dinamikusan mentése erőforráskészlet [méretezhető rugalmas adatbáziskészlet erőforrásainak](sql-database-elastic-pool-scale.md) felfelé és lefelé.
- A felügyelt példányok esetén minden egyes felügyelt példány el vannak különítve más példányok garantált erőforrásokkal. Belül a felügyelt példány, a példány adatbázisok osztoznak a erőforrások –, és képes dinamikusan [méretezni az erőforrásokat a felügyelt példány](sql-database-managed-instance-resource-limits.md) felfelé és lefelé.

Hozza létre első alkalmazását egy egyedülálló, kisméretű adatbázison alacsony áron az általános célú szolgáltatási szinten a havonta, és ezután szolgáltatásszintet manuálisan vagy programon keresztül bármikor módosíthatja az üzleti kritikus szolgáltatási szintre, hogy a megoldás igényeinek. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre, és lehetővé teszi, hogy csak azokért az erőforrásokért fizessen, amelyekre és amikor szüksége van.

A dinamikus méretezhetőség különbözik az automatikus skálázástól. Automatikus skálázás esetén a szolgáltatás adott feltételek alapján, automatikusan méretez, míg a dinamikus méretezhetőség lehetővé teszi a manuális méretezést, amely nem jár állásidővel. Önálló adatbázis támogatja a manuális a dinamikus méretezhetőség, de nem automatikus. Ha *automatikus* megoldást keres, érdemes megfontolni a rugalmas készletek használatát, amely lehetővé teszi, hogy az adatbázisok osztozzanak egy készlet erőforrásain az egyes adatbázisok egyedi igényei alapján. Vannak azonban parancsfájlok, amelyek segítségével automatizálhatja az önálló adatbázis méretezhetősége. Egy vonatkozó példáért lásd: [PowerShell-lel történő egy önálló adatbázis monitorozása és skálázása](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models-service-tiers-compute-sizes-and-storage-amounts"></a>Vásárlási modellek, a szolgáltatási szintek, a számítási méretek és a storage összegek

SQL Database két vásárlási modellt kínál:

- A [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) számítási, memória, IO-erőforrások kis és nagy terhelést jelentő adatbázisokhoz database három szolgáltatásszintet kínálja. A számítási biztosít különböző többféle, ezeket az erőforrásokat, amelyhez további tárterület-erőforrások adhat hozzá az egyes szinteken belül.
- A [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) válassza ki a virtuális magok, a vagy a memória, és a számát és a storage sebességétől teszi lehetővé.

  > [!IMPORTANT]
  > A [nagy kapacitású szolgáltatásszint](sql-database-service-tier-hyperscale.md) jelenleg nyilvános előzetes verzióban érhető el. Nem ajánlott semmilyen éles számítási feladatok futtatása a nagy kapacitású adatbázisokban még. Nagy kapacitású adatbázis egyéb szolgáltatási csomagokra nem frissíthető. Tesztelési célra ajánlott másolatot készít az aktuális adatbázisban, és frissíti a példányt nagy kapacitású szolgáltatásszinthez.

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Rugalmas készletek az erőforrások kihasználtságának maximalizálásához

Számos vállalkozás és alkalmazás számára elegendő az, ha önálló adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. A [rugalmas készletek](sql-database-elastic-pool.md) megoldást jelentenek erre a problémára. A koncepció egyszerű. Az egyes adatbázisok helyett a készlet teljesítmény-erőforrásokat, és a készlet közös teljesítményéért erőforrások helyett egy önálló adatbázis teljesítményéért fizet.

   ![rugalmas készletek](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

A rugalmas készletekkel az erőforrásigények ingadozásakor sem kell a az adatbázis teljesítményének fel- és leskálázására koncentrálnia. A készletezett adatbázisok szükség szerint használják fel a teljesítmény-erőforrásokat. A készletezett adatbázisok használják az adatbáziskészlethez hozzárendelt teljesítményt, de nem lépik túl az adatbáziskészlet teljesítménykorlátait, így költségei előre jelezhetők még akkor is, ha az egyéni adatbázis-használat nem jelezhető előre. Ráadásul [adatbázisokat adhat hozzá a készlethez, és távolíthat el a készletből](sql-database-elastic-pool-manage-portal.md), így előre jelezhető költségek mellett néhány adatbázisról több ezer adatbázisra skálázhatja fel alkalmazását. A készlet adatbázisai számára elérhető erőforrások minimális és maximális mennyiségét is szabályozhatja annak érdekében, hogy a készlet egyetlen adatbázisa se használja fel a készlet összes erőforrását, és hogy minden készletezett adatbázis rendelkezzen egy garantált minimális erőforrás-mennyiséggel. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

A szkriptek segítik a rugalmas készletek megfigyelését és méretezését. Erre a [Rugalmas SQL-készlet monitorozása és skálázása az Azure SQL Database-ben a PowerShell használatával](scripts/sql-database-monitor-and-scale-pool-powershell.md) című témakörben talál példát.

> [!IMPORTANT]
> Felügyelt példány nem támogatja a rugalmas készletek. Felügyelt példány, gyűjteménye, amelyek a felügyelt példány erőforrások megosztása a példányok adatbázisai.

### <a name="blend-single-databases-with-pooled-databases"></a>Önálló adatbázisok beolvasztása a készletezett adatbázisokba

Önálló adatbázisokat rugalmas készletekkel kombinálhatja, és gyorsan és egyszerűen módosíthatja az önálló adatbázisok és rugalmas készletek szolgáltatásszintjét alkalmazkodni az adott helyzethez. Az Azure sokoldalúságának és széles körű alkalmazhatóságának köszönhetően tetszés szerint kombinálhatja az Azure-szolgáltatásokat SQL Database-adatbázisokkal, így kielégíthetők az egyedi, modern alkalmazástervezési igények, növelhető a költség- és erőforrás-hatékonyság, és új üzleti lehetőségek tárhatók fel.

### <a name="extensive-monitoring-and-alerting-capabilities"></a>Széles körű figyelési és riasztási funkciók

Használja a [beépített teljesítménymonitorozási](sql-database-performance.md) és [riasztási](sql-database-insights-alerts-portal.md) eszközöket a teljesítmény-értékelésekkel kombinálva. Ezek lehetővé teszik az aktuális vagy a projekthez kapcsolódó teljesítményigényeken alapuló fel- vagy leskálázás hatásainak gyors kiértékelését. Az SQL Database emellett [metrikák és diagnosztikai naplók kibocsátásával](sql-database-metrics-diag-logging.md) is képes megkönnyíteni a felügyeletet. Az SQL Database beállítható az erőforrás-használatra, feldolgozókra és munkamenetekre, valamint kapcsolatokra vonatkozó adatok tárolására a következő Azure-erőforrások valamelyikén:

- **Az Azure Storage**: Alacsony költségű archiválására telemetriai adatok hatalmas mennyiségű
- **Azure Event Hub**: Az SQL Database telemetriai adatainak integrálása saját egyedi monitorozási megoldásokkal vagy élő adatfolyamatokkal
- **Az Azure Log Analytics**: A beépített monitorozási megoldás jelentéskészítő, riasztó és enyhítő funkciókkal.

    ![architektúra](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Rendelkezésre állás

Az Azure szolgáltatói szerződésében [(SLA)](https://azure.microsoft.com/support/legal/sla/) az ágazatban élenjáró módon 99,99 százalékos elérhetőséget biztosítunk – a Microsoft által kezelt adatbázisok globális hálózata teszi lehetővé, hogy alkalmazása a hét mind a 7 napján, napi 24 órában fusson. Az Azure platform teljes körű minden adatbázis kezeli, és nincs adatvesztés és a magas százalékos adatok rendelkezésre állását garantálja. Az Azure automatikusan kezeli a javításokat, a biztonsági mentéseket, a replikációt, a hibaészlelést, a háttérben álló lehetséges hardver-, szoftver- vagy hálózati hibákat, a hibajavítások telepítését, a feladatátvételeket, adatbázis-frissítéseket és az egyéb karbantartási műveleteket. A standard szintű rendelkezésre állás a számítási és tárolási rétegek elkülönítését foglalja magában. Prémium szintű rendelkezésre állás integrálása a számítási és tárolási teljesítmény egyetlen csomóponton, és majd megvalósítása az Always On rendelkezésre állási csoportok valójában hasonló technológia érhető el. Teljes az Azure SQL Database magas rendelkezésre állású lehetőségeit, lásd: [SQL adatbázis-elérhetőségi](sql-database-high-availability.md). Az SQL Database ezen felül olyan beépített funkciókkal szolgálja [az üzletmenet folytonosságát és a globális méretezhetőséget](sql-database-business-continuity.md), mint például a következők:

- **[Automatikus biztonsági mentések](sql-database-automated-backups.md)**:

  SQL Database automatikusan készít teljes, differenciális és tranzakciónapló biztonsági mentései az Azure SQL Database-adatbázisok ahhoz, hogy bármely időpontra visszaállítása. Az önálló adatbázis és a készletezett adatbázisokat konfigurálhatja az SQL Database-adatbázis teljes biztonsági mentések hosszú távú megőrzés az Azure Storage tárolja. Felügyelt példány esetén is elvégezheti a csak másolatot biztonsági másolatok hosszú távú adatmegőrzés.

- **[Időponthoz visszaállítás](sql-database-recovery-using-backups.md)**:

  Az összes SQL Database változatokhoz minden olyan Azure SQL Database-adatbázis automatikus biztonsági mentések megőrzési időszakon belül bármely időpontra történő helyreállítás támogatja.
- **[Aktív georeplikáció](sql-database-active-geo-replication.md)**:

  Önálló adatbázis és a készletezett adatbázisok lehetővé teszi legfeljebb négy olvasható másodlagos adatbázis konfigurálását akár a az azonos vagy globálisan elosztott Azure-beli adatközpontok.  Ha például egy SaaS-alkalmazás nagyszámú egyidejű csak olvasási tranzakciót kiszolgáló katalógus-adatbázisra épül, akkor az aktív georeplikáció által növelhető az olvashatóság, és megszűnik az elsődleges adatbázis magas olvasási terhelése miatti szűk keresztmetszet. A felügyelt példányok esetén használja az automatikus feladatátvételi csoportok.
- **[Automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md)**:

  Az összes SQL-adatbázis központi telepítési beállítások lehetővé teszik a feladatátvételi csoportok segítségével magas rendelkezésre állást és terheléselosztást kínáljon globális léptékben, beleértve a transzparens georeplikációt és feladatátvételét a Nagy adatkészleteken, adatbázisok, rugalmas készletek és a felügyelt példányok. Feladatátvételi csoportok többletterhelést elhagyó minden az összetett figyelés, Útválasztás és feladatátvétel vezénylési az SQL Database minimális felügyelet engedélyezése a globálisan elosztott SaaS-alkalmazások létrehozását.
- **[Zónaredundáns adatbázisok](sql-database-high-availability.md)**:

  SQL Database segítségével kiépítése prémium vagy üzleti foglalja magába, vagy a rugalmas készletek több rendelkezésre állási zónák között. Mivel ezek az adatbázisok és a rugalmas készletek több redundáns replikával rendelkeznek a magas rendelkezésre állás érdekében, e replikák több rendelkezésre állási zónában történő elhelyezése nagyobb rugalmasságot biztosít, beleértve az adatközpont méretezési hibáiból az adatvesztés nélküli, automatikus helyreállítás lehetőségét is.

## <a name="built-in-intelligence"></a>Beépített intelligencia

Az SQL Database-zel beépített intelligencia is jár, amely segít jelentősen mérsékelni az adatbázisok működtetésének és kezelésének költségét, ugyanakkor fokozni az alkalmazás teljesítményét és biztonságát. Az állandóan ügyfelek millióinak számítási feladatait futtató SQL Database hatalmas mennyiségű telemetriai adatot dolgoz fel, végig szem előtt tartva az ügyfelek adatainak teljes védelmét. A telemetriai adatokat szüntelenül különféle algoritmusok értékelik, hogy a szolgáltatás tanulhasson belőlük és idomulhasson az Ön alkalmazásához. Ezen elemzés alapján a szolgáltatás az adott számítási feladatnak megfelelő javaslatokat tesz a teljesítmény javítására.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatikus teljesítményfigyelés és finomhangolás

Az SQL Database részletes betekintést nyújt a figyelést kívánó lekérdezésekbe. Az SQL Database megismeri az adatbázisaira jellemző mintázatokat, és segít az adatbázis-sémáit a számítási feladathoz igazítani. Az SQL Database [javaslatokat tesz a teljesítmény finomhangolására](sql-database-advisor.md), és Ön eldöntheti, hogy alkalmazza-e a javasolt finomhangolási műveleteket.

Az adatbázisok folyamatos figyelése azonban nehéz, fárasztó feladat, különösen akkor, ha sok adatbázisról van szó. Az [Intelligent Insights](sql-database-intelligent-insights.md) elvégzi Ön helyett ezt a munkát. Automatikusan figyeli az SQL Database teljesítményét, és teljesítménycsökkenési hibák előfordulása esetén értesítést küld, azonosítja a probléma gyökerét, és ahol lehetséges, teljesítményjavítási javaslatokkal is szolgál.

Nagyszámú adatbázis hatékony kezelése már az SQL Database és az Azure Portal által kínált valamennyi eszköz és jelentés birtokában is lehetetlennek bizonyulhat. Az adatbázis személyes figyelése és finomhangolása helyett érdemes megfontolni, hogy a figyelési és hangolási műveletek egy részét az SQL Database [automatikus finomhangoló](sql-database-automatic-tuning.md) funkciójára bízza. Az SQL Database automatikusan alkalmazza a javasolt változásokat, de minden módosítást tesztel és ellenőriz, hogy a teljesítmény csak javulhasson. Az SQL Database ezáltal automatikusan, ellenőrzött és biztonságos módon alkalmazkodik a számítási feladathoz. Az automatikus finomhangolás azt jelenti, hogy ha az adatbázis egyes módosítások előtt és után gondosan mért teljesítményének összehasonlítása nem mutat javulást, akkor a módosító művelet visszavonásra kerül.

Jelenleg számos partnerünk, akik [több-bérlős SaaS-alkalmazásokat](sql-database-design-patterns-multi-tenancy-saas-applications.md) futtatnak SQL Database-en, az automatikus teljesítményhangolásra bízza az alkalmazása mindig stabil és kiszámítható működését. Ez a funkció nagy mértékben csökkenti számukra egy éjszaka közepén fellépő teljesítmény-visszaesés kockázatát. Ráadásul, mivel ügyfélkörük egy része szintén használ SQL Servert, az SQL Database által kínált azonos indexelési ajánlásokat alkalmazva nyújthatnak segítséget ezen ügyfeleknek.

Az [SQL Database](sql-database-automatic-tuning.md) két szempont alapján képes automatikus finomhangolást végezni:

- **Automatikus Indexkezelés**: Azonosítja az adatbázisban az új indexeket és az indexek, el kell távolítani.
- **Automatikus terv-korrekció**: Azonosítja a hibás terveket és javítja az SQL tervek teljesítmény-problémáit (hamarosan megjelenik, az SQL Server 2017-ben már elérhető).

### <a name="adaptive-query-processing"></a>Adaptív lekérdezés-feldolgozás

Emellett az [adaptív lekérdezésfeldolgozási](/sql/relational-databases/performance/adaptive-query-processing) szolgáltatáscsalád is elérhető vált az SQL Database-ben, beleértve a több utasításból álló, táblaértékű függvények kihagyásos végrehajtását, a kötegelt módú memóriaengedély visszajelzését, valamint a kötegelt módú adaptív illesztéseket. Ezen adaptív lekérdezésfeldolgozó funkciók mindegyike hasonló technikával „tanul és alkalmazkodik”, ezzel is hozzájárulva a korábban nyomon követhetetlen lekérdezés-optimalizálási gondokra visszavezethető teljesítményproblémák megoldásához.

## <a name="advanced-security-and-compliance"></a>Magas szintű biztonság és megfelelőség

Az SQl Database számos [beépített biztonsági és megfelelőségi szolgáltatást](sql-database-security-overview.md) kínálva járul hozzá, hogy az Ön alkalmazása eleget tegyen a különféle biztonsági és megfelelőségi elvárásoknak.

### <a name="advance-threat-protection"></a>Fejlett fenyegetésvédelem

Speciális biztonsági egységes csomag egy tapasztalt SQL biztonsági funkciók. Lehetőséget nyújt a bizalmas adatok felderítésére és titkossá minősítésére, az adatbázis biztonsági réseinek kezelésére, továbbá az adatbázisra nézve fenyegetést jelentő rendellenes tevékenységek észlelésére. Segítségével egyetlen helyen engedélyezhetők és kezelhetők ezek a képességek.

- [Adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md):

  Ez a funkció (jelenleg előzetes verzióban érhető el) felderítése, a besorolása, a címkézés és a az adatbázisokban a bizalmas adatok védelme az Azure SQL Database-be beépített szolgáltatásokat biztosít. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül.
- [A biztonságirés-értékelési](sql-vulnerability-assessment.md):

  Ez a szolgáltatás képes felderíteni, nyomon követheti, és segítséget nyújt az adatbázis biztonsági réseinek javítása. Áttekinthetővé teszi az adatbázisok biztonsági állapotát, és végrehajtható lépéseket kínál a biztonsági problémák megoldására, valamint az adatbázisok védelmének fejlesztésére.
- [Veszélyforrások detektálása](sql-database-threat-detection.md):

  Ez a szolgáltatás észleli a elérni vagy kiaknázni az adatbázis szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek. A szolgáltatás folyamatosan figyeli az adatbázisokat, és azonnal értesíti a felhasználót a gyanús tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. Fenyegetésészlelési riasztásokat a gyanús tevékenység részleteit, adja meg, és tegyen javaslatot a műveletre vizsgálata, valamint a fenyegetés.

### <a name="auditing-for-compliance-and-security"></a>Naplózás a megfelelőség és biztonság szolgálatában

[Naplózás](sql-database-auditing.md) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, az Azure storage-fiókban. A naplózás segíthet a jogszabályi megfelelőség fenntartásában és az adatbázison végzett tevékenység megértésében, valamint az esetleg üzleti veszélyeket vagy biztonsági problémákat jelző rendellenességek feltárásában.

### <a name="data-encryption"></a>Adattitkosítás

SQL Database a mozgásban lévő adatok titkosítását biztosításával védi az adatokat [transport layer security](https://support.microsoft.com/kb/3135244), az inaktív adatok [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), és az adatok használatban van a [ mindig titkosított](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

Az SQL Database az [Azure Active Directory-integráció](sql-database-aad-authentication.md) által lehetővé teszi adatbázis-felhasználók és más Microsoft-szolgáltatások identitásainak központi kezelését. Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. Az Azure Active Directory a [többtényezős hitelesítés](sql-database-ssms-mfa-authentication.md) (MFA) támogatásával javítja az adatok és alkalmazások biztonságát, miközben támogatja az egyszeri bejelentkezést.

### <a name="compliance-certification"></a>Megfelelőségi tanúsítvány

Az SQL Database rendszeres ellenőrzéseken vesz részt és számos megfelelőségi szabványnak tesz eleget tanúsított módon. További információkat az [Azure biztonsági és adatkezelési központban](https://azure.microsoft.com/support/trust-center/) talál, az [SQL Database megfelelőségi tanúsítványainak](https://azure.microsoft.com/support/trust-center/services/) aktuális listájával együtt.

## <a name="easy-to-use-tools"></a>Egyszerűen használható eszközök

Az SQL Database egyszerűbbé és hatékonyabbá teszi az alkalmazások létrehozását és karbantartását. Az SQL Database lehetővé teszi, hogy arra koncentrálhasson, amit legjobban tud: kiváló alkalmazások készítésére. Az SQL Database-ben a már meglévő eszközeivel és szakértelmével dolgozhat és fejleszthet.

- **[Az Azure Portalon](https://portal.azure.com/)**:

  A webes alkalmazás kezelése az Azure-szolgáltatásokhoz
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)**:

  Egy ingyenes, letölthető ügyféloldali alkalmazás bármilyen SQL infrastruktúra kezelésére az SQL Servertől az SQL Database
- **[Az SQL Server Data Tools a Visual Studióban](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)**:

  Egy ingyenes, letölthető ügyféloldali alkalmazás SQL Server relációs adatbázisok, Azure SQL Database-adatbázisok, Integration Services-csomagok, Analysis Services adatmodellek és Reporting Services-jelentések fejlesztéséhez.
- **[Visual Studio Code](https://code.visualstudio.com/docs)**:

  Egy ingyenes, letölthető, nyílt forráskódú Kódszerkesztő Windows, macOS és Linux rendszerekre, amely támogatja a bővítményeket, beleértve a [mssql bővítményt](https://aka.ms/mssql-marketplace) a Microsoft SQL Server, az Azure SQL Database és az SQL Data Warehouse lekérdezéséhez.

Az SQL Database támogatja a Python, Java, Node.js, PHP, Ruby, és .NET alkalmazásfejlesztést a MacOS, Linux, és Windows rendszeren. Az SQL Database az SQL Serverrel azonos [adatkapcsolattárakat](sql-database-libraries.md) támogat.

## <a name="engage-with-the-sql-server-engineering-team"></a>Kapcsolatfelvétel az SQL Server műszaki csoportjával

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Adatbázis-felügyeleti kérdéseket
- [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-server): Kérdések
- [MSDN fórumok](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Tegyen fel műszaki kérdéseket
- [Visszajelzés](https://aka.ms/sqlfeedback): Hibák bejelentése és funkciók kérése
- [Reddit](https://www.reddit.com/r/SQLServer/): SQL Server megvitatása

## <a name="next-steps"></a>További lépések

- Tekintse meg az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/sql-database/) az önálló adatbázisok és a rugalmas készletek költségeinek összehasonlításáért és árkalkulációjáért.
- Tekintse meg ezeket a rövid útmutatókat a kezdéshez:

  - [SQL Database létrehozása az Azure Portalon](sql-database-single-database-get-started.md)  
  - [SQL Database létrehozása az Azure CLI-vel](sql-database-get-started-cli.md)
  - [SQL Database létrehozása PowerShell használatával](sql-database-get-started-powershell.md)

- Több Azure CLI és PowerShell-mintát talál itt:
  - [Azure CLI-minták az SQL Database-hez](sql-database-cli-samples.md)
  - [Azure PowerShell-minták az SQL Database-hez](sql-database-powershell-samples.md)
