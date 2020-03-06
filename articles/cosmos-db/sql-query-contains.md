---
title: Azure Cosmos DB lekérdezési nyelvet tartalmaz
description: Ismerje meg, hogy az Azure Cosmos DB SQL System függvénye hogyan ad vissza egy logikai értéket, amely azt jelzi, hogy az első karakterlánc-kifejezés tartalmazza-e a másodpercet
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302593"
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

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
