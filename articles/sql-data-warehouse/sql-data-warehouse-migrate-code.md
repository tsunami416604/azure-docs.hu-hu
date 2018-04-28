---
title: Telepítse át az SQL-kódot az SQL Data Warehouse |} Microsoft Docs
description: Tippek az SQL-kódot az Azure SQL Data Warehouse adattárházzal történő, megoldások történő áttelepítéséhez.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: b17e8e306c01bef4c58658b35f3a67d0e721633c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Az SQL Data Warehouse az SQL-kódot áttelepítése
Ez a cikk azt ismerteti, valószínűleg szüksége lesz, ha a kódot egy másik adatbázis áttelepítése az SQL Data Warehouse kódmódosításokat. Néhány SQL Data Warehouse funkció jelentősen fejleszti a teljesítményt, úgy vannak kialakítva, hogy elosztott módon működnek. Azonban fenntartása a teljesítmény és méretezhetőség érdekében egyes szolgáltatások nem érhetők el is.

## <a name="common-t-sql-limitations"></a>Közös T-SQL-korlátozások
Az alábbi lista a leggyakrabban használt szolgáltatásai nem támogatja az SQL Data Warehouse foglalja össze. A hivatkozások a nem támogatott funkciókat lehetséges megoldásai:

* [A frissítések ANSI illesztések][ANSI joins on updates]
* [Törli a ANSI illesztések][ANSI joins on deletes]
* [merge utasítás][merge statement]
* adatbázisok közötti illesztések
* [A kurzorok][cursors]
* [INSERT... EXEC][INSERT..EXEC]
* Output záradékban
* belső felhasználó által definiált függvények
* több utasításból álló funkciók
* [közös táblakifejezésekben](#Common-table-expressions)
* [rekurzív közös táblakifejezésekben (Táblakifejezés)] (#Recursive-common-table-expressions-(CTE)
* CLR-függvények és eljárások
* $partition-függvény
* a Táblaváltozók
* tábla értékkel rendelkező paraméterek
* az elosztott tranzakciók
* Commit / rollback munka
* tranzakció mentése
* végrehajtási környezetek (EXECUTE AS)
* [Group by záradék összesítő / adatkocka / csoportosítási készletek beállítások][group by clause with rollup / cube / grouping sets options]
* [8 felett beágyazási szinttel][nesting levels beyond 8]
* [nézetek frissítése][updating through views]
* [Válassza ki a változó-hozzárendelés használata][use of select for variable assignment]
* [Nincs maximális adattípus dinamikus SQL-karakterlánc:][no MAX data type for dynamic SQL strings]

Szerencsére a legtöbb ezekkel a korlátozásokkal körül dolgozhat. Ismereteket szeretnének elsajátítani a megfelelő fejlesztői cikkeket a fentiekben említett szerepelnek.

## <a name="supported-cte-features"></a>Támogatott Táblakifejezés szolgáltatások
Közös táblakifejezésekben (Táblakifejezés) részben támogatottak az SQL Data Warehouse.  A következő Táblakifejezés szolgáltatások jelenleg támogatottak:

* Egy Táblakifejezés egy olyan SELECT utasításon adható meg.
* Egy Táblakifejezés adható meg a NÉZET létrehozása utasításban.
* Egy Táblakifejezés létrehozása TABLE AS kiválasztása (CTAS) utasításban adható meg.
* Egy Táblakifejezés létrehozása távoli tábla AS kiválasztása (CRTAS) utasításban adható meg.
* Egy Táblakifejezés létrehozása külső tábla AS kiválasztása (CETAS) utasításban adható meg.
* Egy Táblakifejezés távoli tábla lehet hivatkozni.
* A külső tábla egy Táblakifejezés lehet hivatkozni.
* Több Táblakifejezés lekérdezés definíciója egy Táblakifejezés adható meg.

## <a name="cte-limitations"></a>Táblakifejezés korlátozásai
Közös táblakifejezésekben a SQL Data Warehouse, amely bizonyos korlátozásokkal rendelkezik:

* Egy Táblakifejezés egyetlen SELECT utasítással kell követnie. INSERT, UPDATE, DELETE és MERGE utasítások nem támogatottak.
* Saját magát (rekurzív közös táblakifejezés) hivatkozásait közös táblakifejezés nem támogatott (lásd az alábbi szakaszt).
* Egy Táblakifejezés ad meg egynél több ZÁRADÉKKAL nem engedélyezett. Például ha egy CTE_query_definition allekérdezést tartalmaz, a segédlekérdezés nem tartalmazhat beágyazott, amely meghatározza egy másik Táblakifejezés ZÁRADÉKKAL.
* ORDER BY záradék nem használható a CTE_query_definition, kivéve ha TOP záradék meg van adva.
* Egy Táblakifejezés olyan utasításban, amely része egy kötegelt használata esetén az utasítás előtt pontosvesszővel kell követnie.
* Amikor használatát olyan utasításokban sp_prepare készítette, Táblakifejezés más KIVÁLASZTÓ utasítást a PDW azonos módon fognak működni. Azonban Táblakifejezés sp_prepare tanulmányát CETAS részeként használata esetén a viselkedés is késlelteti az SQL Server és az egyéb PDW utasítások sp_prepare használható a kötés módjával. Válassza ki, hogy hivatkozásokat Táblakifejezés használ a megfelelő oszlop, amely nem létezik a Táblakifejezés, ha a sp_prepare anélkül, hogy a hiba észlelése fogja továbbítani, de a hibát fog jelezni, sp_execute során helyette.

## <a name="recursive-ctes"></a>Rekurzív Táblakifejezés
Rekurzív Táblakifejezés nem támogatottak az SQL Data Warehouse.  Az áttelepítés a Táblakifejezés rekurzív némileg bonyolult, és az ajánlott eljárás az, hogy azt felosztása több lépést. Általában egy hurok használja, és egy ideiglenes tábla feltöltéséhez, akkor a rekurzív ideiglenes lekérdezések ismétlés. Amennyiben van feltöltve, az ideiglenes tábla majd térhet vissza az adatokat egy eredményhalmaz. Megoldására használatban van a hasonló megközelítés `GROUP BY WITH CUBE` a a [group by záradék összesítő / adatkocka / csoportosítási készletek beállítások] [ group by clause with rollup / cube / grouping sets options] cikk.

## <a name="unsupported-system-functions"></a>A rendszer nem támogatott funkciók
Van még néhány rendszer funkció nem támogatott. A fő lévőktől általában előfordulhat adatraktározási vannak:

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

A problémák dolgozhat körül.

## <a name="rowcount-workaround"></a>@@ROWCOUNT megoldás
Kerülő @ támogatása hiánya@ROWCOUNT, hozzon létre egy tárolt eljárás, amely lekéri az utolsó sorok számát a sys.dm_pdw_request_steps és hajthat végre `EXEC LastRowCount` egy DML-utasítás után.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>További lépések
Az összes támogatott T-SQL-utasítások teljes listáját lásd: [Transact-SQL témakörök][Transact-SQL topics].

<!--Image references-->

<!--Article references-->
[ANSI joins on updates]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ANSI joins on deletes]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[merge statement]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Transact-SQL topics]: ./sql-data-warehouse-reference-tsql-statements.md

[cursors]: ./sql-data-warehouse-develop-loops.md
[group by clause with rollup / cube / grouping sets options]: ./sql-data-warehouse-develop-group-by-options.md
[nesting levels beyond 8]: ./sql-data-warehouse-develop-transactions.md
[updating through views]: ./sql-data-warehouse-develop-views.md
[use of select for variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[no MAX data type for dynamic SQL strings]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
