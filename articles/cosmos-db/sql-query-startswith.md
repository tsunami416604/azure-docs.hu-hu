---
title: STARTSWITH Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function STARTSWITH Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a7822425f17d6e121dfcb20d8766f0b3bc7032a2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349318"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
 Adja vissza egy logikai jelzi-e az első karakterlánc-kifejezés második kezdődik.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   Egy karakterlánc-kifejezés.
  
*str_expr2*  
   A *str_expr1*elejéhez hasonlító karakterlánc-kifejezés.

## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa ellenőrzi, hogy ha a karakterlánc "abc" kezdődik "b" és "a".  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
