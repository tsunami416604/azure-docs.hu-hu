---
title: Alsztring Azure Cosmos DB lekérdezési nyelven
description: További információ az SQL System Function alsztringről Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6cb96270d1db970230e3abb528b58b7341d652f0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081467"
---
# <a name="substring-azure-cosmos-db"></a>Alsztring (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Egy karakterlánc-kifejezés egy részét adja vissza, amely a megadott karakteres nulla alapú pozíciótól kezdődik, és a megadott hosszra, illetve a karakterlánc végére mutat.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.
  
*num_expr1*  
   Egy numerikus kifejezés, amely a kezdő karaktert jelöli. A 0 érték a *str_expr* első karaktere.
  
*num_expr2*  
   Egy numerikus kifejezés, amely a visszaadott *str_expr* karaktereinek maximális számát jelöli. A 0 vagy kevesebb érték üres karakterláncot eredményez.

## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa az "ABC" alsztringet adja vissza, amely 1 és 1 karakter hosszúságú.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy) , ha a kezdő pozíció `0` .

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
