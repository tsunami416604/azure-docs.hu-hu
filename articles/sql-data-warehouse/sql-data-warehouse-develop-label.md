---
title: Címkék használata a hangeszközök lekérdezéséhez
description: Tippek Címkék létrehozásához a Azure SQL Data Warehouse a megoldások fejlesztéséhez.
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
ms.openlocfilehash: 4ca0b3564418aafe774158057bc3efd541f71f66
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692832"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Címkék használata a Azure SQL Data Warehousei eszközökön való lekérdezésekhez
Tippek Címkék létrehozásához a Azure SQL Data Warehouse a megoldások fejlesztéséhez.


## <a name="what-are-labels"></a>Mik a Címkék?
SQL Data Warehouse támogatja a lekérdezési címkék nevű koncepciót. Mielőtt bármilyen mélységbe kerül, tekintsük át a következő példát:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Az utolsó sorban a "saját lekérdezés" karakterláncot kell a lekérdezéshez felcímkézni. Ez a címke különösen hasznos, mivel a címke lekérdezéssel képes a DMV. A címkék lekérdezése egy olyan mechanizmust biztosít, amely lehetővé teszi a problémák lekérdezését, és segít az előrehaladás azonosításában egy ELT futtatásával.

A jó elnevezési konvenció igazán segít. Ha például a címkét a projekt, eljárás, utasítás vagy Megjegyzés alapján indítja el, a lekérdezés egyedi módon azonosítható a forrás vezérlőelemben lévő összes kód között.

A következő lekérdezés dinamikus felügyeleti nézetet használ a címke szerinti kereséshez.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> A lekérdezés során elengedhetetlen, hogy szögletes zárójeleket vagy idézőjeleket helyezzen el a szó címkéje körül. A címke egy foglalt szó, és hibát okoz, ha nincs elválasztva. 
> 
> 

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).


