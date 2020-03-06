---
title: LTRIM Azure Cosmos DB lekérdezési nyelven
description: A LTRIM SQL System függvényének megismerése Azure Cosmos DB egy karakterlánc-kifejezés visszaadása, miután eltávolítja a kezdő üreseket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 08c069de70684a8562e86963ddb2e84ee889e7cc
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302253"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM (Azure Cosmos DB)
 Egy karakterlánc-kifejezés adja vissza, miután eltávolítja a vezető üres.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
LTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan használható a `LTRIM` egy lekérdezésen belül.  
  
```sql
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
