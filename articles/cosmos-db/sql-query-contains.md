---
title: TARTALMAZZA az Azure Cosmos DB lekérdezési nyelv
description: Ismerje meg, hogyan tartalmazza az SQL rendszer függvényaz Azure Cosmos DB ad egy logikai jelzi, hogy az első karakterlánc-kifejezés tartalmazza a második
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302593"
---
# <a name="contains-azure-cosmos-db"></a>TARTALMAZZA (Azure Cosmos DB)
 Logikai értéket ad vissza, amely azt jelzi, hogy az első karakterlánc-kifejezés tartalmazza-e a másodikat.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   A megkeresni a karakterlánc-kifejezés.  
  
*str_expr2*  
   A karakterlánc-kifejezés, amelyet meg kell találni.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Logikai kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa azt ellenőrzi, hogy az "abc" "ab"-t, az "abc" pedig a "d"-t tartalmazza-e.  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
