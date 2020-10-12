---
title: ASIN Azure Cosmos DB lekérdezési nyelven
description: Ismerje meg, hogy a Azure Cosmos DB arkusz (ASIN) SQL System függvénye hogyan adja vissza azt a szöget (radiánban), amelynek szinusza a megadott numerikus kifejezés
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "78302695"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Azt a szöget adja vissza radiánban, amelynek szinusza a megadott numerikus kifejezés. Ezt az arkusz tangens is hívja.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a-1 értéket adja vissza `ASIN` .  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
