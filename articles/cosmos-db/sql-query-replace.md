---
title: REPLACE az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL rendszer funkció csere az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302202"
---
# <a name="replace-azure-cosmos-db"></a>CSERE (Azure Cosmos DB)
 Egy megadott karakterlánc-érték összes előfordulását lecseréli egy másik karakterlánc-értékre.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   A megkeresni a karakterlánc-kifejezés.  
  
*str_expr2*  
   A karakterlánc-kifejezés található.  
  
*str_expr3*  
   A *str_expr1* str_expr2 előfordulásait helyettesítő *str_expr1*karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa bemutatja, hogyan használható `REPLACE` egy lekérdezésben.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
