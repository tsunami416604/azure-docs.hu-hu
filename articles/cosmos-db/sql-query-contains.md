---
title: Azure Cosmos DB lekérdezési nyelvet tartalmaz
description: Az SQL System függvény ismertetése a Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5d5018d0173c316a0a31bd2f70e15e5ff972d153
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351089"
---
# <a name="contains-azure-cosmos-db"></a>TARTALMAZZA (Azure Cosmos DB)
 Visszaadja egy logikai arról a második-e az első karakterlánc-kifejezést tartalmaz.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   A keresendő karakterlánc-kifejezés.  
  
*str_expr2*  
   A keresendő karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa ellenőrzi, hogy az "ABC" tartalmazza-e az "AB" kifejezést, és ha az "ABC" a "d" kifejezést tartalmazza.  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
