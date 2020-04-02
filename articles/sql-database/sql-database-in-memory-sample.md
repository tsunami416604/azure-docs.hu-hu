---
title: Memórián belüli minta
description: Próbálja ki az Azure SQL Database In-Memory technológiákolt OLTP és oszlopcentrikus minta.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: b707d67c88eb550d397134b2294c1c5b0e1f7f7d
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528214"
---
# <a name="in-memory-sample"></a>Memórián belüli minta

Az Azure SQL Database memóriabeli technológiái lehetővé teszik az alkalmazás teljesítményének növelését és az adatbázis költségeinek potenciális csökkentését. Az Azure SQL Database memóriabeli technológiáinak használatával teljesítménybeli javulást érhet el a különböző munkaterhelésekkel.

Ebben a cikkben két minta jelenik meg, amelyek bemutatják a memórián belüli OLTP használatát, valamint az oszlopcentrikus indexek az Azure SQL Database-ben.

További információkért lásd:
- [In-Memory OLTP áttekintése és használati forgatókönyvek](https://msdn.microsoft.com/library/mt774593.aspx) (hivatkozásokat tartalmaz az ügyfél esettanulmányok és információk az első lépésekhez)
- [Dokumentáció a memórián belüli OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Oszlopcentrikus indexek útmutató](https://msdn.microsoft.com/library/gg492088.aspx)
- Hibrid tranzakciós/analitikai feldolgozás (HTAP), más néven [valós idejű működési elemzés](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Telepítse a memórián belüli OLTP mintát

Az AdventureWorksLT mintaadatbázist néhány kattintással létrehozhatja az [Azure Portalon.](https://portal.azure.com/) Ezután az ebben a szakaszban ismertetett lépések ismertetik, hogyan gazdagíthatja az AdventureWorksLT-adatbázist a memórián belüli OLTP-objektumokkal, és hogyan mutathatja be a teljesítménybeli előnyöket.

A memórián belüli OLTP egyszerűbb, de tetszetősebb teljesítménydemójáért lásd:

- Kiadás: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Forráskód: [in-memory-oltp-demo-source-code](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/in-memory-database)

#### <a name="installation-steps"></a>A telepítés lépései

1. Az [Azure Portalon](https://portal.azure.com/)hozzon létre egy prémium szintű vagy üzleti legkritikusabb adatbázist a kiszolgálón. Állítsa be a **forrást** az AdventureWorksLT mintaadatbázisra. Részletes útmutatást [az első Azure SQL-adatbázis létrehozása című](sql-database-single-database-get-started.md)témakörben talál.

2. Csatlakozzon az adatbázishoz az SQL Server Management Studio [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx)segítségével.

3. Másolja a vágólapra a memóriában lévő [OLTP Transact-SQL parancsfájlt.](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) A T-SQL parancsfájl létrehozza a szükséges memórián belüli objektumokat az 1.

4. Illessze be a T-SQL parancsfájlt az SSMS-be, majd hajtsa végre a parancsfájlt. A `MEMORY_OPTIMIZED = ON` CREATE TABLE záradék-utasítások döntő fontosságúak. Példa:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>40536-os hiba


Ha a T-SQL parancsfájl futtatásakor a 40536-os hibaüzenet jelenik meg, futtassa a következő T-SQL parancsfájlt annak ellenőrzéséhez, hogy az adatbázis támogatja-e a memóriát:


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


A **0** eredmény azt jelenti, hogy a memóriában lévő nem támogatott, **és 1** azt jelenti, hogy támogatott. A probléma diagnosztizálása érdekében győződjön meg arról, hogy az adatbázis a prémium szintű szolgáltatási szinten.


#### <a name="about-the-created-memory-optimized-items"></a>A létrehozott memóriaoptimalizált elemekről

**Táblázatok**: A minta a következő, memóriaoptimalizált táblákat tartalmazza:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


A memóriaoptimalizált táblákat az **SSMS objektumkezelőjében** tekintheti meg. Kattintson a jobb gombbal **a Táblák** > **szűrőbeállításai** > **Filter Settings** > **memória optimalizált**. Az érték 1.


Vagy lekérdezheti a katalógusnézeteket, például:


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Natívan összeállított tárolt eljárás**: A SalesLT.usp_InsertSalesOrder_inmem-t katalógusnézet-lekérdezéssel ellenőrizheti:


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Futtassa a minta OLTP számítási feladatok

Az egyetlen különbség a következő két *tárolt eljárás* között az, hogy az első eljárás a táblák memóriaoptimalizált verzióit használja, míg a második a normál lemeztáblákat használja:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


Ebben a részben láthatja, hogyan használhatja a praktikus **ostress.exe** segédprogramot a két tárolt eljárás stresszes szinten történő végrehajtásához. Össze lehet hasonlítani, hogy mennyi ideig tart a két stressz fut befejezni.


Az ostress.exe futtatásakor azt javasoljuk, hogy adja meg a paraméterértékeket, amelyeket mindkét alábbihoz terveztek:

- Az -n100 használatával nagyszámú egyidejű kapcsolatot futtathat.
- Az -r500 használatával minden egyes kapcsolati ciklust több száz alkalommal kell használnia.


Előfordulhat azonban, hogy sokkal kisebb értékekkel szeretne kezdeni, például az -n10 és az -r50 értékkel, hogy minden működjön.


### <a name="script-for-ostressexe"></a>Az ostress.exe forgatókönyve


Ez a szakasz az ostress.exe parancssorba ágyazott T-SQL parancsfájlt jeleníti meg. A parancsfájl olyan elemeket használ, amelyeket a korábban telepített T-SQL parancsfájl hozott létre.


A következő parancsfájl egy öt sort tartalmazó mintaértékesítési rendelést szúr be a következő, memóriaoptimalizált *táblákba:*

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


Az előző T-SQL-parancsfájl *_ondisk* verziójának az ostress.exe fájlhoz való hozadékához a *_inmem* részkarakterlánc mindkét előfordulását le kell *cserélnie _ondisk*. Ezek a cserék befolyásolják a táblák nevét és a tárolt eljárásokat.


### <a name="install-rml-utilities-and-ostress"></a>Telepítse az RML segédprogramokat és`ostress`


Ideális esetben azt tervezi, hogy az ostress.exe-t egy Azure virtuális gépen (VM) futtatja. Hozzon létre egy [Azure-beli virtuális gép](https://azure.microsoft.com/documentation/services/virtual-machines/) ugyanabban az Azure-földrajzi régióban, ahol a AdventureWorksLT-adatbázis található. De futtathatja az ostress.exe-t a laptopján.


A virtuális gépen, vagy bármilyen állomáson választja, telepítse a Replay Markup Language (RML) segédprogramok. A segédprogramok közé tartozik ostress.exe.

További információkért lásd:
- Az ostress.exe vita [a minta adatbázis in-memory OLTP](https://msdn.microsoft.com/library/mt465764.aspx).
- [Mintaadatbázis a memórián belüli OLTP-hoz.](https://msdn.microsoft.com/library/mt465764.aspx)
- A [blog telepítéséhez ostress.exe](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-_inmem-stress-workload-first"></a>Először futtassa a *_inmem* stresszterhelést


Az Ostress.exe parancssor futtatásához az *RML cmd prompt* ablakot használhatja. A parancssori paraméterek `ostress` közvetlenül a következő:

- Futtasson egyszerre 100 kapcsolatot (-n100).
- Minden kapcsolat futtassa a T-SQL parancsfájlt 50-szer (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Az előző ostress.exe parancssor futtatása:


1. Állítsa alaphelyzetbe az adatbázis adattartalmát a következő parancs futtatásával az SSMS-ben, hogy törölje az összes korábbi futtatással beszúrt adatot:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Másolja az előző ostress.exe parancssor szövegét a vágólapra.

3. Cserélje `<placeholders>` le a -S -U -P -d paramétereket a megfelelő valós értékekre.

4. Futtassa a szerkesztett parancssort egy RML Cmd ablakban.


#### <a name="result-is-a-duration"></a>Az eredmény egy időtartam


Amikor `ostress.exe` befejeződik, az RML-cmd ablakban a futtatási időtartamot írja a kimenet utolsó soraként. Egy rövidebb tesztfuttatás például körülbelül 1,5 percig tartott:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Alaphelyzetbe állítás, *szerkesztés _ondisk,* majd futtassa újra


Miután a *_inmem* futtatáseredményét kapta, hajtsa végre a következő lépéseket a *_ondisk* futtatáshoz:


1. Állítsa alaphelyzetbe az adatbázist az SSMS következő parancsának futtatásával az előző futtatásáltal beszúrt összes adat törléséhez:
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Az ostress.exe parancssor szerkesztésével az összes *_inmem* *_ondisk*cserélje le.

3. Futtassa újra az ostress.exe-t második alkalommal, és rögzítse az időtartam eredményét.

4. Ismét állítsa alaphelyzetbe az adatbázist (a nagy mennyiségű tesztadat felelősségteljes törléséhez).


#### <a name="expected-comparison-results"></a>Várható összehasonlítási eredmények

A memórián belüli tesztek azt mutatták, hogy a teljesítmény **kilencszer** javult a leegyszerűsítő számítási feladatok, az Azure virtuális gép `ostress` fut ugyanabban az Azure-régióban, mint az adatbázis.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Telepítse a memórián belüli elemzési mintát


Ebben a szakaszban összehasonlíthatja az I/O-t és a statisztikai eredményeket, ha egy oszlopcentrikus indexet használ egy hagyományos b-fa indexszel.


Az OLTP-számítási feladatok valós idejű elemzéséhez gyakran a legjobb, ha nem fürtözött oszlopcentrikus indexet használ. További információt az [Oszlopcentrikus indexek leírása című témakörben talál.](https://msdn.microsoft.com/library/gg492088.aspx)



### <a name="prepare-the-columnstore-analytics-test"></a>Az oszlopcentrikus elemzési teszt előkészítése


1. Az Azure Portal on hozzon létre egy friss AdventureWorksLT-adatbázist a mintából.
   - Használd pontosan ezt a nevet.
   - Válasszon bármelyik prémium szolgáltatási szintet.

2. Másolja a [sql_in memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) a vágólapra.
   - A T-SQL parancsfájl létrehozza a szükséges memórián belüli objektumokat az 1.
   - A parancsfájl létrehozza a Dimenzió táblát és két ténytáblát. A ténytáblák egyenként 3,5 millió sorral vannak feltöltve.
   - A parancsfájl 15 percet is igénybe vehet.

3. Illessze be a T-SQL parancsfájlt az SSMS-be, majd hajtsa végre a parancsfájlt. A **CREATE INDEX** utasításBAN szereplő **COLUMNSTORE** kulcsszó kulcsfontosságú, mint például:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Állítsa az AdventureWorksLT-t a 130-as kompatibilitási szintre:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    A 130-as szint nem kapcsolódik közvetlenül a memórián belüli funkciókhoz. A 130-as szint azonban általában 120-nál gyorsabb lekérdezési teljesítményt biztosít.


#### <a name="key-tables-and-columnstore-indexes"></a>Kulcstáblák és oszlopcentrikus indexek


- dbo. FactResellerSalesXL_CCI egy olyan tábla, amely fürtözött oszlopcentrikus indexszel rendelkezik, amely *nek speciális* tömörítése van az adatok szintjén.

- dbo. FactResellerSalesXL_PageCompressed egy olyan táblázat, amelynek megfelelő normál fürtözött indexe van, amely csak az *oldal* szintjén van tömörítve.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Az oszlopcentrikus index összehasonlítására irányuló legfontosabb lekérdezések


Számos [T-SQL lekérdezési típus futtatható](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) a teljesítmény javulásának megtekintéséhez. A T-SQL parancsfájl 2. Csak egy sorban különböznek:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


A fürtözött oszlopcentrikus index a FactResellerSalesXL\_CCI táblában található.

A következő T-SQL-parancsfájlrészlet az i/o és a TIME statisztikáit nyomtatja ki az egyes táblalekérdezéshez.


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

A P2 tarifacsomaggal rendelkező adatbázisokban a fürtözött oszlopcentrikus index használatával a hagyományos indexhez képest a lekérdezés teljesítménynyereségének körülbelül kilencszeresére számíthat. A P15-tel a teljesítménynövekedés 57-szeresére számíthat az oszlopcentrikus index használatával.



## <a name="next-steps"></a>További lépések

- [1. rövid útmutató: In-Memory OLTP Technologies a gyorsabb T-SQL teljesítmény érdekében](https://msdn.microsoft.com/library/mt694156.aspx)

- [A memórián belüli OLTP használata egy meglévő Azure SQL-alkalmazásban](sql-database-in-memory-oltp-migration.md)

- [Memóriabe való OLTP-tároló monitora](sql-database-in-memory-oltp-monitoring.md) a memórián belüli OLTP-hoz


## <a name="additional-resources"></a>További források

#### <a name="deeper-information"></a>Mélyebb információk

- [Ismerje meg, hogyan duplázza meg a Quorum a kulcsadatbázis munkaterhelését, miközben 70%-kal csökkenti a DTU-t a memórián belüli OLTP segítségével az SQL Database-ben](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Memórián belüli OLTP az Azure SQL Database blogbejegyzésében](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Tudjon meg többet a memórián belüli OLTP-ról](https://msdn.microsoft.com/library/dn133186.aspx)

- [További információ az oszlopcentrikus indexekről](https://msdn.microsoft.com/library/gg492088.aspx)

- [Ismerje meg a valós idejű operatív elemzést](https://msdn.microsoft.com/library/dn817827.aspx)

- Lásd: [Gyakori számítási feladatok mintái és áttelepítési szempontok](https://msdn.microsoft.com/library/dn673538.aspx) (amely leírja a számítási feladatok mintáit, ahol a memórián belüli OLTP általában jelentős teljesítménynövekedést biztosít)

#### <a name="application-design"></a>Az alkalmazás kialakítása

- [Memórián belüli OLTP (memórián belüli optimalizálás)](https://msdn.microsoft.com/library/dn133186.aspx)

- [A memórián belüli OLTP használata egy meglévő Azure SQL-alkalmazásban](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Eszközök

- [Azure-portál](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
