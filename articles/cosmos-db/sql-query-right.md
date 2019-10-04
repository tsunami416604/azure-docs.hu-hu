---
title: JOBB Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System függvénysel közvetlenül a Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5aeee91db0b1ce891d7e4090a074ddda1f15a576
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349534"
---
# <a name="right-azure-cosmos-db"></a>JOBB (Azure Cosmos DB)
 A megadott számú karaktert a karakterlánc jobb oldali részét adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   A karakterlánc-kifejezés kinyerni a karaktereket a következőből:.  
  
*num_expr*  
   Egy numerikus kifejezés, amely a karakterek számát határozza meg.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa a különböző hosszúságú értékek az "abc" megfelelő részét adja vissza.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
