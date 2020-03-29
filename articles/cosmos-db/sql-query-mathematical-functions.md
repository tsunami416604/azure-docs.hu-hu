---
title: Matematikai függvények az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az Azure Cosmos DB matematikai függvényeit, amelyek argumentumként megadott bemeneti értékek alapján számításokat hajtanak végre, és numerikus értéket adnak vissza.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873267"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Matematikai függvények (Azure Cosmos DB)  

A matematikai függvények egy-egy számítást hajtanak végre az argumentumként megadott bemeneti értékek alapján, és numerikus értéket adnak vissza.

A következő példához hasonló lekérdezéseket futtathat:

```sql
    SELECT VALUE ABS(-4)
```

Az eredmény:

```json
    [4]
```

## <a name="functions"></a>Functions

A következő támogatott beépített matematikai függvények végeznek számítást, általában bemeneti argumentumok alapján, és numerikus kifejezést adnak vissza.
  
||||  
|-|-|-|  
|[ABS](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[Mennyezet](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[DEGREES](sql-query-degrees.md)|  
|[Exp](sql-query-exp.md)|[Padló](sql-query-floor.md)|[Napló](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[Kerek](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[Bűn](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[SQUARE](sql-query-square.md)|[TAN](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
A RAND kivételével minden matematikai függvény determinisztikus függvény. Ez azt jelenti, hogy ugyanazt az eredményt adják vissza minden alkalommal, amikor egy adott bemeneti értékkészlettel hívják őket.

## <a name="next-steps"></a>További lépések

- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)
- [Összesítések](sql-query-aggregates.md)
