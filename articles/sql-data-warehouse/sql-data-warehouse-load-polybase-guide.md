---
title: "A PolyBase használatával az SQL Data Warehouse útmutatója |} Microsoft Docs"
description: "Irányelvek és javaslatok a PolyBase az SQL Data Warehouse forgatókönyvekben."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4757fce1-96b3-48ea-8a51-be1385705f9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 9/13/2017
ms.custom: loading
ms.author: cakarst;barbkess
ms.openlocfilehash: e8ae0eb96200c167a8758df4ce20b51452cc59a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Útmutató az SQL Data Warehouse PolyBase használatával
Ez az útmutató az SQL Data Warehouse PolyBase használatára vonatkozó gyakorlati információkat biztosít.

Első lépésként tekintse meg a [adatok betöltése a PolyBase] [ Load data with PolyBase] oktatóanyag.

## <a name="rotating-storage-keys"></a>Tárolási kulcsok elforgatása
Időről időre lesz módosítani szeretné a hozzáférési kulcsot a blob Storage biztonsági okokból.

A legtöbb elegáns a feladat végrehajtásához módja kövesse "a kulcsok elforgatása" néven ismert folyamat. Talán észrevette, hogy két kulccsal is rendelkezik tárolási a blob storage-fiók esetében. Ez azért, hogy, hogy térjen át

Az Azure tárfiókkulcsok elforgatása rendkívül egyszerű három lépést folyamat

1. A másodlagos tárelérési kulcs alapján második adatbázishoz kötődő hitelesítő adatok létrehozása
2. Ki az új hitelesítőadat-alapú második külső adatforrás létrehozása
3. Dobja el, és a külső táblák, mutasson az új külső adatforrás létrehozása

Ha áttelepítette a külső táblák az új külső adatforráshoz, majd az eltávolítási feladatokat hajthat végre:

1. Első külső adatforrásból eldobási
2. Első adatbázis kötődő hitelesítő adatok az elsődleges tárelérési kulcs alapján
3. Jelentkezzen be Azure és az készen áll a következő elsődleges elérési kulcs újragenerálása



## <a name="load-data-with-external-tables"></a>Adatok betöltése a külső táblákhoz
Ebben a példában az Azure blob storage adatokat tölt az SQL Data Warehouse-adatbázishoz.

Az adatátviteli idő lekérdezések adattárolás közvetlenül eltávolítja. Adattárolás egy oszloptárindexet tartalmazó növeli a lekérdezési teljesítményt, ha a akár 10 x elemzési lekérdezések.

Ebben a példában a CREATE TABLE AS SELECT utasítást használja az adatok betöltése. Az új táblázat örökli a lekérdezésben szereplő oszlopokat. Ezek az oszlopok adattípusai az örökli a külső tábla definíciójában.

CREATE TABLE AS SELECT a magas performant Transact-SQL-utasítást az SQL Data Warehouse összes számítási csomópont párhuzamosan az adatok betöltésekor.  A nagymértékben párhuzamos feldolgozási (MPP) motor az Analytics Platform System eredetileg, és jelenleg az SQL Data Warehouse.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Lásd: [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)].

> [!NOTE]
> Egy külső tábla használatával betöltése sikertelen lehet a hibával *"lekérdezés végrehajtása megszakadt – a maximális elutasítás küszöbérték elérésekor külső forrásból történő beolvasás során"*. Ez azt jelzi, hogy a külső adatokat tartalmaz *inkonzisztencia* rögzíti. Rekord "hibás" tekintendő, ha a tényleges adatok típusok/oszlopok száma nem egyezik az oszlopdefiníciók a külső tábla, vagy ha az adatok nem felelnek meg a megadott külső fájlformátum. A javítás érdekében győződjön meg arról, hogy a külső tábla és a külső fájlformátum-meghatározások helyességéről, valamint a külső adatokat megfelel e definíciókat. Abban az esetben, ha egy külső rekordok részét inkonzisztencia, dönthet úgy, hogy ezeket a rekordokat, a lekérdezések utasítsa el az alkalmazást a külső tábla létrehozása DDL.
> 
> 


## <a name="create-statistics-on-newly-loaded-data"></a>Statisztika létrehozása újonnan betöltött adatokról
Az Azure SQL Data Warehouse még nem támogatja a statisztikák automatikus létrehozását és frissítését.  A legjobb lekérdezési teljesítmény eléréséhez fontos létrehozni statisztikákat a táblák összes oszlopához az első betöltés után, illetve az adatok minden lényeges módosítását követően.  A statisztika részletes ismertetését a Fejlesztés témakörcsoport [Statisztika][Statistics] témakörében találja.  Alább egy gyors példát létrehozására a táblázatos ebben a példában betöltött van.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-with-external-tables"></a>Az adatok külső táblák exportálása
Ez a szakasz bemutatja, hogyan segítségével exportál adatokat az SQL Data Warehouse az Azure blob storage külső táblák segítségével. A példa létrehozása külső TABLE AS SELECT egy magas performant Transact-SQL-utasítást az adatok párhuzamos exportálása a számítási csomópontok egyben.

Az alábbi példa létrehoz egy külső tábla Weblogs2014 oszlopdefiníciók és dbo adatait. Webes naplók tábla. A külső tábla definíciójának SQL Data Warehouse tárolja, és a SELECT utasítás eredményét exportálják a "/ / log2014/archiválására" az adatforrás által megadott blob tároló könyvtárat. Az adatok exportálása a megadott szöveg formátumban.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```
## <a name="isolate-loading-users"></a>Különítse el a felhasználók betöltésekor
Gyakran szükség van egy SQL DW adatok betölthetnek több felhasználó számára. Mivel a [CREATE TABLE AS SELECT (Transact-SQL)] [ CREATE TABLE AS SELECT (Transact-SQL)] VEZÉRLÉSI engedélyekkel kell rendelkeznie az adatbázis kat, az elérés több felhasználóval rendelkező összes keresztül. Ez korlátozza, a MEGTAGADÁSI vezérlő utasítás is használhat.

Példa: Fontolja meg adatbázis sémák schema_A az osztály A, és a részleg B segítségével adatbázis felhasználók user_A schema_B és user_B felhasználóknak PolyBase betöltése az osztály A és B, illetve a. Mindkettő rendelkezik vezérlő adatbázis-engedélyek.
Séma A és B most zárolás creators le, hogy a sémáikat MEGTAGADÁS használatával:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   
 Ennek user_A és user_B most zárolja a más osztály séma.
 
## <a name="polybase-performance-optimizations"></a>A PolyBase teljesítményoptimalizálás
Eléréséhez optimális teljesítményt és a PolyBase az betöltése a következőt javasoljuk:
- Nagy tömörített fájlok ossza fel kisebb tömörített fájlok. A jelenleg támogatott a tömörítési típusok nincsenek feloszthatók. Ennek eredményeképpen teljesítménye csökkenhet egy nagyméretű fájlt tölt.
- A leggyorsabb betöltése betölthető round_robin, előkészítési tábla halommemóriában. Ez a tárolási réteg az adatok áthelyezése az adatraktár lehető leghatékonyabb módon lesz.
- Minden fájlformátum más-más teljesítménybeli jellemzőkkel bírnak. A leggyorsabb betöltési használja a tömörített tagolt szövegfájlok. Az UTF-8 és UTF-16 teljesítmény közötti különbség minimális.
- Közös elhelyezése a tárolási réteg és a késés csökkentése érdekében érdemes az adatraktár
- Az adatraktár növelheti, ha egy nagy betöltése feladat várja.

## <a name="polybase-limitations"></a>A PolyBase korlátozásai
PolyBase az SQL DW korlátai, amelyeket egy betöltése feladat tervezésekor figyelembe kell venni:
- Egyetlen sor nem lehet nagyobb, mint 1 000 000 bájt. Ez igaz a következő tábla sémáját definiált függetlenül.
- Ha egy SQL Server vagy az Azure SQL Data Warehouse szöveg nehéz oszlopok ORC formátummal exportáló adatok lehet korlátozni legalább 50 oszlopok java nincs elég memória hibák miatt. Ez elkerülhető, exportálja az oszlopok egy részét.





## <a name="next-steps"></a>Következő lépések
Adatok áthelyezése az SQL Data Warehouse kapcsolatos további tudnivalókért tekintse meg a [adatok áttelepítése – áttekintés][data migration overview].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[data migration overview]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->
