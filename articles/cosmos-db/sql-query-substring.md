---
title: Alsztring Azure Cosmos DB lekérdezési nyelven
description: További információ az SQL System Function alsztringről Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303698"
---
# <a name="substring-azure-cosmos-db"></a>Alsztring (Azure Cosmos DB)
 Egy karakterlánc-kifejezés egy részét adja vissza, amely a megadott karakteres nulla alapú pozíciótól kezdődik, és a megadott hosszra, illetve a karakterlánc végére mutat.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.
  
*num_expr1*  
   Egy numerikus kifejezés, amely a kezdő karaktert jelöli. A 0 érték a *str_expr*első karaktere.
  
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

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
