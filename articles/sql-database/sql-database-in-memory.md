---
title: Az Azure SQL Database In-Memory technologies |} A Microsoft Docs
description: Az Azure SQL Database In-Memory technologies jelentősen javíthatja a tranzakciós teljesítményét és elemzési számítási feladatok.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jodebrui
ms.author: jodebrui
ms.reviewer: ''
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: 8fd430aa710c7e36133b40c7079b9d727774c68f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166928"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Teljesítmény optimalizálása, memóriabeli technológiákat az SQL Database használatával

Memóriabeli technológiákat az Azure SQL Database használatával érheti el a különböző számítási feladatokat a teljesítménnyel kapcsolatos fejlesztések: (online tranzakciós tranzakciófeldolgozás (OLTP)), elemzés (online analitikus feldolgozási (OLAP)), és a vegyes (hibrid tranzakció / analitikus feldolgozás (HTAP)). Hatékonyabb lekérdezési és tranzakciós feldolgozást memóriabeli technológiákat is segítenek költségek csökkentése érdekében. Általában nem kell a teljesítménynövekedést érhet el az adatbázis tarifacsomagjának frissítése. Bizonyos esetekben is lehet csökkenteni a a tarifacsomagot, miközben továbbra is megjelenik a teljesítménnyel kapcsolatos fejlesztések a memóriabeli technológiák.

Az alábbiakban két példát, hogyan segített a In-Memory OLTP jelentősen javíthatja a teljesítményt:

- Az In-Memory OLTP, [kvórum üzleti megoldások, miközben nő a dtu-k 70 %-kal munkaterhelési duplán képes volt](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).
    - Azt jelenti, hogy a dtu-k *adatbázis-tranzakciós egységek*, és a egy erőforrás-használat mértéke tartalmazza.
- A következő videó bemutatja a jelentős fejlesztéseket tartalmaz az erőforrás-használat az egy mintául szolgáló számítási feladatok: [In-Memory OLTP az Azure SQL Database Video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
    - További információkért tekintse meg a következő blogbejegyzésben:: [az Azure SQL Database ebben a blogbejegyzésben In-Memory OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

A prémium szintű, beleértve az prémium szintű rugalmas készletek minden adatbázis, memóriabeli technológiákat érhetők el.

Az alábbi videó az Azure SQL Database-ben a memóriabeli technológiák lehetséges teljesítménynövekedést ismerteti. Ne feledje, hogy az mindig látható teljesítményt nyereség függ, hogy sok tényező befolyásolja, többek között a számítási feladatok és az adatokat, az adatbázis-hozzáférési minta jellegét, és így tovább.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Az Azure SQL Database In-Memory technologies alábbi rendelkezik:

- *Memóriabeli OLTP* tranzakció növeli és csökkenti a késést, tranzakció-feldolgozás. Memóriabeli OLTP előnyeit kihasználó forgatókönyvek: nagy átviteli sebességű tranzakció-feldolgozási, például a kereskedelmi és a játékok, adatbetöltést események vagy IoT-eszközök, gyorsítótárazás, az adatok betöltése, és az ideiglenes tábla és a tábla változó forgatókönyvek.
- *Fürtözött oszlopcentrikus indexek* csökkentheti a storage erőforrás-igényű (legfeljebb 10 alkalommal), és javíthatja a teljesítményt, jelentéskészítési és elemzési lekérdezések. Használhatja azt a ténytáblák adatközpontjait a további adatok elfér az adatbázis és a teljesítmény javítása. Is használhatja azt az előzményadatok az operatív adatbázis archiválása, és a legfeljebb 10-szer több adat lekérdezésére.
- *Nem fürtözött oszlopcentrikus indexek* a HTAP segítséget nyújtanak a valós idejű betekintést az üzleti keresztül az operatív adatbázis közvetlen lekérdezése nélkül kell futtatni a költséges kinyerési, átalakítási, és load (ETL) folyamat, és várjon, amíg a adatraktár kell feltöltenie. Fürtözetlen oszlopcentrikus indexek az OLTP adatbázis csökkenti a működési munkaterhelés gyakorolt elemzési lekérdezések nagyon gyors végrehajtásának engedélyezése.
- Oszlopcentrikus indexszel rendelkező memóriaoptimalizált tábla kombinációját is rendelkezhet. Ez a kombináció lehetővé teszi, hogy nagyon gyors tranzakció-feldolgozás, és a *egyidejűleg* elemzési lekérdezések futtatása nagyon gyorsan ugyanazokat az adatokat.

Oszlopcentrikus indexek és az In-Memory OLTP óta része az SQL Server-termék 2012 és a 2014-es, illetve. Az Azure SQL Database és az SQL Server megosztani, memóriabeli technológiákat azonos megvalósítását. Továbbítja, ezek a technológiák az új képességek jelennek meg az Azure SQL Database először előtt a az SQL Server.

Ez a cikk írja le In-Memory OLTP és oszloptár-indexek konkrétan az Azure SQL Database aspektusait, és mintát is tartalmazza:
- Ezek a technológiák hatását a tárolás és adatok méretkorlátozások megjelenik.
- Látni fogja, hogyan kezelheti a ezeknek a technológiáknak a különböző árképzési szintek közötti használó adatbázisok áthelyezését.
- Látni fogja a két minta, amelyek In-Memory OLTP, valamint az oszlopcentrikus indexek az Azure SQL Database használatát mutatják be.

További információ a következő forrásanyagokban talál.

A technológiák részletes információit:

- [A memóriában tárolt OLTP-k áttekintése és a használati forgatókönyvek](https://msdn.microsoft.com/library/mt774593.aspx) (hivatkozások ügyféleset-tanulmányok és a kezdéshez információkat tartalmazza)
- [Memóriabeli OLTP dokumentációja](http://msdn.microsoft.com/library/dn133186.aspx)
- [Oszlopcentrikus indexek áttekintésével](https://msdn.microsoft.com/library/gg492088.aspx)
- Hibrid tranzakciós/analitikus feldolgozás (HTAP), más néven [valós idejű működési elemzések](https://msdn.microsoft.com/library/dn817827.aspx)

Az In-Memory OLTP gyors alapozó: [Quick Start 1: In-Memory OLTP technológiák gyorsabb a T-SQL-teljesítmény](http://msdn.microsoft.com/library/mt694156.aspx) (egy másik cikkben segítséget első lépések)

A technológiák részletes szolgáltatásról:

- [Az Azure SQL Database in-Memory OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (amely tartalmaz egy bemutatót a teljesítménybeli előnyei és saját kezűleg ezekkel az eredményekkel reprodukálás lépései)
- [Memóriabeli OLTP videók: Mi és mikor és hogyan lehet a használatára](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)
- [Az Oszlopcentrikus Index: Memórián belüli Analytics videóit Ignite 2016-ra](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

## <a name="storage-and-data-size"></a>Tárolás és adatok mérete

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Adatok mérete és a tárolási korlátot In-Memory OLTP-hez

In-Memory OLTP magában foglalja a memóriaoptimalizált táblákhoz, amelyeken a felhasználói adatok tárolására szolgálnak. Ezek a táblák férnek el a memóriában van szükség. Mivel Ön kezeli az SQL Database szolgáltatás közvetlenül a memória, kell, hogy a felhasználói adatok kvóta fogalmát. Ezt az elképzelést nevezzük *In-Memory OLTP storage*.

Tarifacsomag és minden egyes tarifacsomagja a rugalmas készlet minden egyes támogatott egyetlen adatbázis bizonyos mennyiségű In-Memory OLTP-tár tartalmazza. Lásd: [DTU-alapú erőforráskorlátok – önálló adatbázis](sql-database-dtu-resource-limits-single-databases.md), [DTU-alapú erőforráskorlátok – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md),[Virtuálismag-alapú erőforráskorlátok – önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [Virtuálismag-alapú erőforráskorlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md).

A következő elemek beleszámítanak az In-Memory OLTP Tárhelykorlát:

- A memóriaoptimalizált táblák és Táblaváltozók adatsor aktív felhasználó. Vegye figyelembe, hogy a régi sor verziók nem számítanak bele a napi korlát felé.
- A memóriaoptimalizált táblák indexei.
- Az ALTER TABLE műveletek üzemeltetési terheit.

Ha eléri a korlátot, ki a kvótát, hibaüzenetet kap, és mostantól nem Ön beszúrása vagy frissítheti az adatokat. Ez a hiba elhárításához, törli az adatokat, vagy növelje az adatbázis vagy készlet tarifacsomagját.

További információk a In-Memory OLTP tárterület kihasználtsága figyelés és riasztások konfigurálása, ha szinte eléri a korlátot: [memóriabeli megfigyelés tárolási](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Tudnivalók a rugalmas készletek

A rugalmas készletekkel az In-Memory OLTP storage közösen használja a készletben található összes adatbázishoz. Ezért a használat egy adott adatbázis hatással lehet más adatbázisok. A két megoldások a következők:

- Konfigurálja a `Max-eDTU` vagy `MaxvCore` adatbázisok, alacsonyabb, mint a teljes készlet edtu-k vagy virtuális mag száma. Ez a maximális érték caps a memórián belüli online Tranzakciófeldolgozási tárterület kihasználtsága, a készlet mérete, amely megfelel az edtu-k száma minden adatbázisban.
- Konfigurálja a `Min-eDTU` vagy `MinvCore` 0-nál nagyobb. Ez a minimum garantálja, hogy a készletben lévő minden egyes adatbázishoz rendelkezik, amely megfelel a konfigurált elérhető In-Memory OLTP-tárhely `Min-eDTU` vagy `vCore`.

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Adatok mérete és az oszlopcentrikus indexek

Az Oszlopcentrikus indexek nem férnek el a memóriában szükséges. A csak kap az indexek mérete ezért a maximális általános adatbázis méret, amely ismerteti a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) cikkeket.

Fürtözött oszlopcentrikus indexek használata esetén a Oszlopalapú tömörítés alaptábla tárolására szolgál. Ez a fajta tömörítés jelentősen csökkentheti a felhasználói adatokat, ami azt jelenti, hogy további adatokat is elférjen az adatbázisban a storage erőforrás-igényű. A tömörítés tovább növelhető az és [Oszlopalapú archiválási tömörítési](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression). Az adatok természetétől függ, amely akkor érhető el, tömörítés mértéke, de 10 alkalommal a tömörítés nem ritka.

Például ha egy adatbázis maximális mérete 1 terabájt (TB) és 10 alkalommal a tömörítés elérése érdekében az oszlopcentrikus indexek, elhelyezhessen összesen 10 TB-os felhasználói adatokat az adatbázisban.

Nem fürtözött oszlopcentrikus indexek használata esetén az alaptábla továbbra is a hagyományos sortárindex formátumban tárolódik. Ezért a tárhely-megtakarítás nem, big Data típusú, a fürtözött oszloptár-indexekben. Azonban ha hagyományos fürtözetlen index számos egyetlen oszlopcentrikus indexszel rendelkező, továbbra is megjelenik egy átfogó, akár a storage erőforrás-igényű táblához.

## <a name="moving-databases-that-use-in-memory-technologies-between-pricing-tiers"></a>Árképzési szintek közötti, memóriabeli technológiákat használó adatbázisok áthelyezése

Nincsenek soha nem inkompatibilitásokat vagy egyéb probléma Amikor frissít egy magasabb díjszabási csomagot, például a standardról prémium szintre. A rendelkezésre álló funkciók és erőforrások csak növelni.

De alacsonyabb verziójúra változtatása a tarifacsomag negatívan befolyásolhatja az adatbázishoz. A hatás akkor nyilvánvaló, különösen akkor, ha, Visszaléptetés a prémium szintű Standard vagy Alapszintűre amikor az adatbázis-In-Memory OLTP objektumokat tartalmazza. Memóriaoptimalizált táblák nem érhetők el az alacsonyabb szintű után (még akkor is, ha azok továbbra is látható). A fenti szempontok érvényesek, amikor csökkentése a rugalmas készlet tarifacsomagját, vagy áthelyezi, memóriabeli technológiákat, az adatbázis egy standard szintű vagy alapszintű rugalmas készlet.

### <a name="in-memory-oltp"></a>Memóriabeli OLTP

*Alapszintű/standard alacsonyabb verziójúra változtatása*: In-Memory OLTP-adatbázisok a Standard vagy alapszintű csomagra nem támogatott. Emellett nem lehet áthelyezni egy adatbázist, amelynek a standard szintű vagy alapszintű csomagra In-Memory OLTP objektumokat.

Nincs a megismeréséhez, hogy támogatja-e egy adott adatbázishoz In-Memory OLTP programozott módon. A következő Transact-SQL-lekérdezést futtathat:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Ha a lekérdezés visszaadja az **1**, In-Memory OLTP támogatott ebben az adatbázisban.

Mielőtt Visszaléptetés a Standard és alapszintű az adatbázist, távolítsa el az összes memóriaoptimalizált táblák és táblatípusok, valamint a T-SQL natív módon lefordított modulok. A következő lekérdezéseket azonosítsa az összes objektum, amely előtt az adatbázis is lehet visszaminősíteni, Standard és alapszintű, el kell távolítani:

```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

*Egy alacsonyabb prémium szintjének alacsonyabb verziójúra változtatása*: a memóriaoptimalizált táblákban lévő adatokat hozzá kell férnie az In-Memory OLTP storage társítva a tarifacsomagot az adatbázis vagy a rugalmas készlet érhető el. Ha megpróbálja csökkentheti a tarifacsomagot, vagy az adatbázis egy készletbe, amely nem rendelkezik elegendő rendelkezésre álló In-Memory OLTP-tár áthelyezése a művelet sikertelen lesz.

### <a name="columnstore-indexes"></a>Oszlopcentrikus indexek

*Alapszintű vagy Standard alacsonyabb verziójúra változtatása*: az Oszlopcentrikus indexek csak a prémium tarifacsomag és a Standard szintű, S3 és fent, és nem az alapszintű csomag támogatja. Ha az adatbázis egy nem támogatott szint vagy a szintet, csomagok, az oszlopcentrikus index nem érhető el. A rendszer megőrzi az oszlopcentrikus index, de a modul soha nem az index. Ha később frissít, térjen vissza a támogatott szint vagy a szintet, újra adatbáziscsoportok azonnal készen áll az oszlopcentrikus index.

Ha rendelkezik egy **fürtözött** oszlopcentrikus index, az egész tábla elérhetetlenné válik a visszalépés után. Ezért azt javasoljuk, hogy törölte-e az összes *fürtözött* oszlopcentrikus indexek előtt egy nem támogatott szint vagy a szintet az adatbázisról.

*Egy támogatott alacsonyabb szintre vagy szint alacsonyabb verziójúra változtatása*: A Visszalépés sikeres lesz, abban az esetben, ha a teljes adatbázis megfelel a cél tarifacsomag az adatbázis maximális méretét, vagy a rendelkezésre álló tár az a rugalmas készlet belül. Az oszlopcentrikus indexek nem adott hatással van.


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Az In-Memory OLTP-minta telepítése

Néhány kattintással hozhat létre az AdventureWorksLT mintaadatbázis az [az Azure portal](https://portal.azure.com/). Ezt követően a jelen szakaszban ismertetett lépések azt ismertetik, hogyan In-Memory OLTP-objektumot az AdventureWorksLT adatbázis bővítését, és bemutatják a verziófrissítéssel.

Egy több egyszerűsített, de több tetszetős teljesítmény bemutató In-Memory OLTP-hez lásd:

- Verzió: [a-memória-oltp-demo-1.0-s verzió](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Forráskód: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Telepítési lépések

1. Az a [az Azure portal](https://portal.azure.com/), a prémium és üzletileg kritikus fontosságú adatbázis létrehozása a kiszolgálón. Állítsa be a **forrás** az AdventureWorksLT mintaadatbázist. Részletes útmutatásért lásd: [hozzon létre az első Azure SQL database](sql-database-get-started-portal.md).

2. Csatlakozzon az adatbázishoz az SQL Server Management Studióval [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Másolás a [In-Memory OLTP Transact-SQL parancsfájl](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) a vágólapra. A T-SQL parancsfájlt a szükséges memórián belüli objektumok az AdventureWorksLT mintaadatbázis az 1. lépésben létrehozott hoz létre.

4. Illessze be a T-SQL parancsfájl ssms-ben, és ezután hajtsa végre a parancsfájlt. A `MEMORY_OPTIMIZED = ON` záradék CREATE TABLE utasítás alapvető fontosságúak. Példa:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>40536 hiba


Ha hiba 40536 a T-SQL parancsfájl futtatásakor, futtassa a következő T-SQL parancsfájlt, és győződjön meg arról, hogy az adatbázis támogatja a memóriabeli:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Eredménye **0** azt jelenti, hogy a memóriában nem támogatott, és **1** azt jelenti, hogy támogatott. A probléma diagnosztizálása érdekében győződjön meg arról, hogy az adatbázis a prémium szolgáltatásszinten.


#### <a name="about-the-created-memory-optimized-items"></a>A létrehozott memóriaoptimalizált elemekre vonatkozó

**Táblák**: A minta a következő memóriaoptimalizált táblákat tartalmaz:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


A memóriaoptimalizált táblák használatával vizsgálhatja meg a **Object Explorer** az ssms-ben. Kattintson a jobb gombbal **táblák** > **szűrő** > **szűrőbeállítások** > **Memóriaoptimalizált**. Az érték egyenlő 1.


Vagy lekérdezheti, ha a Rendszerkatalógus-nézetek, például:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Natív módon lefordított tárolt eljárás**: SalesLT.usp_InsertSalesOrder_inmem katalógus megtekintése lekérdezéssel ellenőrizheti:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>A minta OLTP számítási feladatok futtatásához

Az egyetlen különbség a következő két *tárolt eljárások* , hogy az első eljárás verziók a memóriaoptimalizált táblák használ, a második eljárás használja a normál lemezen lévő táblák:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


Ebben a szakaszban bemutatjuk, hogyan használhatja a praktikus **ostress.exe** segédprogram megterhelő feladatot szinten két tárolt eljárást végrehajtani. Összehasonlíthatja mennyi ideig tart a két stress futtatások befejezéséhez.


Ostress.exe futtatásakor azt javasoljuk, hogy adja meg a paraméterértékeket a következő két készült:

- Futtassa a nagyszámú egyidejű kapcsolatok használatával – n100.
- Minden kapcsolat hurok több száz időpontokban, a rendelkezik használatával – r500.


Azonban érdemes például - n10 és - r 50 sokkal kisebb értékek kezdje annak érdekében, hogy minden működik.


### <a name="script-for-ostressexe"></a>Ostress.exe parancsfájlja


Ez a szakasz a T-SQL parancsfájl a ostress.exe parancssori beágyazott jeleníti meg. A szkript, amelyet korábban telepített a T-SQL-parancsfájl által létrehozott elemek.


A következő parancsfájl értékesítési rendelés sablonja az öt sorban elemet szúr be a következő memóriaoptimalizált *táblák*:

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


Győződjön meg arról, hogy a *_ondisk* ostress.exe az előző T-SQL parancsfájl verziója cserélje a mindkét előfordulását a *_inmem* a karakterláncrészletre *_ondisk*. Ezek a lecserélendő hatással táblák és tárolt eljárások neve.


### <a name="install-rml-utilities-and-ostress"></a>RML segédprogramok és ostress telepítése


Ideális esetben szeretné futtatni tervezett ostress.exe-beli virtuális gépen (VM). Akkor kell létrehoznia egy [Azure virtuális gép](https://azure.microsoft.com/documentation/services/virtual-machines/) az azonos Azure földrajzi régióban, ahol az AdventureWorksLT adatbázisban található. De futtathatja ostress.exe inkább a laptopján.


A virtuális gép vagy a gazdagép bármilyen, válassza ki, telepítse a visszajátszás Markup Language (RML) segédeszközöket. A segédprogramok ostress.exe tartalmazza.

További információkért lásd:
- A hozzászólás ostress.exe [In-Memory OLTP adatbázist](http://msdn.microsoft.com/library/mt465764.aspx).
- [Az In-Memory OLTP adatbázis minta](http://msdn.microsoft.com/library/mt465764.aspx).
- A [blog ostress.exe telepítéséhez](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Futtassa a *_inmem* számítási feladatok terhelési először


Használhat egy *RML Cmd Rákérdezés* ablak a ostress.exe parancssort futtathat. A parancssori paraméterek ostress való közvetlen:

- 100 kapcsolat egyidejű futtatását (-n100).
- Minden kapcsolat, a T-SQL parancsfájl futtatása 50 alkalommal (-r 50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


A fenti ostress.exe parancssor futtatása:


1. Az adatbázis-adatok tartalom visszaállítása az ssms-ben, az előző futtatásokat által beszúrt adatok törléséhez a következő parancs futtatásával:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. A fenti ostress.exe parancssor szöveg másolása a vágólapra.

3. Cserélje le a `<placeholders>` a paraméterek -S - U -P - d a megfelelő valós értékekkel.

4. A szerkesztett parancssor futtatása a egy RML Cmd ablakot.


#### <a name="result-is-a-duration"></a>Eredmény egy időtartam


Ha ostress.exe befejezését követően a Futási időtartam alatt a végső kimenet üzletági ír a RML Cmd ablakot. Ha például egy rövidebb teszt futtatása tartott körülbelül 1,5 percet:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Alaphelyzetbe állítása, a Szerkesztés *_ondisk*, majd futtassa újra a


Az eredmény, miután a *_inmem* futnak, hajtsa végre az alábbi lépéseket a *_ondisk* futtatása:


1. Az adatbázis visszaállítása az ssms-ben minden adatot törölnie kell, hogy az előző futtatásból állapotszolgáltatást nem a következő parancs futtatásával:
```
EXECUTE Demo.usp_DemoReset;
```

2. Cserélje le az összes ostress.exe parancssori szerkesztése *_inmem* a *_ondisk*.

3. Futtassa újra a második alkalommal ostress.exe, és az időtartam eredményt.

4. Újra állítsa vissza az adatbázist (a Mi lehet Tesztadatok nagy mennyiségű osztott törlése).


#### <a name="expected-comparison-results"></a>Várt összehasonlítás eredménye

Memóriabeli tesztek kimutatták, hogy a javítja a teljesítményt **kilenc alkalommal** ostress egy Azure virtuális gépen ugyanabban a régióban az Azure-adatbázisként a az egyszerűsített számítási feladatokhoz.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Telepítse a memóriabeli elemzési minta


Ebben a szakaszban összehasonlítjuk az i/o- és statisztikai eredményeket és a egy hagyományos b-fa indexet oszlopcentrikus index használatakor.


Az OLTP-munkaterhelés valós idejű elemzését célszerű gyakran egy fürtözetlen oszlopcentrikus indexet használni. További információkért lásd: [Oszlopcentrikus indexek leírt](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Az oszlopcentrikus analytics tesztelés előkészítése


1. Az Azure portal segítségével hozzon létre egy új AdventureWorksLT adatbázis a mintát.
 - A pontos nevét használja.
 - Válassza ki bármelyik prémium szolgáltatásszint.

2. Másolás a [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) a vágólapra.
 - A T-SQL parancsfájlt a szükséges memórián belüli objektumok az AdventureWorksLT mintaadatbázis az 1. lépésben létrehozott hoz létre.
 - A parancsfájl a dimenziótáblában és két ténytáblák hoz létre. A ténytáblák 3,5 millió sort is megjelenik.
 - A parancsfájl 15 percet is igénybe vehet.

3. Illessze be a T-SQL parancsfájl ssms-ben, és ezután hajtsa végre a parancsfájlt. A **OSZLOPCENTRIKUS** kulcsszó a **a CREATE INDEX** utasítás elengedhetetlen, mint:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Kompatibilitási szint 130 AdventureWorksLT beállítani:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Szint 130 nem kapcsolódik közvetlenül a memóriával kapcsolatos szolgáltatásainak. De 130 szint általánosan biztosít, mint 120 gyorsabb lekérdezési teljesítmény.


#### <a name="key-tables-and-columnstore-indexes"></a>Kulcs táblákat és az oszlopcentrikus indexek


- dbo. FactResellerSalesXL_CCI, amelyek a továbbfejlesztett tömörítés, fürtözött oszlopcentrikus indexszel rendelkező táblában a *adatok* szintjét.

- dbo. FactResellerSalesXL_PageCompressed egy táblát, amely egyenértékű rendszeres fürtözött index tartozik, amely csak tömörített a *oldal* szintjét.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Az oszlopcentrikus index összehasonlítására elsődlegeskulcs-lekérdezések


Nincsenek [számos futtathatja a T-SQL lekérdezés különböző](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) teljesítménnyel kapcsolatos fejlesztések megtekintéséhez. A 2. lépésben a T-SQL parancsfájl a lekérdezések párjai figyelmet fordítania. Csak egy sorban különböznek:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Fürtözött oszlopcentrikus index van a FactResellerSalesXL\_CCI tábla.

A következő T-SQL parancsfájl cikkből szerint i/o-és a lekérdezés minden tábla idő jelenít meg statisztikákat.


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

A P2 tarifacsomag adatbázist, a várható nagyjából kilenc alkalommal a teljesítmény nyereség ehhez a lekérdezéshez a fürtözött oszlopcentrikus indexet, mint a hagyományos indexszel rendelkező használatával. P15, a várható 57 alkalommal készül a teljesítmény nyereség az oszlopcentrikus index használatával.



## <a name="next-steps"></a>További lépések

- [Gyors üzembe helyezési 1: In-Memory OLTP technológiák gyorsabb a T-SQL-teljesítmény](http://msdn.microsoft.com/library/mt694156.aspx)

- [Egy meglévő Azure SQL-alkalmazásban használható In-Memory OLTP](sql-database-in-memory-oltp-migration.md)

- [A figyelő In-Memory OLTP storage](sql-database-in-memory-oltp-monitoring.md) In-Memory OLTP-hez


## <a name="additional-resources"></a>További források

#### <a name="deeper-information"></a>Részletesebb információhoz juthat

- [Ismerje meg, hogy kvórum megduplázódik DTU takarítható meg és az SQL Database In-Memory OLTP 70 %-kal kulcsának adatbázis-munkaterhelés](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Ebben a blogbejegyzésben az Azure SQL Database in-Memory OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [További információ a memóriában tárolt OLTP-k](http://msdn.microsoft.com/library/dn133186.aspx)

- [Ismerje meg az oszlopcentrikus indexek](https://msdn.microsoft.com/library/gg492088.aspx)

- [Ismerje meg a valós idejű működési elemzések](http://msdn.microsoft.com/library/dn817827.aspx)

- Lásd: [közös munkaterhelési mintákat és az áttelepítés szempontjai](http://msdn.microsoft.com/library/dn673538.aspx) (amely azt ismerteti, ahol In-Memory OLTP gyakran biztosít jelentős teljesítménynövekedést munkaterhelési mintákat)

#### <a name="application-design"></a>Alkalmazás-tervezés

- [Memóriában tárolt OLTP-k (memóriabeli optimalizálás)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Egy meglévő Azure SQL-alkalmazásban használható In-Memory OLTP](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Eszközök

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)
