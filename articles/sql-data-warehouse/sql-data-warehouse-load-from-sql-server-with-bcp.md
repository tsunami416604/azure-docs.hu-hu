---
title: "Adatok betöltése az SQL Serverről az Azure SQL Data warehouse-ba (bcp) |} Microsoft Docs"
description: "Kisebb adatméret esetében a bcp segítségével exportál adatokat az SQL Serverről egybesimított fájlokba, majd közvetlenül az SQL Data Warehouse-ba importálja őket."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: f87d8d7c-8276-43c5-90e7-d4ccc0e3a224
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: dae7b5f7456f4ec0daf60d55f9c38b780896ff83
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-flat-files"></a>Adatok betöltése az SQL Serverből az Azure SQL Data Warehouse-ba (egybesimított fájlok)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Kisebb adatkészletek esetében a bcp parancssori segédprogrammal exportálhat adatokat az SQL Serverből, majd közvetlenül az SQL Data Warehouse-ba töltheti be őket.

Az oktatóanyag során a következőket hajtja végre a bcp segítségével:

* Exportál egy táblát az SQL Serverből a bcp out paranccsal (vagy létrehoz egy egyszerű mintafájlt)
* Importál egy táblát egy egybesimított fájlból az SQL Data Warehouse-ba.
* Statisztikákat készít a betöltött adatokról.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="before-you-begin"></a>Előkészületek
### <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy SQL Data Warehouse-adatbázis
* Telepített bcp parancssori segédprogram
* Telepített sqlcmd parancssori segédprogram

A bcp és az sqlcmd parancssori segédprogramot a [Microsoft letöltőközpontból][Microsoft Download Center] töltheti le.

### <a name="data-in-ascii-or-utf-16-format"></a>Adatok ASCII vagy UTF-16 formátumban
Ha a saját adataival próbálja használni ezt az oktatóanyagot, az adatoknak ASCII vagy UTF-16 kódolást kell használniuk, mert a bcp nem támogatja az UTF-8 formátumot. 

A PolyBase támogatja az UTF-8 formátumot, de az UTF-16 formátumot még nem. Ügyeljen arra, hogy ha a bcp-t és a PolyBase-t együtt kívánja használni, az adatokat az SQL Serverből való exportálás után UTF-8 formátumra kell alakítania. 

## <a name="1-create-a-destination-table"></a>1. Céltábla létrehozása
Adjon meg egy táblát az SQL Data Warehouse-ban a betöltés céltáblájául. A tábla oszlopainak meg kell felelnie az adatfájl egyes soraiban szereplő adatoknak.

Tábla létrehozásához nyisson meg egy parancssort, és az sqlcmd.exe segítségével futtassa a következő parancsot:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```


## <a name="2-create-a-source-data-file"></a>2. Forrásadatfájlok létrehozása
Nyissa meg a Jegyzettömböt, és másolja az alábbi adatsorokat egy új szöveges fájlba, majd mentse ezt a fájlt a helyi ideiglenes könyvtárba (C:\Temp\DimDate2.txt). Ezek az adatok ASCII formátumban vannak.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(Opcionális) A saját adatok SQL Server-adatbázisból való exportálásához nyisson meg egy parancssort, és futtassa az alábbi parancsot. Cserélje le a TableName, ServerName, DatabaseName, Username és Password adatokat a saját adataira.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## <a name="3-load-the-data"></a>3. Az adatok betöltése
Az adatok betöltéséhez nyisson meg egy parancssort, és futtassa az alábbi, a kiszolgáló nevét, az adatbázis nevét, a felhasználónevet és a jelszót a saját értékeire lecserélő parancsot.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

A parancs segítségével ellenőrizze, hogy megfelelő volt-e az adatok betöltése

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Az eredménynek így kell kinéznie:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="4-create-statistics"></a>4. Statisztika létrehozása
Az SQL Data Warehouse még nem támogatja a statisztikák automatikus létrehozását vagy automatikus frissítését. A legjobb lekérdezési teljesítmény eléréséhez fontos létrehozni statisztikákat a táblák összes oszlopához az első betöltés után, illetve az adatok minden lényeges módosítását követően. A statisztika részletes ismertetése, [statisztika][Statistics]. 

A statisztika létrehozásához futtassa az alábbi parancsot az újonnan betöltött táblákon.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="5-export-data-from-sql-data-warehouse"></a>5. Adatok exportálása az SQL Data Warehouse-ból
Ha szeretné, exportálhatja az SQL Data Warehouse-ból épp visszatöltött adatokat.  Az exportáláshoz használt parancs pontosan megegyezik az SQL Serverről való exportáláshoz használttal.

Az eredmények viszont eltérőek lesznek. Minthogy az adatokat a rendszer elosztott helyeken tárolja az SQL Data Warehouse-ban, az adatok exportálásakor minden számítási csomópont a kimeneti fájlba írja az adatait. A kimeneti fájl adatainak sorrendje valószínűleg eltér a bemeneti fájl adatainak sorrendjétől.

### <a name="export-a-table-and-compare-exported-results"></a>Tábla exportálása és az exportált eredmények összehasonlítása
Az exportált adatok megtekintéséhez nyissa meg a parancssort, és futtassa ezt a parancsot a saját paramétereivel. A ServerName az Azure logikai SQL Server neve.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Az adatok helyes exportálását az új fájl megnyitásával ellenőrizheti. A fájl adatainak egyeznie kell az alábbi szöveggel, de a sorrendje valószínűleg eltérő lesz:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### <a name="export-the-results-of-a-query"></a>Lekérdezés eredményeinek exportálása
A bcp **queryout** függvényével exportálhatja egy lekérdezés eredményeit az egész tábla exportálása helyett. 

## <a name="next-steps"></a>További lépések
A betöltés áttekintése: [Adatok betöltése az SQL Data Warehouse-ba][Load data into SQL Data Warehouse].
További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés][SQL Data Warehouse development overview].
Lásd: [tábla áttekintése] [ Table Overview] vagy [CREATE TABLE szintaxis] [ CREATE TABLE syntax] SQL Data Warehouse a táblázatok létrehozásáról további információt.

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Table Overview]: ./sql-data-warehouse-tables-overview.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
