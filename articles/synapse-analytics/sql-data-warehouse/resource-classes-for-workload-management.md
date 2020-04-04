---
title: Erőforrásosztályok a munkaterhelés-kezeléshez
description: Útmutató az erőforrásosztályok használatával az Azure Synapse Analytics lekérdezéseihez szükséges erőforrások kezeléséhez.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 86cc081ef47eb2ac2e8e0a49bc79e8973f34baf1
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633691"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>Számítási feladatok kezelése erőforrás-osztályokkal az Azure Synapse Analytics szolgáltatásban

Útmutató az erőforrásosztályok használatával a synapszia SQL-készlet lekérdezéseinek memóriájának és egyidejűsse kezelésére az Azure Synapse-ban.  

## <a name="what-are-resource-classes"></a>Mik azok az erőforrásosztályok?

A lekérdezés teljesítménykapacitását a felhasználó erőforrásosztálya határozza meg.  Az erőforrásosztályok előre meghatározott erőforráskorlátok a Synapse SQL-készletben, amelyek a számítási erőforrásokat és a lekérdezés végrehajtásának egyidejűségét szabályozzák. Az erőforrásosztályok segítségével konfigurálhatja az erőforrásokat a lekérdezésekhez azáltal, hogy korlátozza az egyidejűleg futó lekérdezések számát és az egyes lekérdezésekhez rendelt számítási erőforrásokat.  Van egy kompromisszum a memória és az egyidejűség között.

- A kisebb erőforrásosztályok csökkentik a lekérdezésenkénti maximális memóriát, de növelik az egyidejűséget.
- A nagyobb erőforrásosztályok növelik a lekérdezésenkénti maximális memóriát, de csökkentik az egyidejűséget.

Az erőforrásosztályoknak két típusa van:

- Statikus erőforrásosztályok, amelyek kiválóan alkalmasak a rögzített adatkészletméret megnövekedett egyidejűségéhez.
- Dinamikus erőforrásosztályok, amelyek kiválóan alkalmasak a növekvő méretű adatkészletek számára, és a szolgáltatási szint növelésével nagyobb teljesítményt igényelnek.

Az erőforrásosztályok egyidejűségi bővítőhelyeket használnak az erőforrás-felhasználás mérésére.  [Egyidejűségi bővítőhelyek](#concurrency-slots) magyarázata a cikk későbbi részében.

- Az erőforrásosztályok erőforrás-kihasználtságának megtekintéséhez olvassa el a [Memória- és egyidejűségi korlátok című témakört.](memory-concurrency-limits.md)
- Az erőforrásosztály módosításához futtathatja a lekérdezést egy másik felhasználó alatt, vagy [módosíthatja az aktuális felhasználó erőforrásosztály-tagságát.](#change-a-users-resource-class)

### <a name="static-resource-classes"></a>Statikus erőforrásosztályok

A statikus erőforrásosztályok az aktuális teljesítményszinttől függetlenül azonos mennyiségű memóriát foglalnak le, amelyet [az adattárház egységekben](what-is-a-data-warehouse-unit-dwu-cdwu.md)mérnek. Mivel a lekérdezések a teljesítményszinttől függetlenül ugyanazt a memóriafoglalást kapják, [az adattárház horizontális felskálázása](quickstart-scale-compute-portal.md) lehetővé teszi, hogy egy erőforrásosztályon belül több lekérdezés fusson.  A statikus erőforrásosztályok akkor ideálisak, ha az adatmennyiség ismert és állandó.

A statikus erőforrásosztályok az előre definiált adatbázis-szerepkörökkel vannak megvalósítva:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Dinamikus erőforrásosztályok

A dinamikus erőforrásosztályok az aktuális szolgáltatási szinttől függően változó mennyiségű memóriát foglalnak le. Bár a statikus erőforrásosztályok előnyösek a nagyobb egyidejűség és a statikus adatkötetek számára, a dinamikus erőforrásosztályok jobban megfelelnek a növekvő vagy változó mennyiségű adatnak.  Ha nagyobb szolgáltatási szintre skáláz, a lekérdezések automatikusan több memóriát kapnak.  

A dinamikus erőforrásosztályok az előre definiált adatbázis-szerepkörökkel vannak megvalósítva:

- smallrc között
- közepesrc
- nagyobbc
- xlargerc

Az egyes erőforrás-osztályok memóriafoglalása a következő.

| Szolgáltatási szint  | smallrc között           | közepesrc               | nagyobbc                | xlargerc               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25%               | 25%                    | 25%                    | 70%                    |
| DW200c         | 12.5%             | 12.5%                  | 22%                    | 70%                    |
| DW300c         | 8%                | 10%                    | 22%                    | 70%                    |
| DW400c         | 6.25%             | 10%                    | 22%                    | 70%                    |
| DW500c lehetőséget         | 5%                | 10%                    | 22%                    | 70%                    |
| DW1000c a<br> DW30000c | 3%       | 10%                    | 22%                    | 70%                    |

### <a name="default-resource-class"></a>Alapértelmezett erőforrásosztály

Alapértelmezés szerint minden felhasználó tagja a dynamic resource class **smallrc**.

A szolgáltatás-rendszergazda erőforrásosztálya smallrc-ben van rögzítve, és nem módosítható.  A szolgáltatás-rendszergazda a kiépítési folyamat során létrehozott felhasználó.  Az ebben a környezetben a szolgáltatás rendszergazdája a "Kiszolgálói bejelentkezéshez" megadott bejelentkezés, amikor új Synapse SQL-készletet hoz létre egy új kiszolgálóval.

> [!NOTE]
> Az Active Directory-rendszergazdaként definiált felhasználók vagy csoportok szintén szolgáltatás-rendszergazdák.
>
>

## <a name="resource-class-operations"></a>Erőforrásosztály-műveletek

Az erőforrásosztályok célja az adatkezelési és -kezelési tevékenységek teljesítményének javítása. Összetett lekérdezések is részesülhetnek egy nagy erőforrás-osztály alatt futó. A nagy illesztések és rendezések lekérdezési teljesítménye például javulhat, ha az erőforrásosztály elég nagy ahhoz, hogy a lekérdezés tamásak a memóriában is végrehajthatók.

### <a name="operations-governed-by-resource-classes"></a>Erőforrásosztályok által szabályozott műveletek

Ezeket a műveleteket az erőforrásosztályok szabályozzák:

- BESZÚRÁS–KIJELÖLÉS, FRISSÍTÉS, TÖRLÉS
- SELECT (felhasználói táblák lekérdezésekekénekesetén)
- ALTER INDEX - ÚJJÁÉPÍTÉS VAGY ÁTSZERVEZÉS
- TÁBLA-ÚJRAÉPÍTÉS MÓDOSÍTÁSA
- CREATE INDEX
- FÜRTÖZÖTT OSZLOPCENTRIKUS INDEX LÉTREHOZÁSA
- TÁBLA LÉTREHOZÁSA KIJELÖLÉSKÉNT (CTA)CREATE TABLE AS SELECT (CTAS)
- Az adatok betöltése
- Az adatmozgatási szolgáltatás (DMS) által végzett adatmozgatási műveletek

> [!NOTE]  
> Select utasítások dinamikus felügyeleti nézetek (DMVs) vagy más rendszernézetek nem szabályozza sem az egyidejűségi korlátokat. A rendszeren futó lekérdezések számától függetlenül figyelheti a rendszert.
>
>

### <a name="operations-not-governed-by-resource-classes"></a>Erőforrásosztályok által nem szabályozott műveletek

Egyes lekérdezések mindig a smallrc erőforrásosztályban futnak, még akkor is, ha a felhasználó egy nagyobb erőforrásosztály tagja. Ezek a kivételező lekérdezések nem számítanak bele az egyidejűségi korlátba. Ha például az egyidejűségi korlát 16, sok felhasználó választhat a rendszernézetekből anélkül, hogy befolyásolná a rendelkezésre álló egyidejűségi bővítőhelyeket.

A következő utasítások mentesülnek az erőforrásosztályok alól, és mindig smallrc-ben futnak:

- LÉTREHOZÁS I VAGY CSEPPTÁBLA
- TÁBLA MÓDOSÍTÁSA ... KAPCSOLÓ, SPLIT VAGY FELOSZTÁSPARTÍCIÓ
- INDEX LETILTÁSÁNAK MÓDOSÍTÁSA
- DROP INDEX
- LÉTREHOZÁSI, FRISSÍTÉSI VAGY CSEPPSTATISZTIKA
- TÁBLÁZAT CSONKOLÁSA
- ENGEDÉLYEZÉSI MÓDOSÍTOTT
- BEJELENTKEZÉS LÉTREHOZÁSA
- CREATE, ALTER vagy DROP USER
- LÉTREHOZÁSI, ALTER- VAGY EJTÉSI ELJÁRÁS
- LÉTREHOZÁS VAGY LEGÖRDÜLŐ NÉZET
- ÉRTÉKEK BESZÚRÁSA
- SELECT a rendszernézetekből és a DMV-kből
- Magyarázni
- Dbcc

<!--
Removed as these two are not confirmed / supported under SQL DW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Egyidejűségi bővítőhelyek

Egyidejűségi bővítőhelyek egy kényelmes módja annak, hogy nyomon kövesse a lekérdezés végrehajtásához rendelkezésre álló erőforrásokat. Olyanok, mint a jegyek, amelyeket azért vásárolsz, hogy helyet foglaljon egy koncerten, mert az ülőhelyek korlátozottak. Az adatraktáronkénti egyidejűségi tárolóhelyek teljes számát a szolgáltatási szint határozza meg. A lekérdezés végrehajtása megkezdése előtt elegendő egyidejűségi bővítőhely lefoglalásához szükséges. Amikor egy lekérdezés befejeződik, felszabadítja az egyidejűségi tárolóhelyeket.  

- A 10 egyidejűségi bővítőhellyel futó lekérdezés ekként ötször több számítási erőforrást érhet el, mint a 2 egyidejűségi bővítőhely fut.
- Ha minden lekérdezés 10 egyidejűségi bővítőhelyet igényel, és 40 egyidejűségi bővítőhely van, majd csak 4 lekérdezés futhat egyidejűleg.

Csak az erőforrás-szabályozott lekérdezések használnak egyidejűségi tárolóhelyeket. A rendszerlekérdezések és néhány triviális lekérdezés nem használ fel bővítőhelyeket. A felhasznált egyidejűségi tárolóhelyek pontos számát a lekérdezés erőforrásosztálya határozza meg.

## <a name="view-the-resource-classes"></a>Az erőforrásosztályok megtekintése

Az erőforrásosztályok előre definiált adatbázis-szerepkörként vannak megvalósítva. Az erőforrásosztályoknak két típusa van: dinamikus és statikus. Az erőforrásosztályok listájának megtekintéséhez használja a következő lekérdezést:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Felhasználó erőforrásosztályának módosítása

Az erőforrásosztályok at úgy valósítják meg, hogy a felhasználókat adatbázis-szerepkörökhöz rendelik. Amikor egy felhasználó lekérdezést futtat, a lekérdezés a felhasználó erőforrásosztályával fut. Ha például egy felhasználó a staticrc10 adatbázis-szerepkör tagja, a lekérdezések kis mennyiségű memóriával futnak. Ha egy adatbázis-felhasználó az xlargerc vagy staticrc80 adatbázis-szerepkörök tagja, a lekérdezések nagy mennyiségű memóriával futnak.

A felhasználó erőforrásosztályának növeléséhez használja [a sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) segítségével adja hozzá a felhasználót egy nagy erőforrásosztály adatbázis-szerepköréhez.  Az alábbi kód hozzáad egy felhasználót a nagyobb adatbázis szerepkörhöz.  Minden kérelem a rendszermemória 22%-át kapja meg.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Az erőforrásosztály csökkentéséhez használja [a sp_droprolemember.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql)  Ha a "loaduser" nem tagja, vagy bármely más erőforrás-osztály, akkor az alapértelmezett smallrc erőforrásosztályba kerül, 3%-os memóriatámogatással.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Erőforrásosztály prioritása

A felhasználók több erőforrásosztály tagjai is lehetnek. Ha egy felhasználó egynél több erőforrásosztályhoz tartozik:

- A dinamikus erőforrásosztályok elsőbbséget élveznek a statikus erőforrás-osztályokkal szemben. Ha például egy felhasználó a mediumrc(dynamic) és a staticrc80 (statikus) tagja, a lekérdezések mediumrc-vel futnak.
- A nagyobb erőforrásosztályok elsőbbséget élveznek a kisebb erőforrás-osztályokkal szemben. Ha például egy felhasználó a mediumrc és a largerc tagja, a lekérdezések nagyobbc-vel futnak. Hasonlóképpen, ha a felhasználó a staticrc20 és a statirc80 tagja, a lekérdezések staticrc80 erőforrás-elosztással futnak.

## <a name="recommendations"></a>Javaslatok

>[!NOTE]
>Fontolja meg a számítási feladatok kezelése képességek[(munkaterhelés elkülönítése,](sql-data-warehouse-workload-isolation.md) [besorolása](sql-data-warehouse-workload-classification.md) és [fontossága](sql-data-warehouse-workload-importance.md)) a számítási feladatok jobb szabályozása és a kiszámítható teljesítmény.  
>
>

Azt javasoljuk, hogy hozzon létre egy felhasználót, amely egy adott típusú lekérdezés vagy betöltési művelet futtatására szolgál. Adjon a felhasználónak állandó erőforrásosztályt ahelyett, hogy gyakran módosítaná az erőforrásosztályt. A statikus erőforrásosztályok nagyobb általános ellenőrzést biztosítanak a munkaterhelésen, ezért javasoljuk a statikus erőforrásosztályok használatát a dinamikus erőforrásosztályok mérlegelése előtt.

### <a name="resource-classes-for-load-users"></a>Erőforrásosztályok a betöltő felhasználók számára

`CREATE TABLE`fürtözött oszlopcentrikus indexeket használ alapértelmezés szerint. Az adatok oszlopcentrikus indexbe való tömörítése memóriaigényes művelet, és a memórianyomás csökkentheti az index minőségét. A memórianyomás az adatok betöltésekor magasabb erőforrásosztályt eredményezhet. Annak érdekében, hogy a betöltések elegendő memóriával rendelkezzenek, hozzon létre egy olyan felhasználót, amely a betöltések futtatására van kijelölve, és hozzárendelheti a felhasználót egy magasabb erőforrásosztályhoz.

A terhelések hatékony feldolgozásához szükséges memória a betöltött tábla jellegétől és az adatok méretétől függ. A memóriakövetelményekről a [sorcsoportminőségének maximalizálása című](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)témakörben talál további információt.

Miután meghatározta a memóriaszükségletet, válassza ki, hogy a betöltési felhasználót statikus vagy dinamikus erőforrásosztályhoz rendeli-e.

- Statikus erőforrásosztályt akkor használjon, ha a táblamemória követelményei egy adott tartományba esnek. A terhelések megfelelő memóriával futnak. Az adatraktár méretezésekénél a terhelések nem igényelnek több memóriát. Statikus erőforrásosztály használatával a memórialefoglalások állandóak maradnak. Ez a konzisztencia memóriát takarít meg, és lehetővé teszi, hogy több lekérdezés egyidejűleg fusson. Azt javasoljuk, hogy az új megoldások először a statikus erőforrásosztályokat használják, mivel ezek nagyobb vezérlést biztosítanak.
- Dinamikus erőforrásosztályt használjon, ha a táblamemória követelményei igen eltérőek. A terhelések több memóriát igényelhetnek, mint amennyit az aktuális DWU vagy cDWU szint biztosít. Az adattárház méretezése több memóriát ad a betöltési műveletekhez, ami lehetővé teszi a betöltések gyorsabb elvégzését.

### <a name="resource-classes-for-queries"></a>Lekérdezések erőforrásosztályai

Egyes lekérdezések számításigényesek, mások nem.  

- Akkor válasszon dinamikus erőforrásosztályt, ha a lekérdezések összetettek, de nincs szükségük magas egyidejűségre.  Például a napi vagy heti jelentések létrehozása alkalmi erőforrásigény. Ha a jelentések nagy mennyiségű adatot dolgoznak fel, az adattárház méretezése több memóriát biztosít a felhasználó meglévő erőforrásosztályának.
- Válasszon statikus erőforrásosztályt, ha az erőforrás-elvárások a nap folyamán változnak. Például egy statikus erőforrásosztály jól működik, ha az adatraktárt sokan kérdezik le. Az adattárház méretezésekénte a felhasználó számára lefoglalt memória mennyisége nem változik. Ennek következtében további lekérdezések hajthatók végre párhuzamosan a rendszeren.

A megfelelő memóriatámogatások számos tényezőtől függenek, például a lekérdezett adatok mennyiségétől, a táblasémák jellegétől, valamint a különböző illesztésektől, kijelöléstől és csoportosítási predikátumoktól. Általában több memória lefoglalása lehetővé teszi a lekérdezések gyorsabb befejezését, de csökkenti a teljes egyidejűséget. Ha az egyidejűség nem probléma, a memória túlfoglalása nem károsítja az átviteli kapacitást.

A teljesítmény finomhangolásához használjon különböző erőforrásosztályokat. A következő szakasz egy tárolt eljárást ad, amely segít kitalálni a legjobb erőforrásosztályt.

## <a name="example-code-for-finding-the-best-resource-class"></a>Példa kód a legjobb erőforrásosztály megkeresésére

A következő megadott tárolt eljárás segítségével kiszámíthatja az erőforrásosztályonkénti egyidejűségi és memóriatámogatás egy adott SLO-n, és a legjobb erőforrásosztályt a memóriaigényes CCI-műveletekhez a nem particionált CCI-táblában egy adott erőforrásosztályban:

Itt a célja ennek a tárolt eljárás:

1. Az egyidejűség i és memóriatámogatás megtekintése erőforrásosztályonként egy adott SLO-ban. A felhasználónak null értéket kell megadnia a sémához és a táblanévhez is, ahogy az ebben a példában látható.  
2. A memóriaigényes CCI-műveletek legjobb erőforrásosztályának megtekintéséhez (betöltés, tábla másolása, index újraépítése stb.) egy adott erőforrásosztály nem particionált CCI tábláján. A tárolt proc táblasémát használ a szükséges memóriatámogatás megkeresésére.

### <a name="dependencies--restrictions"></a>Függőségek & korlátozások

- Ez a tárolt eljárás nem a particionált cci tábla memóriaigényének kiszámítására szolgál.
- Ez a tárolt eljárás nem veszi figyelembe a memóriakövetelményeket a CTAS/INSERT-SELECT SELECT részében, és feltételezi, hogy az egy SELECT.
- Ez a tárolt eljárás egy ideiglenes táblát használ, amely abban a munkamenetben érhető el, ahol a tárolt eljárás létrejött.
- Ez a tárolt eljárás az aktuális ajánlatoktól függ (például hardverkonfiguráció, DMS konfiguráció), és ha ezek bármelyike megváltozik, akkor ez a tárolt proc nem fog megfelelően működni.  
- Ez a tárolt eljárás a meglévő egyidejűségi korlát-ajánlatoktól függ, és ha ezek megváltoznak, akkor ez a tárolt eljárás nem fog megfelelően működni.  
- Ez a tárolt eljárás a meglévő erőforrásosztály-ajánlatoktól függ, és ha ezek megváltoznak, akkor ez a tárolt eljárás nem fog megfelelően működni.  

>[!NOTE]  
>Ha nem kapja meg a kimenetet a tárolt eljárás végrehajtása után a megadott paraméterekkel, akkor két eset lehet.
>
>1. Vagy a DW paraméter érvénytelen SLO-értéket tartalmaz
>2. Vagy nincs megfelelő erőforrásosztály a kki-művelethez a táblában.
>
>A DW100c esetében például a rendelkezésre álló legmagasabb memóriatámogatás 1 GB, és ha a táblaséma elég széles ahhoz, hogy átlépje az 1 GB-os követelményt.

### <a name="usage-example"></a>Példa használati

Szintaxis:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU:Vagy adjon meg null paramétert az aktuális DWU kinyeréséhez a DW DB-ból, vagy adjon meg bármely támogatott DWU-t "DW100c" formájában.
2. @SCHEMA_NAME:Adja meg a tábla sémanevét
3. @TABLE_NAME:Adja meg a kamat táblanevét

Példák a tárolt proc végrehajtására:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

A következő utasítás létrehozza az előző példákban használt 1.
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

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500
  ELSE Mem*100
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
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
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
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
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>További lépések

Az adatbázis-felhasználók kezeléséről és a biztonságról az [Adatbázis védelme az SQL Analytics szolgáltatásban](sql-data-warehouse-overview-manage-security.md)című témakörben talál további információt. Ha többet szeretne tudni arról, hogy a nagyobb erőforrásosztályok hogyan javíthatják a fürtözött oszlopcentrikus index minőségét, olvassa el [a Memóriaoptimalizálás az oszlopcentrikus tömörítéshez című témakört.](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
