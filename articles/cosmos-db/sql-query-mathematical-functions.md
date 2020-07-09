---
title: Matematikai függvények Azure Cosmos DB lekérdezési nyelven
description: Megtudhatja, hogyan végezheti el a Azure Cosmos DB matematikai funkcióit a számítások elvégzéséhez, az argumentumként megadott bemeneti értékek alapján, és numerikus értéket adhat vissza.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bd53feb175c5be77f559a4d2e724a55e41df48eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85562829"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Matematikai függvények (Azure Cosmos DB)  

A matematikai függvények mindegyike az argumentumként megadott bemeneti értékek alapján végez számítást, és numerikus értéket ad vissza.

A következő példához hasonló lekérdezéseket futtathat:

```sql
    SELECT VALUE ABS(-4)
```

Az eredmény a következőket eredményezi:

```json
    [4]
```

## <a name="functions"></a>Functions

A következő támogatott beépített matematikai függvények számításokat hajtanak végre, általában bemeneti argumentumok alapján, és numerikus kifejezést adnak vissza:
 
* [ABS](sql-query-abs.md)
* [ACOS](sql-query-acos.md)
* [ASIN](sql-query-asin.md)
* [ATAN](sql-query-atan.md)
* [ATN2](sql-query-atn2.md)
* [CEILING](sql-query-ceiling.md)
* [COS](sql-query-cos.md)
* [COT](sql-query-cot.md)
* [DEGREES](sql-query-degrees.md)
* [EXP](sql-query-exp.md)
* [FLOOR](sql-query-floor.md)
* [LOG](sql-query-log.md)
* [LOG10](sql-query-log10.md)
* [PI](sql-query-pi.md)
* [POWER](sql-query-power.md)
* [RADIANS](sql-query-radians.md)
* [RAND](sql-query-rand.md)
* [ROUND](sql-query-round.md)
* [SIGN](sql-query-sign.md)
* [SIN](sql-query-sin.md)
* [SQRT](sql-query-sqrt.md)
* [SQUARE](sql-query-square.md)
* [TAN](sql-query-tan.md)
* [TRUNC](sql-query-trunc.md)

  
A RAND kivételével minden matematikai függvény determinisztikus függvény. Ez azt jelenti, hogy a rendszer minden alkalommal ugyanazt az eredményt adja vissza, amikor meghívja őket egy adott bemeneti értékkel.

## <a name="next-steps"></a>További lépések

- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)
- [Összesítések](sql-query-aggregates.md)
