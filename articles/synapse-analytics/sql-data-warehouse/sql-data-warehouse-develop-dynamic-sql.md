---
title: A dinamikus SQL használata
description: Tippek a dinamikus SQL használatával az Azure SQL Data Warehouse-ban a megoldások fejlesztéséhez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a44bec72029a50c2ef348bcdda497803e35f586d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350558"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dinamikus SQL az SQL Data Warehouse-ban
Tippek a dinamikus SQL használatával az Azure SQL Data Warehouse-ban a megoldások fejlesztéséhez.

## <a name="dynamic-sql-example"></a>Dinamikus SQL-példa

Az SQL Data Warehouse alkalmazáskódjának fejlesztése során előfordulhat, hogy dinamikus sql-t kell használnia a rugalmas, általános és moduláris megoldások biztosításához. Az SQL Data Warehouse jelenleg nem támogatja a blobadat-típusokat. A blobadat-típusok támogatása korlátozhatja a karakterláncok méretét, mivel a blob-adattípusok varchar(max) és nvarchar(max) típusokat is tartalmaznak. Ha ezeket a típusokat használta az alkalmazáskódban nagy karakterláncok létrehozásához, akkor a kódot adattömbökre kell bontania, és helyette az EXEC utasítást kell használnia.

Egy egyszerű példa:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Ha a karakterlánc rövid, [a sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) a szokásos módon is használhatja.

> [!NOTE]
> A dinamikus SQL-ként végrehajtott utasításokra továbbra is vonatkoznak az összes TSQL érvényesítési szabály.
> 
> 

## <a name="next-steps"></a>További lépések
További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)

