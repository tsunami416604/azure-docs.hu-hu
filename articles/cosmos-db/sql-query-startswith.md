---
title: STARTSWITH Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function STARTSWITH Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9ed49c067946186f8b79f67bad0a460113eacb73
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78295708"
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
   Egy olyan karakterlánc-kifejezés, amelyet a rendszer a *str_expr1*elejéhez hasonlít.

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

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
