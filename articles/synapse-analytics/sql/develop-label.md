---
title: Lekérdezéscímkék használata a Synapse SQL-ben
description: Ez a cikk alapvető tippeket a lekérdezési címkék használata a Synapse SQL.
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
ms.openlocfilehash: 47b476cbc6997ca5ec63968bdc269e2273662100
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430031"
---
# <a name="use-query-labels-in-synapse-sql"></a>Lekérdezéscímkék használata a Synapse SQL-ben
Ez a cikk alapvető tippeket a lekérdezési címkék használata a Synapse SQL.

> [!NOTE]
> Az SQL igény szerinti (előzetes verzió) nem támogatja a címkézési lekérdezéseket.

## <a name="what-are-query-labels"></a>Mik azok a lekérdezési címkék?
Az SQL-készlet támogatja a lekérdezéscímkéknek nevezett fogalmat. Mielőtt bármilyen mélységbe menne, nézzünk meg egy példát:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Az utolsó sor a "Saját lekérdezés címke" karakterláncot címkézi a lekérdezéshez. Ez a címke különösen hasznos, mivel a címke lekérdezésre alkalmas a DMV-ken keresztül. Címkék lekérdezése lehetővé teszi a problémalekérdezések megkeresésére szolgáló mechanizmust, és segít az ELT-futtatás on keresztüli előrehaladás azonosításában.

A jó elnevezési konvenció a leghasznosabb. Ha például a címkét PROJECT, PROCEDURE, STATEMENT vagy COMMENT programmal indítja el, a lekérdezés tanusíthatja a forrásvezérlő összes kódja között.

A következő lekérdezés dinamikus felügyeleti nézetet használ a címke szerint való kereséshez:

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
További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](develop-overview.md)


