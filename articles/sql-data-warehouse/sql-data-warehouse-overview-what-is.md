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
   ms.date="06/01/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# Mi az Azure SQL Data Warehouse?

Az Azure SQL Data Warehouse egy felhőalapú, horizontálisan felskálázható adatbázis, amely nagy mennyiségű relációs és nem relációs adatot képes feldolgozni. A nagymértékben párhuzamos feldolgozási (MPP) architektúrára épülő SQL Data Warehouse képes kezelni a vállalati terhelést. 

SQL Data Warehouse:

- Egyesíti a megbízható SQL Server relációs adatbázist és az Azure-felhő horizontális felskálázhatóságát. Másodpercek alatt növelheti, csökkentheti, szüneteltetheti vagy folytathatja a számítást.  Ezáltal költségeket takaríthat meg a processzor szükség szerinti felskálázásával és a használat csúcsidőn kívüli csökkentésével.
- Kihasználja az Azure platformot. Könnyű üzembe helyezni, problémamentesen karbantartható. Ráadásul az automatikus biztonsági mentéseknek köszönhetően teljesen hibatűrő. 
- Kiegészíti az SQL Server ökoszisztémáját.  Az ismerős SQL Server T-SQL nyelven, a megszokott eszközökkel fejleszthet.

A továbbiakban többet is megtudhat az SQL Data Warehouse kulcsszolgáltatásairól.

## Optimalizált

### Nagymértékben párhuzamos feldolgozási (MPP) architektúra

Az SQL Data Warehouse a Microsoft nagymértékben párhuzamos feldolgozási (MPP) architektúráját használja, amelyet arra terveztek, hogy a világ legnagyobb helyszíni adatraktárait futtassa.

Az MPP architektúra az adatokat jelenleg 60 meg nem osztott tárfiók és feldolgozóegység között osztja el. A rendszer az adatokat a Premium Storage Azure Storage Blobs szolgáltatásában tárolja, és a lekérdezés-végrehajtáshoz összekapcsolja azokat a számítási csomópontokkal. Ezzel az architektúrával oszd meg és uralkodj megközelítést alkalmazhatunk az összetett T-SQL lekérdezések futtatásánál. A feldolgozás során a vezérlő csomópont elemzi a lekérdezést, majd minden számítási csomópont párhuzamosan „meghódítja” az adatok rá eső részét. 

Az MPP architektúra és az Azure tárolási képességeinek egyesítésével az SQL Data Warehouse a következőkre képes:

- A tárterület a számítástól függetlenül növekedhet és csökkenhet.
- A számítási kapacitás az adatok áthelyezése nélkül növekedhet és csökkenhet. 
- A számítási kapacitás az adatok sérülése nélkül szüneteltethető.
- A számítási kapacitás egy pillanat alatt újra üzembe helyezhető.

Az architektúra részletes leírása alább található. 

![Az SQL Data Warehouse architektúrája][1]


- **Vezérlő csomópont:** A vezérlő csomópont „vezérli” a rendszert. Ez az az előtérbeli rendszer, amely az összes alkalmazással és kapcsolattal együttműködik. Az SQL Data Warehouse szolgáltatásban a vezérlő csomópontot az SQL Database működteti, és ugyanúgy lehet hozzá csatlakozni, mint eddig. A felszín alatt a vezérlő csomópont koordinálja az összes adatmozgatást és a számítást, ami a párhuzamos lekérdezések elosztott adatokon történő futtatásához szükséges. Amikor TSQL-lekérdezést küld az SQL Data Warehouse-nak, a vezérlő csomópont külön lekérdezésekké alakítja, amelyek párhuzamosan fognak futni minden számítási csomóponton.

- **Számítási csomópontok:** A számítási csomópontok állnak az SQL Data Warehouse teljesítménye mögött. Ezek mindegyike egy SQL Database, amely feldolgozza a lekérdezések lépéseit és kezeli az adatokat. Adatok hozzáadásakor az SQL Data Warehouse a számítási csomópontok segítségével osztja el a sorokat. A párhuzamos lekérdezéseket is a számítási csomópontok futtatják az adatokon. A feldolgozást követően ezek a csomópontok visszaküldik az eredményeket a vezérlő csomópontnak. A lekérdezés befejezéséhez a vezérlő csomópont aggregálja az eredményeket, majd visszaadja a végeredményt.


- **Tárolás:** A rendszer az adatokat az Azure Storage Blobs szolgáltatásban tárolja. Amikor a számítási csomópontok kommunikálnak az adatokkal, közvetlenül a blobtárolóra írnak és arról olvasnak. Mivel az Azure-tárterület átlátható módon és korlátlanul bővíthető, az SQL Data Warehouse is képes ugyanerre. Mivel a számítás és a tárolás nem függ egymástól, az SQL Data Warehouse automatikusan át tudja méretezni a tárolást, külön, a számítás méretezése nélkül, és fordítva.  Az Azure Storage emellett teljesen hibatűrő, és megkönnyíti a biztonsági mentési és helyreállítási folyamatokat.
   

- **Adatátviteli szolgáltatás:** Az adatátviteli szolgáltatás (DMS) az adatok csomópontok közötti áthelyezésére szolgáló technológiánk. A DMS hozzáférést biztosít a számítási csomópontoknak az összekapcsolásokhoz és az aggregációhoz szükséges adatokhoz. A DMS nem Azure-szolgáltatás. Ez egy Windows-szolgáltatás, amely minden csomóponton együtt fut az SQL Database szolgáltatással. Mivel a DMS a háttérben fut, nem fogja közvetlenül használni. Ha azonban megtekinti a lekérdezésterveket, megfigyelheti, hogy van köztük néhány DMS-művelet, mivel a lekérdezések párhuzamos futtatásához szükség van az adatmozgatás valamilyen formájára.


### Optimalizált lekérdezési teljesítmény

Az oszd meg és uralkodj stratégia mellett az MPP megközelítést több adatraktározásra vonatkozó teljesítményoptimalizálás segíti, köztük:

- Egy elosztott lekérdezésoptimalizáló és az összes adatra vonatkozó összetett statisztikák készlete. Az adatok méretével és a terjesztésével kapcsolatos információk felhasználásával a szolgáltatás képes optimalizálni a lekérdezéseket, mivel felméri az elosztott lekérdezési műveletek költségét.

- Az adatátviteli folyamatba integrált speciális algoritmusok és technikák hatékonyan mozgatják a lekérdezés végrehajtásához szükséges adatokat a számítási erőforrások között. Ezek az adatátviteli műveletek beépítettek, és minden adatátviteli szolgáltatást érintő optimalizálás automatikusan történik.

- Alapértelmezés szerint fürtözött oszlopcentrikus indexek. Az oszlopalapú tárolás használatával az SQL Data Warehouse akár 5x nagyobb szintű tömörítésre képes, mint a hagyományos, soros tárolással, és akár 10x-es lekérdezési teljesítménynövekedést érhet el. A rengeteg sort vizsgáló elemzési lekérdezések kiválóan működnek az oszlopcentrikus indexeken. 

## Méretezhető

Az SQL Data Warehouse architektúrája bevezeti a különálló tárolást és számítást, és lehetővé teszi a kettő egymástól független méretezését. Az SQL Database gyors és egyszerű üzembe helyezési struktúrája lehetővé teszi, hogy pillanatok alatt további számítási erőforrásokat lehessen elérni. Ehhez hozzájárul az Azure Storage Blobs használata. A Blobs használata a stabil replikált tárolás mellett alacsony költségű infrastruktúrát is biztosít az egyszerű bővítéshez.  A felhőméretű tárolás és az Azure-számítás kombinációjával az SQL Data Warehouse lehetővé teszi, hogy a lekérdezési teljesítmény tárolásáért csak abban az esetben és akkor fizessen, amikor szüksége van rá. A számítási kapacitás megváltoztatásához mindössze egy csúszkát kell jobbra vagy balra mozdítani az Azure portálon, de folyamat T-SQL és a PowerShell használatával is ütemezhető.

A számítási kapacitás tárolásfüggetlen teljes irányítása mellett az SQL Data Warehouse lehetővé teszi, hogy teljesen felfüggessze az adatraktárat. Ekkor a tárolástól függetlenül az összes számítási kapacitás visszakerül az Azure fő készletébe, így azonnal pénzt takaríthat meg. Amikor szükség van rá, egyszerűen folytathatja a számítást, és előkészítheti az adatait és a számítást a számítási feladatok számára.

Az SQL Data Warehouse a számítás használatát SQL adattárházegységekben méri (DWU). A DWU az adatraktár mögöttes teljesítményét mutatja, és a kialakítása olyan, hogy mindig biztosítsa az adatraktárhoz rendelt standard teljesítmény meglétét.  A DWU-kat a következők biztosítására használjuk:

- Az adatraktárak hatékonyan méretezhetők anélkül, hogy aggódni kellene a mögöttes hardver vagy szoftver miatt.

- Értelmezni lehet a DWU szintjén látható teljesítményt az adatraktár méretének megváltoztatása előtt.

- Meg lehet változtatni vagy át lehet helyezni a példány mögöttes hardverét és szoftverét anélkül, hogy az hatással lenne a számítási feladat teljesítményére

- Módosítani lehet a szolgáltatás mögöttes architektúráját anélkül, hogy az hatással lenne a számítási feladatok teljesítményére.

- Amint gyors ütemben fejlesztjük az SQL Data Warehouse teljesítményét, gondoskodunk a szolgáltatás további méretezhetőségéről és arról, hogy a fejlődés a teljes rendszeren egyenletes.

### Adattárházegységek

Az adattárházegységekre úgy tekintünk, mint egy három pontos mérőszámból álló mértékre, ami szorosan összefügg az adatraktározás munkateljesítményével. Az általános rendelkezésre állás tekintetében a Microsoft célja, hogy ezeket a kulcsfontosságú munkaterhelési mérőszámokat lineárisan lehessen méretezni az adatraktárhoz választott DWU-kkal.

**Vizsgálat/aggregáció:** Ehhez a munkaterhelési mérőszámhoz egy standard adatraktározási lekérdezésre van szükség, ami számos sort átvizsgál, majd komplex összesítést készít. Ez egy I/O- és processzorigényes művelet.

**Betöltés:** Ez a mérőszám az adatok szolgáltatásba történő bevitelét méri. A betöltéseket a PolyBase végzi, ami egy jellemző adatkészletet tölt be az Azure Storage Blob szolgáltatásból. A mérőszám úgy lett kialakítva, hogy a szolgáltatást a hálózat és a processzor szempontjából emelje ki.

**CREATE TABLE AS SELECT (CTAS):** A CTAS a táblamásolatok létrehozásának képességét méri. Ebbe beletartozik az adatok beolvasása a tárfiókból, az adatok szétosztása a készülék csomópontjai között és az adatok tárfiókba történő újbóli írása. Ez egy processzor- és hálózatigényes művelet.

### Mikor érdemes méretezni

Összességében azt szeretnénk, hogy a DWU-k egyszerűek legyenek. Amikor gyorsabban kellenek az eredmények, növelje meg a DWU-k számát, és fizessen a jobb teljesítményért.  Amikor kevesebb számítási teljesítményre van szüksége, csökkentse a DWU-k számát, és csak azért fizessen, amire szüksége van. Néhány eset, amikor érdemes megváltoztatni a DWU-k számát:

- Amikor nem kell lekérdezéseket futtatnia, például esténként vagy a hétvégeken, az összes futó lekérdezés megszakításához és az adatraktárhoz rendelt DWU-k eltávolításához szüneteltesse a számítási erőforrásokat.

- Amikor sok adatot kell betöltenie vagy átalakítási műveletet kell végeznie, akkor ajánlott vertikálisan felskálázni, hogy gyorsabban elérhetővé váljanak az adatok.

- Az ideális DWU érték megtalálásához próbáljon meg vertikálisan le- és felskálázni, az adatok betöltése után pedig futtasson néhány lekérdezést. Mivel a skálázás nem időigényes, több különböző teljesítményszintet is kipróbálhat anélkül, hogy egy óránál több időt szánna rá.

> [AZURE.NOTE] Vegye figyelembe, hogy az SQL Data Warehouse architektúrája miatt a kisebb adatmennyiségek esetében nem láthatók a várt teljesítménybeli képességek.  Javasoljuk, hogy kezdjen 1 TB-os, vagy annál nagyobb adatkötetekkel, hogy a valós teljesítménybeli előnyök jelenjenek meg.

## Integrált

Az SQL Data Warehouse az SQL Server megbízható relációs adatbázismotorján alapul, és számos, vállalati adatraktáraktól elvárt szolgáltatást tartalmaz. Ha már ismeri a Transact-SQL nyelvet, könnyedén hasznosíthatja ismereteit az SQL Data Warehouse szolgáltatásban. Függetlenül attól, hogy kezdő vagy haladó, a dokumentációban található példák segítenek a kezdésben. Gondoljon arra, hogyan épülnek fel az SQL Data Warehouse nyelvi elemei:

- Az SQL Data Warehouse számos művelethez az SQL Server Transact-SQL (TSQL) szintaxisát használja, és a hagyományos SQL szerkezetek széles körét támogatja, például a tárolt eljárásokat, a felhasználó által definiált függvényeket, a táblaparticionálást, az indexeket és a rendezéseket.

- Az SQL Data Warehouse emellett számos élvonalbeli SQL Server szolgáltatást is tartalmaz, beleérve a fürtözött oszlopcentrikus indexeket, a PolyBase-integrációt és az adatok naplózását (fenyegetésértékeléssel).

- Mivel az SQL Data Warehouse még fejlesztés alatt áll, előfordulhat, hogy nem lesznek elérhetők a TSQL nyelv egyes elemei, amelyek kevésbé gyakran fordulnak elő az adatraktározás számítási feladatainál vagy újonnan lettek bevezetve az SQL Serverbe. Az ezzel kapcsolatos információkat az áttelepítési dokumentációban találja.

A Transact-SQL nyelv és az SQL Server, az SQL Data Warehouse, az SQL Database és az Analytics Platform System egyező szolgáltatásai segítségével olyan megoldást fejleszthet, amely megfelel az adattárolási igényeinek. A teljesítmény, a biztonság és a méretezési követelmények alapján eldöntheti, hol szeretné tárolni az adatait, majd szükség szerint továbbíthatja az adatokat a különböző rendszerek között.

Az SQL Server TSQL felületének bevezetése mellett az SQL Data Warehouse számos olyan eszközzel integrálódik, ami ismerős lehet az SQL Server felhasználói számára. Néhány eszközkategória SQL Data Warehouse szolgáltatásba történő integrálására koncentráltunk, ilyenek a következők:

**Hagyományos SQL Server-eszközök:** Az SQL Data Warehouse szolgáltatás teljesen integrálható az SQL Server Analysis Services, az Integration Services és a Reporting Services szolgáltatásokkal.

**Felhőalapú eszközök:** Az SQL Data Warehouse szolgáltatást számos új eszközzel együtt lehet használni az Azure-ban, és átfogóan integrálja az Azure Data Factory, a Stream Analytics, a Machine Learning és a Power BI szolgáltatásokat.

**Harmadik felektől származó eszközök:** Sok külső eszközszolgáltató tanúsított módon integrálta az eszközeit az SQL Data Warehouse szolgáltatással. Tekintse meg a teljes listát.

## Hibrid

Az SQL Data Warehouse PolyBase szolgáltatással való használata egyedülálló képességet biztosít a felhasználók számára az adatok ökoszisztémán belüli mozgatására, és ezzel lehetővé teszi a speciális hibrid forgatókönyvek kialakítását nem relációs és helyszíni adatforrásokkal.

A PolyBase használata egyszerű, és lehetővé teszi a különböző forrásokból származó adatok használatát a jól ismert T-SQL parancsokkal. A PolyBase lehetővé teszi az Azure Blob Storage szolgáltatásban tárolt nem relációs adatok normál táblákhoz hasonló lekérdezését. A PolyBase szolgáltatást akkor használja, ha nem relációs adatokat szeretne lekérdezni vagy nem relációs adatokat szeretne importálni az SQL Data Warehouse szolgáltatásba.

- A PolyBase külső táblák segítségével fér hozzá a nem relációs adatokhoz. A tábladefiníciókat az SQL Data Warehouse tárolja, és az SQL-eszközökkel, illetve a szokványos relációs adatok elérésére használt eszközökkel lehet őket elérni.

- A PolyBase integrációs szempontból rendszerfüggetlen. Minden általa támogatott forrás ugyanazokat a szolgáltatásokat és funkciókat érheti el. A PolyBase által beolvasott adatok többféle formátumúak lehetnek, a tagolt fájlokat és az ORC fájlokat is beleértve.

- A PolyBase szolgáltatással hozzá lehet férni azokhoz a blobtárolókhoz, amelyet egy HD Insight-fürt tárolóként használ, így relációs és nem relációs eszközökkel is élvonalbeli hozzáférést biztosít ugyanazokhoz az adatokhoz.

## További lépések

Most, hogy jobban megismerte az SQL Data Warehouse szolgáltatást, tudjon meg többet az [adatraktározás számítási feladatáról], az SQL Data Warehouse [kiépítéséről] és a [mintaadatok betöltéséről].  Esetleg tekintsen meg néhányat a többi SQL Data Warehouse erőforrás közül.  

- [Blogok] 
- [Funkciókérések]
- [Videók]
- [CAT csapatblogok]
- [Támogatási jegy létrehozása]
- [MSDN fórum]
- [Stack Overflow fórum]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Támogatási jegy létrehozása]: ./sql-data-warehouse-get-started-create-support-ticket.md
[adatraktározás számítási feladatáról]: ./sql-data-warehouse-overview-workload.md
[mintaadatok betöltése]: ./sql-data-warehouse-get-started-manually-load-samples.md
[kiépítés]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->
[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT csapatblogok]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN fórum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow fórum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse



<!--HONumber=Jun16_HO2-->


