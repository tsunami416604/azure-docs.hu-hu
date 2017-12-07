---
title: "Adattípus-útmutatót - Azure SQL Data Warehouse |} Microsoft Docs"
description: "Javaslatok az SQL Data Warehouse szolgáltatással kompatibilis adatok típusát."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: 2bde5da8593c559e2afb33c9c4842695dc273ac3
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="guidance-for-defining-data-types-for-tables-in-sql-data-warehouse"></a>Útmutató az SQL Data Warehouse táblák adattípusok definiálása
Ezek a javaslatok használatával az SQL Data Warehouse szolgáltatással kompatibilis tábla adattípusok definiálása. Mellett kompatibilitási minimalizálja a adattípusok mérete növeli a lekérdezések teljesítményét.

Az SQL Data Warehouse támogatja a leggyakrabban használt adattípusokat. A támogatott adattípusok listájáért lásd: [adattípusok](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) a CREATE TABLE utasításban. 


## <a name="minimize-row-length"></a>Minimalizálása érdekében a sor hossza
Minimalizálja a adattípusok mérete lerövidíti a sor hossza, ami jobb teljesítmény-küszöbérték. A legkisebb adattípus, amely használja az adatok. 

- Ne adjon meg egy nagy alapértelmezett hosszúságú karakteres oszlopokra. Meghatározhatja például, ha a leghosszabb érték 25 karakternél, majd az oszlop VARCHAR(25) mint. 
- Kerülje a [NVARCHAR] [ NVARCHAR] amikor csak szüksége VARCHAR.
- Ha lehetséges, használja a NVARCHAR(4000) vagy VARCHAR(8000) NVARCHAR(MAX) vagy VARCHAR(MAX) helyett.

A táblák betöltése a Polybase segítségével végzi, ha a táblázat a meghatározott hossza nem haladhatja meg a 1 MB. Ha egy sor változó hosszúságú adatokkal, mint 1 MB, betöltése a BCP-vel, de nem a polybase-zel sor.

## <a name="identify-unsupported-data-types"></a>Nem támogatott adattípusú azonosítása
Ha az adatbázis egy másik SQL-adatbázis telepít, adattípusok nem támogatott az SQL Data Warehouse léphetnek fel. Ez a lekérdezés segítségével a meglévő SQL-séma nem támogatott adattípusok felderítése.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Lehetséges megoldások használata nem támogatott adattípusok

Az alábbi lista mutatja az adatok típusát, hogy az SQL Data Warehouse nem támogatja, és lehetőséget ad a nem támogatott adattípusokat helyett használható alternatív.

| Nem támogatott adattípusú: | Megkerülő megoldás |
| --- | --- |
| [geometriai][geometry] |[varbinary][varbinary] |
| [földrajzi hely][geography] |[varbinary][varbinary] |
| [Hierarchiaazonosító][hierarchyid] |[nvarchar][nvarchar](4000) |
| [kép][ntext,text,image] |[varbinary][varbinary] |
| [szöveg][ntext,text,image] |[varchar][varchar] |
| [ntext][ntext,text,image] |[nvarchar][nvarchar] |
| [sql_variant][sql_variant] |Oszlop felosztása több szigorú típusmegadású oszlopot. |
| [tábla][table] |Az ideiglenes táblák átalakítása. |
| [időbélyeg][timestamp] |Használandó kódját átdolgozási [datetime2] [ datetime2] és `CURRENT_TIMESTAMP` függvény.  Csak állandó támogatott alapértelmezettként, ezért current_timestamp nem definiálható mint az alapértelmezett megkötés. Ha sor verzióértékek át gépelt Timestamp típusú oszlopa van szüksége, használja [bináris][BINARY](8) vagy [VARBINARY][BINARY](8) a NOT NULL értékű vagy Verzió sorérték null értékű. |
| [XML][xml] |[varchar][varchar] |
| [felhasználó által definiált típus][user defined types] |Vissza a natív adattípusúra konvertálása amikor lehetséges. |
| alapértelmezett értékek | Alapértelmezett értékek szövegkonstansok és csak állandók támogatja.  A nem determinisztikus kifejezésekre vagy funkciók, például a `GETDATE()` vagy `CURRENT_TIMESTAMP`, nem támogatottak. |


## <a name="next-steps"></a>Következő lépések
További tudnivalókért lásd:

- [SQL Data Warehouse gyakorlati tanácsok][SQL Data Warehouse Best Practices]
- [Tábla – áttekintés][Overview]
- [Egy tábla terjesztése][Distribute]
- [Egy tábla indexelő][Index]
- [A tábla particionálása][Partition]
- [Tábla statisztikai adatainak karbantartása][Statistics]
- [Az ideiglenes táblák][Temporary]

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[geometry]: https://msdn.microsoft.com/library/cc280487.aspx
[geography]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[user defined types]: https://msdn.microsoft.com/library/ms131694.aspx
