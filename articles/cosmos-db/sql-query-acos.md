---
title: ACO-Azure Cosmos DB lekérdezési nyelv
description: Ismerje meg, hogy az ACO-arccosice (Azure Cosmos DB) SQL System függvénye hogyan adja vissza azt a szöget (radiánban), amelynek koszinusza a megadott numerikus kifejezés
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300961"
---
# <a name="acos-azure-cosmos-db"></a>ACO-i (Azure Cosmos DB)
 Adja vissza a szög radiánban, amelynek a koszinusza a megadott numerikus kifejezés; egy szám arkusz koszinusza néven is ismert.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a-1 `ACOS` adja vissza.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
