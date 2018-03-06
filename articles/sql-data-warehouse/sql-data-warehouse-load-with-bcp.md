---
title: "Adatok betöltése az SQL Data Warehouse-ba bcp segítségével | Microsoft Docs"
description: "Megismerheti a bcp-t és az adatraktározási forgatókönyvekben való használatát."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/22/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: 55211e29149cd334421bd8723d47278a19afbfbb
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="load-data-with-bcp"></a>Adatok betöltése bcp-vel

A **[bcp](/sql/tools/bcp-utility.md)** egy tömeges betöltésre szolgáló parancssori eszköz, amellyel adatokat másolhat az SQL Server, az adatfájlok és az SQL Data Warehouse között. A bcp segítségével nagy mennyiségű sort importálhat az SQL Data Warehouse tábláiba, vagy adatokat exportálhat az SQL Server tábláiból adatfájlokba. Ha nem a queryout kapcsolóval használja, a bcp-hez nem szükséges a Transact-SQL ismerete.

A bcp egyszerű módot biztosít a kisebb adatkészletek áthelyezésére az SQL Data Warehouse-adatbázisokba, illetve onnan máshová. A betöltésre/kinyerésre ajánlott adatok pontos mennyisége az Azure hálózati kapcsolatától függ.  A kis dimenziótáblák könnyen betölthetők és kinyerhetők a bcp használatával. Nagy adatkötetek betöltéséhez és kinyeréséhez azonban a PolyBase, és nem a bcp az ajánlott eszköz. A PolyBase az SQL Data Warehouse nagymértékben párhuzamos feldolgozási architektúrájához van kialakítva.

A bcp-vel a következőket teheti:

* Egy parancssori segédprogrammal adatokat tölthet be az SQL Data Warehouse-ba.
* Egy parancssori segédprogrammal adatokat nyerhet ki az SQL Data Warehouse-ból.

Ez az oktatóanyag:

* Adatokat importál egy táblába a bcp in paranccsal
* Adatokat exportál egy táblából a bcp out paranccsal

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy SQL Data Warehouse-adatbázis
* bcp és sqlcmd parancssor eszközök. Ezeket letöltheti a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=36433). 

### <a name="data-in-ascii-or-utf-16-format"></a>Adatok ASCII vagy UTF-16 formátumban
Ha a saját adataival próbálja használni ezt az oktatóanyagot, az adatoknak ASCII vagy UTF-16 kódolást kell használniuk, mert a bcp nem támogatja az UTF-8 formátumot. 

A PolyBase támogatja az UTF-8 formátumot, de az UTF-16 formátumot még nem. Ha az adatexportáláshoz a bcp-t, majd az adatok betöltéséhez a PolyBase-t szeretné használni, az adatokat az SQL Serverből való exportálás után UTF-8 formátumra kell alakítania. 

## <a name="import-data-into-sql-data-warehouse"></a>Adatok importálása az SQL Data Warehouse-ba
Ebben az oktatóanyagban létrehoz egy táblát az Azure SQL Data Warehouse-ban, és adatokat importál a táblába.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>1. lépés: Tábla létrehozása az Azure SQL Data Warehouse-ban
Egy parancssorban az sqlcmd paranccsal futtassa a következő lekérdezést. Ezzel egy táblát hoz létre a példányán:

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

További információk a táblázatok létrehozásáról: [Táblák áttekintése](sql-data-warehouse-tables-overview.md) vagy [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse.md) szintaxis.
 

### <a name="step-2-create-a-source-data-file"></a>2. lépés: Forrásadatfájlok létrehozása
Nyissa meg a Jegyzettömböt, és másolja az alábbi adatsorokat egy új szöveges fájlba, majd mentse ezt a fájlt a helyi ideiglenes könyvtárba (C:\Temp\DimDate2.txt).

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

> [!NOTE]
> Fontos észben tartani, hogy a bcp.exe nem támogatja az UTF-8 kódolást. A bcp.exe használatakor használjon ASCII-fájlokat vagy UTF-16 kódolást.
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>3. lépés: Csatlakozás és az adatok importálása
A bcp és az alábbi parancs segítségével csatlakozhat és importálhatja az adatokat. Az értékeket szükség szerint cserélje le:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Az adatok betöltésének ellenőrzéséhez futtassa az alábbi lekérdezést az sqlcmd segítségével:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

A lekérdezés az alábbi eredményeket adja vissza:

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

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>4. lépés: Statisztikák létrehozása az újonnan betöltött adatokról
Az adatok betöltése után a végső lépés a statisztikák létrehozása vagy frissítése. Ez segít a lekérdezési teljesítmény javításában. További információ: [Statisztika](sql-data-warehouse-tables-statistics.md). A következő sqlcmd-példa statisztikát hoz létre az újonnan betöltött adatokat tartalmazó tábláról.


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Adatok exportálása az SQL Data Warehouse-ból
Ez az oktatóanyag létrehoz egy adatfájlt egy SQL Data Warehouse-táblából. Exportálja az előző szakaszban importált adatokat. Az eredmények a DimDate2_export.txt fájlba kerülnek.

### <a name="step-1-export-the-data"></a>1. lépés: Az adatok exportálása
A bcp segédprogram és az alábbi parancs segítségével csatlakozhat és exportálhatja az adatokat. Az értékeket szükség szerint cserélje le:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Az adatok helyes exportálását az új fájl megnyitásával ellenőrizheti. A fájl adatainak egyezniük kell az alábbi szöveggel:

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

> [!NOTE]
> Az elosztott rendszerek jellemzői miatt előfordulhat, hogy az adatok sorrendje nem egyforma a különböző SQL Data Warehouse-adatbázisokban. Másik lehetőségként használhatja a bcp **queryout** függvényét, amellyel a teljes táblázat helyett egy lekérdezéssel kinyert tartalom írható.
> 
> 

## <a name="next-steps"></a>További lépések
A betöltési folyamat megtervezéséhez lásd: [A betöltés áttekintése](sql-data-warehouse-design-elt-data-loading.md).  



<!--MSDN references-->



<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
