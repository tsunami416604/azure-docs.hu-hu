---
title: Lecserélés Azure Cosmos DB lekérdezési nyelvre
description: További információ az SQL System függvény LECSERÉLÉSéről Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2cb7d82efd010fd7c3395a4f6a9217370d9e5779
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349580"
---
# <a name="replace-azure-cosmos-db"></a>Replace (Azure Cosmos DB)
 A megadott karakterlánc értéket az összes előfordulását lecseréli egy másik karakterláncérték.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   A keresendő karakterlánc-kifejezés.  
  
*str_expr2*  
   A megtalált karakterlánc-kifejezés.  
  
*str_expr3*  
   A karakterlánc-kifejezés a *str_expr2* *str_expr1*-beli előfordulásait váltja fel.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan használható a `REPLACE` a lekérdezésekben.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
