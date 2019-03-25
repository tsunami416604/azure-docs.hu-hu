---
title: Az SQL Data Warehouse SQL kódok migrálása |} A Microsoft Docs
description: Migrálás az SQL-kódot az Azure SQL Data Warehouse-megoldások fejlesztése a tippek.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: fae3ae16ee0100ad446c0b6c7851553a3376bb4f
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400979"
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Az SQL Data Warehouse az SQL kód áttelepítése

Ez a cikk ismerteti, hogy a kód egy másik adatbázis-ről az SQL Data Warehouse való áttelepítés esetén valószínűleg kell kódmódosítás. Néhány SQL Data Warehouse szolgáltatás jelentősen javíthatja a teljesítményt, a célja, hogy elosztott módon működnek. Azonban a teljesítmény és méretezhetőség fenntartása egyes funkciók nem érhetők el is.

## <a name="common-t-sql-limitations"></a>Gyakori a T-SQL-korlátozások

A következő lista foglalja össze a leggyakrabban használt funkciók, amelyek az SQL Data Warehouse nem támogatja. A hivatkozásokra kattintva megoldásai a nem támogatott funkciók:

* [A frissítések ANSI illesztések][ANSI joins on updates]
* [Törli az ANSI illesztések][ANSI joins on deletes]
* [a merge utasítás][merge statement]
* adatbázisok közti összekapcsolásokat
* [A kurzorok][cursors]
* [INSERT... EXEC][INSERT..EXEC]
* Output záradékban
* belső felhasználó által definiált függvények
* több utasításból álló függvények
* közös táblakifejezésekben
* [rekurzív közös táblakifejezésekben (irányuló)] (#Recursive-common-table-expressions-(CTE)
* CLR-függvények és eljárások
* $partition függvény
* Táblaváltozók
* tábla érték paraméterek
* Elosztott tranzakciók
* Commit / rollback működik
* Mentse a tranzakció
* végrehajtási környezeteket (EXECUTE AS)
* [Group by záradék kumulatív frissítéssel ellátott / adatkocka / csoportosítási készletek beállítások][group by clause with rollup / cube / grouping sets options]
* [a beágyazási szinteket túli 8][nesting levels beyond 8]
* [nézetek frissítése][updating through views]
* [nem maximális adattípus a dynamic SQL-karakterláncok][no MAX data type for dynamic SQL strings]

Szerencsére a legtöbb ilyen korlátozás macről körül. Magyarázatokat a megfelelő fejlesztéssel kapcsolatos cikket a fentiekben említett szerepelnek.

## <a name="supported-cte-features"></a>Támogatott irányuló szolgáltatások

Közös táblakifejezésekben (amely) részben támogatottak az SQL Data warehouse-bA.  Jelenleg a következő irányuló szolgáltatások támogatottak:

* Egy irányuló olyan SELECT utasításban adható meg.
* A CREATE VIEW utasításban a irányuló adható meg.
* A CREATE TABLE AS SELECT (CTAS) utasításban a irányuló adható meg.
* Hozzon létre távoli tábla AS kiválasztása (CRTAS) utasításban a irányuló adható meg.
* Egy irányuló létrehozása külső tábla AS kiválasztása (CETAS) utasításban adható meg.
* Távoli tábla, egy irányuló lehet hivatkozni.
* Egy külső táblából származó egy irányuló lehet hivatkozni.
* Több irányuló lekérdezés definíciója egy irányuló lehet definiálni.

## <a name="cte-limitations"></a>Irányuló korlátozások

Közös táblakifejezésekben van a SQL Data Warehouse, amely bizonyos korlátozások:

* Egy irányuló egyetlen SELECT utasítással kell követnie. INSERT, UPDATE, DELETE, és a MERGE utasítások nem támogatottak.
* Magát (rekurzív közös táblakifejezés) mutató hivatkozásokat tartalmazó közös táblakifejezés nem támogatott (lásd az alábbi szakaszt).
* Egynél több ZÁRADÉKKAL egy irányuló megadása nem engedélyezett. Például ha egy CTE_query_definition allekérdezést tartalmaz, a segédlekérdezés nem tartalmazhatnak beágyazott, amely meghatározza egy másik irányuló ZÁRADÉKKAL.
* Az ORDER BY záradék nem használható a CTE_query_definition, kivéve ha TOP záradék meg van adva.
* Egy irányuló olyan utasításban, amely része a batch használata esetén, mielőtt az utasítást pontosvesszővel kell követnie.
* Ha sp_prepare által utasítások használja, amely ugyanúgy, mint más kiválasztott utasítást a PDW fognak működni. Azonban amely sp_prepare által készített CETAS részeként használata esetén viselkedését a kiadásuktól számítva az SQL Server és egyéb PDW kötés van megvalósítva sp_prepare módja miatt. Válassza ki, hogy hivatkozásokat irányuló használ, amely nem létezik a irányuló megfelelő oszlop, ha a sp_prepare észlelése a hiba nélkül továbbítja, de a hiba, a rendszer hibajelzést során sp_execute helyette.

## <a name="recursive-ctes"></a>A rekurzív amely

A rekurzív, amely nem támogatottak az SQL Data Warehouse.  A rekurzív irányuló migrálásának viszonylag összetett és az ajánlott eljárás az, hogy azt felosztása több lépést. Általában egy ciklus használata, és a egy ideiglenes tábla feltöltése adatokkal, akkor a rekurzív közbenső lekérdezések ciklustevékenység. Az ideiglenes tábla feltöltése után egyetlen eredményhalmaz majd visszaállíthatja az adatokat. Hasonló megközelítést használták megoldásához `GROUP BY WITH CUBE` a a [group by záradék kumulatív frissítéssel ellátott / adatkocka / csoportosítási készletek beállítások] [ group by clause with rollup / cube / grouping sets options] cikk.

## <a name="unsupported-system-functions"></a>A rendszer nem támogatott funkciók

Vannak bizonyos rendszer függvények, amelyeket nem támogat. Az adattárház-használt fő azokat általában találhatja a következők:

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

A problémák macről körül.

## <a name="rowcount-workaround"></a>@@ROWCOUNT megkerülő megoldás

@ Támogatása hiánya megkerüléséhez@ROWCOUNT, hozzon létre egy tárolt eljárást, amely betölti az utolsó sorok száma a sys.dm_pdw_request_steps, és végre `EXEC LastRowCount` egy DML-utasítás után.

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

Az összes támogatott T-SQL utasítást teljes listáját lásd: [Transact-SQL-témakörök][Transact-SQL topics].

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
