---
title: Címkék használata a hangeszközök lekérdezéséhez
description: Tippek Címkék létrehozásához a szinapszis SQL-készletben a megoldások fejlesztéséhez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d4459547300f5dfc7b7c22d1e531b928a13aa66c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213431"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Címkék használata a szinapszis SQL-készletben lévő eszközök lekérdezéséhez

Ebben a cikkben a megoldások az SQL-készletben címkék segítségével történő fejlesztésével kapcsolatos tippeket talál.

Tippek Címkék létrehozásához a Azure SQL Data Warehouse a megoldások fejlesztéséhez.

## <a name="what-are-labels"></a>Mik a Címkék?

Az SQL-készlet támogatja a lekérdezési címkék nevű koncepciót. Mielőtt bármilyen mélységbe kerül, tekintsük át a következő példát:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Az utolsó sorban a "saját lekérdezés" karakterláncot kell a lekérdezéshez felcímkézni. Ez a címke hasznos lehet, mert a címke lekérdezési lehetőséggel rendelkezik a DMV.

A címkék lekérdezése egy olyan mechanizmust biztosít, amely lehetővé teszi a problémák lekérdezését, és segít az előrehaladás azonosításában egy ELT futtatásával.

A jó elnevezési konvenció igazán segít. Ha például a címkét a projekt, eljárás, utasítás vagy Megjegyzés alapján indítja el, egyedi módon azonosítja a lekérdezést a verziókövetés összes kódja között.

A következő lekérdezés dinamikus felügyeleti nézetet használ a címke szerinti kereséshez:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> A lekérdezés során elengedhetetlen, hogy szögletes zárójeleket vagy idézőjeleket helyezzen el a szó címkéje körül. A címke egy foglalt szó, és hibát okoz, ha nincs elválasztva.

## <a name="next-steps"></a>További lépések

További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).
