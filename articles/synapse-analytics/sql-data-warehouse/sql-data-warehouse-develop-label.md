---
title: Címkék használata a lekérdezések műszerezésére
description: Tippek a címkék használatával az Azure SQL Data Warehouse-ban a megoldások fejlesztéséhez.
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
ms.openlocfilehash: 828e4a406cd0fb12877af44263ab1f338c20850c
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351679"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Címkék használata az Azure SQL Data Warehouse-ban lévő lekérdezések eszközkezeléséhez
Tippek a címkék használatával az Azure SQL Data Warehouse-ban a megoldások fejlesztéséhez.


## <a name="what-are-labels"></a>Mik azok a címkék?
Az SQL Data Warehouse támogatja a lekérdezéscímkéknek nevezett fogalmat. Mielőtt bármilyen mélységbe menne, nézzünk meg egy példát:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Az utolsó sor a "Saját lekérdezés címke" karakterláncot címkézi a lekérdezéshez. Ez a címke különösen hasznos, mivel a címke lekérdezésre alkalmas a DMV-ken keresztül. Címkék lekérdezése lehetővé teszi a problémalekérdezések megkeresésére és az ELT-futtatás on keresztüli előrehaladás azonosítására.

Egy jó elnevezési konvenció tényleg segít. Ha például a címkét PROJECT, PROCEDURE, STATEMENT vagy COMMENT alkalmazással indítja el, a forrásvezérlő összes kódja között egyedileg azonosíthatja a lekérdezést.

A következő lekérdezés dinamikus felügyeleti nézetet használ a címke szerint való kereséshez.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> A lekérdezés során fontos, hogy szögletes zárójeleket vagy dupla idézőjeleket helyezzen a szócímke köré. A címke fenntartott szó, és hibát okoz, ha nincs elválasztva. 
> 
> 

## <a name="next-steps"></a>További lépések
További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)


