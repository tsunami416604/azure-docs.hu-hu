---
title: LOG10 Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function LOG10 Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 56f1e96e7d4ee1b5f38ee7392665e17819ae554b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349717"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 A megadott numerikus kifejezés 10-es alapú logaritmusát adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expression*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="remarks"></a>Megjegyzések
  
  A LOG10 és a POWER függvény intenzitásfokozatok kapcsolódnak egymáshoz. Ha például 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa egy változó deklarálja, és a megadott változó (100) LOG10 értékét adja vissza.  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
