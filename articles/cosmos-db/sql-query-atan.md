---
title: ATAN az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg, hogyan adja vissza az Arctangent (ATAN) SQL rendszer függvényét az Azure Cosmos DB-ben a radiánban megadott szöget, amelynek érintője a megadott numerikus kifejezés.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 899c94a939be7825dca82522eab235bde9252896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302678"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 A radiánban lévő szöget adja eredményül, amelynek érintője a megadott numerikus kifejezés. Ezt arctangensnek is nevezik.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa `ATAN` a megadott értéket adja vissza.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
