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
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 8c931521e77123844817dff0e9209f8f95f7ad59
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279902"
---
# <a name="what-is-azure-sql-database-service"></a>Mi az Azure SQL Database szolgáltatás?

A Azure SQL Database egy általános célú, kapcsolati adatbázis által felügyelt szolgáltatás, amely lehetővé teszi, hogy magas rendelkezésre állású és nagy teljesítményű adattárolási réteget hozzon létre Microsoft Azure Felhőbeli alkalmazások és megoldások számára. A SQL Database a különböző modern felhőalapú alkalmazások számára megfelelő választás lehet, mivel lehetővé teszi, hogy hatékony funkciókat használjon mind a kapcsolati, mind a [nem rokon struktúrák](sql-database-multi-model-features.md) , például a diagramok, a JSON, a térbeli és az XML feldolgozásához. A szolgáltatás a [Microsoft SQL Server adatbázismotor](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) legújabb stabil verziójára épül, és lehetővé teszi a speciális lekérdezés-feldolgozási funkciók, például a [nagy teljesítményű memóriában lévő technológiák](sql-database-in-memory.md) és az [intelligens lekérdezések feldolgozásának széles választékát. ](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json).
A Microsoft felhőt előnyben részesítő stratégiájának jegyében az SQL Server új lehetőségei először az SQL Database-ben jelentek meg, és csak aztán magában az SQL Serverben. Ennek a hozzáállásnak köszönhetően az SQL Server legújabb lehetőségei a karbantartás és frissítés terhei nélkül állnak az Ön rendelkezésére úgy, hogy az új funkciókat már több millió adatbázison tesztelték. A SQL Database lehetővé teszi a teljesítmény egyszerű meghatározását és méretezését két különböző beszerzési modellen belül: egy [virtuális mag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md) és egy [DTU-alapú vásárlási modellt](sql-database-service-tiers-dtu.md). A SQL Database teljes körűen felügyelt szolgáltatás, amely beépített availablility, biztonsági mentésekkel és egyéb gyakori karbantartási műveletekkel rendelkezik. A Microsoft zökkenőmentesen kezeli az SQL és az operációsrendszer-kód javítását és frissítését, és elvégzi az összes mögöttes infrastruktúra felügyeletét.

> [!NOTE]
> A Azure SQL Database használati feltételeinek szószedetét lásd: [SQL Database kifejezések szószedete](sql-database-glossary-terms.md)

Az Azure SQL Database az alábbi lehetőségeket kínálja az Azure SQL-adatbázisok üzembe helyezésére:

![deployment-options](./media/sql-database-technical-overview/deployment-options.png)

- Az [önálló adatbázisok](sql-database-single-database.md) egy teljes körűen felügyelt elkülönített adatbázist jelentenek, amely tökéletes választás a modern Felhőbeli alkalmazások és szolgáltatások számára, amelyeknek egyetlen megbízható adatforrásra van szükségük. Az önálló adatbázisok a [Microsoft SQL Server adatbázismotor](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)egy [tárolt adatbázisához](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) hasonlóak.
- A [felügyelt példány](sql-database-managed-instance.md) a [Microsoft SQL Server adatbázismotor](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) egy teljes körűen felügyelt példánya, amely a közös használatú adatbázisok készletét tartalmazza. Tökéletes választás a helyszíni SQL Server-adatbázisok egyszerű áttelepítésére az Azure-felhőbe, valamint olyan alkalmazásokhoz, amelyeknek a hatékony adatbázis-funkciókat kell kihasználnia, SQL Server az adatbázismotor biztosítja.
- A [rugalmas készlet](sql-database-elastic-pool.md) olyan [önálló adatbázisok](sql-database-single-database.md) gyűjteménye, amelyek erőforrásai, például a processzor vagy a memória közös készletével rendelkeznek. Az önálló adatbázisok egy rugalmas készletbe helyezhetők és kivehetők.

> [!IMPORTANT]
> A SQL Database és SQL Server közötti szolgáltatások közötti különbségek megismeréséhez, valamint a különböző Azure SQL Database telepítési lehetőségek közötti különbségekről lásd: [SQL-szolgáltatások](sql-database-features.md).

A SQL Database kiszámítható teljesítményt nyújt több erőforrástípus, szolgáltatási réteg és számítási méret révén, amely leállást, beépített intelligens optimalizációt, globális skálázhatóságot és rendelkezésre állást, valamint fokozott biztonságot biztosít a dinamikus méretezhetőség érdekében. beállítások – mind a közel nulla felügyelethez. Ezen képességek birtokában az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására összpontosíthat ahelyett, hogy értékes időt és erőforrásokat kellene lefoglalnia a virtuális gépek és infrastruktúra kezeléséhez. Az SQL Database szolgáltatás jelenleg a világ 38 adatközpontjában áll rendelkezésre, és újabb adatközpontok is rendszeresen lépnek működésbe, így Ön a saját adatbázisát egy közeli adatközponton futtathatja.

## <a name="scalable-performance-and-pools"></a>Méretezhető teljesítmény és készletek

A SQL Database összes változata lehetővé teszi, hogy meghatározza a hozzárendelni kívánt erőforrások mennyiségét. 
- Az önálló adatbázisok esetében minden adatbázis el van különítve egymástól és hordozható rendszertől, amelyek mindegyike saját, garantált mennyiségű számítási, memória-és tárolási erőforrásokkal rendelkezik. Az adatbázishoz rendelt erőforrások mennyisége dedikált az adatbázishoz, és nem lesz megosztva az Azure-felhőben lévő más adatbázisokkal. Lehetővé teszi az [önálló adatbázis-erőforrások](sql-database-single-database-scale.md) felfelé és lefelé történő dinamikus méretezését is. A önálló adatbázis különböző számítási, memória-és tárolási erőforrásokat biztosít a különböző igényekhez, amelyek 1 – 80 virtuális mag, 32 GB és 4 TB között változnak. Az önálló adatbázisok [nagy kapacitású-szolgáltatási rétege](sql-database-service-tier-hyperscale.md) lehetővé teszi, hogy a gyors biztonsági mentési és visszaállítási képességekkel bővítse a 100 TB-ot.
- Rugalmas készletekkel hozzárendelhet erőforrásokat, amelyeket a készlet összes adatbázisa meg fog osztani. Létrehozhat egy új adatbázist, vagy áthelyezheti a meglévő önálló adatbázisokat egy erőforráskészletbe, így maximalizálhatja az erőforrások használatát, és pénzt takaríthat meg, és a [rugalmas készlet erőforrásainak fel-és leskálázását](sql-database-elastic-pool-scale.md) is lehetővé teszi.
- Felügyelt példányok esetén minden felügyelt példány el van különítve a garantált erőforrásokkal rendelkező más példányokból. Felügyelt példányon belül a példány-adatbázisok több erőforrást is megoszthatnak egymással, és a [felügyelt példányok erőforrásai](sql-database-managed-instance-resource-limits.md) fel és le dinamikusan méretezhetők.

Az általános célú szolgáltatási szinten az első alkalmazást egy kis méretű, egyetlen adatbázison is felépítheti, és a szolgáltatási szintet manuálisan vagy programozottan módosíthatja az üzleti szempontból kritikus szolgáltatási rétegre, hogy megfeleljen a megoldás igényeinek. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre, és lehetővé teszi, hogy csak azokért az erőforrásokért fizessen, amelyekre és amikor szüksége van.

A dinamikus méretezhetőség különbözik az automatikus skálázástól. Automatikus skálázás esetén a szolgáltatás adott feltételek alapján, automatikusan méretez, míg a dinamikus méretezhetőség lehetővé teszi a manuális méretezést, amely nem jár állásidővel. Egyetlen adatbázis támogatja a manuális dinamikus méretezhetőséget, de nem automatikus méretezést. Ha *automatikus* megoldást keres, érdemes megfontolni a rugalmas készletek használatát, amely lehetővé teszi, hogy az adatbázisok osztozzanak egy készlet erőforrásain az egyes adatbázisok egyedi igényei alapján. Vannak azonban olyan parancsfájlok, amelyek segítségével automatizálható egy adott adatbázis skálázhatósága. Példa: [egyetlen adatbázis figyelése és méretezése a PowerShell használatával](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models-service-tiers-compute-sizes-and-storage-amounts"></a>Modellek, szolgáltatási szintek, számítási méretek és tárolási összegek vásárlása

SQL Database két vásárlási modellt kínál:
- A [virtuális mag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) segítségével kiválaszthatja a virtuális mag számát, a mennyiségét vagy a memóriát, valamint a tárterület mennyiségét és sebességét. A virtuális mag-alapú vásárlási modell azt is lehetővé teszi, hogy a megtakarítások megszerzéséhez a [SQL Server Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) is használja. További információ a Azure Hybrid Benefitről: [Gyakori kérdések](#sql-database-frequently-asked-questions-faq).
- A [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) a számítási, a memória-és az i/o-erőforrások keverékét kínálja három szolgáltatási szinten a könnyű és a nehéz adatbázis-számítási feladatok támogatásához. Az egyes szintjein belüli számítási méretek különböző mennyiségű erőforrást biztosítanak, amelyhez további tárolási erőforrásokat adhat hozzá.

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Rugalmas készletek az erőforrások kihasználtságának maximalizálásához

Számos vállalkozás és alkalmazás számára elegendő az, ha önálló adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. A [rugalmas készletek](sql-database-elastic-pool.md) megoldást jelentenek erre a problémára. A koncepció egyszerű. A teljesítményadatokat egy készlethez kell hozzárendelni, és nem egy önálló adatbázishoz, hanem a készlet kollektív teljesítmény-erőforrásaiért kell fizetnie, nem pedig egyetlen adatbázis teljesítményére.

   ![rugalmas készletek](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

A rugalmas készletekkel az erőforrásigények ingadozásakor sem kell a az adatbázis teljesítményének fel- és leskálázására koncentrálnia. A rugalmas készletbe helyezett adatbázisok szükség szerint használják fel a teljesítmény-erőforrásokat. A rugalmas készletbe helyezett adatbázisok használják az adatbáziskészlethez hozzárendelt teljesítményt, de nem lépik túl az adatbáziskészlet teljesítménykorlátait, így költségei előre jelezhetők még akkor is, ha az egyéni adatbázis-használat nem jelezhető előre. Ráadásul [adatbázisokat adhat hozzá a készlethez, és távolíthat el a készletből](sql-database-elastic-pool-manage-portal.md), így előre jelezhető költségek mellett néhány adatbázisról több ezer adatbázisra skálázhatja fel alkalmazását. A készlet adatbázisai számára elérhető erőforrások minimális és maximális mennyiségét is szabályozhatja annak érdekében, hogy a készlet egyetlen adatbázisa se használja fel a készlet összes erőforrását, és hogy minden rugalmas készletbe helyezett adatbázis rendelkezzen egy garantált minimális erőforrás-mennyiséggel. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

A szkriptek segítik a rugalmas készletek megfigyelését és méretezését. Erre a [Rugalmas SQL-készlet monitorozása és skálázása az Azure SQL Database-ben a PowerShell használatával](scripts/sql-database-monitor-and-scale-pool-powershell.md) című témakörben talál példát.

> [!IMPORTANT]
> A felügyelt példányok nem támogatják A rugalmas készleteket. Ehelyett a felügyelt példány olyan példány-adatbázisok gyűjteménye, amelyek megosztják a felügyelt példányok erőforrásait.

### <a name="blend-single-databases-with-pooled-databases"></a>Önálló adatbázisok beolvasztása a rugalmas készletbe helyezett adatbázisokba

Az önálló adatbázisokat rugalmas készletekkel vegyítheti, és gyorsan és egyszerűen módosíthatja az önálló adatbázisok és a rugalmas készletek szolgáltatási rétegeit, így alkalmazkodva a helyzethez. Az Azure sokoldalúságának és széles körű alkalmazhatóságának köszönhetően tetszés szerint kombinálhatja az Azure-szolgáltatásokat SQL Database-adatbázisokkal, így kielégíthetők az egyedi, modern alkalmazástervezési igények, növelhető a költség- és erőforrás-hatékonyság, és új üzleti lehetőségek tárhatók fel.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Széles körű figyelési és riasztási funkciók

A Azure SQL Database fejlett monitorozási és hibaelhárítási funkciókat kínál, amelyek segítségével a számítási feladatok jellemzőinek teljes körű betekintést kaphat. Lehetséges, hogy a funkciók és az eszközök a következőképpen vannak kategorizálva:
 - A SQL Server adatbázismotor legújabb verziója által biztosított beépített figyelési képességek, amelyek lehetővé teszik a valós idejű teljesítménnyel kapcsolatos elemzések megkeresését. 
 - Az Azure platformon elérhető, a nagy számú adatbázis-példányok egyszerű figyelésére és a teljesítménnyel kapcsolatos problémák megoldásához segítséget nyújtó hibaelhárítási tanácsokat is megadhat.

A legfontosabb beépített adatbázismotor-figyelési funkciója a [lekérdezési tároló](sql-database-operate-query-store.md) összetevő, amely valós időben rögzíti a lekérdezések teljesítményét, és lehetővé teszi a lehetséges teljesítménybeli problémák és a legfelső szintű erőforrás azonosítását. fogyasztók. Az automatikus hangolás és a javaslatok útmutatást nyújtanak a romlott teljesítményével és hiányzó vagy duplikált indexekkel kapcsolatos lekérdezésekkel kapcsolatban. A Azure SQL Database automatikus hangolásával manuálisan alkalmazhatja a hibákat kijavító szkripteket, vagy engedélyezheti Azure SQL Database a javítás, a tesztelés és az ellenőrzés alkalmazásával, és az eredménytől függően megtarthatja vagy visszaállíthatja a változást. A lekérdezési tároló és az Automatikus hangolási funkciók mellett a standard [DMV és a XEvent](sql-database-monitoring-with-dmvs.md) is használható a munkaterhelés teljesítményének figyelésére.

Az Azure platform biztosítja a [beépített teljesítmény-figyelési](sql-database-performance.md) és [riasztási](sql-database-insights-alerts-portal.md) eszközöket a teljesítmény-minősítésekkel együtt, amelyek segítségével könnyedén figyelheti a több ezer adatbázis állapotát. Ezeknek az eszközöknek a használatával gyorsan elemezheti a fel-és leskálázás hatásait az aktuális vagy előrejelzett teljesítménybeli igények alapján. Az SQL Database emellett [metrikák és diagnosztikai naplók kibocsátásával](sql-database-metrics-diag-logging.md) is képes megkönnyíteni a felügyeletet. Az SQL Database beállítható az erőforrás-használatra, feldolgozókra és munkamenetekre, valamint kapcsolatokra vonatkozó adatok tárolására a következő Azure-erőforrások valamelyikén:

- **Azure Storage**: Nagy mennyiségű telemetria kis költségű archiválásához.
- **Azure Event Hub**: SQL Database telemetria integrálásához az egyéni figyelési megoldással vagy a gyors folyamatokkal.
- **Naplók Azure monitor**: Beépített figyelési megoldás jelentéskészítési, riasztási és enyhítési képességekkel.

    ![architektúra](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Rendelkezésre állás

Egy hagyományos SQL Server környezetben általában (legalább) 2 gépen helyileg be kell állítania az adatmásolatokat (például AlwaysOn rendelkezésre állási csoportokkal vagy feladatátvételi fürtökkel), hogy védelmet nyújtson a szolgáltatás egyetlen gép/összetevő meghibásodása. Ez magas rendelkezésre állást biztosít, de nem nyújt védelmet az adatközpontot megsemmisítő természeti katasztrófák ellen.

A vész-helyreállítás azt feltételezi, hogy egy katasztrofális esemény földrajzilag honosítva van ahhoz, hogy egy másik gép vagy gép legyen, amelyről az adatai egy példánya távol van.  A SQL Server az aszinkron módban futó always on rendelkezésre állási csoportokat használhatja a funkció beszerzéséhez.  A fény gyorsasága általában azt jelenti, hogy az emberek nem szeretnének várni, amíg a replikáció el nem kerül a tranzakció véglegesítése előtt, így lehetséges, hogy a nem tervezett feladatátvételek során adatvesztés történik.

A prémium és az üzleti szempontból kritikus szolgáltatási rétegek adatbázisai már [nagyon hasonlítanak](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) a rendelkezésre állási csoportok szinkronizálásához. Az alacsonyabb szolgáltatási szinten lévő adatbázisok egy [másik, de ezzel egyenértékű mechanizmus](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)használatával biztosítanak redundanciát a tárterületen. Van olyan logika, amely védelmet nyújt egyetlen gép meghibásodása ellen.  Az aktív geo-replikációs szolgáltatás lehetővé teszi, hogy védelmet nyújtson a katasztrófák ellen, amikor az egész régió megsemmisül.

Azure Availability Zones a magas rendelkezésre állással kapcsolatos probléma.  Egy adott régión belül egy adatközpont-összeállítás KIMARADÁSÁVAL próbálkozik.  Ezért szeretné megvédeni az energia vagy a hálózat elvesztését egy épületben. A SQL Azureban ez a különböző replikák különböző rendelkezésre állási zónákban (különböző épületekben, hatékonyan) való elhelyezésével fog működni, és a korábban már nem működik.

Valójában az Azure piacvezető, a Microsoft által felügyelt adatközpontok globális hálózata által biztosított 99,99%-os rendelkezésre állási szolgáltatói szerződés [(SLA)](https://azure.microsoft.com/support/legal/sla/)segít megőrizni az alkalmazást a 24/7-es verzióban. Az Azure platform teljes mértékben felügyeli az összes adatbázist, és garantálja az adatvesztést és az adatmennyiség magas százalékos arányát. Az Azure automatikusan kezeli a javításokat, a biztonsági mentéseket, a replikációt, a hibaészlelést, a háttérben álló lehetséges hardver-, szoftver- vagy hálózati hibákat, a hibajavítások telepítését, a feladatátvételeket, adatbázis-frissítéseket és az egyéb karbantartási műveleteket. A standard szintű rendelkezésre állás a számítási és tárolási rétegek elkülönítését foglalja magában. A prémium szintű rendelkezésre állást úgy érheti el, ha a számítási és tárolási kapacitást egyetlen csomóponton integrálja a teljesítményre, majd a fedezetek alatt az Always On rendelkezésre állási csoportokhoz hasonló technológiát implementál. A Azure SQL Database magas rendelkezésre állási képességeinek teljes körű megvitatását lásd: [SQL Database rendelkezésre állás](sql-database-high-availability.md). Az SQL Database ezen felül olyan beépített funkciókkal szolgálja [az üzletmenet folytonosságát és a globális méretezhetőséget](sql-database-business-continuity.md), mint például a következők:

- **[Automatikus biztonsági mentések](sql-database-automated-backups.md)** :

  A SQL Database automatikusan végrehajtja az Azure SQL Database-adatbázisok teljes, különbözeti és tranzakciós naplójának biztonsági másolatát, így bármikor visszaállíthatja őket. Az önálló adatbázisok és a készletezett adatbázisok esetében beállíthatja, hogy SQL Database az adatbázis teljes biztonsági mentését az Azure Storage-ba tárolja a hosszú távú biztonsági mentés megőrzése érdekében. Felügyelt példányok esetén a biztonsági másolatok hosszú távú megőrzéséhez is elvégezheti a csak másolással készített biztonsági mentést.

- **[Időponthoz tartozó visszaállítások](sql-database-recovery-using-backups.md)** :

  Az összes SQL Database üzembe helyezési lehetőség az Azure SQL Database-adatbázisok automatikus biztonsági mentés megőrzési időszakában bármely időpontra támogatja a helyreállítást.
- **[Aktív földrajzi replikálás](sql-database-active-geo-replication.md)** :

  Az önálló adatbázisok és a készletezett adatbázisok lehetővé teszik legfeljebb négy olvasható másodlagos adatbázis konfigurálását akár azonos, akár globálisan elosztott Azure-adatközpontokban.  Ha például egy SaaS-alkalmazás nagyszámú egyidejű csak olvasási tranzakciót kiszolgáló katalógus-adatbázisra épül, akkor az aktív georeplikáció által növelhető az olvashatóság, és megszűnik az elsődleges adatbázis magas olvasási terhelése miatti szűk keresztmetszet. Felügyelt példányok esetén használja az automatikus feladatátvételi csoportokat.
- **[Automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md)** :

  Az összes SQL Database üzembe helyezési lehetőség lehetővé teszi a feladatátvételi csoportok használatát a magas rendelkezésre állás és a terheléselosztás globális méretekben történő engedélyezéséhez, beleértve az átlátható földrajzi replikálást és a nagyméretű adatbázisok, rugalmas készletek és felügyelt példányok feladatátvételét. A feladatátvételi csoportok lehetővé teszik a globálisan elosztott SaaS-alkalmazások létrehozását a minimális adminisztrációs terheléssel, így az összes összetett figyelési, útválasztási és feladatátvételi folyamat SQL Database.
- **[Zóna – redundáns adatbázisok](sql-database-high-availability.md)** :

  SQL Database lehetővé teszi prémium vagy üzleti szempontból kritikus adatbázisok vagy rugalmas készletek kiépítését több rendelkezésre állási zónában. Mivel ezek az adatbázisok és a rugalmas készletek több redundáns replikával rendelkeznek a magas rendelkezésre állás érdekében, e replikák több rendelkezésre állási zónában történő elhelyezése nagyobb rugalmasságot biztosít, beleértve az adatközpont méretezési hibáiból az adatvesztés nélküli, automatikus helyreállítás lehetőségét is.

## <a name="built-in-intelligence"></a>Beépített intelligencia

Az SQL Database-zel beépített intelligencia is jár, amely segít jelentősen mérsékelni az adatbázisok működtetésének és kezelésének költségét, ugyanakkor fokozni az alkalmazás teljesítményét és biztonságát. Az állandóan ügyfelek millióinak számítási feladatait futtató SQL Database hatalmas mennyiségű telemetriai adatot dolgoz fel, végig szem előtt tartva az ügyfelek adatainak teljes védelmét. A telemetriai adatokat szüntelenül különféle algoritmusok értékelik, hogy a szolgáltatás tanulhasson belőlük és idomulhasson az Ön alkalmazásához. Az elemzés alapján a szolgáltatás az adott számítási feladathoz igazított teljesítmény-javító javaslatokat tartalmaz.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatikus teljesítményfigyelés és finomhangolás

Az SQL Database részletes betekintést nyújt a figyelést kívánó lekérdezésekbe. Az SQL Database megismeri az adatbázisaira jellemző mintázatokat, és segít az adatbázis-sémáit a számítási feladathoz igazítani. Az SQL Database [javaslatokat tesz a teljesítmény finomhangolására](sql-database-advisor.md), és Ön eldöntheti, hogy alkalmazza-e a javasolt finomhangolási műveleteket.

Az adatbázisok folyamatos figyelése azonban nehéz és unalmas feladat, különösen sok adatbázis kezelésekor. Az [Intelligent Insights](sql-database-intelligent-insights.md) elvégzi Ön helyett ezt a munkát. Automatikusan figyeli az SQL Database teljesítményét, és teljesítménycsökkenési hibák előfordulása esetén értesítést küld, azonosítja a probléma gyökerét, és ahol lehetséges, teljesítményjavítási javaslatokkal is szolgál.

Nagyszámú adatbázis hatékony kezelése már az SQL Database és az Azure Portal által kínált valamennyi eszköz és jelentés birtokában is lehetetlennek bizonyulhat. Az adatbázis személyes figyelése és finomhangolása helyett érdemes megfontolni, hogy a figyelési és hangolási műveletek egy részét az SQL Database [automatikus finomhangoló](sql-database-automatic-tuning.md) funkciójára bízza. A SQL Database automatikusan alkalmazza a javaslatokat, a teszteket és ellenőrzi az egyes hangolási műveleteket, így biztosítva a teljesítmény javítását. Így SQL Database automatikusan alkalmazkodik a számítási feladatokhoz egy ellenőrzött és biztonságos módon. Az automatikus finomhangolás azt jelenti, hogy ha az adatbázis egyes módosítások előtt és után gondosan mért teljesítményének összehasonlítása nem mutat javulást, akkor a módosító művelet visszavonásra kerül.

Jelenleg számos partnerünk, akik [több-bérlős SaaS-alkalmazásokat](sql-database-design-patterns-multi-tenancy-saas-applications.md) futtatnak SQL Database-en, az automatikus teljesítményhangolásra bízza az alkalmazása mindig stabil és kiszámítható működését. Ez a funkció nagy mértékben csökkenti számukra egy éjszaka közepén fellépő teljesítmény-visszaesés kockázatát. Ráadásul, mivel ügyfélkörük egy része szintén használ SQL Servert, az SQL Database által kínált azonos indexelési ajánlásokat alkalmazva nyújthatnak segítséget ezen ügyfeleknek.

Az [SQL Database](sql-database-automatic-tuning.md) két szempont alapján képes automatikus finomhangolást végezni:

- **Automatikus indexelés kezelése**: Azonosítja az adatbázisba felvenni kívánt indexeket, valamint azokat az indexeket, amelyeket el kell távolítani.
- **Automatikus terv javítása**: Azonosítja a problémás terveket és javítja az SQL-terv teljesítményével kapcsolatos problémákat (hamarosan elérhető, SQL Server 2017).

### <a name="adaptive-query-processing"></a>Adaptív lekérdezés-feldolgozás

Emellett az [adaptív lekérdezésfeldolgozási](/sql/relational-databases/performance/intelligent-query-processing) szolgáltatáscsalád is elérhető vált az SQL Database-ben, beleértve a több utasításból álló, táblaértékű függvények kihagyásos végrehajtását, a kötegelt módú memóriaengedély visszajelzését, valamint a kötegelt módú adaptív illesztéseket. Ezen adaptív lekérdezésfeldolgozó funkciók mindegyike hasonló technikával „tanul és alkalmazkodik”, ezzel is hozzájárulva a korábban nyomon követhetetlen lekérdezés-optimalizálási gondokra visszavezethető teljesítményproblémák megoldásához.

## <a name="advanced-security-and-compliance"></a>Magas szintű biztonság és megfelelőség

Az SQl Database számos [beépített biztonsági és megfelelőségi szolgáltatást](sql-database-security-overview.md) kínálva járul hozzá, hogy az Ön alkalmazása eleget tegyen a különféle biztonsági és megfelelőségi elvárásoknak.

> [!IMPORTANT]
> Azure SQL Database (az összes központi telepítési lehetőség), számos megfelelőségi szabványnak megfelelő minősítéssel rendelkezik. További információkért tekintse meg a [Microsoft Azure adatvédelmi központot](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , ahol megtalálhatja a SQL Database megfelelőségi minősítések legfrissebb listáját.

### <a name="advance-threat-protection"></a>Fejlett fenyegetésvédelem

A speciális adatbiztonság a speciális SQL-alapú biztonsági képességek egységes csomagja. Lehetőséget nyújt a bizalmas adatok felderítésére és titkossá minősítésére, az adatbázis biztonsági réseinek kezelésére, továbbá az adatbázisra nézve fenyegetést jelentő rendellenes tevékenységek észlelésére. Segítségével egyetlen helyen engedélyezhetők és kezelhetők ezek a képességek.

- [Adatfelderítési & besorolása](sql-database-data-discovery-and-classification.md):

  Ez a funkció (jelenleg előzetes verzióban érhető el) olyan képességeket biztosít, amelyekkel a Azure SQL Database az adatbázisaiban található bizalmas adatok védelme, osztályozása, címkézése &. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül.
- [Sebezhetőségi felmérés](sql-vulnerability-assessment.md):

  A szolgáltatás felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket. Áttekinthetővé teszi az adatbázisok biztonsági állapotát, és végrehajtható lépéseket kínál a biztonsági problémák megoldására, valamint az adatbázisok védelmének fejlesztésére.
- [Veszélyforrások észlelése](sql-database-threat-detection.md):

  Ez a szolgáltatás rendellenes tevékenységeket észlel, amelyekben szokatlan és potenciálisan ártalmas kísérleteket jelez az adatbázis eléréséhez vagy kiaknázásához. A szolgáltatás folyamatosan figyeli az adatbázisokat, és azonnal értesíti a felhasználót a gyanús tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. A veszélyforrások észlelésével kapcsolatos riasztások részletesen ismertetik a gyanús tevékenységeket, és javaslatot tesznek a fenyegetés kivizsgálására és enyhítésére.

### <a name="auditing-for-compliance-and-security"></a>Naplózás a megfelelőség és biztonság szolgálatában

A [naplózás](sql-database-auditing.md) nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókban lévő naplóba írja azokat. A naplózás segíthet a jogszabályi megfelelőség fenntartásában és az adatbázison végzett tevékenység megértésében, valamint az esetleg üzleti veszélyeket vagy biztonsági problémákat jelző rendellenességek feltárásában.

### <a name="data-encryption"></a>Adattitkosítás

A SQL Database biztosítja az adatok védelmét azáltal, hogy a mozgásban lévő adatok titkosítását biztosítja a [Transport Layer Security](https://support.microsoft.com/kb/3135244)szolgáltatással, a REST-adatok [átlátható adattitkosítással](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)és a használatban lévő adatokhoz, és [mindig titkosított](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

Az SQL Database az [Azure Active Directory-integráció](sql-database-aad-authentication.md) által lehetővé teszi adatbázis-felhasználók és más Microsoft-szolgáltatások identitásainak központi kezelését. Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. Az Azure Active Directory a [többtényezős hitelesítés](sql-database-ssms-mfa-authentication.md) (MFA) támogatásával javítja az adatok és alkalmazások biztonságát, miközben támogatja az egyszeri bejelentkezést.

### <a name="compliance-certification"></a>Megfelelőségi tanúsítvány

Az SQL Database rendszeres ellenőrzéseken vesz részt és számos megfelelőségi szabványnak tesz eleget tanúsított módon. További információkért tekintse meg a [Microsoft Azure adatvédelmi központot](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , ahol megtalálhatja a SQL Database megfelelőségi minősítések legfrissebb listáját.

## <a name="easy-to-use-tools"></a>Egyszerűen használható eszközök

Az SQL Database egyszerűbbé és hatékonyabbá teszi az alkalmazások létrehozását és karbantartását. Az SQL Database lehetővé teszi, hogy arra koncentrálhasson, amit legjobban tud: kiváló alkalmazások készítésére. Az SQL Database-ben a már meglévő eszközeivel és szakértelmével dolgozhat és fejleszthet.

- **[A Azure Portal](https://portal.azure.com/)** :

  Web-alapú alkalmazás az összes Azure-szolgáltatás kezeléséhez
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)** :

  Ingyenes, letölthető ügyfélalkalmazás bármely SQL-infrastruktúra kezelésére, SQL Serverról SQL Database
- **[SQL Server Data Tools a Visual Studióban](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)** :

  Ingyenes, letölthető ügyfélalkalmazás, amely SQL Server a kapcsolódó adatbázisok, az Azure SQL Database-adatbázisok, az integrációs szolgáltatások csomagjai, a Analysis Services adatmodellek és a Reporting Services jelentéseinek fejlesztésére.
- **[Visual Studio Code](https://code.visualstudio.com/docs)** :

  Ingyenes, letölthető, nyílt forráskódú, a Windows, macOS és Linux rendszerekhez használható, bővítményeket támogató, az [MSSQL bővítményt](https://aka.ms/mssql-marketplace) , beleértve a Microsoft SQL Server, a Azure SQL Database és a SQL Data Warehouse lekérdezését.

Az SQL Database támogatja a Python, Java, Node.js, PHP, Ruby, és .NET alkalmazásfejlesztést a MacOS, Linux, és Windows rendszeren. Az SQL Database az SQL Serverrel azonos [adatkapcsolattárakat](sql-database-libraries.md) támogat.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions-faq"></a>SQL Database gyakori kérdések (GYIK)

### <a name="what-is-the-current-version-of-sql-database"></a>A SQL Database aktuális verziója

SQL Database jelenlegi verziója a V12-es verzió. A v11 verziója ki lett vonva.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Szabályozható a javítási állásidő

Nem. A javítások hatása általában nem észlelhető, ha az alkalmazásban [újrapróbálkozási logikát alkalmaz](sql-database-develop-overview.md#resiliency) . Az Azure SQL Database-ben tervezett karbantartási események előkészítésével kapcsolatos további információkért lásd: [Az Azure karbantartási eseményeinek tervezése Azure SQL Databaseban](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Azure Hybrid Benefit kérdések

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Vannak olyan kettős használati jogosultságok, amelyek a Azure Hybrid Benefit SQL Server

A licencek 180 napos kettős használati jogosultsággal rendelkeznek, így biztosítható, hogy a Migrálás zökkenőmentesen fusson. Az 180 napos időszak után a SQL Server licenc csak a felhőben használható SQL Database, és nem rendelkezik kettős használati jogokkal a helyszínen és a felhőben.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Miben különbözik a SQL Server Azure Hybrid Benefit a licencek mobilitásával

Napjainkban a licencek mobilitási előnyeit a frissítési garanciával rendelkező ügyfelek SQL Server biztosítjuk, amelyek lehetővé teszik a licencek újbóli hozzárendelését harmadik felek megosztott kiszolgálóira. Ezt az előnyt az Azure IaaS és az AWS EC2 használhatja.
A SQL Server Azure Hybrid Benefit két kulcsfontosságú területen különbözik a licenc mobilitástól:

- Gazdasági előnyöket biztosít a nagyvirtualizált munkaterhelések Azure-ba való áthelyezéséhez. Az SQL EE-ügyfelek 4 maggal rendelkezhetnek az Azure-ban a általános célú SKU-ban minden olyan mag számára, amely a helyszínen található a nagyvirtualizált alkalmazások számára. A licencek mobilitása nem teszi lehetővé a virtualizált számítási feladatok felhőbe való áthelyezésének speciális költségeit.
- A szolgáltatás az Azure-ban (SQL Database felügyelt példányon) található, a SQL Server helyszíni

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Milyen jogokkal rendelkezik a Azure Hybrid Benefit SQL Server

SQL Database ügyfeleknek a következő jogosultságokkal kell rendelkezniük a SQL Server Azure Hybrid Benefithoz:

|Licenc-lábnyom|Mire Azure Hybrid Benefit a SQL Server?|
|---|---|
|SQL Server Enterprise Edition Core-ügyfelek a SA-val|<li>általános célú vagy üzletileg kritikus SKU-ra is fizethet alapdíj</li><br><li>1 mag helyszíni = 4 mag általános célú SKU-ban</li><br><li>1 mag a helyszínen = 1 mag üzletileg kritikus SKU-ban</li>|
|SQL Server Standard Edition Core-ügyfelek a SA-val|<li>Csak általános célú SKU-ra fizethet alapdíj</li><br><li>1 mag a helyszínen = 1 mag általános célú SKU-ban</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Kapcsolatfelvétel az SQL Server műszaki csoportjával

- [DBA stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Adatbázis-felügyeleti kérdések megkérdezése
- [Stack overflow](https://stackoverflow.com/questions/tagged/sql-server): A fejlesztési kérdések megkérdezése
- [MSDN-fórumok](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Forduljon a technikai kérdésekhez
- [Visszajelzés](https://aka.ms/sqlfeedback): Hibák jelentése és szolgáltatás kérése
- [Reddit](https://www.reddit.com/r/SQLServer/): A SQL Server megvitatása

## <a name="next-steps"></a>További lépések

- Tekintse meg az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/sql-database/) az önálló adatbázisok és a rugalmas készletek költségeinek összehasonlításáért és árkalkulációjáért.
- Tekintse meg ezeket a rövid útmutatókat a kezdéshez:

  - [SQL Database létrehozása az Azure Portalon](sql-database-single-database-get-started.md)  
  - [SQL Database létrehozása az Azure CLI-vel](sql-database-get-started-cli.md)
  - [SQL Database létrehozása PowerShell használatával](sql-database-get-started-powershell.md)

- Több Azure CLI és PowerShell-mintát talál itt:
  - [Azure CLI-minták az SQL Database-hez](sql-database-cli-samples.md)
  - [Azure PowerShell-minták az SQL Database-hez](sql-database-powershell-samples.md)

 - További információ a bejelentett új képességekről: 
   - **[Azure-útiterv SQL Database](https://azure.microsoft.com/roadmap/?category=databases)** – itt találhatja meg az újdonságokat és a jövőt.
  - **[Azure SQL Database blog](https://azure.microsoft.com/blog/topics/database)** – a SQL Database hírekkel és szolgáltatásokkal kapcsolatos SQL Server termék csapat tagjainak blogja.

