---
title: Adattípus - Azure SQL Data Warehouse meghatározása |} Microsoft Docs
description: Javaslatok az Azure SQL Data Warehouse tábla adattípusok definiálása.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 4d8a222a6d4cfa4138fe833fb4e9cc895dbc5f65
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523506"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse tábla adattípusok
Javaslatok az Azure SQL Data Warehouse tábla adattípusok definiálása. 

## <a name="what-are-the-data-types"></a>Mik az adattípusok?

Az SQL Data Warehouse támogatja a leggyakrabban használt adattípusokat. A támogatott adattípusok listájáért lásd: [adattípusok](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) a CREATE TABLE utasításban. 

## <a name="minimize-row-length"></a>Minimalizálása érdekében a sor hossza
Minimalizálja a adattípusok mérete lerövidíti a sor hossza, ami jobb teljesítmény-küszöbérték. A legkisebb adattípus, amely használja az adatok. 

- Ne adjon meg egy nagy alapértelmezett hosszúságú karakteres oszlopokra. Meghatározhatja például, ha a leghosszabb érték 25 karakternél, majd az oszlop VARCHAR(25) mint. 
- Kerülje a [NVARCHAR] [NVARCHAR] Ha VARCHAR csak van szükség.
- Ha lehetséges, használja a NVARCHAR(4000) vagy VARCHAR(8000) NVARCHAR(MAX) vagy VARCHAR(MAX) helyett.

A PolyBase külső táblák segítségével végzi a táblák betölteni, ha a táblázat a meghatározott hossza nem haladhatja meg a 1 MB. Ha egy sor változó hosszúságú adatokkal, mint 1 MB, betöltése a BCP-vel, de nem a polybase-zel sor.

## <a name="identify-unsupported-data-types"></a>Nem támogatott adattípusú azonosítása
Ha az adatbázis egy másik SQL-adatbázis telepít, adattípusok nem támogatott az SQL Data Warehouse léphetnek fel. Ez a lekérdezés segítségével a meglévő SQL-séma nem támogatott adattípusok felderítése.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Nem támogatott adattípusú megoldásai

Az alábbi lista mutatja az adatok típusát, hogy az SQL Data Warehouse nem támogatja, és lehetőséget ad a nem támogatott adattípusokat helyett használható alternatív.

| Nem támogatott adattípusú: | Megkerülő megoldás |
| --- | --- |
| [geometriai](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [földrajzi hely](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Hierarchiaazonosító](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Szöveg](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Oszlop felosztása több szigorú típusmegadású oszlopot. |
| [Tábla](/sql/t-sql/data-types/table-transact-sql) |Az ideiglenes táblák átalakítása. |
| [időbélyeg](/sql/t-sql/data-types/date-and-time-types) |Használandó kódját átdolgozási [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) és a [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) függvény. Csak állandó támogatott alapértelmezettként, ezért current_timestamp nem definiálható mint az alapértelmezett megkötés. Ha sor verzióértékek át gépelt Timestamp típusú oszlopa van szüksége, használja [bináris](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) vagy [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) nem null értékű vagy üres sor verzióértékek. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [felhasználó által definiált típus](/sql/relational-databases/native-client/features/using-user-defined-types) |Vissza a natív adattípusúra konvertálása amikor lehetséges. |
| alapértelmezett értékek | Alapértelmezett értékek szövegkonstansok és csak állandók támogatja. |


## <a name="next-steps"></a>További lépések
Táblák fejlesztéséről további információkért lásd: [tábla áttekintése](sql-data-warehouse-tables-overview.md).
