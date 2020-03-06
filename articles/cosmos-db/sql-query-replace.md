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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302202"
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
   A *str_expr1* *str_expr2* előfordulásait lecserélő karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan használható a `REPLACE` egy lekérdezésben.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
