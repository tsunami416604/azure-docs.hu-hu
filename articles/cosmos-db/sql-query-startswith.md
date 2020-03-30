---
title: STARTSWITH az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL rendszer függvény STARTSWITH az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9ed49c067946186f8b79f67bad0a460113eacb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295708"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
 Logikai értéket ad vissza, amely azt jelzi, hogy az első karakterlánc-kifejezés a másodikkal kezdődik-e.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   Egy karakterlánc-kifejezés.
  
*str_expr2*  
   A karakterlánc-kifejezés a *str_expr1*kezdetéhez hasonlítható.

## <a name="return-types"></a>Visszatérési típusok
  
  Logikai kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa ellenőrzi, hogy az "abc" karakterlánc "b" és "a" karakterrel kezdődik-e.  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció egy [tartományindex](index-policy.md#includeexclude-strategy)előnyeit élvezi .

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
