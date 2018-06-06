---
title: Az Azure SQL Data Warehouse-kibocsátási megjegyzések előfordulhat, hogy 2018 |} Microsoft Docs
description: Kibocsátási megjegyzések az Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/28/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 843621a8f6e08b2b51d4b7abd05d0ae6c3393fe1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726033"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Újdonságok az Azure SQL Data Warehouse (Előfordulhat, hogy 2018)?
Az SQL Data Warehouse fejlesztései folyamatosan kap. Ez a cikk ismerteti az új szolgáltatásokat és módosításokat vezettek be lehet, hogy 2018. 

## <a name="gen-2-instances"></a>2. generációs példányok
![ALT](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) Azure SQL Data Warehouse számítási optimalizált Gen2 réteg szabványait új teljesítményének felhőből adatraktárra vonatkozó. Az ügyfelek most akár ötször nagyobb teljesítményre van szüksége lekérdezés, négy alkalommal több egyidejű és ötször nagyobb számítási teljesítményt az aktuális generációjában képest. Most már képes kiszolgálni egyetlen fürtből, a legmagasabb bármely adatok vámraktározási felhőszolgáltatás 128 egyidejű lekérdezéseket.

Tekintse meg a [Turbocharge felhő analytics az Azure SQL Data Warehouse szolgáltatással](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) blog közlemény Rohan Kumar, vállalati alelnök Azure adatokból.

## <a name="features"></a>Szolgáltatások

### <a name="auto-statistics"></a>Automatikus statisztikák
A statisztikákat a kritikus moderl költség-alapú optimalizálót, például az SQL Data Warehouse motor a lekérdezésterv létrehozása optimalizálása érdekében. Az összes lekérdezés előre ismert meghatározása a statisztika objektumokat kell létrehozni rendkívül egy elérhető feladatütemezés. Azonban amikor a rendszer az ad hoc és véletlenszerű lekérdezések, amely jellemző az adatraktározás számítási feladatainál tapasztalt, a rendszergazdák is kihívást jelent a statisztika kell létrehozni potenciálisan optimálisnál lekérdezés végrehajtási tervek vezető előrejelzése és már lekérdezési válaszidőt. Egyik módja a probléma mérséklése érdekében előre létrehozni statisztikákat objektumok a tábla oszlopait. Azonban, hogy a folyamat tartalmaz a szövegminősítési statisztika objektumok van táblát, betöltése folyamatban, a betöltés hosszabb időt, amely során fenntartásához.

Az SQL Data Warehouse mostantól támogatja a statisztikák objektumok nagyobb rugalmasság, hatékonyságot és könnyen használható biztosítása a rendszergazdák és fejlesztők számára, biztosítva a rendszer továbbra is minőségi végrehajtási terv és leginkább automatikus létrehozása válaszidejét.

Engedélyezi vagy letiltja az automatikus statisztikák létrehozása az SQL Data Warehouse, hogy hajtsa végre a következő utasítást:
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

Bevált gyakorlat és útmutatást, javasoljuk, hogy állítsa `AUTO_CREATE_STATISTICS` lehetőséggel `ON`.

> [!NOTE]
> Automatikus statisztika létrehozásának *alapértelmezés szerint engedélyezett* az összes új adatraktárak.
>  

Tekintse meg a [adatbázis beállítása ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options) cikk további részleteket.

### <a name="rejected-row-support"></a>Elutasított sor támogatása
Az ügyfelek gyakran használt [(külső táblázatokat) az adatok betöltése a PolyBase](design-elt-data-loading.md) az SQL Data Warehouse miatt a nagy teljesítményű, párhuzamos Adatbetöltési jellegét. A PolyBase az alapértelmezett betöltése modell keresztül-adatainak betöltésekor [Azure Data Factory](http://azure.com/adf) is. 

Az SQL Data Warehouse hozzáadja a keresztül elutasított sor hely megadásának képességét a `REJECTED_ROW_LOCATION` paraméterrel a [külső tábla létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) utasítást. Végrehajtása után a [létrehozása TABLE AS kiválasztása (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) a külső tábla azokat a sorokat, nem tölthető be tárolódnak további vizsgálat közelében a forrás egy fájlt. 

Tekintse meg a [magabiztosan betöltése a következő SQL adatok Warehouse PolyBase elutasított sor helye](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/) blogbejegyzésben olvashat a elutasítva sor működését.

A következő példa bemutatja az új szintaxisa sorok elutasítva.

```sql
CREATE EXTERNAL TABLE [dbo].[Reject_Example]
(
    ...
)
WITH
(
    ...
    ,REJECTED_ROW_LOCATION=‘/Reject_Directory'
)
```

### <a name="alter-view"></a>MÓDOSÍTSA ÚGY A NÉZETET
[Az ALTER NÉZET](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) lehetővé teszi, hogy a felhasználó egy korábban létrehozott nézetet módosítsa a nézet törlése/létrehozása nélkül, és alkalmazza újra engedélyeket. 

A következő példa egy korábban létrehozott nézetet módosítja.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

### <a name="concatws"></a>CONCAT_WS
[A CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) függvény a két vagy több értéket kapott eredő végpont módon karakterláncot ad vissza. Az első argumentumban megadott elválasztó elválasztja azt a összefűzött értékeit. A `CONCAT_WS` hasznos a Comma-Separated érték (CSV) kimeneti generálásához.

A következő példa bemutatja, hogy hozzáfűzésével int értékek vesszővel válassza el.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
Az utasítás a következő eredményt adja vissza:
```
result
---------
1,2,3
```
A következő példa bemutatja, hogy hozzáfűzésével vegyes adatok típusú értékek egy vesszővel válassza el egymástól.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
Az utasítás a következő eredményt adja vissza:
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```
### <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
A [sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) rendszer tárolt eljárás a jelenlegi környezet által támogatott adattípusokat információt ad vissza. Az adatok típusa vizsgálat ODBC-kapcsolatokon keresztül csatlakozó eszközök általában arra használják.

A következő példa az SQL Data Warehouse által támogatott összes adattípus részletek kéri le.

```sql
EXEC sp_datatype_info
```

## <a name="behavior-changes"></a>Viselkedésváltozások
### <a name="select-into-with-order-by"></a>A SELECT INTO az ORDER BY
Az SQL Data Warehouse most blokkolja `SELECT INTO` lekérdezések, amelyek tartalmaznak egy `ORDER BY` záradékban. Korábban a művelet által a memória és a céltábla újra sorrendbe rendezés az adatok a táblázatban alakzat megfelelő majd beilleszteni az adatok rendezése járnak.

#### <a name="previous-behavior"></a>Előző viselkedése
A következő utasítás járnak a további feldolgozási terhelés.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

#### <a name="current-behavior"></a>Jelenlegi viselkedése
A következő utasítás kivételhibát hibaüzenetet a `ORDER BY` záradék nem támogatott egy `SELECT INTO` utasítást.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
A visszaadott hiba utasítást:
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

### <a name="set-parseonly-on-query-status"></a>A lekérdezés állapot beállítása PARSEONLY
Használja a `SET PARSEONLY ON` szintaxis lehetővé teszi, hogy egy felhasználó az SQL Data Warehouse motor minden T-SQL-utasítás szintaxisa a következő megvizsgálja, és térjen vissza a hibaüzeneteket fordítása, és az utasítás végrehajtása nélkül. Korábban a a [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) rendszernézet, ezekre az utasításokra állapota mappában marad a `Running` állapotát. A `sys.dm_pdw_exec_requests` nézet most visszatér a állapotának `Complete`.