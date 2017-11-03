---
title: Az SQL Data Warehouse dinamikus SQL |} Microsoft Docs
description: "Tippek az Azure SQL Data Warehouse adattárházzal történő, megoldások dinamikus SQL használatát."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: a948c2c3-3cd1-4373-90a9-79e59414b778
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 29228676373aee8dbc7b1b2a7d92ffc978333804
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Az SQL Data Warehouse dinamikus SQL
Az SQL Data Warehouse alkalmazáskód fejlesztése során szükség lehet dinamikus sql segítségével rugalmas, általános és moduláris megoldásokat. SQL Data Warehouse jelenleg nem támogatja a blob adattípusokat. Ez a karakterlánc mérete korlátozhatja, blob típusok varchar(max) és a típus: nvarchar(max) típusok közé. Ha ezek a típusok már használta az alkalmazás kódjában, nagyon nagy karakterláncok felépítése közben, akkor a kód felosztása adattömböket, és a EXEC utasítás használata.

Egy egyszerű példa:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Ha a karakterlánc rövid használhatja [sp_executesql] [ sp_executesql] normál.

> [!NOTE]
> Az utasítás dinamikus SQL végrehajtásra továbbra is minden TSQL-ellenőrzési szabályok érvényesek lesznek.
> 
> 

## <a name="next-steps"></a>Következő lépések
További fejlesztési tippek, lásd: [fejlesztői áttekintés][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->
