---
title: ASIN az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg, hogyan adja vissza az Arcsine (ASIN) SQL rendszer függvényét az Azure Cosmos DB-ben, amelynek szinusza a megadott numerikus kifejezés.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302695"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Radiánban lévő szöget számít ja, amelynek szinusza a megadott numerikus kifejezés. Ezt arcsinnak is nevezik.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa `ASIN` a -1 értékét adja vissza.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
