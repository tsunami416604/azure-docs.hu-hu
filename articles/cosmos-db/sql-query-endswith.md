---
title: ENDSWITH Azure Cosmos DB lekérdezési nyelven
description: A Azure Cosmos DB ENDSWITH SQL System függvényének megismerése egy olyan logikai érték visszaadásához, amely azt jelzi, hogy az első karakterlánc kifejezése a másodikval végződik-e
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c5a8b3c44c5ac46b837e4d851d22f85aeaf39c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299448"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 Adja vissza egy logikai jelzi-e az első karakterlánc-kifejezés második végződik.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   Egy karakterlánc-kifejezés.  
  
*str_expr2*  
   A *str_expr1*végéhez összehasonlítandó karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa adja vissza, az "abc" a "b" és "bc" végződik.  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
