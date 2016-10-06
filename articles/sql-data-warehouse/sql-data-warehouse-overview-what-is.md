<properties
   pageTitle="Mi az Azure SQL Data Warehouse? | Microsoft Azure"
   description="Vállalati szintű elosztott adatbázis, amely petabájtnyi mennyiségű relációs és nem relációs adatot képes feldolgozni. Ez az iparág első felhőalapú adatraktára, amely másodpercek alatt szüneteltethető, illetve növelhető vagy csökkenthető a mérete."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/27/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>



# Mi az Azure SQL Data Warehouse?

Az Azure SQL Data Warehouse egy felhőalapú, horizontálisan felskálázható adatbázis, amely nagy mennyiségű relációs és nem relációs adatot képes feldolgozni. A nagymértékben párhuzamos feldolgozási (MPP) architektúrára épülő SQL Data Warehouse képes kezelni a vállalati terhelést.

SQL Data Warehouse:

- Egyesíti az SQL Server relációs adatbázist és az Azure-felhő horizontális felskálázhatóságát. Másodpercek alatt növelheti, csökkentheti, szüneteltetheti vagy folytathatja a számítást. A processzor szükség szerinti felskálázásával és a használat csúcsidőn kívüli csökkentésével költségeket takaríthat meg.
- Kihasználja az Azure platformot. Egyszerűen üzembe helyezhető, zökkenőmentesen karbantartható, és teljes mértékben hibatűrő az automatikus biztonsági mentésnek köszönhetően.
- Kiegészíti az SQL Server ökoszisztémáját. Az ismerős SQL Server Transact-SQL (T-SQL) nyelven, a megszokott eszközökkel fejleszthet.

A cikk az SQL Data Warehouse főbb szolgáltatásait ismerteti.

## Nagymértékben párhuzamos feldolgozási architektúra

Az SQL Data Warehouse egy nagymértékben párhuzamos feldolgozási (MPP) elosztott adatbázisrendszerre épül. Az adatok és a feldolgozási képesség több csomópont közötti elosztásával az SQL Data Warehouse olyan rendkívüli méretezhetőséget kínál, amely messze meghaladja bármilyen különálló rendszer lehetőségeit.  Az SQL Data Warehouse a háttérben számos olyan tárolóban és feldolgozási egységen osztja el az adatokat, amelyek nem osztanak meg semmit. A rendszer az adatokat a helyileg redundáns Premium tárolóban tárolja, és a lekérdezés-végrehajtáshoz összekapcsolja azokat a számítási csomópontokkal. Ezzel az architektúrával az SQL Data Warehouse az „oszd meg és uralkodj” megközelítést alkalmazza a műveletek és az összetett lekérdezések futtatásához. A kérelmeket a vezérlő csomópont fogadja, optimalizálja, majd átadja a számítási csomópontoknak, hogy azok végrehajthassák a saját feladataikat párhuzamosan.

Az MPP architektúra és az Azure tárolási képességeinek egyesítésével az SQL Data Warehouse a következőkre képes:

- A tárterület a számítástól függetlenül növekedhet és csökkenhet.
- A számítási kapacitás az adatok áthelyezése nélkül növekedhet és csökkenhet.
- A számítási kapacitás az adatok sérülése nélkül szüneteltethető.
- A számítási kapacitás egy pillanat alatt újra üzembe helyezhető.

Az architektúrát a következő ábra szemlélteti.

![Az SQL Data Warehouse architektúrája][1]


**Vezérlő csomópont:** a vezérlő csomópont kezeli és optimalizálja a lekérdezéseket. Ez az az előtérbeli rendszer, amely az összes alkalmazással és kapcsolattal együttműködik. Az SQL Data Warehouse szolgáltatásban a vezérlő csomópontot az SQL Database működteti, és ugyanúgy lehet hozzá csatlakozni, mint eddig. A felszín alatt a vezérlő csomópont koordinálja az összes adatmozgatást és a számítást, ami a párhuzamos lekérdezések elosztott adatokon történő futtatásához szükséges. Amikor T-SQL-lekérdezést küld az SQL Data Warehouse-nak, a vezérlő csomópont külön lekérdezésekké alakítja át, amelyek párhuzamosan fognak futni minden számítási csomóponton.

**Számítási csomópontok:** a számítási csomópontok állnak az SQL Data Warehouse teljesítménye mögött. Ezek olyan SQL-adatbázisok, amelyek tárolják az adatokat, és feldolgozzák a lekérdezést. Adatok hozzáadásakor az SQL Data Warehouse a számítási csomópontokra osztja el a sorokat. A párhuzamos lekérdezéseket is a számítási csomópontok futtatják az adatokon. A feldolgozást követően ezek a csomópontok visszaküldik az eredményeket a vezérlő csomópontnak. A lekérdezés befejezéséhez a vezérlő csomópont aggregálja az eredményeket, majd visszaadja a végeredményt.

**Tárolás:** a rendszer az adatokat az Azure Blob szolgáltatásban tárolja. Amikor a számítási csomópontok kommunikálnak az adatokkal, közvetlenül a blobtárolóra írnak és arról olvasnak. Mivel az Azure-tárolót transzparens módon és korlátlanul bővítheti, az SQL Data Warehouse is képes ugyanerre. Mivel a számítás és a tárolás nem függ egymástól, az SQL Data Warehouse automatikusan át tudja méretezni a tárolást, külön, a számítás méretezése nélkül, és fordítva. Az Azure Blob tároló emellett teljesen hibatűrő, és megkönnyíti a biztonsági mentési és helyreállítási folyamatokat.

**Adatátviteli szolgáltatás:** az adatátviteli szolgáltatás (DMS) az adatok csomópontok közötti áthelyezését végzi. A DMS hozzáférést biztosít a számítási csomópontoknak az összekapcsolásokhoz és az aggregációhoz szükséges adatokhoz. A DMS nem Azure-szolgáltatás. Ez egy Windows-szolgáltatás, amely minden csomóponton együtt fut az SQL Database szolgáltatással. Mivel a DMS a háttérben fut, nem fogja közvetlenül használni. Ha azonban megtekinti a lekérdezésterveket, megfigyelheti, hogy van köztük néhány DMS-művelet, mivel a lekérdezések párhuzamos futtatásához szükség van az adatmozgatásra.


## Az adatraktár munkaterhelésére optimalizálva

Az MPP megközelítést több adatraktározásra vonatkozó teljesítményoptimalizálás segíti, beleértve a következőket:

- Egy elosztott lekérdezésoptimalizáló és az összes adatra vonatkozó összetett statisztikák készlete. Az adatok méretével és a terjesztésével kapcsolatos információk felhasználásával a szolgáltatás képes optimalizálni a lekérdezéseket, mivel felméri az elosztott lekérdezési műveletek költségét.

- Az adatátviteli folyamatba integrált speciális algoritmusok és technikák hatékonyan mozgatják a lekérdezés végrehajtásához szükséges adatokat a számítási erőforrások között. Ezek az adatátviteli műveletek beépítettek, és minden adatátviteli szolgáltatást érintő optimalizálás automatikusan történik.

- Alapértelmezés szerint fürtözött **oszlopcentrikus** indexek. Az oszlopalapú tárolás használatával az SQL Data Warehouse akár 5-ször nagyobb szintű tömörítésre is képes, mint a hagyományos, soralapú tárolással, és akár 10-szeres vagy nagyobb lekérdezési teljesítménynövekedést érhet el. A rengeteg sort vizsgáló elemzési lekérdezések kiválóan működnek az oszlopcentrikus indexeken.


## Kiszámítható és méretezhető teljesítmény

Az SQL Data Warehouse elkülöníti a tárolást és a számítást, ami lehetővé teszi a funkciók egymástól független skálázását. Az SQL Data Warehouse gyorsan és könnyen skálázható, ha az adott pillanatban további számítási erőforrásokra van szükség. Ehhez hozzájárul az Azure Blob tároló használata. A blobok használata a stabil replikált tárolás mellett alacsony költségű infrastruktúrát is biztosít az egyszerű bővítéshez. A felhőméretű tárolás és az Azure-számítás kombinációjával az SQL Data Warehouse lehetővé teszi, hogy a lekérdezési teljesítmény tárolásáért csak abban az esetben és akkor fizessen, amikor szüksége van arra. A számítási kapacitás megváltoztatásához mindössze egy csúszkát kell jobbra vagy balra mozdítani az Azure Portalon, de a folyamat a T-SQL és a PowerShell használatával is ütemezhető.

A számítási kapacitás tárolásfüggetlen teljes irányítása mellett az SQL Data Warehouse lehetővé teszi, hogy teljesen felfüggessze az adatraktárat, ami azt jelenti, hogy nem kell fizetnie a számításért, amikor nincs szüksége arra. Ekkor a tárolástól függetlenül az összes számítási kapacitás visszakerül az Azure fő készletébe, így pénzt takaríthat meg. Amikor szükség van rá, egyszerűen folytathatja a számítást, és előkészítheti az adatait és a számítást a számítási feladatok számára.

## Adattárházegységek

Az erőforrások SQL Data Warehouse szolgáltatásnak történő kiosztása az adattárházegységekkel (DWU) mérhető. A DWU-val az SQL Data Warehouse számára kiosztott háttérerőforrások, például a processzor, a memória és az IOPS mérhető. A DWU-k számának növelése növeli az erőforrásokat és a teljesítményt. A DWU-k a következőket biztosítják:

- Az adatraktárak könnyen skálázhatók anélkül, hogy aggódni kellene a mögöttes hardver vagy szoftver miatt.

- A DWU-szintek teljesítményjavulása is előrejelezhető az adatraktár méretének megváltoztatása előtt.

- Meg lehet változtatni vagy át lehet helyezni a példány mögöttes hardverét és szoftverét anélkül, hogy az hatással lenne a számítási feladat teljesítményére.

- A Microsoft módosíthatja a szolgáltatás mögöttes architektúráját anélkül, hogy az hatással lenne a számítási feladatok teljesítményére.

- A Microsoft gyors ütemben fejleszti az SQL Data Warehouse teljesítményét, és gondoskodik a szolgáltatás további méretezhetőségéről, valamint arról, hogy a fejlődés a teljes rendszeren mindenhol tetten érhető legyen.

Az adattárházegységek három pontos mérőszámból álló mértéknek tekinthetők, amely szorosan összefügg az adatraktározás munkateljesítményével. A cél az, hogy ezeket a kulcsfontosságú munkaterhelési mérőszámokat lineárisan lehessen skálázni az adatraktárhoz választott DWU-kkal.

**Vizsgálat/aggregáció:** Ehhez a munkaterhelési mérőszámhoz egy standard adatraktározási lekérdezésre van szükség, ami számos sort átvizsgál, majd komplex összesítést készít. Ez egy I/O- és processzorigényes művelet.

**Betöltés:** Ez a mérőszám az adatok szolgáltatásba történő bevitelét méri. A betöltéseket a PolyBase végzi úgy, hogy egy jellemző adatkészletet tölt be az Azure Blob tárolóból. A mérőszám úgy lett kialakítva, hogy a szolgáltatást a hálózat és a processzor szempontjából emelje ki.

**Create Table As Select (CTAS):** A CTAS a táblák másolásának képességét méri. Ebbe beletartozik az adatok beolvasása a tárfiókból, az adatok szétosztása a készülék csomópontjai között és az adatok tárfiókba történő újbóli írása. Ez egy processzor-, I/O- és hálózatigényes művelet.

## Felfüggesztés és méretezés igény szerint

Amikor gyorsabban kellenek az eredmények, növelje meg a DWU-k számát, és fizessen a jobb teljesítményért. Amikor kevesebb számítási teljesítményre van szüksége, csökkentse a DWU-k számát, és csak azért fizessen, amire szüksége van. Előfordulhat, hogy a következő forgatókönyvek esetében módosítania kell a DWU-kat:

- Amikor nem kell lekérdezéseket futtatnia, például esténként vagy a hétvégéken, kikapcsolhatja a lekérdezéseket. Ezután felfüggesztheti a számítási erőforrások működését, hogy ne kelljen fizetnie a DWU-k után, amikor nincs szüksége rájuk.

- Ha a rendszer iránti igény alacsony, érdemes kis méretűre csökkentenie a DWU-t. Az adatokhoz továbbra is hozzáférhet, de jelentős költségmegtakarítást érhet el.

- Amikor sok adatot kell betöltenie vagy átalakítási műveletet kell végeznie, akkor ajánlott vertikálisan felskálázni, hogy gyorsabban elérhetővé váljanak az adatok.

Az ideális DWU érték megtalálásához próbáljon meg vertikálisan le- és felskálázni, az adatok betöltése után pedig futtasson néhány lekérdezést. Mivel a skálázás nem időigényes, több különböző teljesítményszintet is kipróbálhat akár egy óránál rövidebb idő alatt.  Tartsa szem előtt, hogy az SQL Data Warehouse nagy mennyiségű adat feldolgozására van kialakítva, és ahhoz, hogy ténylegesen ki tudja használni a skálázási lehetőségeit – különösen az általunk kínált nagyobb méretek esetében –, olyan adathalmazt célszerű használnia, amelynek mérete megközelíti vagy meghaladja az 1 TB-ot.


## Alapja az SQL Server

Az SQL Data Warehouse az SQL Server relációs adatbázismotorján alapul, és számos, a vállalati adatraktáraktól elvárt szolgáltatást tartalmaz. Ha már ismeri a T-SQL nyelvet, könnyűszerrel hasznosíthatja ismereteit az SQL Data Warehouse szolgáltatásban. Függetlenül attól, hogy kezdő vagy haladó, a dokumentációban található példák segítenek a kezdésben. Gondoljon arra, hogyan épülnek fel az SQL Data Warehouse nyelvi elemei:

- Az SQL Data Warehouse számos művelethez a T-SQL szintaxisát használja. Ezenkívül a hagyományos SQL-szerkezetek széles körét támogatja, például a tárolt eljárásokat, a felhasználó által definiált függvényeket, a táblaparticionálást, az indexeket és a rendezéseket.

- Az SQL Data Warehouse emellett számos újabb SQL Server szolgáltatást is tartalmaz, beleérve a fürtözött **oszlopcentrikus** indexeket, a PolyBase-integrációt és az adatok naplózását (fenyegetésértékeléssel).

- Előfordulhat, hogy jelenleg nem érhetők el a T-SQL egyes nyelvi elemei, amelyeket ritkán használnak az adatraktározási feladatoknál, vagy amelyek újdonságnak számítanak az SQL Serverben. További információt az [áttelepítési dokumentációban][] talál.

A Transact-SQL nyelv és az SQL Server, az SQL Data Warehouse, az SQL Database és az Analytics Platform System egyező szolgáltatásai segítségével olyan megoldást fejleszthet, amely megfelel az adattárolási igényeinek. A teljesítmény, a biztonság és a méretezési követelmények alapján eldöntheti, hol szeretné tárolni az adatait, majd szükség szerint továbbíthatja az adatokat a különböző rendszerek között.

## Adatvédelem

Az SQL Data Warehouse minden adatot az Azure Premium helyileg redundáns tárolóban tárol. A rendszer több szinkron másolatot tart az adatokról a helyi adatközpontban, így transzparens adatvédelmet garantál helyi hibák esetén. Emellett az SQL Data Warehouse rendszeres időközönként automatikusan végrehajtja az aktív (nem felfüggesztett) adatbázisok biztonsági mentését az Azure Storage Snapshots használatával. A biztonsági mentés és a visszaállítás működésének részletes ismertetését [A biztonsági mentés és a visszaállítás áttekintése][] című cikkben olvashatja el.

## Integráció a Microsoft eszközeivel

Az SQL Data Warehouse együttműködik számos olyan eszközzel, amelyet az SQL Server felhasználói jól ismernek. Ezek a következők:

**Hagyományos SQL Server-eszközök:** az SQL Data Warehouse szolgáltatás teljesen integrálható az SQL Server Analysis Services, az Integration Services és a Reporting Services szolgáltatással.

**Felhőalapú eszközök:** az SQL Data Warehouse szolgáltatást számos új eszközzel együtt lehet használni az Azure-ban, beleértve az Azure Data Factory, a Stream Analytics, a Machine Learning és a Power BI szolgáltatást. A teljes listát lásd: [Az integrált eszközök áttekintése][].

**Harmadik felektől származó eszközök:** sok külső eszközszolgáltató tanúsított módon integrálta az eszközeit az SQL Data Warehouse szolgáltatással. A teljes listát lásd: [Az SQL Data Warehouse megoldási partnerei][].

## Hibrid adatforrások forgatókönyvei

Az SQL Data Warehouse a PolyBase technológiával való használata egyedülálló képességet biztosít a felhasználók számára az adatok ökoszisztémán belüli mozgatására, és ezzel lehetővé teszi a speciális hibrid forgatókönyvek kialakítását nem relációs és helyszíni adatforrásokkal.

A PolyBase lehetővé teszi a különböző forrásokból származó adatok használatát a jól ismert T-SQL parancsokkal. A PolyBase lehetővé teszi az Azure Blob tárolóban tárolt nem relációs adatok normál táblákhoz hasonló lekérdezését. A PolyBase szolgáltatást akkor használja, ha nem relációs adatokat szeretne lekérdezni vagy nem relációs adatokat szeretne importálni az SQL Data Warehouse szolgáltatásba.

- A PolyBase külső táblák segítségével fér hozzá a nem relációs adatokhoz. A tábladefiníciókat az SQL Data Warehouse tárolja, és az SQL-eszközökkel, illetve a relációs adatok elérésére használt szokásos eszközökkel érheti el őket.

- A PolyBase integrációs szempontból rendszerfüggetlen. Minden általa támogatott forrás ugyanazokat a szolgáltatásokat és funkciókat érheti el. A PolyBase által beolvasott adatok többféle formátumúak lehetnek, a tagolt fájlokat és az ORC fájlokat is beleértve.

- A PolyBase technológiával hozzá lehet férni azokhoz a blobtárolókhoz, amelyet egy HD Insight-fürt tárolóként használ. Így ugyanazokat az adatokat relációs és nem relációs eszközökkel is elérheti.

## Következő lépések

Miután a fentiekben áttekintést kapott az SQL Data Warehouse használatáról, ismerje meg, hogyan hozhat létre gyorsan egy [SQL Data Warehouse létrehozása][] és hogyan [mintaadatokat tölthet be][]. Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét][], amikor az új fogalmakkal ismerkedik. Esetleg tekintsen meg néhányat a többi SQL Data Warehouse erőforrás közül.  

- [Ügyfelek sikertörténetei]
- [Blogok]
- [Funkciókérések]
- [Videók]
- [Az ügyféltanácsadói csapat blogjai]
- [Támogatási jegy létrehozása]
- [MSDN-fórum]
- [Stack Overflow-fórum]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Támogatási jegy létrehozása]: ./sql-data-warehouse-get-started-create-support-ticket.md
[mintaadatokat tölthet be]: ./sql-data-warehouse-load-sample-databases.md
[SQL Data Warehouse létrehozása]: ./sql-data-warehouse-get-started-provision.md
[áttelepítési dokumentációban]: ./sql-data-warehouse-overview-migrate.md
[Az SQL Data Warehouse megoldási partnerei]: ./sql-data-warehouse-partner-business-intelligence.md
[Az integrált eszközök áttekintése]: ./sql-data-warehouse-overview-integrate.md
[A biztonsági mentés és a visszaállítás áttekintése]: ./sql-data-warehouse-restore-database-overview.md
[Azure szószedetét]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Ügyfelek sikertörténetei]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Az ügyféltanácsadói csapat blogjai]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-fórum]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow-fórum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse



<!--HONumber=Sep16_HO4-->


