---
title: Mi az Azure SQL Database szolgáltatás? | Microsoft Docs
description: 'Bevezetés az SQL Database-be: a Microsoft felhőalapú relációs adatbázis-kezelő rendszerének (RDBMS) technikai részletei és funkciói.'
keywords: bevezetés az sql-be,az sql bemutatása,mi az sql database
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.topic: overview
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: f7a314b3de112ad1fa7a5a356c2325846e9371b1
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413382"
---
# <a name="what-is-the-azure-sql-database-service"></a>Mi az Azure SQL Database szolgáltatás? 

Az SQL Database általános célú, felügyelt relációsadatbázis-szolgáltatás a Microsoft Azure-ban, amely egyebek mellett relációs, JSON-, térbeli és XML-struktúrákat is támogat. Az SQL Database dinamikusan skálázható teljesítményt nyújt két különböző vásárlási modellen belül ([vCore-alapú vásárlási modell ](sql-database-service-tiers-vcore.md) vagy [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md)). Az SQL Database emellett olyan lehetőségeket kínál, mint az [oszlopcentrikus indexelés](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) a kivételes mélységű elemzéshez és jelentéskészítéshez, illetve a [memóriabeli OLTP](sql-database-in-memory.md) a rendkívül nagy teljesítményű tranzakciófeldolgozáshoz. A Microsoft zökkenőmentesen kezeli az SQL kódbázis karbantartását és frissítését, és teljesen átveszi az alapul szolgáló infrastruktúra kezelését. 

Az Azure SQL Database az alábbi lehetőségeket kínálja az Azure SQL-adatbázisok üzembe helyezésére:
- Egy logikai kiszolgáló által felügyelt önálló, saját erőforráskészlettel rendelkező adatbázisként 
- Készletezett adatbázisként egy [rugalmas készletben](sql-database-elastic-pool.md) egy logikai kiszolgálón keresztül felügyelt közös erőforráskészlettel
- Egy adatbázis-gyűjtemény, más néven [felügyelt példány](sql-database-managed-instance.md) részeként (nyilvános előzetes verzióban), amely rendszer- és felhasználói adatbázisokat tartalmaz, és egy közös erőforráskészleten osztozik

Az alábbi ábra ezt a három üzembehelyezési modellt mutatja be:

![deployment-options](./media/sql-database-technical-overview/deployment-options.png) 

Az SQL Database kódbázisa közös a [Microsoft SQL Server adatbázismotorjáéval](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation). A Microsoft felhőt előnyben részesítő stratégiájának jegyében az SQL Server új lehetőségei először az SQL Database-ben jelentek meg, és csak aztán magában az SQL Serverben. Ennek a hozzáállásnak köszönhetően az SQL Server legújabb lehetőségei a karbantartás és frissítés terhei nélkül állnak az Ön rendelkezésére úgy, hogy az új funkciókat már több millió adatbázison tesztelték. A bejelentett új funkciókról az alábbi helyeken kaphat tájékoztatást:

- **[SQL Database Azure fejlesztési ütemterve](https://azure.microsoft.com/roadmap/?category=databases)**: Itt találja meg a jelenlegi és tervezett újdonságokat. 
- **[Azure SQL Database blog](https://azure.microsoft.com/blog/topics/database)**: Itt az SQL Server csapatának tagjai írnak az SQL Database újdonságairól és funkcióiról. 

> [!IMPORTANT]
> Az SQL Database és az SQL Server közötti különbségek ismertetéséért lásd az [SQL-szolgáltatásokkal](sql-database-features.md) foglalkozó témakört. 

A több szolgáltatási szinten is kiszámítható teljesítményt nyújtó SQL Database dinamikus méretezhetőséget kínál állásidő nélkül, beépített intelligens optimalizálással, globális méretezhetőséggel és rendelkezésre állással és fejlett biztonsági beállításokkal – mindezt szinte adminisztráció nélkül. Ezen képességek birtokában az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására összpontosíthat ahelyett, hogy értékes időt és erőforrásokat kellene lefoglalnia a virtuális gépek és infrastruktúra kezeléséhez. Az SQL Database szolgáltatás jelenleg a világ 38 adatközpontjában áll rendelkezésre, és újabb adatközpontok is rendszeresen lépnek működésbe, így Ön a saját adatbázisát egy közeli adatközponton futtathatja.

> [!NOTE]
> A felügyelt SQL Database-példány jelenleg előzetes verzióban és csak egyetlen szolgáltatási szinten érhető el. További információért tekintse meg a [felügyelt SQL Database-példányt](sql-database-managed-instance.md) ismertető cikket.
>

## <a name="scalable-performance-and-pools"></a>Méretezhető teljesítmény és készletek

Az SQL Database-szel minden adatbázis önálló, hordozható, és saját garantált teljesítményt nyújtó szolgáltatásszinttel rendelkezik a [DTU-alapú vásárlási modellen](sql-database-service-tiers-dtu.md) vagy a [vCore-alapú vásárlási modellen](sql-database-service-tiers-vcore.md) belül. Az SQL Database a különböző igényekre különböző teljesítmény-szinteket kínál, és lehetővé teszi az adatbázisok készletekbe rendezését az erőforrások hatékony és költségkímélő kihasználása érdekében.

A felügyelt SQL Database-példányok használata esetén az egyes példányok garantált erőforrásokkal vannak elkülönítve egymástól. További információért tekintse meg a [felügyelt SQL Database-példányt](sql-database-managed-instance.md) ismertető cikket. 

### <a name="adjust-performance-and-scale-without-downtime"></a>Teljesítmény módosítása és skálázása leállási idő nélkül

Az SQL Database a [DTU-alapú vásárlási modellt](sql-database-service-tiers-dtu.md) vagy a [vCore-alapú vásárlási modellt](sql-database-service-tiers-vcore.md) kínálja. 
- A DTU-alapú vásárlási modell a számítás, memória és IO-erőforrások egyvelegét kínálja három szolgáltatásszinten az adatbázisok kis és nagy terheléssel járó számítási feladatainak támogatása érdekében. Ezek a szintek az Alapszintű, a Standard és a Prémium. Az egyes szolgáltatásszintek teljesítményszintjei ezen erőforrások különféle keverékét kínálják, amelyhez további tárterület-erőforrások is hozzáadhatók.
- A vCore-alapú vásárlási modell lehetővé teszi a virtuális magok számának, a memória mennyiségének, illetve a tárterület mennyiségének és sebességének a kiválasztását.

Csekély havi díjért létrehozhatja első alkalmazását egy egyedülálló, kisméretű adatbázison, majd később az aktuális szükségletek alapján manuálisan vagy programon keresztül bármikor módosíthatja a szolgáltatásszintet. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre, és lehetővé teszi, hogy csak azokért az erőforrásokért fizessen, amelyekre és amikor szüksége van.

A dinamikus méretezhetőség különbözik az automatikus skálázástól. Automatikus skálázás esetén a szolgáltatás adott feltételek alapján, automatikusan méretez, míg a dinamikus méretezhetőség lehetővé teszi a manuális méretezést, amely nem jár állásidővel. Az önálló Azure SQL Database a manuális dinamikus méretezhetőséget támogatja, de az automatikus skálázást nem. Ha *automatikus* megoldást keres, érdemes megfontolni a rugalmas készletek használatát, amely lehetővé teszi, hogy az adatbázisok osztozzanak egy készlet erőforrásain az egyes adatbázisok egyedi igényei alapján. Vannak olyan szkriptek azonban, amelyek segítségével egy önálló Azure SQL Database esetén is automatizálhatja a méretezhetőséget. Erre az [Egyetlen SQL-adatbázis monitorozása és skálázása a PowerShell használatával](scripts/sql-database-monitor-and-scale-database-powershell.md) című témakörben láthat példát. 

A felügyelt SQL Database-példány előzetes verzióban érhető el, és egyetlen szolgáltatási szintet kínál. További információért tekintse meg a [felügyelt SQL Database-példányt](sql-database-managed-instance.md) ismertető cikket.

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Rugalmas készletek az erőforrások kihasználtságának maximalizálásához

Számos vállalkozás és alkalmazás számára elegendő az, ha önálló adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. A [rugalmas készletek](sql-database-elastic-pool.md) megoldást jelentenek erre a problémára. A koncepció egyszerű. A készletekhez az önálló adatbázis helyett teljesítmény-erőforrásokat foglalhat le, így nem egy önálló adatbázis teljesítményéért, hanem a készlet közös teljesítményéért fizet. 

   ![rugalmas készletek](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

A rugalmas készletekkel az erőforrásigények ingadozásakor sem kell a az adatbázis teljesítményének fel- és leskálázására koncentrálnia. A készletezett adatbázisok szükség szerint használják fel a teljesítmény-erőforrásokat. A készletezett adatbázisok használják az adatbáziskészlethez hozzárendelt teljesítményt, de nem lépik túl az adatbáziskészlet teljesítménykorlátait, így költségei előre jelezhetők még akkor is, ha az egyéni adatbázis-használat nem jelezhető előre. Ráadásul [adatbázisokat adhat hozzá a készlethez, és távolíthat el a készletből](sql-database-elastic-pool-manage-portal.md), így előre jelezhető költségek mellett néhány adatbázisról több ezer adatbázisra skálázhatja fel alkalmazását. A készlet adatbázisai számára elérhető erőforrások minimális és maximális mennyiségét is szabályozhatja annak érdekében, hogy a készlet egyetlen adatbázisa se használja fel a készlet összes erőforrását, és hogy minden készletezett adatbázis rendelkezzen egy garantált minimális erőforrás-mennyiséggel. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt. 

A szkriptek segítik a rugalmas készletek megfigyelését és méretezését. Erre a [Rugalmas SQL-készlet monitorozása és skálázása az Azure SQL Database-ben a PowerShell használatával](scripts/sql-database-monitor-and-scale-pool-powershell.md) című témakörben talál példát.

> [!IMPORTANT]
> A felügyelt SQL Database-példány nem támogatja a rugalmas készleteket.

### <a name="blend-single-databases-with-pooled-databases"></a>Önálló adatbázisok beolvasztása a készletezett adatbázisokba

Akár önálló adatbázisokat, akár rugalmas készleteket választ, a későbbiekben is sok mindent megváltoztathat. Az önálló adatbázisokat rugalmas készletekkel kombinálhatja, továbbá gyorsan és egyszerűen módosíthatja az önálló adatbázisok és a rugalmas készletek szolgáltatásszintjét, hogy mindig az adott helyzethez tudjon igazodni. Az Azure sokoldalúságának és széles körű alkalmazhatóságának köszönhetően tetszés szerint kombinálhatja az Azure-szolgáltatásokat SQL Database-adatbázisokkal, így kielégíthetők az egyedi, modern alkalmazástervezési igények, növelhető a költség- és erőforrás-hatékonyság, és új üzleti lehetőségek tárhatók fel.

### <a name="extensive-monitoring-and-alerting-capabilities"></a>Széles körű figyelési és riasztási funkciók

Hogyan hasonlítható össze az önálló adatbázisok és a rugalmas készletek relatív teljesítménye? Hogyan lehet megállapítani a fel- és leskálázás megfelelő mértékét? Használja a [beépített teljesítménymonitorozási](sql-database-performance.md) és [riasztási](sql-database-insights-alerts-portal.md) eszközöket a teljesítmény-értékelésekkel kombinálva. Ezek lehetővé teszik az aktuális vagy a projekthez kapcsolódó teljesítményigényeken alapuló fel- vagy leskálázás hatásainak gyors kiértékelését. További információ: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [vCore-alapú vásárlási modell](sql-database-service-tiers-vcore.md).

Az SQL Database emellett [metrikák és diagnosztikai naplók kibocsátásával](sql-database-metrics-diag-logging.md) is képes megkönnyíteni a felügyeletet. Az SQL Database beállítható az erőforrás-használatra, feldolgozókra és munkamenetekre, valamint kapcsolatokra vonatkozó adatok tárolására a következő Azure-erőforrások valamelyikén:

- **Azure Storage**: Nagy tömegű telemetriai adat alacsony költségű archiválására
- **Azure Event Hub**: Az SQL Database telemetriai adatainak integrálásra saját egyedi figyelési megoldásokkal vagy élő adatfolyamatokkal
- **Azure Log Analytics**: Beépített monitorozási megoldás jelentéskészítő, riasztó és enyhítő funkciókkal. Ez az [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) egyik funkciója

    ![architektúra](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Rendelkezésre állás

Az Azure szolgáltatói szerződésében [(SLA)](http://azure.microsoft.com/support/legal/sla/) az ágazatban élenjáró módon 99,99 százalékos elérhetőséget biztosítunk – a Microsoft által kezelt adatbázisok globális hálózata teszi lehetővé, hogy alkalmazása a hét mind a 7 napján, napi 24 órában fusson. Az Azure platform teljes mértékben felügyeli az összes Azure SQL Database-példányt, garantálva az adatvesztés kiküszöbölését és az adatok nagy arányú rendelkezésre állását. Az Azure automatikusan kezeli a javításokat, a biztonsági mentéseket, a replikációt, a hibaészlelést, a háttérben álló lehetséges hardver-, szoftver- vagy hálózati hibákat, a hibajavítások telepítését, a feladatátvételeket, adatbázis-frissítéseket és az egyéb karbantartási műveleteket. A standard szintű rendelkezésre állás a számítási és tárolási rétegek elkülönítését foglalja magában. A prémium szintű rendelkezésre állás a számítás és tárolás egy csomóponton, teljesítménynövelés céljából történő integrálását, majd az Always On rendelkezésre állási csoportok színfalak mögötti bevezetését jelenti. Az Azure SQL Database magas rendelkezésre állási képességeinek átfogó ismertetéséért lásd [az SQL Database rendelkezésre állásáról](sql-database-high-availability.md) szóló részt. Az SQL Database ezen felül olyan beépített funkciókkal szolgálja [az üzletmenet folytonosságát és a globális méretezhetőséget](sql-database-business-continuity.md), mint például a következők:

- **[Automatikus biztonsági mentések](sql-database-automated-backups.md)**: Az SQL Database automatikusan készít teljes, differenciális és tranzakciónapló-alapú biztonsági mentéseket.
- **[Adott időpontnak megfelelő helyreállítás](sql-database-recovery-using-backups.md)**: Az SQL Database támogatja a visszaállást bármely időpontra az automatikus biztonsági mentések megőrzési idején belül.
- **[Aktív georeplikáció](sql-database-geo-replication-overview.md)**: Az SQL Database lehetővé teszi legfeljebb négy olvasható másodlagos adatbázis konfigurálását akár egyazon Azure adatközponton belül, akár globálisan elosztva.  Ha például egy SaaS-alkalmazás nagyszámú egyidejű csak olvasási tranzakciót kiszolgáló katalógus-adatbázisra épül, akkor az aktív georeplikáció által növelhető az olvashatóság, és megszűnik az elsődleges adatbázis magas olvasási terhelése miatti szűk keresztmetszet. 
- **[Feladatátvételi csoportok](sql-database-geo-replication-overview.md)**: Az SQL Database lehetővé teszi, hogy magas rendelkezésre állást és terheléselosztást kínáljon globális léptékben, beleértve a transzparens georeplikációt, nagy adatbázis-készletek feladatátvételét és rugalmas készleteket. A feladatátvételi csoportok és az aktív georeplikáció által a lehető legkevesebb adminisztrációs többletmunkával hozhatók létre globálisan elosztott SaaS-alkalmazások úgy, hogy a figyelés, útválasztás és feladatátvétel összetett feladatát az SQL Database-re bízzuk.
- **[Zónaredundáns adatbázisok](sql-database-high-availability.md)**: Az SQL Database segítségével több rendelkezésre állási zónában is kiépíthet Prémium vagy üzleti szempontból kritikus jellegű adatbázisokat vagy rugalmas készleteket. Mivel ezek az adatbázisok és a rugalmas készletek több redundáns replikával rendelkeznek a magas rendelkezésre állás érdekében, e replikák több rendelkezésre állási zónában történő elhelyezése nagyobb rugalmasságot biztosít, beleértve az adatközpont méretezési hibáiból az adatvesztés nélküli, automatikus helyreállítás lehetőségét is.  

## <a name="built-in-intelligence"></a>Beépített intelligencia

Az SQL Database-zel beépített intelligencia is jár, amely segít jelentősen mérsékelni az adatbázisok működtetésének és kezelésének költségét, ugyanakkor fokozni az alkalmazás teljesítményét és biztonságát. Az állandóan ügyfelek millióinak számítási feladatait futtató SQL Database hatalmas mennyiségű telemetriai adatot dolgoz fel, végig szem előtt tartva az ügyfelek adatainak teljes védelmét. A telemetriai adatokat szüntelenül különféle algoritmusok értékelik, hogy a szolgáltatás tanulhasson belőlük és idomulhasson az Ön alkalmazásához. Ezen elemzés alapján a szolgáltatás az adott számítási feladatnak megfelelő javaslatokat tesz a teljesítmény javítására. 

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatikus teljesítményfigyelés és finomhangolás

Az SQL Database részletes betekintést nyújt a figyelést kívánó lekérdezésekbe. Az SQL Database megismeri az adatbázisaira jellemző mintázatokat, és segít az adatbázis-sémáit a számítási feladathoz igazítani. Az SQL Database [javaslatokat tesz a teljesítmény finomhangolására](sql-database-advisor.md), és Ön eldöntheti, hogy alkalmazza-e a javasolt finomhangolási műveleteket. 

Az adatbázisok folyamatos figyelése azonban nehéz, fárasztó feladat, különösen akkor, ha sok adatbázisról van szó. Az [Intelligent Insights](sql-database-intelligent-insights.md) elvégzi Ön helyett ezt a munkát. Automatikusan figyeli az SQL Database teljesítményét, és teljesítménycsökkenési hibák előfordulása esetén értesítést küld, azonosítja a probléma gyökerét, és ahol lehetséges, teljesítményjavítási javaslatokkal is szolgál.

Nagyszámú adatbázis hatékony kezelése már az SQL Database és az Azure Portal által kínált valamennyi eszköz és jelentés birtokában is lehetetlennek bizonyulhat. Az adatbázis személyes figyelése és finomhangolása helyett érdemes megfontolni, hogy a figyelési és hangolási műveletek egy részét az SQL Database [automatikus finomhangoló](sql-database-automatic-tuning.md) funkciójára bízza. Az SQL Database automatikusan alkalmazza a javasolt változásokat, de minden módosítást tesztel és ellenőriz, hogy a teljesítmény csak javulhasson. Az SQL Database ezáltal automatikusan, ellenőrzött és biztonságos módon alkalmazkodik a számítási feladathoz. Az automatikus finomhangolás azt jelenti, hogy ha az adatbázis egyes módosítások előtt és után gondosan mért teljesítményének összehasonlítása nem mutat javulást, akkor a módosító művelet visszavonásra kerül.

Jelenleg számos partnerünk, akik [több-bérlős SaaS-alkalmazásokat](sql-database-design-patterns-multi-tenancy-saas-applications.md) futtatnak SQL Database-en, az automatikus teljesítményhangolásra bízza az alkalmazása mindig stabil és kiszámítható működését. Ez a funkció nagy mértékben csökkenti számukra egy éjszaka közepén fellépő teljesítmény-visszaesés kockázatát. Ráadásul, mivel ügyfélkörük egy része szintén használ SQL Servert, az SQL Database által kínált azonos indexelési ajánlásokat alkalmazva nyújthatnak segítséget ezen ügyfeleknek.

Az [SQL Database](sql-database-automatic-tuning.md) két szempont alapján képes automatikus finomhangolást végezni:

- **Automatikus indexkezelés**: Azonosítja az adatbázishoz hozzáadandó és az abból eltávolítandó indexeket.
- **Automatikus tervkorrekció**: Azonosítja a hibás terveket, és javítja az SQL-tervek teljesítményproblémáit (hamarosan megjelenik, az SQL Server 2017-ben már elérhető).

### <a name="adaptive-query-processing"></a>Adaptív lekérdezés-feldolgozás

Emellett az [adaptív lekérdezésfeldolgozási](/sql/relational-databases/performance/adaptive-query-processing) szolgáltatáscsalád is elérhető vált az SQL Database-ben, beleértve a több utasításból álló, táblaértékű függvények kihagyásos végrehajtását, a kötegelt módú memóriaengedély visszajelzését, valamint a kötegelt módú adaptív illesztéseket. Ezen adaptív lekérdezésfeldolgozó funkciók mindegyike hasonló technikával „tanul és alkalmazkodik”, ezzel is hozzájárulva a korábban nyomon követhetetlen lekérdezés-optimalizálási gondokra visszavezethető teljesítményproblémák megoldásához.

## <a name="advanced-security-and-compliance"></a>Magas szintű biztonság és megfelelőség

Az SQl Database számos [beépített biztonsági és megfelelőségi szolgáltatást](sql-database-security-overview.md) kínálva járul hozzá, hogy az Ön alkalmazása eleget tegyen a különféle biztonsági és megfelelőségi elvárásoknak. 

### <a name="advance-threat-protection"></a>Fejlett fenyegetésvédelem

Az SQL Advanced Threat Protection fejlett SQL-biztonsági funkciókat tartalmazó egyesített biztonsági csomag. Lehetőséget nyújt a bizalmas adatok felderítésére és titkossá minősítésére, az adatbázis biztonsági réseinek kezelésére, továbbá az adatbázisra nézve fenyegetést jelentő rendellenes tevékenységek észlelésére. Segítségével egyetlen helyen engedélyezhetők és kezelhetők ezek a képességek.

- Az [adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md) (jelenleg előzetes verzió) az Azure SQL-be épített, az adatbázisokban található bizalmas adatok felderítésére, besorolására, címkézésére és védelmére alkalmas képességeket biztosít. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül.
- A [sebezhetőségi felmérés](sql-vulnerability-assessment.md) egy könnyen konfigurálható szolgáltatás, amely képes felderíteni és követni az adatbázisok lehetséges biztonsági réseit, és segít orvosolni azokat. Áttekinthetővé teszi az adatbázisok biztonsági állapotát, és végrehajtható lépéseket kínál a biztonsági problémák megoldására, valamint az adatbázisok védelmének fejlesztésére.
- A [fenyegetésészlelés](sql-database-threat-detection.md) észleli az adatbázisok hozzáférésére és az adatbázisok biztonságának megsértésére tett szokatlan és potenciálisan kártevő szándékú kísérleteket. A szolgáltatás folyamatosan figyeli az adatbázisokat, és azonnal értesíti a felhasználót a gyanús tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. A fenyegetésészlelés által adott riasztások tartalmazzák a gyanús tevékenység részleteit, és javaslatot tesznek a fenyegetés kivizsgálására és mérséklésére tett műveletekre.

### <a name="auditing-for-compliance-and-security"></a>Naplózás a megfelelőség és biztonság szolgálatában

Az [SQL Database naplózási szolgáltatása](sql-database-auditing.md) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, amely a felhasználó Azure Storage-fiókjában található. A naplózás segíthet a jogszabályi megfelelőség fenntartásában és az adatbázison végzett tevékenység megértésében, valamint az esetleg üzleti veszélyeket vagy biztonsági problémákat jelző rendellenességek feltárásában.

### <a name="data-encryption"></a>Adattitkosítás

Az SQL Database a mozgásban lévő adatokat a [Transport Layer Security](https://support.microsoft.com/kb/3135244) protokoll, az inaktív adatokat [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), a használatban lévő adatokat pedig az [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) protokoll használatával titkosítja az adatok védelme érdekében.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

Az SQL Database az [Azure Active Directory-integráció](sql-database-aad-authentication.md) által lehetővé teszi adatbázis-felhasználók és más Microsoft-szolgáltatások identitásainak központi kezelését. Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. Az Azure Active Directory a [többtényezős hitelesítés](sql-database-ssms-mfa-authentication.md) (MFA) támogatásával javítja az adatok és alkalmazások biztonságát, miközben támogatja az egyszeri bejelentkezést.

### <a name="compliance-certification"></a>Megfelelőségi tanúsítvány

Az SQL Database rendszeres ellenőrzéseken vesz részt és számos megfelelőségi szabványnak tesz eleget tanúsított módon. További információkat az [Azure biztonsági és adatkezelési központban](https://azure.microsoft.com/support/trust-center/) talál, az [SQL Database megfelelőségi tanúsítványainak](https://azure.microsoft.com/support/trust-center/services/) aktuális listájával együtt.

## <a name="easy-to-use-tools"></a>Egyszerűen használható eszközök

Az SQL Database egyszerűbbé és hatékonyabbá teszi az alkalmazások létrehozását és karbantartását. Az SQL Database lehetővé teszi, hogy arra koncentrálhasson, amit legjobban tud: kiváló alkalmazások készítésére. Az SQL Database-ben a már meglévő eszközeivel és szakértelmével dolgozhat és fejleszthet.

- **[Az Azure Portal](https://portal.azure.com/)**: Web-alapú alkalmazás az összes Azure-szolgáltatás kezelésére 
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)**: Ingyenes, letölthető ügyféloldali alkalmazás bármilyen SQL infrastruktúra kezelésére az SQL Servertől az SQL Database-ig
- **[SQL Server Data Tools a Visual Studióban](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)**: Ingyenes, letölthető ügyféloldali alkalmazás SQL Server relációs adatbázisok, Azure SQL adatbázisok, Integration Services csomagok, Analysis Services adatmodellek, és Reporting Services-jelentések fejlesztéséhez.
- **[Visual Studio Code](https://code.visualstudio.com/docs)**: Ingyenes, letölthető, nyílt forráskódú kódszerkesztő Windows, macOS és Linux rendszerekre, amely támogatja a bővítményeket, beleértve a Microsoft SQL Server, az Azure SQL Database és az SQL Data Warehouse lekérdezéséhez szükséges [mssql bővítményt](https://aka.ms/mssql-marketplace).

Az SQL Database támogatja a Python, Java, Node.js, PHP, Ruby, és .NET alkalmazásfejlesztést a MacOS, Linux, és Windows rendszeren. Az SQL Database az SQL Serverrel azonos [adatkapcsolattárakat](sql-database-libraries.md) támogat.

## <a name="engage-with-the-sql-server-engineering-team"></a>Kapcsolatfelvétel az SQL Server műszaki csoportjával

- [DBA-veremcsere](https://dba.stackexchange.com/questions/tagged/sql-server): Kérdések az adatbázis rendszergazdájának
- [Veremtúlcsordulás](http://stackoverflow.com/questions/tagged/sql-server): Kérdések a fejlesztőknek
- [MSDN fórumok](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Műszaki kérdések
- [Visszajelzés](http://aka.ms/sqlfeedback): Hibák jelentése és funkciók kérése
- [Reddit](https://www.reddit.com/r/SQLServer/): Az SQL Server megvitatása

## <a name="next-steps"></a>További lépések

- Tekintse meg az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/sql-database/) az önálló adatbázisok és a rugalmas készletek költségeinek összehasonlításáért és árkalkulációjáért.

- Tekintse meg ezeket a rövid útmutatókat a kezdéshez:

  - [SQL Database létrehozása az Azure Portalon](sql-database-get-started-portal.md)  
  - [SQL Database létrehozása az Azure CLI-vel](sql-database-get-started-cli.md)
  - [SQL Database létrehozása PowerShell használatával](sql-database-get-started-powershell.md)

- Több Azure CLI és PowerShell-mintát talál itt:
  - [Azure CLI-minták az SQL Database-hez](sql-database-cli-samples.md)
  - [Azure PowerShell-minták az SQL Database-hez](sql-database-powershell-samples.md)
