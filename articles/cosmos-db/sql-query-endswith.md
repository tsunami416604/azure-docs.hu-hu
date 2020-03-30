---
title: ENDSWITH az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az Azure Cosmos DB ENDSWITH SQL rendszerfunkcióját egy logikai érték visszaadásához, amely jelzi, hogy az első karakterlánc-kifejezés a másodikkal ér-e véget
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c5a8b3c44c5ac46b837e4d851d22f85aeaf39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299448"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 Logikai értéket ad vissza, amely azt jelzi, hogy az első karakterlánc-kifejezés a másodikkal végződik-e.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   Egy karakterlánc-kifejezés.  
  
*str_expr2*  
   A karakterlánc-kifejezés a *str_expr1*végéhez hasonlítható.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Logikai kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa az "abc" "b" és "bc" végződést adja vissza.  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
