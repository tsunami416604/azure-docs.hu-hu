---
title: Az eszköz-lekérdezésekre címkék használata az SQL Data Warehouse |} Microsoft Docs
description: Tippek az eszköz lekérdezések címkék használata az Azure SQL Data Warehouse adattárházzal történő, megoldások.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 48fe625573639c0ec98e672f02a35e4a9ae268e8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Az eszköz-lekérdezésekre címkék használata az Azure SQL Data Warehouse
Tippek az eszköz lekérdezések címkék használata az Azure SQL Data Warehouse adattárházzal történő, megoldások.


## <a name="what-are-labels"></a>Mik azok a címkék?
Az SQL Data Warehouse nevű lekérdezés címkék elvét támogatja. Mielőtt továbblép az a mélység, nézzük például:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Az utolsó sort a "Saját lekérdezés címke" karakterláncot a lekérdezés címkéket. Ezt a címkét is különösen hasznos, mivel a lekérdezés-tudja a dinamikus felügyeleti nézetek használatával címkéje. A címkék lekérdezése lehetővé teszi a probléma lekérdezések megkeresése, és segít azonosítani egy ELT futtatása során.

Jó elnevezési valóban segítségével. Például projekt verziótól kezdődően a címke eljárás, utasítás vagy megjegyzés segít a lekérdezés a verziókövetési a kód között egyedi azonosításához.

A következő lekérdezés felirat keresésére használt dinamikus felügyeleti nézetben.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Fontos, amelyre a szögletes zárójelek között vagy a word címke dupla idézőjelbe lekérdezésekor. Címke egy fenntartott szó, és hibát okoz, ha azt nem tagolt. 
> 
> 

## <a name="next-steps"></a>További lépések
További fejlesztési tippek, lásd: [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).


