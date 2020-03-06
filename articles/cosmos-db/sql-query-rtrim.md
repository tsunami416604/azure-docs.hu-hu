---
title: RTRIM Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function RTRIM Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b740d14315f6d9ba2f1788c56d6b1fcd8945c83e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302083"
---
# <a name="rtrim-azure-cosmos-db"></a>RTRIM (Azure Cosmos DB)
 Egy karakterlánc-kifejezés adja vissza, miután eltávolítja a záró szóközöket.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
RTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Van bármilyen érvényes karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan használható a `RTRIM` egy lekérdezésen belül.  
  
```sql
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
