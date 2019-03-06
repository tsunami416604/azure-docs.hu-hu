---
title: Az Azure SQL Data Warehouse kibocsátási megjegyzések 2018 május |} A Microsoft Docs
description: Kibocsátási megjegyzések az Azure SQL Data warehouse-hoz.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 45a391f45d11d968818bafc97a705411a133b273
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57430849"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Mi az új Azure SQL Data warehouse? 2018. május 
Az Azure SQL Data Warehouse fejlesztései folyamatosan kap. Ez a cikk ismerteti az új szolgáltatásokat és változásokat tartalmazza, amelyek a 2018 május. 

## <a name="gen-2-instances"></a>2. generációs gyűjtések példányok
![ALT](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) Azure SQL Data Warehouse számítási optimalizált Gen2 szint szabványait új teljesítményét a felhőbeli adattárház. Ügyfelek mostantól akár ötször jobb lekérdezési teljesítményt, a négyszer további egyidejűség és ötször nagyobb számítási teljesítményt az aktuális generációjában képest. Most már képes kiszolgálni a egyetlen fürthöz, a legmagasabb minden olyan felhőalapú adattárház szolgáltatás 128 lekérdezést.

Tekintse meg a [Turbocharge felhőalapú analytics az Azure SQL Data Warehouse](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) blogbejegyzésben Rohan Kumar, vállalati alelnöke, az Azure-adatokból.

## <a name="auto-statistics"></a>Az automatikus statisztikák
A statisztikákat a kritikus fontosságú optimalizálhatja a lekérdezésterv a modern költségalapú optimalizálókkal, például a motor az SQL Data Warehouse létrehozása. Az összes lekérdezés előzetes ismert meghatározása a statisztikák objektumokat kell létrehozni egy elérhető feladatütemezés. Azonban amikor a rendszer az ad hoc szembesül és véletlenszerű lekérdezéseket, amelyek általában az adatraktározási feladatoknál, a rendszergazdák is kihívást jelent a előrejelzése milyen statisztika kell létrehozni vezető potenciálisan optimálisnál lekérdezés végrehajtási tervét, és hosszabb lekérdezések válaszidejét. Egyik módja a probléma mérséklése érdekében, hogy előre létre statisztika objektumok a tábla oszlopait. Azonban a folyamat tartalmaz olyan módon statisztika objektumok során tábla betöltése folyamatban, ami hosszabb betöltési időt karban kell tartani.

Az SQL Data Warehouse mostantól támogatja a statisztikák objektumok nagyobb rugalmasságot, hatékonyságot és a könnyű használatra biztosítása a rendszergazdák és a fejlesztők számára, miközben a rendszer továbbra is minőségi végrehajtási terv és leginkább automatikus létrehozása Válaszidők.

Engedélyezi vagy letiltja az SQL Data Warehouse a statisztikák automatikus létrehozását, futtassa a következő utasítást:
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

Ajánlott eljárások és útmutatást, javasoljuk, hogy a beállítás `AUTO_CREATE_STATISTICS` beállítást `ON`.

> [!NOTE]
> Automatikus statisztika létrehozása *alapértelmezés szerint engedélyezett* minden új adattárházak számára.
>  

Tekintse meg a [az ALTER DATABASE beállítása beállításai](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options) további részleteivel.

## <a name="rejected-row-support"></a>Elutasított sor támogatása
Ügyfeleink gyakran használnak [(külső táblák) az adatok betöltése a PolyBase](design-elt-data-loading.md) az SQL Data Warehouse miatt a nagy teljesítményű, párhuzamos Adatbetöltési jellegét. PolyBase az alapértelmezett betöltése modellje keresztül adatok betöltése során [Azure Data Factory](http://azure.com/adf) is. 

Az SQL Data Warehouse definiálhat egy visszautasított sorok helye keresztül felveszi a `REJECTED_ROW_LOCATION` paraméterrel a [CREATE EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) utasítást. Végrehajtása után egy [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) a külső tábla, egy fájlt a közelében a forrás további vizsgálat azokat a sorokat, nem tölthető be kerülnek. 

Tekintse meg a [magabiztosan betöltése az SQL adatok Warehouse PolyBase elutasított sor elhelyezése](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/) blog további részletekért a elutasítva sor működését.

Az alábbi példa bemutatja az új szintaxisa sorok elutasítva.

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

## <a name="alter-view"></a>AZ ALTER VIEW
[Az ALTER VIEW](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) lehetővé teszi, hogy a felhasználó egy korábban létrehozott nézetet módosítsa a nézet törlése/létrehozása nélkül, és alkalmazza újra az engedélyeket. 

Az alábbi példa egy korábban létrehozott nézetet módosítja.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

## <a name="concatws"></a>CONCAT_WS
A [CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) függvény a teljes körű módon két vagy több érték összefűzésén eredő karakterláncot ad vissza. Összefűzött értékek-a az első argumentumban megadott elválasztó karakter választja el. A `CONCAT_WS` függvény akkor hasznos, Comma-Separated érték (CSV) kimenet előállítása érdekében.

Az alábbi példa bemutatja, hogy összefűzné int értékek egy vesszővel válassza el egymástól.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
Az utasítás a következő eredményt adja vissza:
```
result
---------
1,2,3
```
Az alábbi példa bemutatja, hogy összefűzné vegyes adatok típusa értékek egy vesszővel válassza el egymástól.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
Az utasítás a következő eredményt adja vissza:
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```

## <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
A [sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) rendszer tárolt eljárás a az aktuális környezet által támogatott adattípusokat kapcsolatos információkat ad vissza. Ez gyakran adatok típusa vizsgálat ODBC-kapcsolatokon keresztül csatlakozó eszközök használják.

Az alábbi példa az SQL Data Warehouse által támogatott összes adattípus adatait kérdezi le.

```sql
EXEC sp_datatype_info
```

## <a name="select-into-with-order-by-behavior-change"></a>Válassza ki az INTO a rendezési viselkedés módosítása
Most már letiltja az SQL Data Warehouse `SELECT INTO` lekérdezések, amelyek tartalmaznak egy `ORDER BY` záradékban. Ez a művelet korábban, a memória és majd a céltábla átrendezésével végezze az adatokat a táblázat alakzat beszúrása az adatok sorrendje szerint járnak.

### <a name="previous-behavior"></a>Korábbi működése
A következő utasítást a további feldolgozási terhelés járnak.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

### <a name="current-behavior"></a>Jelenlegi működése
A következő utasítás kivételt fog eredményezni, egy hibaüzenet, miszerint a `ORDER BY` záradék nem támogatott a egy `SELECT INTO` utasítást.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
A visszaadott hiba utasítás:
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

## <a name="set-parseonly-on-query-status-behavior-change"></a>PARSEONLY állítsa be a lekérdezés állapot (viselkedésének módosítása)
Használatával a `SET PARSEONLY ON` szintaxis lehetővé teszi, hogy a felhasználót, hogy rendelkezik az SQL Data Warehouse motor vizsgálja meg az egyes T-SQL-utasítás szintaxisa a következő hibaüzeneteket vissza a kódja lefordításának és az utasítás végrehajtása nélkül. Korábban a a [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) rendszernézet, ezek az utasítások állapota mappában marad a `Running` állapota. A `sys.dm_pdw_exec_requests` megtekintése most adja vissza, az állapot `Complete`.

## <a name="next-steps"></a>További lépések
Most, hogy jobban megismerte az SQL Data warehouse, megtudhatja, hogyan lehet gyorsan [hozzon létre egy SQL Data Warehouse][create a SQL Data Warehouse]. Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét][Azure glossary], amikor az új fogalmakkal ismerkedik. Vagy tekintsen meg néhányat a többi SQL Data Warehouse-erőforrás közül.  

* [Ügyfelek sikertörténetei]
* [Blogok]
* [Funkciókérések]
* [Videók]
* [Az ügyféltanácsadói csapat blogjai]
* [Stack Overflow-fórum]
* [Twitter]


[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Az ügyféltanácsadói csapat blogjai]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Ügyfelek sikertörténetei]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-fórum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
