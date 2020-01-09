---
title: FELSŐ határ Azure Cosmos DB lekérdezési nyelven
description: Megtudhatja, hogyan adja vissza a Azure Cosmos DB a PLAFONt tartalmazó SQL System függvényt a megadott numerikus kifejezésnél nagyobb vagy azzal egyenlő legkisebb egész szám értékkel.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2da7820a6c9f1f90585b4deb605bb99c7580b0e5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444806"
---
# <a name="ceiling-azure-cosmos-db"></a>FELSŐ határ (Azure Cosmos DB)
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
  
  Az alábbi példa a pozitív numerikus, negatív és nulla értékeket jeleníti meg az `CEILING` függvénnyel.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
