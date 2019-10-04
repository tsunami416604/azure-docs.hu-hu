---
title: ASIN Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function ASIN Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5a64fc8d8f87d38f001bf2bc9dd581692c97fe64
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348494"
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
  
  A következő példa a `ASIN` értéket adja vissza.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
