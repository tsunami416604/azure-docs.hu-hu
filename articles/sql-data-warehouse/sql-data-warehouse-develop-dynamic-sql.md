---
title: Dinamikus SQL-lel az Azure SQL Data Warehouse |} A Microsoft Docs
description: Dinamikus SQL használatával az Azure SQL Data Warehouse-megoldások fejlesztése a tippek.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 567637cab1c983992b08f65352ab9a92bd448c5a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861822"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Az SQL Data Warehouse dinamikus SQL
Dinamikus SQL használatával az Azure SQL Data Warehouse-megoldások fejlesztése a tippek.

## <a name="dynamic-sql-example"></a>Példa dinamikus SQL

Az SQL Data Warehouse alkalmazáskód fejlesztésekor szükség lehet dinamikus sql segítségével rugalmas, általános és moduláris megoldások. Az SQL Data Warehouse jelenleg nem támogatja a blob adattípusokat. Nem támogatja a blob adattípusok előfordulhat, hogy korlátozza a karakterláncok méretét, mivel blob adattípusok közé tartozik a varchar(max), mind az nvarchar(max) típusú. Ha ezek a típusok hozhat létre nagy karakterláncokat az alkalmazás kódjában használta, a kód felosztása adattömbökre, és inkább az EXEC utasítás szüksége.

Egy egyszerű példa:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Ha a karakterlánc rövid, használhatja [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) szokásos módon.

> [!NOTE]
> Dinamikus SQL végrehajtásra utasítások továbbra is az összes TSQL-ellenőrzési szabályok érvényesek lesznek.
> 
> 

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).

