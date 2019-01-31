---
title: Az Azure SQL Database In-Memory minta |} A Microsoft Docs
description: Próbálja ki az Azure SQL Database In-Memory OLTP és columnstore minta technológiák.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 9a394c0dff74ec5f926356a3d700c5bbba4c0e4f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478287"
---
# <a name="in-memory-sample"></a>A memóriában minta

Az Azure SQL Database-ben a memóriabeli technológiák lehetővé teszi az alkalmazás teljesítményének javítása érdekében, és potenciálisan költségcsökkentés az adatbázis. Az Azure SQL Database In-Memory technologies használatával érheti el teljesítménnyel kapcsolatos fejlesztések a különböző számítási feladatokat.

Ebben a cikkben látni fogja a két minta, amelyek In-Memory OLTP, valamint az oszlopcentrikus indexek az Azure SQL Database használatát mutatják be.

További információkért lásd:
- [A memóriában tárolt OLTP-k áttekintése és a használati forgatókönyvek](https://msdn.microsoft.com/library/mt774593.aspx) (hivatkozások ügyféleset-tanulmányok és a kezdéshez információkat tartalmazza)
- [Memóriabeli OLTP dokumentációja](https://msdn.microsoft.com/library/dn133186.aspx)
- [Oszlopcentrikus indexek áttekintésével](https://msdn.microsoft.com/library/gg492088.aspx)
- Hibrid tranzakciós/analitikus feldolgozás (HTAP), más néven [valós idejű működési elemzések](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Az In-Memory OLTP-minta telepítése

Néhány kattintással hozhat létre az AdventureWorksLT mintaadatbázis az [az Azure portal](https://portal.azure.com/). Ezt követően a jelen szakaszban ismertetett lépések azt ismertetik, hogyan In-Memory OLTP-objektumot az AdventureWorksLT adatbázis bővítését, és bemutatják a verziófrissítéssel.

Egy több egyszerűsített, de több tetszetős teljesítmény bemutató In-Memory OLTP-hez lásd:

- Verzió: [a-memória-oltp-demo-1.0-s verzió](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Forráskód: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Telepítési lépések

1. Az a [az Azure portal](https://portal.azure.com/), a prémium és üzletileg kritikus fontosságú adatbázis létrehozása a kiszolgálón. Állítsa be a **forrás** az AdventureWorksLT mintaadatbázist. Részletes útmutatásért lásd: [hozzon létre az első Azure SQL database](sql-database-get-started-portal.md).

2. Csatlakozzon az adatbázishoz az SQL Server Management Studióval [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx).

3. Másolás a [In-Memory OLTP Transact-SQL parancsfájl](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) a vágólapra. A T-SQL parancsfájlt a szükséges memórián belüli objektumok az AdventureWorksLT mintaadatbázis az 1. lépésben létrehozott hoz létre.

4. Illessze be a T-SQL parancsfájl ssms-ben, és ezután hajtsa végre a parancsfájlt. A `MEMORY_OPTIMIZED = ON` záradék CREATE TABLE utasítás alapvető fontosságúak. Példa:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>40536 hiba


Ha hiba 40536 a T-SQL parancsfájl futtatásakor, futtassa a következő T-SQL parancsfájlt, és győződjön meg arról, hogy az adatbázis támogatja a memóriabeli:


```sql
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


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Natív módon lefordított tárolt eljárás**: Katalógus megtekintése lekérdezéssel SalesLT.usp_InsertSalesOrder_inmem ellenőrizheti:


```sql
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


```sql
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


### <a name="install-rml-utilities-and-ostress"></a>RML segédprogramok telepítése és `ostress`


Ideális esetben szeretné futtatni tervezett ostress.exe-beli virtuális gépen (VM). Akkor kell létrehoznia egy [Azure virtuális gép](https://azure.microsoft.com/documentation/services/virtual-machines/) az azonos Azure földrajzi régióban, ahol az AdventureWorksLT adatbázisban található. De futtathatja ostress.exe inkább a laptopján.


A virtuális gép vagy a gazdagép bármilyen, válassza ki, telepítse a visszajátszás Markup Language (RML) segédeszközöket. A segédprogramok ostress.exe tartalmazza.

További információkért lásd:
- A hozzászólás ostress.exe [In-Memory OLTP adatbázist](https://msdn.microsoft.com/library/mt465764.aspx).
- [Az In-Memory OLTP adatbázis minta](https://msdn.microsoft.com/library/mt465764.aspx).
- A [blog ostress.exe telepítéséhez](https://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Futtassa a *_inmem* számítási feladatok terhelési először


Használhat egy *RML Cmd Rákérdezés* ablak a ostress.exe parancssort futtathat. Közvetlenül a parancssori paraméterek `ostress` való:

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


Amikor `ostress.exe` futtatása befejeződik, ír a kimeneti végső üzletági a Futási időtartam alatt a RML Cmd ablakban. Ha például egy rövidebb teszt futtatása tartott körülbelül 1,5 percet:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Alaphelyzetbe állítása, a Szerkesztés *_ondisk*, majd futtassa újra a


Az eredmény, miután a *_inmem* futnak, hajtsa végre az alábbi lépéseket a *_ondisk* futtatása:


1. Az adatbázis visszaállítása az ssms-ben minden adatot törölnie kell, hogy az előző futtatásból állapotszolgáltatást nem a következő parancs futtatásával:
```sql
EXECUTE Demo.usp_DemoReset;
```

2. Cserélje le az összes ostress.exe parancssori szerkesztése *_inmem* a *_ondisk*.

3. Futtassa újra a második alkalommal ostress.exe, és az időtartam eredményt.

4. Újra állítsa vissza az adatbázist (a Mi lehet Tesztadatok nagy mennyiségű osztott törlése).


#### <a name="expected-comparison-results"></a>Várt összehasonlítás eredménye

Memóriabeli tesztek kimutatták, hogy a javítja a teljesítményt **kilenc alkalommal** az egyszerűsített számítási feladathoz, a `ostress` egy Azure virtuális gépen ugyanabban a régióban az Azure-adatbázisként.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Telepítse a memóriabeli elemzési minta


Ebben a szakaszban összehasonlítjuk az i/o- és statisztikai eredményeket és a egy hagyományos b-fa indexet oszlopcentrikus index használatakor.


Az OLTP-munkaterhelés valós idejű elemzését célszerű gyakran egy fürtözetlen oszlopcentrikus indexet használni. További információkért lásd: [Oszlopcentrikus indexek leírt](https://msdn.microsoft.com/library/gg492088.aspx).



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


```sql
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

- [1 a rövid útmutató: Memóriabeli OLTP technológiák gyorsabb T-SQL-teljesítmény](https://msdn.microsoft.com/library/mt694156.aspx)

- [Egy meglévő Azure SQL-alkalmazásban használható In-Memory OLTP](sql-database-in-memory-oltp-migration.md)

- [A figyelő In-Memory OLTP storage](sql-database-in-memory-oltp-monitoring.md) In-Memory OLTP-hez


## <a name="additional-resources"></a>További források

#### <a name="deeper-information"></a>Részletesebb információhoz juthat

- [Ismerje meg, hogy kvórum megduplázódik DTU takarítható meg és az SQL Database In-Memory OLTP 70 %-kal kulcsának adatbázis-munkaterhelés](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Ebben a blogbejegyzésben az Azure SQL Database in-Memory OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [További információ a memóriában tárolt OLTP-k](https://msdn.microsoft.com/library/dn133186.aspx)

- [Ismerje meg az oszlopcentrikus indexek](https://msdn.microsoft.com/library/gg492088.aspx)

- [Ismerje meg a valós idejű működési elemzések](https://msdn.microsoft.com/library/dn817827.aspx)

- Lásd: [közös munkaterhelési mintákat és az áttelepítés szempontjai](https://msdn.microsoft.com/library/dn673538.aspx) (amely azt ismerteti, ahol In-Memory OLTP gyakran biztosít jelentős teljesítménynövekedést munkaterhelési mintákat)

#### <a name="application-design"></a>Alkalmazás-tervezés

- [Memóriában tárolt OLTP-k (memóriabeli optimalizálás)](https://msdn.microsoft.com/library/dn133186.aspx)

- [Egy meglévő Azure SQL-alkalmazásban használható In-Memory OLTP](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Eszközök

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
