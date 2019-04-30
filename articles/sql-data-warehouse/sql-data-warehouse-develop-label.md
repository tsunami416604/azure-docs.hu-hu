---
title: Eszköz lekérdezések címkék használatával az SQL Data Warehouse |} A Microsoft Docs
description: Tippek a fizetőeszköz lekérdezések címkék használatával az Azure SQL Data Warehouse-megoldások fejlesztése.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
origin.date: 04/17/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.openlocfilehash: 5c60a7dc44471599834c4b1225b397c8e6eabbd5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439774"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse eszköz lekérdezések címkék használatával
Tippek a fizetőeszköz lekérdezések címkék használatával az Azure SQL Data Warehouse-megoldások fejlesztése.


## <a name="what-are-labels"></a>Mik azok a címkék?
SQL Data Warehouse támogat egy lekérdezés címkék nevű koncepciót. Való elhelyezés előtt minden mélysége, lássunk erre egy példát:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Az utolsó sort a "Saját lekérdezés címke" karakterláncot a lekérdezés címkék. Ez a címke különösen hasznos, mivel a címke a lekérdezés-tudja a dinamikus felügyeleti nézetek használatával. A címkék lekérdezése lehetővé teszi a probléma lekérdezések keresése, és azonosíthatja a folyamat futtatásához egy ELT keresztül védi.

Egy jó elnevezési konvenciója nagyon segítségével. Például projekt verziótól kezdődően a címke eljárás, nyilatkozat vagy megjegyzés segít a lekérdezést, többek között a kódot verziókövetési rendszerben az összes egyedi azonosításához.

A következő lekérdezést a címke szerinti kereséshez dinamikus felügyeleti nézetek használ.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Ez elengedhetetlen, helyezze a szögletes zárójelek között, vagy a word címke körüli idézőjeleket lekérdezésekor. Címke egy fenntartott szó, és hibát okoz, ha nem jogosult tagolt. 
> 
> 

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).


