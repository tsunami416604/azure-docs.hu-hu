---
title: Címkék használata a lekérdezések műszerezésére
description: Tippek a címkék használatához a Synapse SQL-készletben lévő lekérdezések műszeréhez megoldások fejlesztéséhez.
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
ms.openlocfilehash: 5e2cd03ae878e80139a7f7a8ba67cef15b24d571
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633491"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Címkék használata a synapse SQL-készletben lévő lekérdezések eszközéhez

Ez a cikk tippeket tartalmaz a címkék használatával az SQL-készletben lévő instrument lekérdezések használatával megoldások fejlesztéséhez.

Tippek a címkék használatával az Azure SQL Data Warehouse-ban a megoldások fejlesztéséhez.

## <a name="what-are-labels"></a>Mik azok a címkék?

Az SQL-készlet támogatja a lekérdezéscímkéknek nevezett fogalmat. Mielőtt bármilyen mélységbe menne, nézzünk meg egy példát:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Az utolsó sor a "Saját lekérdezés címke" karakterláncot címkézi a lekérdezéshez. Ez a címke azért hasznos, mert a címke lekérdezésre alkalmas a DMV-ken keresztül.

Címkék lekérdezése lehetővé teszi a problémalekérdezések megkeresésére és az ELT-futtatás on keresztüli előrehaladás azonosítására.

Egy jó elnevezési konvenció tényleg segít. Ha például a címkét PROJECT, PROCEDURE, STATEMENT vagy COMMENT programmal indítja el, a lekérdezés tanusíthatja a forrásvezérlő összes kódja között.

A következő lekérdezés dinamikus felügyeleti nézetet használ a címke szerint való kereséshez:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> A lekérdezés során fontos, hogy szögletes zárójeleket vagy dupla idézőjeleket helyezzen a szócímke köré. A címke fenntartott szó, és hibát okoz, ha nincs elválasztva.

## <a name="next-steps"></a>További lépések

További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)
