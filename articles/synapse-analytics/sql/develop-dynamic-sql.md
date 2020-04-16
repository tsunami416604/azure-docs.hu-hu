---
title: Dinamikus SQL használata a Synapse SQL-ben
description: Tippek a dinamikus SQL synapse SQL használatához.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: b546e6ba6967edcf41e2830a639e69d436827c40
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429602"
---
# <a name="dynamic-sql-in-synapse-sql"></a>Dinamikus SQL a Synapse SQL-ben
Ebben a cikkben tippeket talál a dinamikus SQL használatához és a Synapse SQL használatával kapcsolatos megoldások fejlesztéséhez.

## <a name="dynamic-sql-example"></a>Dinamikus SQL-példa

Az alkalmazáskód fejlesztése során előfordulhat, hogy dinamikus SQL-t kell használnia a rugalmas, általános és moduláris megoldások biztosításához.

> [!NOTE]
> Az SQL-készlet jelenleg nem támogatja a blob-adattípusokat. A blobadat-típusok támogatása korlátozhatja a karakterláncok méretét, mivel a blob-adattípusok varchar(max) és nvarchar(max) típusokat is tartalmaznak. Ha ezeket a típusokat használta az alkalmazáskódban nagy karakterláncok létrehozásához, akkor a kódot adattömbökre kell bontania, és helyette az EXEC utasítást kell használnia.

Egy egyszerű példa:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Ha a karakterlánc rövid, [a sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a szokásos módon is használhatja.

> [!NOTE]
> A dinamikus SQL-ként végrehajtott utasításokra továbbra is vonatkozik az összes T-SQL érvényesítési szabály.

## <a name="next-steps"></a>További lépések

További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](develop-overview.md)
