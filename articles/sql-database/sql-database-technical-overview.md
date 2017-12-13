---
title: "Mi az Azure SQL Database szolgáltatás? | Microsoft Docs"
description: "Bevezetés az SQL Database-be: a Microsoft felhőalapú relációs adatbázis-kezelő rendszerének (RDBMS) technikai részletei és funkciói."
keywords: "bevezetés az sql-be,az sql bemutatása,mi az sql database"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.custom: overview, mvc
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 09/20/2017
ms.author: carlrab
ms.openlocfilehash: 884935074dd19304f72fc761c89fc7ae8fd5b3a3
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="what-is-the-azure-sql-database-service"></a>Mi az Azure SQL Database szolgáltatás? 

Az SQL Database általános célú relációs adatbázis-szolgáltatás a Microsoft Azure-ban, amely egyebek mellett relációs, JSON-, térbeli és XML-struktúrákat is támogat. [Dinamikusan méretezhető teljesítményt](sql-database-service-tiers.md) nyújt és olyan lehetőségeket kínál, mint az [oszlopcentrikus indexelés](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) kivételes mélységű elemzéshez és jelentéskészítéshez, és a [memóriabeli OLTP](sql-database-in-memory.md) a kivételesen nagy teljesítményű tranzakció-feldolgozáshoz. A Microsoft zökkenőmentesen kezeli az SQL kódbázis karbantartását és frissítését, és teljesen átveszi az alapul szolgáló infrastruktúra kezelését. 

Az SQL Database kódbázisa közös a [Microsoft SQL Server adatbázismotorjáéval](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation). A Microsoft felhőt előnyben részesítő stratégiájának jegyében az SQL Server új lehetőségei először az SQL Database-ben jelentek meg, és csak aztán magában az SQL Serverben. Ennek a hozzáállásnak köszönhetően az SQL Server legújabb lehetőségei a karbantartás és frissítés terhei nélkül állnak az Ön rendelkezésére úgy, hogy az új funkciókat már több millió adatbázison tesztelték. A bejelentett új funkciókról az alábbi helyeken kaphat tájékoztatást:

- **[SQL Database Azure fejlesztési ütemterve](https://azure.microsoft.com/roadmap/?category=databases)**: Itt találja meg a jelenlegi és tervezett újdonságokat. 
- **[Azure SQL Database blog](https://azure.microsoft.com/blog/topics/database)**: Itt az SQL Server csapatának tagjai írnak az SQL Database újdonságairól és funkcióiról. 

A több szolgáltatási szinten is kiszámítható teljesítményt nyújtó SQL Database dinamikus méretezhetőséget kínál állásidő nélkül, beépített intelligens optimalizálással, globális méretezhetőséggel és rendelkezésre állással és fejlett biztonsági beállításokkal – mindezt szinte adminisztráció nélkül. Ezen képességek birtokában az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására összpontosíthat ahelyett, hogy értékes időt és erőforrásokat kellene lefoglalnia a virtuális gépek és infrastruktúra kezeléséhez. Az SQL Database szolgáltatás jelenleg a világ 38 adatközpontjában áll rendelkezésre, és újabb adatközpontok is rendszeresen lépnek működésbe, így Ön a saját adatbázisát egy közeli adatközponton futtathatja.

> [!NOTE]
> Az Azure platformbiztonságáról az [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/security/) részben talál információkat.
>

## <a name="scalable-performance-and-pools"></a>Méretezhető teljesítmény és készletek

Az SQL Database-zel minden adatbázis önálló, hordozható, és saját garantált teljesítményt nyújtó [szolgáltatásszinttel](sql-database-service-tiers.md) rendelkezik. Az SQL Database a különböző igényekre különböző teljesítmény-szinteket kínál, és lehetővé teszi az adatbázisok készletekbe rendezését az erőforrások hatékony és költségkímélő kihasználása érdekében.

### <a name="adjust-performance-and-scale-without-downtime"></a>Teljesítmény módosítása és skálázása leállási idő nélkül

Az SQL Database négy szolgáltatásszintet kínál a kis és nagy terhelést jelentő adatbázisokhoz: ezek a Basic, Standard, Premium, és Premium RS. Csekély havi díjért létrehozhatja első alkalmazását egy egyedülálló, kisméretű adatbázison, majd később az aktuális szükségletek alapján manuálisan vagy programon keresztül bármikor módosíthatja a szolgáltatásszintet. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre, és lehetővé teszi, hogy csak azokért az erőforrásokért fizessen, amelyekre és amikor szüksége van.

   ![méretezés](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Rugalmas készletek az erőforrások kihasználtságának maximalizálásához

Számos vállalkozás és alkalmazás számára elegendő az, ha önálló adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. A [rugalmas készletek](sql-database-elastic-pool.md) megoldást jelentenek erre a problémára. A koncepció egyszerű. A készletekhez az önálló adatbázis helyett teljesítmény-erőforrásokat foglalhat le, így nem egy önálló adatbázis teljesítményéért, hanem a készlet közös teljesítményéért fizet. 

   ![rugalmas készletek](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

A rugalmas készletekkel az erőforrásigények ingadozásakor sem kell a az adatbázis teljesítményének fel- és leskálázására koncentrálnia. A készletezett adatbázisok szükség szerint használják fel a teljesítmény-erőforrásokat. A készletezett adatbázisok használják az adatbáziskészlethez hozzárendelt teljesítményt, de nem lépik túl az adatbáziskészlet teljesítménykorlátait, így költségei előre jelezhetők még akkor is, ha az egyéni adatbázis-használat nem jelezhető előre. Ráadásul [adatbázisokat adhat hozzá a készlethez, és távolíthat el a készletből](sql-database-elastic-pool-manage-portal.md), így előre jelezhető költségek mellett néhány adatbázisról több ezer adatbázisra skálázhatja fel alkalmazását. A készlet adatbázisai számára elérhető erőforrások minimális és maximális mennyiségét is szabályozhatja annak érdekében, hogy a készlet egyetlen adatbázisa se használja fel a készlet összes erőforrását, és hogy minden készletezett adatbázis rendelkezzen egy garantált minimális erőforrás-mennyiséggel. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

### <a name="blend-single-databases-with-pooled-databases"></a>Önálló adatbázisok beolvasztása a készletezett adatbázisokba

Akár önálló adatbázisokat, akár rugalmas készleteket választ, a későbbiekben is sok mindent megváltoztathat. Az önálló adatbázisokat rugalmas készletekkel kombinálhatja, továbbá gyorsan és egyszerűen módosíthatja az önálló adatbázisok és a rugalmas készletek szolgáltatásszintjét, hogy mindig az adott helyzethez tudjon igazodni. Az Azure sokoldalúságának és széles körű alkalmazhatóságának köszönhetően tetszés szerint kombinálhatja az Azure-szolgáltatásokat SQL Database-adatbázisokkal, így kielégíthetők az egyedi, modern alkalmazástervezési igények, növelhető a költség- és erőforrás-hatékonyság, és új üzleti lehetőségek tárhatók fel.

### <a name="extensive-monitoring-and-alerting-capabilities"></a>Széles körű figyelési és riasztási funkciók

Hogyan hasonlítható össze az önálló adatbázisok és a rugalmas készletek relatív teljesítménye? Hogyan lehet megállapítani a fel- és leskálázás megfelelő mértékét? Használja [az önálló adatbázisok esetében a DTU-kon, rugalmas adatbázisok és adatbáziskészletek esetében pedig az eDTU-kon](sql-database-what-is-a-dtu.md) alapuló, teljesítmény-értékeléssel kombinált, [beépített teljesítményfigyelő](sql-database-performance.md) és [riasztási](sql-database-insights-alerts-portal.md) eszközöket. Ezek lehetővé teszik az aktuális vagy a projekthez kapcsolódó teljesítményigényeken alapuló fel- vagy leskálázás hatásainak gyors kiértékelését. A részletekért olvassa el [Az SQL Database beállításai és teljesítménye: mi érhető el az egyes szolgáltatásszinteken](sql-database-service-tiers.md) című részt.

Az SQL Database emellett [metrikák és diagnosztikai naplók kibocsátásával](sql-database-metrics-diag-logging.md) is képes megkönnyíteni a felügyeletet. Az SQL Database beállítható az erőforrás-használatra, feldolgozókra és munkamenetekre, valamint kapcsolatokra vonatkozó adatok tárolására a következő Azure-erőforrások valamelyikén:

- **Azure Storage**: Nagy tömegű telemetriai adat alacsony költségű archiválására
- **Azure Event Hub**: Az SQL Database telemetriai adatainak integrálásra saját egyedi figyelési megoldásokkal vagy élő adatfolyamatokkal
- **Azure Log Analytics**: Beépített figyelési megoldás jelentéskészítő, riasztó és a hibák következményeit enyhítő funkciókkal

    ![architektúra](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Rendelkezésre állás

Az Azure szolgáltatói szerződésében [(SLA)](http://azure.microsoft.com/support/legal/sla/) az ágazatban élenjáró módon 99,99 százalékos elérhetőséget biztosítunk – a Microsoft által kezelt adatbázisok globális hálózata teszi lehetővé, hogy alkalmazása a hét mind a 7 napján, napi 24 órában fusson. Az SQL Database ezen felül olyan beépített funkciókkal szolgálja [az üzletmenet folytonosságát és a globális méretezhetőséget](sql-database-business-continuity.md), mint például a következők:

- **[Automatikus biztonsági mentések](sql-database-automated-backups.md)**: Az SQL Database automatikusan készít teljes, differenciális és tranzakciónapló-alapú biztonsági mentéseket.
- **[Adott időpontnak megfelelő helyreállítás](sql-database-recovery-using-backups.md)**: Az SQL Database támogatja a visszaállást bármely időpontra az automatikus biztonsági mentések megőrzési idején belül.
- **[Aktív georeplikáció](sql-database-geo-replication-overview.md)**: Az SQL Database lehetővé teszi legfeljebb négy olvasható másodlagos adatbázis konfigurálását akár egyazon Azure adatközponton belül, akár globálisan elosztva.  Ha például egy SaaS-alkalmazás nagyszámú egyidejű csak olvasási tranzakciót kiszolgáló katalógus-adatbázisra épül, akkor az aktív georeplikáció által növelhető az olvashatóság, és megszűnik az elsődleges adatbázis magas olvasási terhelése miatti szűk keresztmetszet. 
- **[Feladatátvételi csoportok](sql-database-geo-replication-overview.md)**: Az SQL Database lehetővé teszi, hogy magas rendelkezésre állást és terheléselosztást kínáljon globális léptékben, beleértve a transzparens georeplikációt, nagy adatbázis-készletek feladatátvételét és rugalmas készleteket. A feladatátvételi csoportok és az aktív georeplikáció által a lehető legkevesebb adminisztrációs többletmunkával hozhatók létre globálisan elosztott SaaS-alkalmazások úgy, hogy a figyelés, útválasztás és feladatátvétel összetett feladatát az SQL Database-re bízzuk.

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

### <a name="intelligent-threat-detection"></a>Intelligens fenyegetésészlelés

 Az [SQL-fenyegetésészlelés](sql-database-threat-detection.md) (Threat Detection) [SQL Database naplózás](sql-database-auditing.md) funkcióját kihasználva folyamatosan figyeli az Azure SQL adatbázisokat, kiszűrendő a bizalmas adatokhoz való vélhetően ártó szándékú hozzáférési kísérleteket. Az SQL-fenyegetésészlelés új biztonsági réteget jelent, amely a rendellenes tevékenységekre adott riasztásokkal teszi lehetővé, hogy az ügyfelek bekövetkezésük pillanatában észlelhessék a vélhető fenyegetéseket, és reagálhassanak azokra. A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. Az SQL-fenyegetésészlelés által adott riasztások tartalmazzák a gyanús tevékenység részleteit, és javaslatot tesznek a fenyegetés kivizsgálására és mérséklésére tett műveletekre. A felhasználók a gyanús események elemzésével megállapíthatják, hogy azok hozzáférési kísérlet, illetéktelen behatolás vagy biztonsági rés kihasználása következtében történtek-e meg az adatbázisban. A fenyegetésészlelés biztonsági szakértelem vagy fejlett biztonsági figyelőrendszerek üzemeltetése nélkül is egyszerűvé teszi az adatbázis elleni lehetséges fenyegetések elhárítását.

## <a name="advanced-security-and-compliance"></a>Magas szintű biztonság és megfelelőség

Az SQl Database számos [beépített biztonsági és megfelelőségi szolgáltatást](sql-database-security-overview.md) kínálva járul hozzá, hogy az Ön alkalmazása eleget tegyen a különféle biztonsági és megfelelőségi elvárásoknak. 

### <a name="auditing-for-compliance-and-security"></a>Naplózás a megfelelőség és biztonság szolgálatában

Az [SQL Database naplózási szolgáltatása](sql-database-auditing.md) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, amely a felhasználó Azure Storage-fiókjában található. A naplózás segíthet a jogszabályi megfelelőség fenntartásában és az adatbázison végzett tevékenység megértésében, valamint az esetleg üzleti veszélyeket vagy biztonsági problémákat jelző rendellenességek feltárásában.

### <a name="data-encryption-at-rest"></a>Adat-titkosítás inaktív állapotban

Az SQL Database [transzparens adattitkosítása](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) azzal járul hozzá a rosszindulatú tevékenység elleni védelemhez, hogy valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást. 2017 májusától minden újonnan létrehozott Azure SQL adatbázis automatikusan transzparens adattitkosítás (TDE) védelmet kap. A TDE az SQL kipróbált inaktív-adattitkosítási technológiája, amelyet számos megfelelőségi szabvány előír az adattárolók eltulajdonítása esetén hatékony védelemként. Az ügyfelek biztonságosan és a szabályoknak megfelelően kezelhetik a TDE titkosítási kulcsokat és más titkos kulcsokat az Azure Key Vault kulcstároló használatával.

### <a name="data-encryption-in-motion"></a>Adattitkosítás menet közben

Az SQL Database az egyetlen adatbázisrendszer, amely a használatban lévő, inaktív és éppen lekérdezett bizalmas adatokat is védi az [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkcióval. Az Always Encrypted az iparágban elsőként kínál páratlan adatbiztonságot a bizalmas adatok eltulajdonításával járó illetéktelen behatolások ellen. Az Always Encrypted funkcióval az ügyfelek hitelkártyaszámai például mindig, még a lekérdezések feldolgozása során is titkosítva tárolódnak az adatbázisban, a visszafejtést csak arra jogosult alkalmazottak vagy az adatokat feldolgozó alkalmazások számára lehetővé téve.

### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

[Az SQL Database dinamikus adatmaszkolása](sql-database-dynamic-data-masking-get-started.md) úgy korlátozza a bizalmas adatok közzétételét, hogy maszkolja azokat a nem kiemelt jogosultságú felhasználók számára. A dinamikus adatmaszkolás azzal segít megelőzni a bizalmas adatokhoz való jogosulatlan hozzáférést, hogy az ügyfél által meghatározhatóvá teszi az alkalmazásrétegre gyakorolt minimális következményekkel felfedhető bizalmas adatok menyiségét. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.

### <a name="row-level-security"></a>Sorszintű biztonság

A [sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) lehetővé teszi az ügyfelek számára, hogy szabályozzák egy adatbázistábla soraihoz való hozzáférést a lekérdezést végrehajtó felhasználó jellemzői (például csoporttagság vagy végrehajtási környezet) alapján. A sorszintű biztonság (RLS) egyszerűsíti az alkalmazás védelmének megtervezését és kódolását. Az RLS használatával korlátozásokat érvényesíthet az adatsorokhoz való hozzáférésre. Biztosítható például, hogy a munkavállalók csak a szervezeti egységükre vonatkozó adatsorokhoz férjenek hozzá, vagy egy ügyfél adathozzáférése korlátozható a vállalatával kapcsolatos adatokra.

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
- [MSDN fórumok](https://social.msdn.microsoft.com/Forums/en-US/home?category=sqlserver): Műszaki kérdések
- [Microsoft Connect](https://connect.microsoft.com/SQLServer/Feedback): Hibák jelentése és funkciók kérése
- [Reddit](https://www.reddit.com/r/SQLServer/): Az SQL Server megvitatása

## <a name="next-steps"></a>Következő lépések

- Tekintse meg az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/sql-database/) az önálló adatbázisok és a rugalmas készletek költségeinek összehasonlításáért és árkalkulációjáért.

- Tekintse meg ezeket a rövid útmutatókat a kezdéshez:

  - [SQL Database létrehozása az Azure Portalon](sql-database-get-started-portal.md)  
  - [SQL Database létrehozása az Azure CLI-vel](sql-database-get-started-cli.md)
  - [SQL Database létrehozása PowerShell használatával](sql-database-get-started-powershell.md)

- Több Azure CLI és PowerShell-mintát talál itt:
  - [Azure CLI-minták az SQL Database-hez](sql-database-cli-samples.md)
  - [Azure PowerShell-minták az SQL Database-hez](sql-database-powershell-samples.md)
