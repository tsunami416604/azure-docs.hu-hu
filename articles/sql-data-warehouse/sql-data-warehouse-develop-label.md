---
title: "Az eszköz-lekérdezésekre címkék használata az SQL Data Warehouse |} Microsoft Docs"
description: "Tippek az eszköz lekérdezések címkék használata az Azure SQL Data Warehouse adattárházzal történő, megoldások."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 44988de8-04c1-4fed-92be-e1935661a4e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 9e75bbe528a427724a623305fbd45e2277e9d0af
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Az eszköz-lekérdezésekre címkék használata az SQL Data Warehouse
Az SQL Data Warehouse nevű lekérdezés címkék elvét támogatja. Mielőtt a felhasználó bármely mélysége be most tekintse meg egy példát:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Utolsó sor címkéket a lekérdezés a "Saját lekérdezés címke" karakterlánc. Ez az különösen hasznos, mert a címke a lekérdezés-tudja a dinamikus felügyeleti nézetek használatával. Ez ad egy olyan mechanizmus, nyomon követheti a probléma lekérdezések és azonosításához az ETL futtatása során.

Jó elnevezési valóban itt segít. Például alábbihoz hasonló "projekt: eljárás: utasítás: COMMENT" segítene a lekérdezést a verziókövetési a kód között egyedi azonosításához.

Címke szerinti kereséshez a következő lekérdezés a dinamikus felügyeleti nézetekkel használó használhatja:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Fontos, hogy burkolása szögletes zárójelek között vagy a word címke dupla idézőjelbe lekérdezésekor. Címke egy fenntartott szó, és fogja a hiba oka, hogy nem lettek tagolva.
> 
> 

## <a name="next-steps"></a>Következő lépések
További fejlesztési tippek, lásd: [fejlesztői áttekintés][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
