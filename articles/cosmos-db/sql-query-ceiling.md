---
title: FELSŐ határ Azure Cosmos DB lekérdezési nyelven
description: Megtudhatja, hogyan adja vissza a Azure Cosmos DB a PLAFONt tartalmazó SQL System függvényt a megadott numerikus kifejezésnél nagyobb vagy azzal egyenlő legkisebb egész szám értékkel.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 81f113aa51a7f739b506ec7e3eb5bf2cb9f49a03
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302644"
---
# <a name="ceiling-azure-cosmos-db"></a>FELSŐ határ (Azure Cosmos DB)
 A legkisebb egész értéket ad vissza, nagyobb vagy egyenlő a megadott numerikus kifejezés.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa a pozitív numerikus, negatív és nulla értékeket jeleníti meg az `CEILING` függvénnyel.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
