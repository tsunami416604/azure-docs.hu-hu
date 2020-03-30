---
title: Mi az Azure SQL Database szolgáltatás?
description: 'Bevezetés az SQL Database szolgáltatásba: a Microsoft relációs adatbázis-kezelő rendszerének (RDBMS) technikai részletei és képességei a felhőben.'
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
ms.openlocfilehash: 209b4136678e6f04666b4a2b6180f4768bf6afc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500827"
---
# <a name="what-is-the-azure-sql-database-service"></a>Mi az Azure SQL Database szolgáltatás?

Az Azure SQL Database egy általános célú relációs adatbázis, amelyet felügyelt szolgáltatásként biztosítanak. Ezzel magas rendelkezésre állású és nagy teljesítményű adattárolási réteget hozhat létre az Azure-beli alkalmazásokhoz és megoldásokhoz. Az SQL Database számos modern felhőalapú alkalmazás számára lehet megfelelő választás, mivel lehetővé teszi a relációs adatok és [a nem relációs struktúrák](sql-database-multi-model-features.md), például grafikonok, JSON, térbeli és XML feldolgozását.

Ez alapján a legújabb stabil változata a [Microsoft SQL Server adatbázis-motor](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Speciális lekérdezésfeldolgozási szolgáltatásokat is használhat, például [nagy teljesítményű memóriabeli technológiákat](sql-database-in-memory.md) és [intelligens lekérdezésfeldolgozást.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json) Valójában az SQL Server legújabb funkciói először az SQL Database, majd maga az SQL Server számára kerülnek kiadásra. A legújabb SQL Server-képességeket kapja, amelyek nem rendelkeznek többletterheléssel a javításhoz vagy a frissítéshez, több millió adatbázison tesztelve. 

Az SQL Database lehetővé teszi a teljesítmény egyszerű meghatározását és méretezését két különböző vásárlási modellen belül: egy [virtuálismag-alapú vásárlási modellen](sql-database-service-tiers-vcore.md) és egy [DTU-alapú vásárlási modellen](sql-database-service-tiers-dtu.md)belül. Az SQL Database egy teljes körűen felügyelt szolgáltatás, amely beépített magas rendelkezésre állású, biztonsági mentések és egyéb gyakori karbantartási műveletek. A Microsoft kezeli az SQL és az operációs rendszer kódjának minden javítását és frissítését. Nem kell kezelnie az alapul szolgáló infrastruktúrát.

> [!NOTE]
> A vonatkozó kifejezéseket és azok definícióit az [SQL Database kifejezésszószában található.](sql-database-glossary-terms.md)

## <a name="deployment-models"></a>Üzembe helyezési modellek

Az Azure SQL Database az alábbi lehetőségeket kínálja az Azure SQL-adatbázisok üzembe helyezésére:

![Telepítési lehetőségek diagramja](./media/sql-database-technical-overview/deployment-options.png)

- [Az egyetlen adatbázis](sql-database-single-database.md) egy teljes körűen felügyelt, elkülönített adatbázist jelent. Ezt a beállítást akkor használhatja, ha modern felhőalapú alkalmazásokkal és mikroszolgáltatásokkal rendelkezik, amelyekegyetlen megbízható adatforrást igényelnek. Egyetlen adatbázis hasonlít a Microsoft SQL Server Database Engine [egy tartalmazott](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) [adatbázisához.](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)
- [A felügyelt példány](sql-database-managed-instance.md) a [Microsoft SQL Server Adatbázismotor](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)teljes körűen felügyelt példánya. Ez tartalmaz egy sor adatbázisok, amelyek együtt használhatók. Ezzel a beállítással egyszerűen átlehet migrálása a helyszíni SQL Server-adatbázisok az Azure-felhőbe, és az alkalmazások, amelyek nek szükségük van az SQL Server Database Engine által biztosított adatbázis-szolgáltatások használatához.
- [Rugalmas készlet](sql-database-elastic-pool.md) egyetlen [adatbázisok](sql-database-single-database.md) gyűjteménye, amely nek közös erőforrások, például a CPU vagy a memória. Egyetlen adatbázisok áthelyezhetők, és ki egy rugalmas készlet.

> [!IMPORTANT]
> Az SQL Database és az SQL Server szolgáltatáskülönbségeinek, valamint az Azure SQL Database különböző telepítési beállításai közötti különbségeknek a megértéséhez olvassa el az [SQL Database szolgáltatásai című témakört.](sql-database-features.md)

Az SQL Database kiszámítható teljesítményt nyújt több erőforrástípussal, szolgáltatásréteggel és számítási mérettel. Dinamikus méretezhetőséget biztosít állásidő nélkül, beépített intelligens optimalizálást, globális méretezhetőséget és rendelkezésre állást, valamint speciális biztonsági beállításokat biztosít. Ezek a képességek lehetővé teszik, hogy a virtuális gépek és az infrastruktúra kezelése helyett a gyors alkalmazásfejlesztésre és a piacra jutási idő felgyorsítására összpontosítson. Az SQL Database szolgáltatás jelenleg 38 adatközpontban található világszerte, így az adatbázist egy közeli adatközpontban futtathatja.

## <a name="scalable-performance-and-pools"></a>Méretezhető teljesítmény és készletek

Megadhatja a hozzárendelt erőforrások mennyiségét. 
- Az egyes adatbázisok, minden adatbázis elkülönítve van a többitől, és hordozható. Mindegyik saját garantált mennyiségű számítási, memória- és tárolási erőforrással rendelkezik. Az adatbázishoz rendelt erőforrások mennyisége az adott adatbázishoz van rendelve, és nincs megosztva más azure-beli adatbázisokkal. Az egyes [adatbázis-erőforrásokat](sql-database-single-database-scale.md) dinamikusan skálázhatja fel-le. Az egyetlen adatbázis-beállítás különböző számítási, memória- és tárolási erőforrásokat biztosít a különböző igényekhez. Például 1-80 virtuális mag, vagy 32 GB-4 TB.For example, you can get 1 to 80 vCores, or 32 GB to 4 TB. Az egyetlen adatbázis [nagy méretű szolgáltatási szintje](sql-database-service-tier-hyperscale.md) lehetővé teszi, hogy 100 TB-ra skálázható, gyors biztonsági mentési és visszaállítási képességekkel.
- Rugalmas készletek, erőforrásokat rendelhet, amelyek a készlet ben található összes adatbázis megosztott. Létrehozhat egy új adatbázist, vagy áthelyezheti a meglévő egyetlen adatbázisokat egy erőforráskészletbe, hogy maximalizálja az erőforrások felhasználását és pénzt takarítson meg. Ez a beállítás azt is lehetővé teszi, hogy dinamikusan [skálázhatja a rugalmas készlet erőforrásokat](sql-database-elastic-pool-scale.md) fel-le.
- Felügyelt példányok, minden felügyelt példány elkülönítve van a többi példány garantált erőforrásokkal. Egy felügyelt példányon belül a példány adatbázisok erőforrások egy készletét osztják meg. A felügyelt példány erőforrások dinamikus [skálázható](sql-database-managed-instance-resource-limits.md) fel-le.

Az első alkalmazást egy kis méretű, egyetlen adatbázisra hozhatja létre alacsony havi költséggel az általános célú szolgáltatási szinten. Ezután bármikor manuálisan vagy programozott módon módosíthatja a szolgáltatási szintet az üzleti legkritikusabb szolgáltatási szintre, hogy megfeleljen a megoldás igényeinek. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak azokat a forrásokat kell kifizetnie, amelyekre szüksége van, amikor szüksége van rájuk.

*A dinamikus méretezhetőség* eltér az *automatikus skálázástól.* Automatikus skálázás esetén a szolgáltatás adott feltételek alapján, automatikusan méretez, míg a dinamikus méretezhetőség lehetővé teszi a manuális méretezést, amely nem jár állásidővel. Az egyetlen adatbázis beállítás támogatja a manuális dinamikus méretezhetőséget, de az automatikus skálázást nem. Ha automatikus megoldást keres, érdemes megfontolni a rugalmas készletek használatát, amely lehetővé teszi, hogy az adatbázisok osztozzanak egy készlet erőforrásain az egyes adatbázisok egyedi igényei alapján. Egy másik lehetőség olyan parancsfájlok használata, amelyek segítségével automatizálható a méretezhetőség egyetlen adatbázis esetében. Például a [PowerShell használata egyetlen adatbázis figyelése és méretezése](scripts/sql-database-monitor-and-scale-database-powershell.md)című témakörben található.

### <a name="purchasing-models"></a>Vásárlási modellek

Az SQL Database a következő vásárlási modelleket kínálja:
- A [virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) segítségével kiválaszthatja a virtuális magok számát, a memória mennyiségét, valamint a tárhely mennyiségét és sebességét. A virtuálismag-alapú vásárlási modell lehetővé teszi az [Azure Hybrid Benefit for SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) használatát is, hogy költségmegtakarítást eredményezzön. Az Azure Hybrid Benefitről további információt a cikk későbbi, "Gyakori kérdések" című részében talál.
- A [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) számítási, memória- és I/O-erőforrások keverékét kínálja három szolgáltatási szinten, hogy támogassa a könnyű és nehéz adatbázis-munkaterheléseket. Az egyes rétegeken belüli számítási méretek ezeknek az erőforrásoknak a különböző kombinációját biztosítják, amelyekhez további tárolási erőforrásokat adhat hozzá.
- A [kiszolgáló nélküli modell](sql-database-serverless.md) automatikusan skálázza a számítási feladatok igénye és a másodpercenként felhasznált számítási mennyiség alapján a számítási adatokat. A kiszolgáló nélküli számítási szint is automatikusan szünetelteti az adatbázisokat inaktív időszakokban, amikor csak a tárolás számlázása, és automatikusan folytatja az adatbázisokat, amikor a tevékenység visszatér.

### <a name="service-tiers"></a>Szolgáltatásszintek

Az Azure SQL Database három szolgáltatási réteget kínál, amelyeket különböző típusú alkalmazásokhoz terveztek:
- [Általános cél/Standard](sql-database-service-tier-general-purpose.md) szolgáltatási szint közös számítási feladatokhoz tervezve. Költségvetés-orientált, kiegyensúlyozott számítási és tárolási lehetőségeket kínál.
- [Üzleti kritikus/prémium szolgáltatási](sql-database-service-tier-business-critical.md) szint, amelyet magas tranzakciós aránnyal és a legalacsonyabb késleltetésű I/O-val rendelkező OLTP-alkalmazásokhoz terveztek. A hibák kal szembeni legnagyobb rugalmasságot kínálja több elkülönített replikák használatával.
- [A](sql-database-service-tier-hyperscale.md) nagy MÉRETŰ szolgáltatási szint nagyon nagy OLTP-adatbázishoz, valamint a tárolás és a számítási skálázás gördülékeny méretezéséhez tervezett nagy kapacitású.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Rugalmas készletek az erőforrások kihasználtságának maximalizálásához

Számos vállalkozás és alkalmazás számára elegendő az, ha önálló adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. A kiszámíthatatlan használati minták megnehezíthetik a költségek és az üzleti modell kezelését. A [rugalmas készletek](sql-database-elastic-pool.md) megoldást jelentenek erre a problémára. A teljesítményerőforrásokat nem egy különálló adatbázishoz, hanem egy készlethez rendeli hozzá. A készlet közös teljesítményerőforrásaiért kell fizetni, nem pedig az egyetlen adatbázis teljesítményéért.

   ![Rugalmas készleteket megjelenítő ábra alapszintű, standard és prémium kiadásokban](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Rugalmas készletek, nem kell összpontosítania az adatbázis teljesítményének tárcsázása fel-le, mint az erőforrások iránti kereslet ingadozik. A rugalmas készletbe helyezett adatbázisok szükség szerint használják fel a teljesítmény-erőforrásokat. A készletezésű adatbázisok fogyasztják, de nem lépik túl a készlet korlátait, így a költségek kiszámíthatóak maradnak, még akkor is, ha az egyes adatbázis-használat nem.

Hozzáadhat [és eltávolíthat adatbázisokat a készletbe,](sql-database-elastic-pool-manage-portal.md)az alkalmazást néhány adatbázisról több ezerre skálázhatja, mindezt egy általad szabályzott költségkereten belül. Azt is szabályozhatja, hogy a készletben lévő adatbázisok számára elérhető minimális és maximális erőforrások at, győződjön meg arról, hogy a készletegyetlen adatbázisa sem használja az összes készleterőforrást, és hogy minden készletezett adatbázis garantált minimális erőforrás-összeggel rendelkezik. Ha többet szeretne megtudni a rugalmas készleteket használó szoftverszolgáltatás-alkalmazások tervezési mintáiról, olvassa el a [Tervezési minták az SQL Database több-bérlős SaaS-alkalmazások tervezési mintáit.](sql-database-design-patterns-multi-tenancy-saas-applications.md)

A szkriptek segítik a rugalmas készletek megfigyelését és méretezését. Például a [PowerShell használata sql rugalmas készlet figyelésére és méretezésére az Azure SQL Database-ben című témakörben található.](scripts/sql-database-monitor-and-scale-pool-powershell.md)

> [!IMPORTANT]
> A felügyelt példány nem támogatja a rugalmas készletek. A felügyelt példány inkább a felügyelt példány-erőforrásokat megosztó példány-adatbázisok gyűjteménye.

### <a name="blend-single-databases-with-pooled-databases"></a>Önálló adatbázisok beolvasztása a rugalmas készletbe helyezett adatbázisokba

Az egyes adatbázisok at rugalmas készletekkel keverheti, és módosíthatja az egyes adatbázisok és rugalmas készletek szolgáltatási szintjeit a helyzethez való alkalmazkodás érdekében. Más Azure-szolgáltatásokat is keverhet és párosíthat az SQL Database-lel, hogy megfeleljen az egyedi alkalmazástervezési igényeknek, megnövelhesse a költségeket és az erőforrások hatékonyságát, és új üzleti lehetőségeket szabadíthat fel.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Széles körű figyelési és riasztási funkciók

Az Azure SQL Database speciális figyelési és hibaelhárítási funkciókat biztosít, amelyek segítségével mélyebb betekintést nyerhet a számítási feladatok jellemzőibe. Ezek a funkciók és eszközök a következők:
 - Az SQL Server Database Engine legújabb verziója által biztosított beépített figyelési lehetőségek. Lehetővé teszik, hogy valós idejű teljesítményelemzéseket találjon. 
 - Az Azure által biztosított PaaS-figyelési képességek, amelyek lehetővé teszik számos adatbázis-példány figyelését és hibaelhárítását.

[A Query Store,](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store)amely egy beépített SQL Server figyelési szolgáltatás, valós időben rögzíti a lekérdezések teljesítményét, és lehetővé teszi a lehetséges teljesítményproblémák és a legfontosabb erőforrás-fogyasztók azonosítását. Az automatikus hangolás és javaslatok tanácsot adnak a regressziós teljesítménygel és a hiányzó vagy duplikált indexekkel kapcsolatos lekérdezésekkel kapcsolatban. Az SQL Database automatikus hangolása lehetővé teszi, hogy manuálisan alkalmazza a problémákat megoldó parancsfájlokat, vagy hagyja, hogy az SQL Database alkalmazza a javítást. Az SQL Database tesztelheti és ellenőrizheti, hogy a javítás nyújt-e némi előnyt, és az eredménytől függően megtarthatja vagy visszaállíthatja a módosítást. A Query Store és az automatikus hangolási képességek mellett a szabványos [DMV-k és az XEvent](sql-database-monitoring-with-dmvs.md) segítségével is figyelheti a számítási feladatok teljesítményét.

Az Azure [beépített teljesítményfigyelő](sql-database-performance-guidance.md) és [riasztási](sql-database-insights-alerts-portal.md) eszközöket és teljesítményminősítésekkel kombinálva több ezer adatbázis állapotát figyelheti. Ezekkel az eszközökkel gyorsan felmérheti a felfelé vagy lefelé történő skálázás hatását a jelenlegi vagy a tervezett teljesítményigények alapján. Az SQL Database emellett [metrikák és diagnosztikai naplók kibocsátásával](sql-database-metrics-diag-logging.md) is képes megkönnyíteni a felügyeletet. Az SQL Database beállítható az erőforrás-használatra, feldolgozókra és munkamenetekre, valamint kapcsolatokra vonatkozó adatok tárolására a következő Azure-erőforrások valamelyikén:

- **Azure Storage:** Nagy mennyiségű telemetriai adatok kis áron archiválására.
- **Azure Event Hubs:** Az SQL Database telemetriai adatok integrálása az egyéni figyelési megoldás sal vagy a forró folyamatok.
- **Azure Monitor naplók:** A beépített figyelési megoldás jelentéskészítési, riasztási és enyhítő képességekkel.

![Az Azure figyelési architektúrájának diagramja](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Rendelkezésre állás

Hagyományos SQL Server környezetben általában legalább két gép van beállítva helyileg. Ezek a gépek pontos, szinkron módon karbantartott másolatokkal rendelkeznek az adatokról, hogy megvédjék őket egyetlen gép vagy alkatrész meghibásodása ellen. Ez a környezet magas rendelkezésre állást biztosít, de nem nyújt védelmet az adatközpontot tönkretevő természeti katasztrófák ellen.

Vész-helyreállítási feltételezi, hogy egy katasztrofális esemény földrajzilag elég lokalizált ahhoz, hogy egy másik gép vagy gépkészlet az adatok egy példányát messze. Az SQL Server ben az Aszinkron módban futó Always On Availability Groups használatával szerezheti be ezt a funkciót. Az emberek gyakran nem szeretnék megvárni, hogy a replikáció ilyen messze történjen a tranzakció véglegesítése előtt, így nem tervezett feladatátvételek esetén adatvesztésre lehet lehetőséget.

A prémium szintű és az üzleti legkritikusabb szolgáltatási szinteken lévő adatbázisok már [valami nagyon hasonlót tesznek](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) egy rendelkezésre állási csoport szinkronizálásához. Az alacsonyabb szolgáltatási szinteken lévő adatbázisok redundanciát biztosítanak a tároláson keresztül egy [másik, de egyenértékű mechanizmus](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)használatával. A beépített logika segít az egyetlen gépmeghibásodás elleni védelemben. Az aktív georeplikációs szolgáltatás lehetővé teszi a katasztrófa elleni védelmet, ahol egy egész régió megsemmisül.

Az Azure rendelkezésre állási zónák egyetlen régión belüli egyetlen adatközpont-épület kimaradása ellen próbál védelmet nyújt. Ez segít megvédeni a veszteség et vagy hálózati egy épület. Az SQL Database-ben a különböző replikákat különböző rendelkezésre állási zónákba helyezi (különböző épületek, hatékonyan).

Az Azure szolgáltatásiszint-szerződése [(SLA)](https://azure.microsoft.com/support/legal/sla/) a Microsoft által felügyelt adatközpontok globális hálózatával működik, és a hét minden nap 24 órájában üzemel. Az Azure platform teljes mértékben kezeli az összes adatbázist, és nem garantálja az adatvesztést és az adatok rendelkezésre állásának nagy százalékát. Az Azure automatikusan kezeli a javításokat, a biztonsági mentéseket, a replikációt, a hibaészlelést, az alapul szolgáló potenciális hardver-, szoftver- vagy hálózati hibákat, a hibajavítások, a feladatátvételek, az adatbázis-frissítések és egyéb karbantartási feladatok telepítését. A standard szintű rendelkezésre állás a számítási és tárolási rétegek elkülönítését foglalja magában. A prémium szintű rendelkezésre állás a számítási és tárolási adatok egyetlen csomóponton történő integrálásával érhető el a teljesítmény érdekében, majd az Always On Availability Groups technológiához hasonló technológia implementálásával érhető el. Az Azure SQL Database magas rendelkezésre állási lehetőségeiről az [SQL Database elérhetősége című](sql-database-high-availability.md)témakörben található. 

Emellett az SQL Database beépített [üzletmenet-folytonossági és globális méretezhetőségi](sql-database-business-continuity.md) funkciókat is biztosít. Ezek a következők:

- [Automatikus biztonsági mentések:](sql-database-automated-backups.md)

  Az SQL Database automatikusan elvégzi az SQL-adatbázisok teljes, különbözeti és tranzakciós naplóinak biztonsági mentését, hogy bármikor visszaállítható. Az egyes adatbázisok és a készletbe helyezett adatbázisok, beállíthatja az SQL Database teljes adatbázis-biztonsági mentések az Azure Storage hosszú távú biztonsági mentés megőrzése. Felügyelt példányok esetén is végezhet csak másolási biztonsági mentést a hosszú távú biztonsági mentés megőrzése.

- [Időponthoz-visszaáll:](sql-database-recovery-using-backups.md)

  Az SQL Database összes telepítési lehetősége támogatja a helyreállítást bármely SQL-adatbázis automatikus biztonsági mentési megőrzési időszakán belül bármikor.
- [Aktív georeplikáció:](sql-database-active-geo-replication.md)

  Az egyetlen adatbázis- és készletező adatbázisok beállításai lehetővé teszik, hogy akár négy olvasható másodlagos adatbázist konfiguráljon ugyanazon vagy globálisan elosztott Azure-adatközpontokban. Ha például egy SaaS-alkalmazás egy katalógus-adatbázis, amely nagy mennyiségű egyidejű írásvédett tranzakciók, használja az aktív georeplikáció, hogy globális olvasási skálázás. Ez eltávolítja az elsődleges, az olvasási számítási feladatok miatt szűk keresztmetszeteket. Felügyelt példányok esetén használjon automatikus feladatátvételi csoportokat.
- [Automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md):

  Az SQL Database összes telepítési lehetősége lehetővé teszi feladatátvételi csoportok használatát a magas rendelkezésre állás és a terheléselosztás globális szintű engedélyezéséhez. Ez magában foglalja az átlátszó georeplikációt és az adatbázisok, rugalmas készletek és felügyelt példányok nagy készleteinek feladatátvételét. A feladatátvételi csoportok lehetővé teszik a globálisan elosztott SaaS-alkalmazások létrehozását, minimális adminisztrációs többletterheléssel. Ez az SQL Database összes komplex figyelését, útválasztását és feladatátvételi vezénylését hátrahagyja.
- [Zónaredundáns adatbázisok:](sql-database-high-availability.md)

  Az SQL Database lehetővé teszi prémium szintű vagy üzleti legkritikusabb adatbázisok vagy rugalmas készletek kiépítését több rendelkezésre állási zónában. Mivel ezek az adatbázisok és rugalmas készletek több redundáns replikák magas rendelkezésre állásérdekében, ezek a replikák több rendelkezésre állási zónák nagyobb rugalmasságot biztosít. Ez magában foglalja az adatközpont-méretezési hibák automatikus helyreállításának lehetőségét adatvesztés nélkül.

## <a name="built-in-intelligence"></a>Beépített intelligencia

Az SQL Database beépített intelligenciával jelentősen csökkentheti az adatbázisok futtatásának és kezelésének költségeit, és maximalizálhatja az alkalmazás teljesítményét és biztonságát. Az ügyfél-munkaterhelések millióit futtatja a megfelelő idő alatt, az SQL Database hatalmas mennyiségű telemetriai adatot gyűjt és dolgoz fel, miközben teljes mértékben tiszteletben tartja az ügyfelek adatvédelmét. Különböző algoritmusok folyamatosan értékeli ki a telemetriai adatokat, hogy a szolgáltatás megtanulhatja és alkalmazkodjon az alkalmazással.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatikus teljesítményfigyelés és finomhangolás

Az SQL Database részletes betekintést nyújt a figyelést kívánó lekérdezésekbe. Az SQL Database megismeri az adatbázismintákat, és lehetővé teszi az adatbázisséma hozzáigazítását a munkaterheléshez. Az SQL Database [javaslatokat tesz a teljesítmény finomhangolására](sql-database-advisor.md), és Ön eldöntheti, hogy alkalmazza-e a javasolt finomhangolási műveleteket.

Azonban az adatbázis folyamatos figyelése nehéz és fárasztó feladat, különösen sok adatbázis kezelése esetén. [Az Intelligent Insights](sql-database-intelligent-insights.md) ezt a feladatot úgy végzi el, hogy automatikusan figyeli az SQL Database teljesítményét. Tájékoztatja Önt a teljesítményromlással kapcsolatos problémákról, azonosítja az egyes problémák kiváltó okát, és lehetőség szerint teljesítményjavítási javaslatokat ad.

Nagyszámú adatbázis kezelése lehet, hogy lehetetlen hatékonyan még az összes rendelkezésre álló eszközök és jelentések, amelyek az SQL Database és az Azure nyújt. Ahelyett, hogy az adatbázis manuális figyelése és finomhangolása, érdemes lehet delegálása néhány figyelési és hangolási műveletek SQL Database [segítségével automatikus hangolás.](sql-database-automatic-tuning.md) Az SQL Database automatikusan alkalmazza a javaslatokat, teszteket, és ellenőrzi az egyes hangolási műveleteket, hogy biztosítsa a teljesítmény folyamatos javulását. Ily módon az SQL Database automatikusan alkalmazkodik a számítási feladatokhoz ellenőrzött és biztonságos módon. Az automatikus hangolás azt jelenti, hogy az adatbázis teljesítményét gondosan figyelik, és minden hangolási művelet előtt és után összehasonlítják. Ha a teljesítmény nem javul, a hangolási művelet visszaáll.

Számos, [az SQL Database-en futó több-bérlős SaaS-alkalmazást](sql-database-design-patterns-multi-tenancy-saas-applications.md) kezelő partnerünk automatikus teljesítményhangolásra támaszkodik, hogy alkalmazásaik mindig stabil és kiszámítható teljesítményt nyújtsanak. Ez a funkció nagy mértékben csökkenti számukra egy éjszaka közepén fellépő teljesítmény-visszaesés kockázatát. Emellett mivel az ügyfélkör egy része az SQL Server t is használja, az SQL Database által biztosított indexelési javaslatokat használják az SQL Server-ügyfelek megsegítésére.

Az SQL Database két automatikus hangolási [szempontot is elérhető:](sql-database-automatic-tuning.md)

- **Automatikus indexkezelés**: Azonosítja az adatbázishoz hozzáadandó és az abból eltávolítandó indexeket.
- **Automatikus tervjavítás:** Azonosítja a problémás terveket, és javítja az SQL-terv teljesítményproblémáit.

### <a name="adaptive-query-processing"></a>Adaptív lekérdezés-feldolgozás

Használhatja [az adaptív lekérdezésfeldolgozást,](/sql/relational-databases/performance/intelligent-query-processing)beleértve a többutasításos táblaértékű függvények, a kötegelt módú memória csatolásés a kötegelt mód adaptív illesztések átlapolt végrehajtását. Az adaptív lekérdezésfeldolgozási funkciók mindegyike hasonló "tanulás és adaptálás" technikákat alkalmaz, így tovább kezelheti a korábbi, megoldhatatlan lekérdezésoptimalizálási problémákkal kapcsolatos teljesítményproblémákat.

## <a name="advanced-security-and-compliance"></a>Magas szintű biztonság és megfelelőség

Az SQl Database számos [beépített biztonsági és megfelelőségi szolgáltatást](sql-database-security-overview.md) kínálva járul hozzá, hogy az Ön alkalmazása eleget tegyen a különféle biztonsági és megfelelőségi elvárásoknak.

> [!IMPORTANT]
> A Microsoft számos megfelelőségi szabvány nak megfelelően hitelesítette az Azure SQL Database-t (az összes telepítési lehetőséget). További információt a [Microsoft Azure Adatvédelmi központban](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)talál, ahol az SQL Database megfelelőségi tanúsítványainak legfrissebb listáját találja.

### <a name="advance-threat-protection"></a>Előzetes veszélyforrások elleni védelem

A fejlett adatbiztonság egy egységes csomag a fejlett SQL biztonsági képességekhez. Ez magában foglalja a bizalmas adatok felderítésére és besorolására, az adatbázis biztonsági réseikezelésére és az adatbázist fenyegető, rendellenes tevékenységek észlelésére szolgáló funkciókat. Egyetlen helyet biztosít a képességek engedélyezéséhez és kezeléséhez.

- [Adatfeltárás és -besorolás:](sql-database-data-discovery-and-classification.md)

  Ez a funkció az Azure SQL Database beépített funkcióit biztosítja az adatbázisokban lévő bizalmas adatok felderítéséhez, besorolásához, címkézéséhez és védelméhez. Betekintést nyújt az adatbázis besorolási állapotába, és nyomon követi a bizalmas adatokhoz való hozzáférést az adatbázison belül és határain túl.
- [Sebezhetőség értékelése](sql-vulnerability-assessment.md):

  Ez a szolgáltatás felderítheti, nyomon követheti és segíthet az adatbázis esetleges biztonsági rései elhárításában. Áttekinthetővé teszi az adatbázisok biztonsági állapotát, és végrehajtható lépéseket kínál a biztonsági problémák megoldására, valamint az adatbázisok védelmének fejlesztésére.
- [Fenyegetés észlelése](sql-database-threat-detection.md):

  Ez a funkció észleli azokat a rendellenes tevékenységeket, amelyek szokatlan és potenciálisan káros kísérleteket jeleznek az adatbázis elérésére vagy kihasználására. A szolgáltatás folyamatosan figyeli az adatbázisokat, és azonnal értesíti a felhasználót a gyanús tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. A fenyegetésészlelési riasztások a gyanús tevékenység részleteit szolgálnak, és a fenyegetés kivizsgálásának és csökkentésének lépéseit javasolják.

### <a name="auditing-for-compliance-and-security"></a>Naplózás a megfelelőség és biztonság szolgálatában

[A naplózás](sql-database-auditing.md) nyomon követi az adatbázis-eseményeket, és az Azure storage-fiókjában naplóba írja őket. A naplózás segíthet a jogszabályi megfelelőség fenntartásában, az adatbázis-tevékenységek megértésében, valamint az olyan eltérésekés anomáliák megismerésében, amelyek üzleti problémákra vagy a biztonság feltételezett megsértésére utalhatnak.

### <a name="data-encryption"></a>Adattitkosítás

Az SQL Database titkosítással védi az adatokat. A mozgásban lévő adatok hoz a [transport layer security](https://support.microsoft.com/kb/3135244). Az inaktív adatokhoz [transzparens adattitkosítást](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)használ. A használatban lévő adatokhoz [mindig titkosított](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

Az SQL Database az [Azure Active Directory-integráció](sql-database-aad-authentication.md) által lehetővé teszi adatbázis-felhasználók és más Microsoft-szolgáltatások identitásainak központi kezelését. Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. Az Azure Active Directory támogatja a [többtényezős hitelesítést](sql-database-ssms-mfa-authentication.md) az adatok és az alkalmazások biztonságának növelése érdekében, miközben egyetlen bejelentkezési folyamatot támogat.

## <a name="easy-to-use-tools"></a>Egyszerűen használható eszközök

Az SQL Database egyszerűbbé és hatékonyabbá teszi az alkalmazások létrehozását és karbantartását. Az SQL Database lehetővé teszi, hogy arra koncentrálhasson, amit legjobban tud: kiváló alkalmazások készítésére. Az SQL Database-ben már meglévő eszközök és képességek segítségével kezelheti és fejlesztheti azokat.

- [Az Azure portál:](https://portal.azure.com/)

  Webalapú alkalmazás az összes Azure-szolgáltatás kezeléséhez.
- [SQL Server Felügyeleti stúdió](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  Ingyenes, letölthető ügyfélalkalmazás bármely SQL-infrastruktúra kezeléséhez, az SQL Server kiszolgálótól az SQL Database-ig.
- [SQL Server adateszközök a Visual Studióban:](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)

  Ingyenes, letölthető ügyfélalkalmazás SQL Server relációs adatbázisok, SQL-adatbázisok, Integrációs szolgáltatások csomagok, Analysis Services-adatmodellek és Reporting Services-jelentések fejlesztéséhez.
- [Visual Studio kód](https://code.visualstudio.com/docs):

  Ingyenes, letölthető, nyílt forráskódú kódszerkesztő Windows, macOS és Linux rendszerhez. Támogatja a bővítményeket, beleértve az [mssql bővítményt](https://aka.ms/mssql-marketplace) a Microsoft SQL Server, az Azure SQL Database és az Azure SQL Data Warehouse lekérdezéséhez.

Az SQL Database támogatja az alkalmazások készítését Python, Java, Node.js, PHP, Ruby és .NET rendszerrel macOS, Linux és Windows rendszeren. Az SQL Database az SQL Serverrel azonos [adatkapcsolattárakat](sql-database-libraries.md) támogat.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Gyakori kérdések az SQL Database-ről

### <a name="what-is-the-current-version-of-sql-database"></a>Mi az SQL Database jelenlegi verziója?

Az SQL Database jelenlegi verziója V12. A V11-es verzió kilett vonva.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Szabályozhatom, hogy mikor történik az állásidő javítása?

Nem. A javítás hatása általában nem észrevehető, ha [újrapróbálkozási logikát alkalmaz](sql-database-develop-overview.md#resiliency) az alkalmazásban. További információ: [Tervezés az Azure karbantartási események az Azure SQL Database-ben.](sql-database-planned-maintenance.md)

### <a name="azure-hybrid-benefit-questions"></a>Az Azure Hybrid Benefitkel kapcsolatos kérdések

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Vannak kettős felhasználású jogok az Azure Hybrid Benefit for SQL Server használatával?

Az áttelepítések zökkenőmentes működésének biztosítása érdekében 180 napos kettős használati jogokkal rendelkezik a licenchez. Ezt követően a 180 napos időszak, csak akkor használhatja az SQL Server licenc a felhőben az SQL Database. A továbbiakban nem rendelkezik kettős használati jogokkal a helyszínen és a felhőben.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Miben különbözik az Azure Hybrid Benefit for SQL Server licencmobilitásától?

A frissítési garanciával rendelkező SQL Server-ügyfelek számára licencmobilitási előnyöket kínálunk. Ez lehetővé teszi a licencek átcsoportosítását a partner megosztott kiszolgálóihoz. Ezt az előnyt az Azure IaaS és az AWS EC2 szolgáltatásban is használhatja.

Az Azure Hybrid Benefit for SQL Server két fő területen különbözik a licenchordozhatóságtól:

- Gazdasági előnyökkel jár a nagymértékben virtualizált számítási feladatok Azure-ba való áthelyezéséhez. Az SQL Server Enterprise Edition ügyfelei négy magot szerezhetnek be az Azure-ban az általános célú termékváltozatban minden olyan maghoz, amely a helyszíni, nagy virtualizált alkalmazásokhoz való használatra rendelkezik. A licenchordozhatóság nem teszi lehetővé a virtualizált számítási feladatok felhőbe való áthelyezésének különleges költségelőnyeit.
- Olyan PaaS-célt biztosít az Azure-ban (SQL Database által felügyelt példány), amely nagymértékben kompatibilis a helyszíni SQL Server kiszolgálóval.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Melyek az Azure Hybrid Benefit for SQL Server speciális jogai?

Az SQL Database-ügyfelek a következő jogokkal rendelkeznek az Azure Hybrid Benefit for SQL Server szolgáltatáshoz társított jogokhoz:

|Licenc lábnyoma|Mit kap az Azure Hybrid Benefit for SQL Server?|
|---|---|
|SQL Server Enterprise Edition alapvető ügyfelek sa-val|<li>Alapdíj fizetése általános célvagy üzleti szempontból kritikus termékváltozat alapján</li><br><li>1 mag helyszíni = 4 mag az általános célú termékváltozatban</li><br><li>1 mag helyszíni = 1 mag az üzleti legkritikusabb termékváltozatban</li>|
|SQL Server Standard Edition alapvető ügyfelek sa-val|<li>Csak az általános célú termékváltozatalapdíjának kifizetésére használható</li><br><li>1 mag helyszíni = 1 mag az általános célú termékváltozatban</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Kapcsolatfelvétel az SQL Server műszaki csoportjával

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Adatbázis-adminisztrációs kérdések et tehet fel.
- [Stack túlcsordulás:](https://stackoverflow.com/questions/tagged/sql-server)Fejlesztési kérdéseket tehet fel.
- [MSDN Fórum :](https://social.msdn.microsoft.com/Forums/home?category=sqlserver)Technikai kérdések et tehet fel.
- [Visszajelzés](https://aka.ms/sqlfeedback): Jelentse a hibákat és a kérés funkciót.
- [Reddit](https://www.reddit.com/r/SQLServer/): Vitassák meg az SQL Server.

## <a name="next-steps"></a>További lépések

- Tekintse meg a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/sql-database/) a költség-összehasonlítások és számológépek kapcsolatos egyes adatbázisok és rugalmas készletek.
- Az első lépésekhez tekintse meg ezeket a rövid útmutatókat:

  - [SQL Database létrehozása az Azure Portalon](sql-database-single-database-get-started.md)  
  - [SQL Database létrehozása az Azure CLI-vel](sql-database-get-started-cli.md)
  - [SQL Database létrehozása PowerShell használatával](sql-database-get-started-powershell.md)

- Több Azure CLI és PowerShell-mintát talál itt:
  - [Azure CLI-minták az SQL Database-hez](sql-database-cli-samples.md)
  - [Azure PowerShell-minták az SQL Database-hez](sql-database-powershell-samples.md)

- Az új képességekről a bejelentés során az [Azure Roadmap for SQL Database című](https://azure.microsoft.com/roadmap/?category=databases)témakörben talál további információt.
- Tekintse meg az [Azure SQL Database blogját,](https://azure.microsoft.com/blog/topics/database)ahol az SQL Server termékcsapatának tagjai az SQL Database hírekről és -szolgáltatásokról blogolnak.

