---
title: "Helyettesítő kulcsok létrehozása identitásával |} Microsoft Docs"
description: "Megtudhatja, hogyan IDENTITÁS használatára a táblákon helyettesítő kulcsok létrehozásához."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/13/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 3ab5d159e6eaeb830135fe134e108b0e4de4b7d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-surrogate-keys-by-using-identity"></a>Identitásával helyettesítő kulcsok létrehozása
> [!div class="op_single_selector"]
> * [– Áttekintés][Overview]
> * [Adattípusok][Data Types]
> * [Terjesztése][Distribute]
> * [Index][Index]
> * [Partíció][Partition]
> * [Statisztika][Statistics]
> * [Ideiglenes][Temporary]
> * [Identitás][Identity]
> 
> 

Sok adatok modelers, például azok a data warehouse modellek tervezésekor helyettesítő kulcsok létrehozásához a táblákon. Az IDENTITÁS tulajdonsággal e cél eléréséhez egyszerű és hatékony terhelés teljesítmény befolyásolása nélkül. 

## <a name="get-started-with-identity"></a>Ismerkedés az IDENTITÁS
Egy táblát az azonosító tulajdonság rendelkezőként, amikor először hoz létre a tábla a következő utasítás hasonló szintaxissal definiálhatja:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Ezután `INSERT..SELECT` a táblázat feltöltéséhez.

## <a name="behavior"></a>Viselkedés
Az azonosító tulajdonság célja, hogy az nem befolyásolja a terhelés teljesítmény között az adatraktár összes terjesztési kiterjesztése. IDENTITÁS megvalósítása ezért objektumorientált felé ezen célok eléréséhez. Ez a szakasz a teljes körűen jobb megértése érdekében a végrehajtási apró mutatja be.  

### <a name="allocation-of-values"></a>Foglalási értékek
Az azonosító tulajdonság nem biztosítja a sorrendben, amelyben a helyettesítő értékek le van foglalva, amely tükrözi a működését, az SQL Server és az Azure SQL Database. Azonban az Azure SQL Data Warehouse garancia hiányában hangsúlyozottan. 

Az alábbi példában a következő ábrát:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

Az előző példában két sort terjesztési 1 ki. Az első sort tartalmaz a helyettesítő értéke 1 oszlopban `C1`, és a második sornak 61 helyettesítő értékét. Mindkét ezeket az értékeket az azonosító tulajdonság hoztak létre. Azonban nincs összefüggő a foglalási érték. Ez a működésmód szándékos.

### <a name="skewed-data"></a>Kihasználtságot adatok 
A típusú értékek egyenlően elosztva a terjesztési. Ha egy elosztott táblában romlik a kihasználtságot adatokból, majd az adattípus számára elérhető értékek is felhasználhatók, idő előtt. Például ha az adatok egyetlen terjesztési fejeződik be, majd hatékonyan a tábla el tudja érni csak-hatvanad adattípusú érték. Ezért az azonosító tulajdonság korlátozva `INT` és `BIGINT` adattípus csak.

### <a name="selectinto"></a>VÁLASSZON... A
Meglévő azonosító oszlop van kijelölve, egy új táblába, az új oszlop örökli az azonosító tulajdonság, kivéve, ha a következő feltételek valamelyike teljesül:
- A SELECT utasítás illesztést tartalmaz.
- A UNION összekapcsolhatók több KIVÁLASZTÓ utasítást.
- Az azonosító oszlop szerepel a kiválasztási listán egynél többször.
- Az azonosító oszlop egy kifejezés része.
    
Ha ezek a feltételek bármelyike teljesül, az oszlop nem NULL helyett az azonosító tulajdonság öröklődés jön létre.

### <a name="create-table-as-select"></a>TABLE AS SELECT LÉTREHOZÁSA
A következő dokumentált válassza ki az SQL Server viselkedést létrehozása TABLE AS kiválasztása (CTAS)... . Azonban nem adhat meg egy azonosító tulajdonság oszlop definíciójában a `CREATE TABLE` része az utasítást. Az IDENTITY függvény a is használhatja a `SELECT` a CTAS része. A táblázat feltöltéséhez, kell használnia `CREATE TABLE` megadhatók a tábla követ `INSERT..SELECT` való feltöltése.

## <a name="explicitly-insert-values-into-an-identity-column"></a>Explicit módon értékek beszúrása azonosító oszlop 
Támogatja az SQL Data Warehouse `SET IDENTITY_INSERT <your table> ON|OFF` szintaxist. Ez a szintaxis segítségével explicit módon értékek beszúrása az identitásoszlop.

Sok adatok modelers, például a dimenziók bizonyos soraihoz előre meghatározott negatív értékek használata. Példa: "az ismeretlen tag" sor vagy a -1. 

A következő parancsfájl bemutatja, hogyan explicit módon adja hozzá a sor IDENTITY_INSERT beállítása használatával:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```    

## <a name="load-data-into-a-table-with-identity"></a>Adatok betöltése az IDENTITÁS tartalmazó tábla

Az azonosító tulajdonság jelenléte rendelkezik néhány hatással vannak az Adatbetöltési kód. Ez a szakasz néhány alapvető mintázatokból az adatok táblába töltéséhez identitásával mutatja be. 

### <a name="load-data-with-polybase"></a>Adatok betöltése PolyBase-szel
Adatok betöltése a következő táblába, és hozzon létre egy helyettesítő kulcsot használva IDENTITY, hozzon létre a táblát, és ezután használja az INSERT... Válassza ki, vagy szúrja be. A betöltése ÉRTÉKEKET.

A következő példa az alapvető mintát információk találhatók:
 
```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> Nincs használható `CREATE TABLE AS SELECT` jelenleg, amikor az adatok betöltését azonosító oszlopot tartalmazó tábla.
> 

Az adatok betöltéséhez a tömeges másolási funkciójával (BCP) eszközzel további információkért tekintse meg a következő cikkeket:

- [A polybase-zel betöltése][]
- [PolyBase az ajánlott eljárások][]

### <a name="load-data-with-bcp"></a>Adatok betöltése a BCP használatával
BCP parancssori eszköz segítségével adatok betöltése az SQL Data Warehouse-t. A paraméterek egyike (-E) BCP viselkedését vezérlő az azonosító oszlopot tartalmazó tábla adatainak betöltésekor. 

Ha -E meg van adva, az oszlop a bemeneti fájl identitású tárolt értékek kerülnek. Ha van -E *nem* adott, akkor ebben az oszlopban szereplő értékek figyelmen kívül lesznek hagyva. Ha az azonosító oszlop nincs megadva, majd az adatok betöltése normál. Az értékek a tulajdonság a növekvő, mind a seed házirendnek megfelelően jönnek létre.

Adatok betöltése BCP segítségével további információkért tekintse meg a következő cikkeket:

- [A BCP-vel betöltése][]
- [A BCP az MSDN-en][]

## <a name="catalog-views"></a>Katalógus-nézetek
Az SQL Data Warehouse támogatja a `sys.identity_columns` katalógus megtekintése. Ez a nézet segítségével azonosíthatja a azonosítótulajdonság tartalmazó oszlop.

Jobb megértése érdekében az adatbázis-séma segítségével a példa bemutatja, hogyan integrálható `sys.identity_columns` a többi rendszer katalógus nézetek:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Korlátozások
Az azonosító tulajdonság nem használható a következő esetekben:
- Ha az oszlop adattípusához nincs INT vagy BIGINT
- Ha az oszlop is a terjesztési kulcs
- Ha a tábla nem a külső tábla 

A következő kapcsolódó funkciók nem támogatottak az SQL Data Warehouse:

- [IDENTITY()][]
- [@@IDENTITY][]
- [SCOPE_IDENTITY][]
- [IDENT_CURRENT][]
- [IDENT_INCR][]
- [IDENT_SEED][]
- [DBCC CHECK_IDENT()][]

## <a name="tasks"></a>Feladatok

Ez a témakör néhány mintakód segítségével végrehajthat olyan gyakori feladatokat azonosító oszlop használata.

> [!NOTE] 
> Oszlop C1 az alábbi feladataival IDENTITÁSA.
> 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>A legmagasabb lefoglalt érték található egy táblához
Használja a `MAX()` működnek, mint a legmagasabb érték egy elosztott tábla számára lefoglalt meghatározásához:

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Az azonosító tulajdonság a kezdőértékek és növekményértékek keresése
A katalógus nézetek segítségével mezőértékek identitás növekvő, mind a seed konfigurációs tábla a következő lekérdezés segítségével: 

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Következő lépések

* Táblák fejlesztésével kapcsolatos további tudnivalókért lásd: [tábla áttekintése][Overview], [adattípusok tábla][Data Types], [táblaterjesztése] [ Distribute], [Index táblázat][Index], [tábla particionálásához][Partition], és [ Az ideiglenes táblák][Temporary]. 
* Ajánlott eljárásokra vonatkozó további információkért lásd: [gyakorlati tanácsok az SQL Data Warehouse][SQL Data Warehouse Best Practices].  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Identity]: ./sql-data-warehouse-tables-identity.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

[A BCP-vel betöltése]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-with-bcp/
[A polybase-zel betöltése]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase/
[PolyBase az ajánlott eljárások]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide/


<!--MSDN references-->
[Identity property]: https://msdn.microsoft.com/library/ms186775.aspx
[sys.identity_columns]: https://msdn.microsoft.com/library/ms187334.aspx
[IDENTITY()]: https://msdn.microsoft.com/library/ms189838.aspx
[@@IDENTITY]: https://msdn.microsoft.com/library/ms187342.aspx
[SCOPE_IDENTITY]: https://msdn.microsoft.com/library/ms190315.aspx
[IDENT_CURRENT]: https://msdn.microsoft.com/library/ms175098.aspx
[IDENT_INCR]: https://msdn.microsoft.com/library/ms189795.aspx
[IDENT_SEED]: https://msdn.microsoft.com/library/ms189834.aspx
[DBCC CHECK_IDENT()]: https://msdn.microsoft.com/library/ms176057.aspx

[a BCP az MSDN-en]: https://msdn.microsoft.com/library/ms162802.aspx
  

<!--Other Web references-->  
