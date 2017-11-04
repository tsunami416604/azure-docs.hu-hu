---
title: "Mintaadatok betöltése az SQL Data Warehouse |} Microsoft Docs"
description: "Mintaadatok betöltése az SQL Data Warehouse adatbázisba"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e338ecf8-cfee-419b-b7b6-98108d381c62
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: 1e0df958a2f18fe1e988168918e5cfd293f84e64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="load-sample-data-into-sql-data-warehouse"></a>Mintaadatok betöltése az SQL Data Warehouse adatbázisba
Kövesse az alábbi egyszerű lépéseket betölteni, és az Adventure Works vállalat adatbázisából. Ezek a parancsfájlok először az sqlcmd paranccsal lehet futtatni az SQL, amely táblák és nézetek fog létrehozni. Táblák létrehozása után, akkor a parancsfájlok adatainak betöltése bcp használja.  Ha még nem rendelkezik sqlcmd és telepített bcp, az alábbi hivatkozásokat követve [bcp telepítése] [ install bcp] és [sqlcmd telepítése][install sqlcmd].

## <a name="load-sample-data"></a>Mintaadatok betöltése
1. Töltse le a [Adventure Works mintaparancsfájlok az SQL Data Warehouse] [ Adventure Works Sample Scripts for SQL Data Warehouse] zip-fájl.
2. Csomagolja ki a fájlokat a letöltött zip a helyi számítógép könyvtárra.
3. A kibontott fájl aw_create.bat szerkesztése és a fájl elején található a következő változókat.  Ügyeljen arra, hogy nem állhat csak szóközökből közötti hagyja bejelölve a "=" és a paraméter.  Az alábbiakban példákat hogyan nézhet ki a módosításokat.
   
    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```
4. A szerkesztett aw_create.bat futtassa egy Windows parancssort.  Győződjön meg arról, hogy a könyvtárban, ahová menteni szeretné a aw_create.bat módosított verzióját.
   Ezt a parancsfájlt a rendszer...
   
   * Az Adventure Works táblát és nézetet, az adatbázisban már meglévő DROP
   * Az Adventure Works táblák és nézetek létrehozása
   * A bcp Adventure Works táblákra betöltése
   * A sorok száma az Adventure Works mindegyikhez ellenőrzése
   * Statisztika gyűjtése az Adventure Works mindegyikhez minden egyes oszlophoz

## <a name="query-sample-data"></a>Lekérdezés mintaadatokat
Ha néhány adatot az SQL Data Warehouse-bA már betöltött, gyorsan néhány lekérdezéseket is futtathat.  A lekérdezés futtatásához az újonnan létrehozott Adventure Works adatbázishoz csatlakozni Azure SQL DW használja a Visual Studio és az SSDT, lásd: a [lekérdezése a Visual Studio] [ query with Visual Studio] dokumentum.

Példa: egyszerű select utasítás alkalmazott összes adatainak lekéréséhez:

```sql
SELECT * FROM DimEmployee;
```

Például egy összetettebb lekérdezés szerkezetek GROUP BY például használatával nézze meg az összes értékesítés összesen minden nap:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Példa: egy válassza ki a WHERE záradék rendelést kiszűrhetők egy bizonyos időpont előtt:

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Az SQL Data Warehouse szinte minden, amely támogatja az SQL Server T-SQL szerkezetek támogatja.  Bármely különbségek vannak dokumentálva a [telepítse át a kódot] [ migrate code] dokumentációját.

## <a name="next-steps"></a>Következő lépések
Most, hogy már volt lehetősége kipróbálni az egyes lekérdezések mintaadatokkal, ellenőrizze, hogyan [fejlesztése][develop], [betölteni][load], vagy [ Telepítse át] [ migrate] az SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[query with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migrate code]: sql-data-warehouse-migrate-code.md
[install bcp]: sql-data-warehouse-load-with-bcp.md
[install sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Adventure Works Sample Scripts for SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip
