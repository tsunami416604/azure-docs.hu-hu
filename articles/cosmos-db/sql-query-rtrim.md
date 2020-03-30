---
title: RTRIM az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az Sql rendszer funkció RTRIM az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b740d14315f6d9ba2f1788c56d6b1fcd8945c83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302083"
---
# <a name="rtrim-azure-cosmos-db"></a>RTRIM (Azure Cosmos DB)
 Karakterlánc-kifejezést ad vissza, miután eltávolítja a záró üres helyeket.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
RTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Érvényes karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa bemutatja, hogyan használható `RTRIM` egy lekérdezésen belül.  
  
```sql
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
