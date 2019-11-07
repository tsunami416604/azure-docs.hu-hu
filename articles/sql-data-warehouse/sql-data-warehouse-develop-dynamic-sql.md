---
title: Dinamikus SQL használata
description: Tippek a dinamikus SQL használatának Azure SQL Data Warehouse a megoldások fejlesztéséhez.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 87320128537a235e8047a2f826b0e59c08aef76b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692844"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dinamikus SQL in SQL Data Warehouse
Tippek a dinamikus SQL használatának Azure SQL Data Warehouse a megoldások fejlesztéséhez.

## <a name="dynamic-sql-example"></a>Dinamikus SQL-példa

A SQL Data Warehouse alkalmazás kódjának fejlesztésekor szükség lehet a dinamikus SQL használatára a rugalmas, általános és moduláris megoldások biztosításához. A SQL Data Warehouse jelenleg nem támogatja a blob-adattípusokat. A blob-adattípusok nem támogatják a karakterláncok méretét, mivel a blob-adattípusok a varchar (max) és a nvarchar (max) típusokat is tartalmazzák. Ha már használta ezeket a típusokat az alkalmazás kódjában nagyméretű karakterláncok létrehozásához, akkor a kódot a darabokra kell bontania, és ehelyett az EXEC utasítást kell használnia.

Egy egyszerű példa:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Ha a sztring rövid, a [Sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) normál módon is használhatja.

> [!NOTE]
> A dinamikus SQLként végrehajtott utasítások továbbra is az összes TSQL-érvényesítési szabály hatálya alá esnek.
> 
> 

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).

