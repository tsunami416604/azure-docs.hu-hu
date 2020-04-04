---
title: A dinamikus SQL használata
description: Tippek a dinamikus SQL-t használó fejlesztési megoldásokhoz a Synapse SQL-készletben.
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
ms.openlocfilehash: a9280bb8153204f86096cf8249ff053bee3f71cc
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633526"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>Dinamikus SQL a Synapse SQL-készletben

Ez a cikk a dinamikus SQL-készletben dinamikus SQL-t használó fejlesztési megoldásokra vonatkozó tippeket tartalmazza.

## <a name="dynamic-sql-example"></a>Dinamikus SQL-példa

Az SQL-készlet alkalmazáskódjának fejlesztése során előfordulhat, hogy dinamikus SQL-t kell használnia a rugalmas, általános és moduláris megoldások biztosításához. Az SQL-készlet jelenleg nem támogatja a blobadat-típusokat.

A blobadat-típusok támogatása korlátozhatja a karakterláncok méretét, mivel a blob-adattípusok varchar(max) és nvarchar(max) típusokat is tartalmaznak.

Ha ezeket a típusokat használta az alkalmazáskódban nagy karakterláncok létrehozásához, fel kell bontania a kódot adattömbökre, és helyette az EXEC utasítást kell használnia.

Egy egyszerű példa:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Ha a karakterlánc rövid, [a sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) a szokásos módon is használhatja.

> [!NOTE]
> A dinamikus SQL-ként végrehajtott utasításokra továbbra is vonatkozik az összes T-SQL érvényesítési szabály.

## <a name="next-steps"></a>További lépések

További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)
