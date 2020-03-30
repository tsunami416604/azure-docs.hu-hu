---
title: SUBSTRING az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL-rendszer funkció SUBSTRING az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303698"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING (Azure Cosmos DB)
 A megadott karakternulla alapú pozícióval kezdődő karakterlánc-kifejezés egy részét adja eredményül, és a megadott hosszig vagy a karakterlánc végéig folytatódik.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.
  
*num_expr1*  
   A kezdő karaktert jelölő numerikus kifejezés. A 0 érték a *str_expr*első karaktere.
  
*num_expr2*  
   Olyan numerikus kifejezés, amely a visszaadandó *str_expr* maximális karakterszámát jelöli. A 0 vagy kevesebb érték üres karakterláncot eredményez.

## <a name="return-types"></a>Visszatérési típusok
  
  Karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa az "abc" 1 karaktertől kezdődő és 1 karakter hosszúságú részkarakterláncát adja vissza.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció a [tartományindex](index-policy.md#includeexclude-strategy) előnyeit `0`élvezi, ha a kiindulási pozíció .

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
