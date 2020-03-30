---
title: FLOOR az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az Azure Cosmos DB FLOOR SQL rendszerfunkcióját a megadott numerikus kifejezésnél kisebb vagy azzal egyenlő legnagyobb egész szám visszaadásához
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 04dfa6a028cf7c44bf99c665b396d51d8a0f3cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303188"
---
# <a name="floor-azure-cosmos-db"></a>FLOOR (Azure Cosmos DB)
 A megadott numerikus kifejezésnél kisebb vagy azzal egyenlő legnagyobb egész szám értéket adja eredményül.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa pozitív numerikus, negatív `FLOOR` és nulla értékeket mutat be a függvénnyel.  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció egy [tartományindex](index-policy.md#includeexclude-strategy)előnyeit élvezi .

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
