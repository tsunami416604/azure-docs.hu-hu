---
title: FELSŐ határ Azure Cosmos DB lekérdezési nyelven
description: Megtudhatja, hogyan adja vissza a Azure Cosmos DB a PLAFONt tartalmazó SQL System függvényt a megadott numerikus kifejezésnél nagyobb vagy azzal egyenlő legkisebb egész szám értékkel.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2ffc1ba208ca6b587354fcce683dc7db3a8ccb64
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081688"
---
# <a name="ceiling-azure-cosmos-db"></a>FELSŐ határ (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 A megadott numerikus kifejezésnél nagyobb, vagy azzal egyenlő legkisebb egész értéket adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa a függvényt tartalmazó pozitív numerikus, negatív és nulla értékeket mutatja `CEILING` .  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
