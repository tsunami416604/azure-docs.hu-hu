---
title: "Az Azure SQL adatbázis memórián belüli technológiák |} Microsoft Docs"
description: "Az Azure SQL adatbázis memórián belüli technológiák jelentősen javítja a tranzakciós teljesítményét és elemzés munkaterhelések."
services: sql-database
documentationCenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: 250ef341-90e5-492f-b075-b4750d237c05
ms.service: sql-database
ms.custom: develop databases
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jodebrui
ms.openlocfilehash: 8930595821cc7662c4ff792b73eb357f1ba29307
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>A memórián belüli technológiái az SQL-adatbázis teljesítményének optimalizálása

A memórián belüli technológiái az Azure SQL Database, a különböző munkaterhelések teljesítménynövekedést érhet el: tranzakciós (online tranzakciós feldolgozási (OLTP)), elemzés (online analitikus feldolgozási (OLAP)), és a vegyes (hibrid tranzakció / analitikus feldolgozási (HTAP)). Hatékonyabb lekérdezés és a tranzakció-feldolgozást memórián belüli technológiák is segítséget költségeinek csökkentése. Általában nem kell teljesítménynövekedéshez eléréséhez az adatbázis árképzési szintjének frissítése. Néhány esetben is előfordulhat közben továbbra sem szűnik meg a memórián belüli szolgáltatáshoz. a teljesítménnyel kapcsolatos fejlesztések az árképzési szint csökkentése.

Az alábbiakban a két példa hogyan segített a memórián belüli online Tranzakciófeldolgozási jelentősen fejleszti a teljesítményt:

- A memórián belüli online Tranzakciófeldolgozási, [kvórum üzleti megoldások tudta 70 %-kal dtu-k fokozása mellett az alkalmazások és szolgáltatások duplán](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).
    - DTU azt jelenti, hogy *adatbázis-átviteli egység*, és egy az erőforrás-felhasználás mesurement tartalmazza.
- A következő videó bemutatja az erőforrás-felhasználást egy példa munkaterhelés jelentős fejlesztéseket: [memórián belüli online Tranzakciófeldolgozási az Azure SQL adatbázis Video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
    - További részletekért lásd a következő blogbejegyzésben: [memórián belüli online Tranzakciófeldolgozási az Azure SQL adatbázis Blog utáni](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

A memóriában technológiák minden adatbázisa prémium tarifacsomagra, beleértve az adatbázisok prémium rugalmas készletek érhetők el.

A következő videó ismerteti a lehetséges teljesítménynövekedést érhet el, a memórián belüli szolgáltatáshoz. az Azure SQL-adatbázis. Ne feledje, hogy jobb a teljesítménye, hogy mindig számos tényezőtől függ, többek között a munkaterhelés és adatok, az adatbázis minták, és így tovább.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Az Azure SQL-adatbázis a következő memórián belüli technológiákat rendelkezik:

- *A memórián belüli online Tranzakciófeldolgozási* növeli az adatátviteli sebességet, és csökkenti a késést tranzakció feldolgozásra. A memórián belüli online Tranzakciófeldolgozási előnyeit kihasználó forgatókönyvek a következők: nagy átviteli tranzakció, például kereskedelmi és játékokban, adatfeldolgozást események vagy az IoT-eszközök, gyorsítótárazás, az adatok betöltését, és az ideiglenes tábla és a tábla változó forgatókönyvek feldolgozása.
- *Fürtözött oszlopcentrikus indexek* csökkenti a tárolási erőforrásigényét (legfeljebb 10-szer) és a jelentéskészítési és elemzési lekérdezések teljesítményének javítása. Segítségével azt a ténytáblák az adatpiac jelenti az igényei több adatot az adatbázishoz, és javíthatja a teljesítményt. Is segítségével, és az előzmények az operatív adatbázis archivált, és akár 10-szer több adat lekérdezése.
- *Nem fürtözött oszloptárindex* HTAP segítséget kaphat a valós idejű az operatív adatbázis közvetlen lekérdezése, olcsóbbá kivonatot futtatásának szükségessége nélkül a vállalatra vonatkozó átalakító, és (ETL) folyamat betölteni, és várja meg a az adatraktár kell feltöltenie. Nem fürtözött oszloptárindex OLTP adatbázis nagyon gyorsan elemzési lekérdezések végrehajtása közben csökkenti a működési munkaterhelés gyakorolt hatás engedélyezése.
- A oszlopcentrikus indexszel rendelkező memóriaoptimalizált táblák kombinációja is lehet. Ez a kombináció lehetővé teszi a rendkívül gyors tranzakció-feldolgozást, és a *egyidejűleg* elemzési lekérdezések nagyon gyorsan futtatnak ugyanazokat az adatokat.

Oszlopcentrikus indexek és a memórián belüli online Tranzakciófeldolgozási óta SQL Server termékhez 2012 és a 2014, illetve. Az Azure SQL Database és SQL Server megosztani a memórián belüli technológiák azonos végrehajtására. Következő lépésként ezek a technológiák új lehetőségek kiadott az Azure SQL Database először előtt a SQL Server.

Ez a témakör a memórián belüli online Tranzakciófeldolgozási és oszlopcentrikus indexek, az Azure SQL Database vonatkozó szempontokat ismerteti, és minták is tartalmazza:
- A tárolási és adatok méretkorlátait látni fogja, ezek a technológiák hatását.
- Láthatja, hogy ezek a technológiák között a különböző árképzési szinteket használó adatbázisok mozgása kezelése.
- Látni fogja, hogy bemutassa a memórián belüli online Tranzakciófeldolgozási, valamint az Azure SQL Database oszlopcentrikus indexek használata két minta.

További információ a következő forrásanyagokban talál.

A technológiákkal kapcsolatos részletesebb információk:

- [Memórián belüli online Tranzakciófeldolgozási áttekintése és a használati forgatókönyvek](https://msdn.microsoft.com/library/mt774593.aspx) (tartalmazza a felhasználói esettanulmányok és információk első lépések)
- [A memórián belüli online Tranzakciófeldolgozási dokumentációja](http://msdn.microsoft.com/library/dn133186.aspx)
- [Oszlopcentrikus indexek útmutató](https://msdn.microsoft.com/library/gg492088.aspx)
- Hibrid tranzakciós/analitikus feldolgozási (HTAP), más néven [valós idejű működési elemzés](https://msdn.microsoft.com/library/dn817827.aspx)

Gyors segítséget nyújthat a memórián belüli online Tranzakciófeldolgozási: [Quick Start 1: memórián belüli online Tranzakciófeldolgozási technológiák gyorsabb a T-SQL teljesítmény](http://msdn.microsoft.com/library/mt694156.aspx) (segítségével egy másik cikkben első lépések)

Részletes videók technológiákkal kapcsolatos:

- [Memórián belüli online Tranzakciófeldolgozási az Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (amely tartalmazza a bemutató jobb teljesítményt nyújt, és ezekkel az eredményekkel saját kezűleg reprodukálásához szükséges lépések)
- [Memórián belüli online Tranzakciófeldolgozási videók: Mi van, és ha/hogyan a használatára](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)
- [Az Oszloptárindex: A memórián belüli Analytics videók Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

## <a name="storage-and-data-size"></a>Tárolás és az adatok mérete

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>A memórián belüli online Tranzakciófeldolgozási az adatok méretét és a tárolási cap

A memórián belüli online Tranzakciófeldolgozási memóriaoptimalizált táblákkal, felhasználói adatok tárolásához használt tartalmazza. Ezek a táblázatok memóriában szükségesek. Kezelheti a memória közvetlenül az SQL Database szolgáltatásban, mert a felhasználói adatok egy kvótát fogalmát van. Ezzel az ötlettel nevezzük *memórián belüli online Tranzakciófeldolgozási tárolási*.

Minden tarifacsomag és minden rugalmas készlet árképzési szint támogatott önálló adatbázis bizonyos mennyiségű memórián belüli online Tranzakciófeldolgozási tárolási tartalmazza. Írásának időpontjában a tárolási gigabájt minden 125 adatbázis-tranzakciós egységek (dtu-i) vagy a rugalmas adatbázis-tranzakciós egységek (edtu-k) beolvasása.

A [SQL adatbázis szolgáltatásszintjeinek](sql-database-service-tiers.md) cikk rendelkezik a memórián belüli online Tranzakciófeldolgozási elérhető tároló minden támogatott önálló adatbázis és a rugalmas készlet árképzési szint hivatalos listája.

A következő elemek felé a memórián belüli online Tranzakciófeldolgozási tároló maximális száma:

- A memóriaoptimalizált táblák és Táblaváltozók adatsorok aktív felhasználónként. Vegye figyelembe, hogy a régi sor verziók száma nem a kap felé.
- A memóriaoptimalizált táblák indexei.
- Az ALTER TABLE műveletek működési munkaterhelés.

Elérte a kap, ha ki a kvóta hibaüzenetet kap, és már nem tudunk beszúrásához vagy frissítéséhez adatokat. Ez a hiba elhárítása érdekében, törli az adatokat, vagy növelje az adatbázis vagy a készlet tarifacsomagját.

További információk a tárhely kihasználtságát a memórián belüli online Tranzakciófeldolgozási figyelése és a riasztások konfigurálása, ha majdnem elérte a maximális: [figyelő memórián belüli tároló](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Tudnivalók a rugalmas készletek

A rugalmas készletek a memórián belüli online Tranzakciófeldolgozási tárolási a készletben lévő összes adatbázisok által megosztott. Ezért a használata egy adatbázis hatással lehet más adatbázisok. Ez a két megoldást a következők:

- Állítsa a Max-edtu-k az adatbázisok, amely kisebb, mint egy teljes készlet edtu-k számát. A maximális caps a memórián belüli online Tranzakciófeldolgozási tárhely kihasználtságát, a készlet méretét, amely megfelel az edtu-k száma bármely adatbázisban.
- Állítsa a Min-edtu-k, amely nagyobb, mint 0. Ez a minimum garantálja, hogy a készlet minden egyes adatbázis rendelkezik-e elérhető a memórián belüli online Tranzakciófeldolgozási tárolókapacitást, amely megfelel a konfigurált minimális eDTU.

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Adatok mérete és az oszlopcentrikus indexek tároló

Oszlopcentrikus indexek nem szükséges a memóriában. A csak kap az indexek mérete ezért a maximális általános adatbázis méretet, amelyet dokumentációja a [SQL adatbázis szolgáltatásszintjeinek](sql-database-service-tiers.md) cikk.

Fürtözött oszlopcentrikus indexek használata esetén a oszlopos tömörítési alaptábla tárolására szolgál. Ez a fajta tömörítés jelentősen csökkenti a tárolási kezdjen a felhasználói adatok, ami azt jelenti, hogy több adatot elfér az adatbázisban. A tömörítés további növelni a [oszlopos archiválási tömörítési](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression). Az adatok jellegétől függ érhet el tömörítés mértéke, de 10-szer a tömörítés nem ritka.

Például ha egy adatbázist, mely legfeljebb 1 terabájtnál (TB) és a tömörítést 10-szer oszlopcentrikus indexek használatával érhetők el, is elférjen összesen 10 TB-nyi felhasználói adatokat az adatbázisban.

Fürtözetlen oszlopcentrikus indexek használata esetén a következő alaptáblában továbbra is a hagyományos sortárindex formátum tárolódik. Ezért a tárhely-megtakarítást, nagy, mint a fürtözött oszlopcentrikus indexek nem. Azonban ha egy hagyományos fürtözött indexek száma a egyetlen oszlopcentrikus indexszel rendelkező, továbbra is láthatja a tárolási kezdjen a következő táblázatban található az általános megtakarítások.

## <a name="moving-databases-that-use-in-memory-technologies-between-pricing-tiers"></a>A memóriában technológiák közötti árképzési szinteket használó adatbázisok áthelyezése

Nincsenek soha nem azonosított inkompatibilitásokat vagy egyéb problémák történő frissítésekor egy magasabb szintű tarifacsomagban használható, többek között a Premium szabvány. Az elérhető funkciókat és erőforrások csak növelni.

De alacsonyabb verziójúra változtatása az árképzési szint negatív hatással lehet az adatbázis. A hatás különösen kétségtelenül, amikor Ön visszaminősítését prémiumról alapszintű vagy Standard amikor az adatbázis memórián belüli online Tranzakciófeldolgozási objektumokat tartalmaz. Memóriaoptimalizált táblákkal, és oszlopcentrikus indexek esetében nem érhetők el az alacsonyabb szintre való visszalépést után (még akkor is, ha akkor is látható maradjon,). Ugyanazok a feltételek vonatkoznak, amikor egy rugalmas készlet árképzési szintjének csökkentése, vagy egy adatbázis áthelyezése a szolgáltatáshoz. A memóriában, alapszintű vagy Standard rugalmas készlet.

### <a name="in-memory-oltp"></a>Memóriabeli OLTP

*A Basic vagy Standard alacsonyabb verziójúra változtatása*: memórián belüli online Tranzakciófeldolgozási a Standard vagy alapszintű rétegben adatbázisokban nem támogatott. Emellett nem lehet áthelyezni egy adatbázist, amelynek a Standard vagy Basic réteghez memórián belüli online Tranzakciófeldolgozási objektumokat.

Előtt visszaminősítését az adatbázis Standard/egyszerű, távolítsa el az összes memóriaoptimalizált táblák és táblatípusokban, valamint a T-SQL minden natív módon lefordított modulok.

Nincs olyan tudni, hogy egy adott adatbázisnak támogatja-e a memórián belüli online Tranzakciófeldolgozási programozott módon. A következő Transact-SQL-lekérdezés hajthat végre:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Ha a lekérdezés visszaadja az **1**, a memórián belüli online Tranzakciófeldolgozási támogatott ebben az adatbázisban.


*Egy alacsonyabb prémium csomagra alacsonyabb verziójúra változtatása*: memóriaoptimalizált táblázatok adatait hozzá kell férnie a memórián belüli online Tranzakciófeldolgozási tárolóban, amely az adatbázis árképzési szintjének társított vagy érhető el a rugalmas készletben. Ha kísérli meg az árképzési szint csökkentése, vagy az adatbázist áthelyezi az a készletbe, amely nem rendelkezik elegendő memórián belüli online Tranzakciófeldolgozási tárolóhellyel, a művelet sikertelen lesz.

### <a name="columnstore-indexes"></a>Oszlopcentrikus indexek

*Basic vagy Standard visszaminősítése*: Oszlopcentrikus indexek használata támogatott, csak a prémium tarifacsomag, nem pedig a Standard vagy Basic rétegek. Amikor visszaminősítését alapszintű vagy Standard az adatbázist, az oszlopcentrikus index nem érhető el. A rendszer megőrzi az oszlopcentrikus index, de soha ne használja a az index. Ha később frissíteni vissza Premium, az oszlopcentrikus index azonnal készen áll a újra javítható.

Ha rendelkezik egy **fürtözött** oszlopcentrikus indexet, az egész tábla nem érhető el réteg alacsonyabb szintre való visszalépést után. Ezért ajánlott minden drop *fürtözött* oszlopcentrikus indexeket előtt meg megállapításában, hogy az adatbázis alatt prémium tarifacsomagra.

*Egy alacsonyabb prémium csomagra alacsonyabb verziójúra változtatása*: az alacsonyabb szintre való visszalépést sikeres lesz, ha a teljes adatbázis megfelel a cél IP-címek a maximális méretét, vagy a rendelkezésre álló tár az a rugalmas készlet belül. Nincs az oszlopcentrikus indexek az adott hatással.


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. A memórián belüli online Tranzakciófeldolgozási minta telepítése

A AdventureWorksLT mintaadatbázis mindössze néhány kattintással hozhatja létre a [Azure-portálon](https://portal.azure.com/). A jelen szakaszban szereplő lépéseket, majd azt ismertetik, hogyan a memórián belüli online Tranzakciófeldolgozási objektumok AdventureWorksLT adatbázis kiegészítése, és bemutatják a teljesítménybeli előnyökben.

A több simplistic, de több tetszetős teljesítmény bemutató a memórián belüli online Tranzakciófeldolgozási lásd:

- Kiadás: [a-memória-oltp-bemutató-1.0-s verzió](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Forráskód: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Telepítés lépései

1. Az a [Azure-portálon](https://portal.azure.com/), Premium adatbázis létrehozása a kiszolgálón. Állítsa be a **forrás** AdventureWorksLT minta adatbázisba. Részletes útmutatásért lásd: [az első Azure SQL-adatbázis létrehozása](sql-database-get-started-portal.md).

2. Kapcsolódni az adatbázishoz az SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Másolás a [memórián belüli online Tranzakciófeldolgozási Transact-SQL parancsfájl](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) a vágólapra. A T-SQL parancsfájlt a szükséges memórián belüli objektumok a AdventureWorksLT mintaadatbázis az 1. lépésben létrehozott hoz létre.

4. Illessze be a T-SQL parancsfájl SSMS, és majd végrehajtani a parancsprogramot. A `MEMORY_OPTIMIZED = ON` záradék CREATE TABLE utasítás fontosságúak. Példa:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Hiba 40536


Ha hiba 40536 a T-SQL parancsfájl futtatásakor, futtassa a következő T-SQL parancsfájlt, és győződjön meg arról, hogy az adatbázis támogatja-e a memóriában lévő:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Eredménye **0** azt jelenti, hogy a memóriában nem támogatott, és **1** azt jelenti, hogy esetén támogatott. A probléma diagnosztizálása érdekében győződjön meg arról, hogy az adatbázis, a prémium szolgáltatásszintet.


#### <a name="about-the-created-memory-optimized-items"></a>A létrehozott memóriaoptimalizált elemek kapcsolatos

**Táblák**: A minta a következő memóriaoptimalizált táblákat tartalmaz:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


A memóriaoptimalizált táblák keresztül vizsgálhatja meg a **Object Explorer** szolgáltatáshoz az ssms. Kattintson a jobb gombbal **táblák** > **szűrő** > **beállítások szűrése** > **Memóriaoptimalizált**. Az érték 1.


Vagy a katalógus nézetek, például:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Natív módon lefordított tárolt eljárás**: SalesLT.usp_InsertSalesOrder_inmem vizsgálhatja lekérdezéssel-katalógus megtekintése:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>A minta OLTP-munkaterhelés futtatása

Az egyetlen különbség a következő két *tárolt eljárások* , hogy az első eljárás használja verziók memóriaoptimalizált táblák esetén a második eljárás a lemezen tábláit használja:

- SalesLT**.** usp_InsertSalesOrder**_inmem**
- SalesLT**.** usp_InsertSalesOrder**_ondisk**


Ez a szakasz használata a hasznos látható **ostress.exe** segédprogram stressful szinten két tárolt eljárások végrehajtása. Összehasonlíthatja mennyi ideig tart a két magas terhelés kísérletekhez befejezéséhez.


Ostress.exe futtatásakor azt javasoljuk, hogy sikeresen lezajlott-e az alábbi tervezett paraméterértékek:

- Futtassa a nagyszámú egyidejű kapcsolatok segítségével - n100.
- Minden kapcsolat hurok több száz időpontokban, a rendelkezik használatával - r500.


Érdemes azonban, például - n10 és - r 50 sokkal kisebb értékekkel indítására győződjön meg arról, hogy minden működik.


### <a name="script-for-ostressexe"></a>Ostress.exe parancsfájl


Ez a szakasz a T-SQL-parancsfájl a ostress.exe parancssori beágyazott jeleníti meg. A parancsfájl a korábban telepített a T-SQL-parancsfájl által létrehozott elemek.


A következő parancsfájl egy minta értékesítési sorrendben öt sor elemekhez szúr be a következő memóriaoptimalizált *táblák*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Annak a *_ondisk* az előző T-SQL-parancsfájlt ostress.exe verziója, cserélje a mindkét előfordulását a *_inmem* a karakterláncrészletre *_ondisk*. A csere hatással a táblák és tárolt eljárások neve.


### <a name="install-rml-utilities-and-ostress"></a>RML segédprogramok és ostress telepítése


Ideális esetben tenné szeretné futtatni ostress.exe egy Azure virtuális gépen (VM). Akkor kell létrehoznia egy [Azure virtuális gép](https://azure.microsoft.com/documentation/services/virtual-machines/) a azonos Azure földrajzi régióban, ahol a AdventureWorksLT adatbázis található. De futtathatja ostress.exe inkább a laptopján.


A virtuális Gépet, vagy bármilyen üzemeltetéséhez, akkor válassza, a visszajátszás Markup Language (RML) segédprogramokat telepíthet. A segédprogramok ostress.exe tartalmazza.

További információkért lásd:
- A ostress.exe vitafórum [memórián belüli online Tranzakciófeldolgozási adatbázist](http://msdn.microsoft.com/library/mt465764.aspx).
- [A memórián belüli online Tranzakciófeldolgozási adatbázis minta](http://msdn.microsoft.com/library/mt465764.aspx).
- A [ostress.exe telepítésének blog](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Futtassa a *_inmem* emelje ki először munkaterhelés


Használhat egy *RML Cmd Rákérdezés* ablakban a ostress.exe parancssort futtathat. A parancssori paraméterek ostress való közvetlen:

- 100 kapcsolatok egyidejű futtatását (-n100).
- Minden kapcsolat 50 alkalommal a T-SQL-parancsfájl futtatása (-r 50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


A fenti ostress.exe parancssor futtatása:


1. Alaphelyzetbe állítja az adatbázis az adatok tartalmának szolgáltatáshoz az ssms, az minden korábbi futtatása által beszúrt adatok törléséhez a következő parancs futtatásával:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. A fenti ostress.exe parancssor szövegét másolja a vágólapra.

3. Cserélje le a `<placeholders>` a paraméterek -S - U -P - d a megfelelő valós értékekkel.

4. A szerkesztett parancssor futtatása a egy RML Cmd ablakot.


#### <a name="result-is-a-duration"></a>Eredménye egy időtartam


Ostress.exe befejezése után, a kimenet a végső sorként futtatási időtartama ír a RML Cmd ablakot. Például egy rövidebb vizsgálat tartott körülbelül 1,5 percet:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Alaphelyzetbe állítja, a Szerkesztés *_ondisk*, majd futtassa újra a


Miután kapott eredmény az *_inmem* futnak, hajtsa végre az alábbi lépéseket a *_ondisk* futtatása:


1. Az adatbázis visszaállítása a szolgáltatáshoz az ssms az adatok törléséhez a korábbi futtatás által beszúrt a következő parancs futtatásával:
```
EXECUTE Demo.usp_DemoReset;
```

2. Szerkessze a ostress.exe parancs cseréjét *_inmem* rendelkező *_ondisk*.

3. Futtassa újra a második ostress.exe, és rögzítse a duration eredménye.

4. Ebben az esetben alaphelyzetbe állítása az adatbázis (az osztott törlése, mi is vizsgálati adatok nagy mennyiségű lehet).


#### <a name="expected-comparison-results"></a>Várt összehasonlítás eredménye

A memóriában tesztek kimutatták, hogy javítja a teljesítményt **kilenc alkalommal** a simplistic munkaterhelés, a ostress ugyanabban a régióban Azure-adatbázisként egy Azure virtuális gépen.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. A memórián belüli Analytics minta telepítése


Ebben a szakaszban összehasonlítja az IO és a statisztika eredményeket oszlopcentrikus index és egy hagyományos b-fa index használatakor.


Az OLTP-munkaterhelés valós idejű elemzés célszerű gyakran egy nem fürtözött oszloptárindex használatára. További információkért lásd: [Oszlopcentrikus indexek leírt](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Készítse elő a oszlopcentrikus analytics teszt


1. Az Azure-portál használatával hozható létre a minta egy friss AdventureWorksLT adatbázis.
 - Használja ezt a teljes nevet.
 - Válassza ki a prémium szolgáltatásszintet.

2. Másolás a [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) a vágólapra.
 - A T-SQL parancsfájlt a szükséges memórián belüli objektumok a AdventureWorksLT mintaadatbázis az 1. lépésben létrehozott hoz létre.
 - A parancsfájl a dimenziótáblában és két ténytáblák hoz létre. A ténytáblák feltöltött 3.5 millió sort.
 - A parancsfájl elvégzéséhez 15 percig is eltarthat.

3. Illessze be a T-SQL parancsfájl SSMS, és majd végrehajtani a parancsprogramot. A **OSZLOPCENTRIKUS** kulcsszót a **a CREATE INDEX** utasítás elengedhetetlen, mint:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Állítsa be AdventureWorksLT 130 kompatibilitási szintje:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Szint 130 nem közvetlenül kapcsolódik a memóriával kapcsolatos szolgáltatásainak. De szint 130 általában gyorsabb lekérdezési teljesítményt, mint 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Kulcs táblák és az oszlopcentrikus indexek


- dbo. FactResellerSalesXL_CCI fürtözött oszlopcentrikus index, amely továbbfejlesztett tömörítés a táblának a *adatok* szintjét.

- dbo. FactResellerSalesXL_PageCompressed táblának egyenértékű rendszeres fürtözött indexet, amely csak a rendszer tömöríti a *lap* szintjét.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Az oszloptárindex összehasonlítandó kulcs lekérdezések


Nincsenek [T-SQL lekérdezés számos különböző futtatható](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) teljesítménnyel kapcsolatos fejlesztések megjelenítéséhez. A 2 a T-SQL parancsfájl a lekérdezések pár figyelmet fordítania. Csak egy sorban különböznek:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Fürtözött oszlopcentrikus index szerepel a FactResellerSalesXL\_közösségi koordináló intézet tábla.

A következő T-SQL parancsfájl cikkből statisztika IO és a lekérdezés minden tábla idő nyomtatása.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

A P2 tarifacsomag adatbázisban körülbelül kilenc alkalommal a jobb teljesítménye ehhez a lekérdezéshez számíthat a fürtözött oszlopcentrikus index összehasonlítja a hagyományos index használatával. P15, a várhatóan hamarosan 57 alkalommal a jobb teljesítménye az oszlopcentrikus index használatával.



## <a name="next-steps"></a>Következő lépések

- [Gyors üzembe helyezési 1: A memórián belüli online Tranzakciófeldolgozási technológiák a T-SQL jobb teljesítmény](http://msdn.microsoft.com/library/mt694156.aspx)

- [Használja a memórián belüli online Tranzakciófeldolgozási egy meglévő Azure SQL-alkalmazásban](sql-database-in-memory-oltp-migration.md)

- [A figyelő a memórián belüli online Tranzakciófeldolgozási tárolási](sql-database-in-memory-oltp-monitoring.md) a memórián belüli online Tranzakciófeldolgozási


## <a name="additional-resources"></a>További források

#### <a name="deeper-information"></a>Mélyebb információk

- [Ismerje meg, hogyan kvórum megduplázódik miközben DTU csökkenti a memórián belüli online Tranzakciófeldolgozási az SQL-adatbázis 70 %-kal kulcsának adatbázis munkaterhelés](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Memórián belüli online Tranzakciófeldolgozási Azure SQL adatbázis blogbejegyzésben](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [További tudnivalók a memórián belüli online Tranzakciófeldolgozási](http://msdn.microsoft.com/library/dn133186.aspx)

- [További tudnivalók az oszlopcentrikus indexek](https://msdn.microsoft.com/library/gg492088.aspx)

- [További információk a valós idejű működési elemzés](http://msdn.microsoft.com/library/dn817827.aspx)

- Lásd: [közös terhelési mintázatok és az áttelepítés szempontjai](http://msdn.microsoft.com/library/dn673538.aspx) (amely ismerteti, ahol a memórián belüli online Tranzakciófeldolgozási gyakran biztosít teljesítménynövekedéshez munkaterhelés minták)

#### <a name="application-design"></a>Alkalmazás tervezése

- [Memórián belüli online Tranzakciófeldolgozási (a memóriában optimalizálása)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Használja a memórián belüli online Tranzakciófeldolgozási egy meglévő Azure SQL-alkalmazásban](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Eszközök

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)
