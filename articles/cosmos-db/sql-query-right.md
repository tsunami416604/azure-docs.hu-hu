---
title: JOBB Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System függvénysel közvetlenül a Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 247616d2ac4f2a5799a5896d679f6e6d5917d5a6
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302151"
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

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
