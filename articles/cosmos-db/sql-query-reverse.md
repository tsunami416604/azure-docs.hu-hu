---
title: REVERSE az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL-rendszer reverse funkcióját az Azure Cosmos DB-ben.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a22e1c8a5f4350bd2f966ee48f96368c648a4a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302168"
---
# <a name="reverse-azure-cosmos-db"></a>REVERSE (Azure Cosmos DB)
 Egy karakterlánc-érték fordított sorrendjét adja eredményül.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa bemutatja, hogyan használható `REVERSE` egy lekérdezésben.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
