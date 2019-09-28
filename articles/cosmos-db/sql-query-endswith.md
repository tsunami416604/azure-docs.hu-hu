---
title: ENDSWITH Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function ENDSWITH Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c988d63e597c77bc09a1d21ad391909bb55901
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351052"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 Adja vissza egy logikai jelzi-e az első karakterlánc-kifejezés második végződik.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   Egy karakterlánc-kifejezés.  
  
*str_expr2*  
   Egy karakterlánc-kifejezés, amelyet a rendszer a *str_expr1*végéhez hasonlít.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa adja vissza, az "abc" a "b" és "bc" végződik.  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
