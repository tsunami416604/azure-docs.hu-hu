---
title: HOSSZ Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function HOSSZával Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303715"
---
# <a name="length-azure-cosmos-db"></a>Hossz (Azure Cosmos DB)
 A megadott karakterlánc-kifejezés karaktereinek számát adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   A kiértékelni kívánt karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa egy karakterlánc hosszát adja vissza.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
