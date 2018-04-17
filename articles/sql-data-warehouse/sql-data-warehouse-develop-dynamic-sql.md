---
title: Az Azure SQL Data Warehouse dinamikus SQL használatával |} Microsoft Docs
description: Tippek az Azure SQL Data Warehouse adattárházzal történő, megoldások dinamikus SQL használatát.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 260c8b69cbe783c2cf18e40669fe742867ab133d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Az SQL Data Warehouse dinamikus SQL
Tippek az Azure SQL Data Warehouse adattárházzal történő, megoldások dinamikus SQL használatát.

## <a name="dynamic-sql-example"></a>Dinamikus SQL-példa

Az SQL Data Warehouse alkalmazáskód fejlesztésekor szükség lehet dinamikus sql segítségével rugalmas, általános és moduláris megoldásokat. SQL Data Warehouse jelenleg nem támogatja a blob adattípusokat. Nem támogatja a blob típusú adatokat a karakterlánc mérete korlátozhatja, mivel blob adattípusok varchar(max) és a típus: nvarchar(max) típusokat tartalmaz. Ezek a típusok használta az alkalmazás kódjában nagy karakterláncok létrehozása, ha szeretné a kód felosztása adattömböket, és a EXEC utasítás használata.

Egy egyszerű példa:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Ha a karakterlánc rövid, használhatja [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) normál.

> [!NOTE]
> Az utasítás dinamikus SQL végrehajtásra továbbra is minden TSQL-ellenőrzési szabályok érvényesek lesznek.
> 
> 

## <a name="next-steps"></a>További lépések
További fejlesztési tippek, lásd: [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).

