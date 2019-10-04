---
title: ARRAY_LENGTH Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function ARRAY_LENGTH Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b27b3f09212047e2e8937a4bf649fa3335e15cb2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348673"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
 A megadott tömb kifejezés elemek számát adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*arr_expr*  
   Tömb kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa egy tömb hosszának lekérését mutatja be `ARRAY_LENGTH` használatával.  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"len": 3}]  
```  
  

## <a name="next-steps"></a>További lépések

- [Array függvények Azure Cosmos DB](sql-query-array-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
