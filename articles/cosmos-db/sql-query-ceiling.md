---
title: FELSŐ határ Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function plafon Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7056cfd1773e0d98fa134c9fdec3ad6a43af1bcf
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351117"
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
  
  Az alábbi példa a pozitív numerikus, negatív és nulla értékeket jeleníti meg a `CEILING` függvénnyel.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
