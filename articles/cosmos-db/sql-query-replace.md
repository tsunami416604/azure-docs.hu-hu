---
title: Lecserélés Azure Cosmos DB lekérdezési nyelvre
description: További információ az SQL System függvény LECSERÉLÉSéről Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "78302202"
---
# <a name="replace-azure-cosmos-db"></a>Replace (Azure Cosmos DB)
 Egy megadott karakterláncérték összes előfordulását lecseréli egy másik karakterlánc értékre.  
  
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
   A *str_expr1* *str_expr2* előfordulásait lecserélő karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan használható `REPLACE` egy lekérdezésben.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
