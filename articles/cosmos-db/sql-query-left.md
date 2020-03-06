---
title: Azure Cosmos DB lekérdezési nyelv maradt
description: Ismerkedjen meg az SQL System függvénnyel Azure Cosmos DB a bal oldalon.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0eac35a91e4d5158335d6797d49a09f8f6f391e3
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303749"
---
# <a name="left-azure-cosmos-db"></a>BAL (Azure Cosmos DB)
 A megadott számú karakterből álló karakterlánc bal oldali részét adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   A karakterlánc-kifejezés kinyerni a karaktereket a következőből:.  
  
*num_expr*  
   Egy numerikus kifejezés, amely a karakterek számát határozza meg.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa a különböző hosszúságú értékek az "abc" bal oldali részét adja vissza.  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
