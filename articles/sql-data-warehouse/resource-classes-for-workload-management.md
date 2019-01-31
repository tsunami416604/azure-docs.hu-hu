---
title: Erőforrásosztályok számítási feladatok kezelése – Azure SQL Data Warehouse |} A Microsoft Docs
description: Útmutató a erőforrásosztályok használata az egyidejűség kezelése és a számítási erőforrásokat az Azure SQL Data Warehouse lekérdezések.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/26/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0dab8204450da60a6c1e46c8363e8d21dc307589
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453994"
---
# <a name="workload-management-with-resource-classes-in-azure-sql-data-warehouse"></a>Az Azure SQL Data warehouse erőforrásosztályok számítási feladatok kezelése
Útmutató a erőforrásosztályok használata kezelheti a memória és a lekérdezések az Azure SQL Data Warehouse a CONCURRENCY paraméterének értékét.  
 
## <a name="what-is-workload-management"></a>Mi az számítási feladatok kezeléséhez?
Számítási feladatok teljesítménye minden lekérdezés optimalizálására való képességet. Egy jól látogasson vissza később munkaterhelés fut, lekérdezések és betöltési műveletek hatékonyan függetlenül nagy számítási igényű vagy i/o-igényes.  Az SQL Data Warehouse funkciókat biztosít a számítási feladatok számára többfelhasználós környezetben. A data warehouse-bA több-bérlős számítási feladatokhoz nem szól.

Egy adatraktár teljesítménye kapacitása határozza meg a [adattárházegységek](what-is-a-data-warehouse-unit-dwu-cdwu.md). 

- A memória- és egyidejűségi korlátok, a teljesítmény-profilokhoz megtekintése: [memória- és egyidejűségi korlátok](memory-and-concurrency-limits.md).
- Teljesítmény-kapacitás beállításához is [kisebbre vagy nagyobbra méretezhetők](quickstart-scale-compute-portal.md).

A lekérdezési teljesítmény kapacitása határozza meg a lekérdezés erőforrásosztály. Ez a cikk további része ismerteti erőforrásosztályok vannak, és megtudhatja, hogyan módosíthatja őket.

## <a name="what-are-resource-classes"></a>Mik azok a erőforrásosztályok?
A lekérdezési teljesítmény kapacitása az adott falhasználó erőforrásosztálya határozza meg.  Erőforrásosztályok előre erőforráskorlátok az Azure SQL Data Warehouse, amelyek szabályozzák a számítási erőforrásokat és a lekérdezés-végrehajtáshoz egyidejűségi határozzák meg. Erőforrásosztályok segítségével felügyelheti a számítási feladatok a lekérdezéseket, amelyek egyidejűleg futtathatók, és minden egyes lekérdezés hozzárendelt számítási erőforrások számának korlátozása. Van egy kereskedelmi kikapcsolása között memória- és egyidejűségi.

- Kisebb méretű erőforrásosztályokat csökkentheti a maximális memóriát lekérdezéseként, de az egyidejűség mértékének növelése.
- A nagyobb erőforrásosztályok növeli a maximális memóriát lekérdezéseként, de csökkentse az egyidejűséget. 

Erőforrásosztályok két típusa van:

- Statikus erőforrások osztályok, amelyek kifejezetten a rögzített adatok mérete a nagyobb párhuzamosság.
- Dinamikus erőforrás osztályok, amelyek kifejezetten a növekvő mérete és a teljesítmény növelése, mivel a szolgáltatási szint vertikális felskálázása adatkészleteken.   

Erőforrásosztályok egyidejű helyet foglalnak le erőforrás-használat mérésére használja.  [Párhuzamos időszeletek](#concurrency-slots) magyarázatát a cikk későbbi részében. 

- Az erőforrás-használatot, az erőforrás osztályok megtekintése: [memória- és egyidejűségi korlátok](memory-and-concurrency-limits.md#concurrency-maximums).
- Erőforrásosztály módosításához futtathat a lekérdezést egy másik felhasználói vagy [módosítása az aktuális felhasználó erőforrásosztályával](#change-a-users-resource-class) tagságát. 

### <a name="static-resource-classes"></a>Statikus erőforrásosztályok
Statikus erőforrásosztályok ugyanannyi függetlenül a jelenlegi teljesítményszint, amelyet a memória lefoglalása [adattárházegységek](what-is-a-data-warehouse-unit-dwu-cdwu.md). Mivel a lekérdezések get az azonos memória mennyiségét a teljesítményszint, függetlenül [horizontális felskálázása az adatraktár](quickstart-scale-compute-portal.md) lehetővé teszi, hogy további lekérdezések futtatásához egy erőforrás osztályon belül.  Statikus erőforrásosztályok ideális, ha az adatmennyiség ismert és állandó.

A statikus erőforrásosztályok vannak megvalósítva, ezek előre meghatározott adatbázis-szerepkörökkel:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Dinamikus erőforrás-osztályok
Dinamikus Erőforrásosztályokkal foglalható le memória aktuális szolgáltatási szinttől függően változó mennyiségű. Statikus erőforrásosztályok előnyös a magasabb szintű egyidejűség érdekében és a statikus adatok köteteket, amelyek dinamikus erőforrásosztályokkal alkalmasabbak egy egyre bővülő vagy változó mennyiségű adatot.  Vertikális felskálázás nagyobb szolgáltatási szintre, amikor a lekérdezéseket automatikusan lekérése több memóriát.  

A dinamikus erőforrásosztályok vannak megvalósítva, ezek előre meghatározott adatbázis-szerepkörökkel:

- smallrc
- mediumrc
- largerc
- xlargerc 

### <a name="gen2-dynamic-resource-classes-are-truly-dynamic"></a>Gen2 dinamikus erőforrásosztályokkal valóban dinamikusak
Ha dinamikus erőforrásosztályt Gen1 a részletek digging, van néhány adatra, hogy azok viselkedésének megértése a további összetettséget hozzáadása:

- A smallrc erőforrásosztály, például egy statikus erőforrásosztály rögzített méretű memória modell működik.  Smallrc lekérdezések nem dinamikusan beolvasni a több memória, ahogy nő, a szolgáltatási szint.
- Módosíthatja a szolgáltatási szintek, a rendelkezésre álló lekérdezés egyidejűségi felfelé vagy lefelé meg.
- Szolgáltatások szintek méretezés nem biztosít arányos változást az erőforrás osztályai kiosztott memória.

A **Gen2 csak**, dinamikus erőforrásosztályokkal dinamikusak valóban címzés a fent említett pontokat.  Az új szabály 3-10-22-70 százalékos memórialefoglalások kis-Közepes-nagy-xlarge erőforrás osztályok, a rendszer **függetlenül a szolgáltatási szint**.  Az alábbi táblázat részletesen az összevont memória felosztási százalékok és futása, függetlenül a szolgáltatási szint egyidejű lekérdezések minimális száma.

| Erőforrásosztály | Memória százalékos aránya | Minimális egyidejű lekérdezések |
|:--------------:|:-----------------:|:----------------------:|
| smallrc        | 3%                | 32                     |
| mediumrc       | 10%               | 10                     |
| largerc        | 22%               | 4                      |
| xlargerc       | 70%               | 1                      |


### <a name="default-resource-class"></a>Alapértelmezett erőforrásosztály
Alapértelmezés szerint minden felhasználó tagja, a dinamikus erőforrásosztályt **smallrc**. 

A szolgáltatás-rendszergazdai erőforrás osztályát smallrc van rögzítve, és nem módosítható.  A szolgáltatás-rendszergazda a felhasználó a kiépítési folyamat során létrehozott.  Ebben a környezetben a szolgáltatás-rendszergazda a bejelentkezés, ha a "kiszolgálói rendszergazdai bejelentkezéssel" megadott hozzon létre egy új SQL Data Warehouse-példányt az új kiszolgáló.

> [!NOTE]
> Felhasználók vagy csoportok definiálva, az Active Directory-rendszergazda egyben a szolgáltatás-rendszergazdák.
>
>

## <a name="resource-class-operations"></a>Erőforrás. osztályú műveletek

Erőforrásosztályok úgy tervezték, felügyeleti és adatmanipulációs tevékenységekhez teljesítmény javítása érdekében. Összetett lekérdezések nagy erőforrásosztályba alatt is kihasználhatják. Ha például a lekérdezések a nagyobb egyesítéseknél, és számos javítja a lekérdezés végrehajtása a memóriában ahhoz, hogy elég nagy erőforrásosztály esetén.

### <a name="operations-governed-by-resource-classes"></a>Erőforrásosztályok által szabályozott műveletek

Ezek a műveletek erőforrásosztályok vonatkoznak rájuk:

* INSERT-SELECT, UPDATE, DELETE
* Válassza ki (Ha a felhasználó a táblákat kérdezi le)
* Az ALTER INDEX - ÚJRAÉPÍTÉSI vagy REORGANIZE
* ALTER TABLE REBUILD
* INDEX LÉTREHOZÁSA
* FÜRTÖZÖTT OSZLOPCENTRIKUS INDEX LÉTREHOZÁSA
* TABLE AS SELECT (CTAS) LÉTREHOZÁSA
* Az adatok betöltése
* Az adatátviteli műveletek a az adatátviteli szolgáltatás (DMS) végzett

> [!NOTE]  
> Válassza ki az utasítások a dinamikus felügyeleti nézetekkel (DMV-kkel) vagy más rendszer nézetek nem rendelkezik az egyik az egyidejűségi korlátok alapján. Megfigyelheti, hogy a rendszer a lekérdezéseket számától függetlenül.
> 
> 

### <a name="operations-not-governed-by-resource-classes"></a>Erőforrásosztályok nem szabályozzák műveletek
Néhány lekérdezés mindig fusson smallrc erőforrásosztály, annak ellenére, hogy a felhasználó egy nagyobb erőforrásosztály tagjai. Kivételt képező a lekérdezések nem számít bele a egyidejűségi korlát felé. Például ha az egyidejűségi korlát 16, sok felhasználó is kell jelölje ki rendszernézetek a rendelkezésre álló egyidejű helyet foglalnak le befolyásolása nélkül.

A következő utasításokat mentesülnek az erőforrásosztályok, és a smallrc mindig fusson:

* LÉTREHOZÁS vagy a DROP TABLE
* AZ ALTER TABLE... KAPCSOLÓ, felosztása és EGYESÍTÉSE partíció
* AZ ALTER INDEX LETILTÁSA
* A DROP INDEX
* LÉTREHOZÁS, frissítés és a DROP STATISTICS
* TÁBLA CSONKOLÁSA
* AZ ALTER ENGEDÉLYEZÉSI
* CREATE LOGIN
* CREATE, ALTER vagy DROP USER
* CREATE, ALTER vagy DROP ELJÁRÁST
* LÉTREHOZÁS vagy a DROP megtekintése
* ÉRTÉKEK BESZÚRÁSA
* VÁLASSZA ki rendszernézetek és DMV-kkel
* MAGYARÁZATA
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Párhuzamos időszeletek
Párhuzamos időszeletek egyszerűen nyomon követheti a lekérdezés-végrehajtáshoz rendelkezésre álló erőforrások. Például a jegyeket megvásárolt foglalására egy vírusvédelmet, mert ülőhelykezelő korlátozva vannak. Párhuzamos időszeletek / data warehouse-bA teljes száma a szolgáltatási szint határozza meg. Ahhoz, a lekérdezés végrehajtása, elég egyidejű hely lefoglalásához képesnek kell lennie. A lekérdezés befejeztével felszabadít valamennyit az egyidejű helyet foglalnak le.  

- 10 egyidejű helyet foglalnak le a futó lekérdezések 2 egyidejű helyet foglalnak le a futó lekérdezések, mint 5-ször több számítási erőforrások eléréséhez.
- Ha minden lekérdezéshez 10 egyidejű helyet foglalnak le, és nincsenek a 40 egyidejű helyet foglalnak le, majd csak 4 lekérdezések futhat egy időben.
 
Csak az erőforrás szabályozott lekérdezések párhuzamos időszeletek felhasználni. Rendszer lekérdezések és az egyes triviális lekérdezések nem foglalnak le a tárolóhelyekkel. Felhasznált egyidejű helyet foglalnak le a pontos szám a lekérdezés erőforrásosztály határozza meg.

## <a name="view-the-resource-classes"></a>Az erőforrásosztályok megtekintése

Erőforrásosztályok előre meghatározott adatbázis-szerepkörök vannak implementálva. Erőforrásosztályok két típusa van: a dinamikus és statikus. Az erőforrás osztályok listájának megtekintéséhez használja a következő lekérdezést:

```sql
SELECT name 
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Egy adott falhasználó erőforrásosztálya módosítása

Erőforrásosztályok adatbázis-szerepköröket rendelhet a felhasználók vannak megvalósítva. Amikor egy felhasználó egy lekérdezést futtat, a lekérdezés fut, a felhasználó erőforrásosztályával. Például amikor egy felhasználó a smallrc vagy staticrc10 adatbázis-szerepkör tagja, a lekérdezések futtassa kis mennyiségű memória. Ha egy adatbázis-felhasználót az xlargerc vagy staticrc80 adatbázis-szerepkör tagja, a lekérdezéseket futtathat nagy mennyiségű memóriát. 

Egy adott falhasználó erőforrásosztálya növeléséhez használja a tárolt eljárás [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). 

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Erőforrásosztály csökkentéséhez használja [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Erőforrás osztály sorrend
Felhasználók több erőforrás-osztályok tagjait is lehet. Amikor egy felhasználó több erőforrásosztály tartozik:

- A dinamikus erőforrásosztályokkal elsőbbséget élveznek a statikus erőforrásosztályok. Például ha egy felhasználó mediumrc(dynamic) és staticrc80 (statikus) is tagja, lekérdezések futtatása a mediumrc.
- A nagyobb erőforrásosztályok elsőbbséget élveznek a kisebb méretű erőforrásosztályokat. Például ha egy felhasználó tagja mediumrc és largerc, lekérdezések futtatása a largerc. Hasonlóképpen ha egy felhasználó tagja, mind a staticrc20, illetve a statirc80, lekérdezések futtatása az erőforrás-hozzárendelések staticrc80.

## <a name="recommendations"></a>Javaslatok
Javasoljuk, hogy egy adott típusú lekérdezések futtatására van kijelölve felhasználó létrehozása vagy Adatbetöltési műveleteit. Majd adjon, hogy a felhasználó a gyakran erőforrásosztály módosítása helyett egy állandó erőforrásosztályhoz. Tekintettel arra, hogy a statikus erőforrásosztályok biztosít a számítási feladat teljes szabályozáshoz is javasoljuk, hogy ezen első használata előtt a mérlegeli dinamikus erőforrásosztályokkal.

### <a name="resource-classes-for-load-users"></a>Erőforrásosztályok felhasználók betöltése
`CREATE TABLE` használja a fürtözött oszlopcentrikus indexek alapértelmezés szerint. Adatok tömörítése be egy oszlopcentrikus index a memóriaigényes művelet, és csökkentheti a rendelkezésre álló memória mennyisége az index minőségét. Így Ön nagy valószínűséggel egy nagyobb erőforrásosztály kérése, ha az adatok betöltéséhez. Annak érdekében, hogy nincs elég memória a terheléseket, betöltések futtatására kijelölt felhasználó létrehozása és a egy nagyobb erőforrásosztály, hogy a felhasználó hozzárendelése.

A terhelések hatékonyan feldolgozásához szükséges memóriát jellegét a tábla betöltése és az adatok mérete függ. Memória követelményeiről további információkért lásd: [minőségű sorcsoportokba maximalizálása](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Miután eldöntötte a memóriára vonatkozó követelmény, döntse el, a betöltés felhasználó hozzárendelése egy statikus vagy dinamikus erőforrásosztályt.

- Egy statikus erőforrásosztály használata, amikor tábla memóriakövetelményei meghatározott címtartományba. Betöltések futtatása megfelelő memóriával. Amikor az adatraktárban, a terhelés nem kell több memóriát. Egy statikus erőforrásosztály használata esetén a memórialefoglalások változatlan maradjon. A konzisztencia takarékoskodik a memória, és lehetővé teszi több lekérdezés egyidejű futtatását. Azt javasoljuk, hogy az új megoldások használata a statikus erőforrásosztályok először ezeket adja meg a nagyobb ellenőrzést.
- Dinamikus erőforrásosztályt akkor használja, ha a tábla memóriakövetelményei eltérőek lehetnek. Betöltések szükség lehet a jelenlegi DWU-nál több memóriát vagy cDWU szintet biztosít. Ezért az adattárház méretezése ad hozzá több memóriát betöltési művelet, amely lehetővé teszi a terhelés gyorsabb végrehajtásához.

### <a name="resource-classes-for-queries"></a>Erőforrásosztályok lekérdezések

Egyes lekérdezések nagy számítási igényű, és nem.  

- Válassza ki a dinamikus erőforrásosztályt lekérdezések összetettek, de nincs szükség nagy feldolgozási.  Például naponta vagy hetente jelentéseket hozhat létre az erőforrások alkalmanként szükség. A jelentések olyan feldolgozási nagy mennyiségű adatot, ha a felhasználó meglévő erőforrás osztályhoz több memóriát az adattárház méretezése biztosít.
- Válasszon egy statikus erőforrásosztály, amikor a erőforrás elvárások eltérőek lehetnek, a nap folyamán. Például egy statikus erőforrásosztály jól működik, ha az adatraktár sokan kellettek. Az adattárház méretezése, amikor a felhasználó számára lefoglalt memória mennyisége nem változik. Ennek következtében több lekérdezés párhuzamos, a rendszer hajthatók végre.

Kiválasztja a megfelelő memóriaengedély számos tényezőtől függ, például a lekérdezett adatok mennyisége, a táblasémákat, és különböző való csatlakozás, és a csoport predikátumok. Általánosságban véve, illetve több memória lehetővé teszi, hogy a lekérdezések gyorsabb végrehajtásához, de csökkenti a teljes feldolgozási. Egyidejűségi darabolása nem okoz problémát, ha az túlterhelt memóriafoglalás nem árt átviteli sebességet. 

Teljesítmény hangolására különböző erőforrásosztályok használata. A következő szakaszban biztosít egy tárolt eljárást, amely segít azonosítani a legjobb erőforrásosztály.

## <a name="example-code-for-finding-the-best-resource-class"></a>Példa kódja erőforrásosztály legjobb keresése
 
Használhatja a következő tárolt eljárás **csak Gen1** azonosítani a egyidejűség és a memória biztosítása / erőforrásosztály egy adott slo-t, és a memória-intenzív CCI műveletek CCI nem particionált táblára, a legközelebbi ajánlott erőforrásosztály egy adott erőforrásosztályhoz:

Itt van ez a tárolt eljárás célja:  
1. Egyidejűség és száma, egy adott SLO erőforrásosztály tárról megtekintéséhez. A felhasználónak rendelkezni NULL séma- és tablename ebben a példában látható módon.  
2. A legközelebbi ajánlott erőforrásosztályhoz megtekintéséhez a memóriaigényes CCI műveleteket (terhelés, a másolási tábla, rebuild index, stb.) a nem particionált CCI tábla, egy adott erőforrásosztályhoz. A tárolt eljárás táblaséma segítségével ismerje meg a szükséges memória biztosítása.

### <a name="dependencies--restrictions"></a>Függőségek és korlátozások:
- Ez a tárolt eljárás nem célja a memóriakövetelményét táblázatként particionált cci kiszámításához.    
- Ez a tárolt eljárás nem használ a CTAS/INSERT-válasszon VÁLASSZA részét figyelembe memóriakövetelményét, és feltételezi, hogy azt egy SELECT.
- Ezt a tárolt eljárást használja egy ideiglenes táblát, amely a munkamenet hol jött létre a tárolt eljárás.    
- Ez a tárolt eljárás attól függ, a jelenlegi ajánlatokat (például a hardver konfigurálása, a DMS-config), és módosításakor bármelyik, majd a tárolt eljárás nem működik megfelelően.  
- Ez a tárolt eljárás attól függ, már létező, felajánlott egyidejűségi korlát és módosításakor, majd a tárolt eljárás nem megfelelően fog működni.  
- Ez a tárolt eljárás attól függ, meglévő erőforrás osztály ajánlatait, és módosításakor, majd a tárolt eljárás nem megfelelően fog működni.  

>  [!NOTE]  
>  Ha a kimenet nem jelennek meg a megadott paraméterekkel tárolt eljárás végrehajtása után, majd lehet két eset. <br />1. Mindkét DW paraméter értéke érvénytelen SLO <br />2. Vagy a CCI-művelet a táblán nincs egyező erőforrás osztály van. <br />Például: DW100, a legmagasabb memóriabeli ideiglenes azért, 400 MB, és ha a következő tábla sémáját elég széles ahhoz, hogy a követelmény 400 MB közötti.
      
### <a name="usage-example"></a>Példa a használatra:
Szintaxis:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: Adja meg egy NULL értékű paramétert, bontsa ki az aktuális DWU az Adatraktár-adatbázisban, vagy bármely támogatott DWU "DW100" formájában adja meg vagy
2. @SCHEMA_NAME: Adja meg a tábla sémájának nevét
3. @TABLE_NAME: Adja meg az érdeklődés egy tábla nevét

Példák a tárolt eljárás végrehajtása:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```
> [!NOTE]
> Ebben a verzióban a tárolt eljárás megadott csak Gen1 vonatkoznak.
>
>

A következő utasítás Table1 használt a fenti példákban hoz létre.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Tárolt eljárás definíciója

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```




## <a name="next-steps"></a>További lépések
Adatbázis-felhasználók és biztonsági kezelésével kapcsolatos további információkért lásd: [biztonságossá egy adatbázis az SQL Data Warehouse][Secure a database in SQL Data Warehouse]. További információ a módját a nagyobb erőforrásosztályok javíthatja a fürtözött oszlopcentrikus index minőségét, lásd: [memóriájának optimalizálása az oszlopcentrikus tömörítéshez](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
