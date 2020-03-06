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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302695"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Adja vissza a szög radiánban, amelynek szinusza a megadott numerikus kifejezés. Arkuszszinusz ezt is nevezik.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a-1 `ASIN` adja vissza.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
