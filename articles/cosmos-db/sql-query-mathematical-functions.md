---
title: Matematikai függvények Azure Cosmos DB lekérdezési nyelven
description: Ismerje meg a Azure Cosmos DB matematikai SQL-rendszerfunkcióit.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fe4a84db3f2cbcfc2d9841caf520404afec2a297
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349674"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Matematikai függvények (Azure Cosmos DB)  

A matematika függvényekkel hajtsa végre a számítási, amelyek argumentumként szolgálnak, és a egy numerikus értéket adja vissza a bemeneti értékek alapján.

A következő példához hasonló lekérdezéseket futtathat:

```sql
    SELECT VALUE ABS(-4)
```

Az eredmény a következőket eredményezi:

```json
    [4]
```

## <a name="functions"></a>Funkciók

A következő támogatott beépített matematikai függvények számításokat hajtanak végre, általában a bemeneti argumentumok alapján, és egy numerikus kifejezést adnak vissza.
  
||||  
|-|-|-|  
|[ABS](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[FELSŐ HATÁR](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[DEGREES](sql-query-degrees.md)|  
|[EXP](sql-query-exp.md)|[EMELET](sql-query-floor.md)|[LOG](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[KEREKÍTÉS](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[SIN](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[NÉGYSZÖG](sql-query-square.md)|[BARACKSZÍNŰ](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
A RAND kivételével minden matematikai függvény determinisztikus függvény. Ez azt jelenti, hogy a rendszer minden alkalommal ugyanazt az eredményt adja vissza, amikor meghívja őket egy adott bemeneti értékkel.

## <a name="next-steps"></a>További lépések

- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)
- [Aggregátumok](sql-query-aggregates.md)
