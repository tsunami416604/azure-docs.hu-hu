---
title: LOG10 Azure Cosmos DB lekérdezési nyelven
description: További információ a Azure Cosmos DB LOG10 SQL System függvényéről a megadott numerikus kifejezés 10-es alapú logaritmusának visszaadásához
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302491"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 A megadott numerikus kifejezés 10-es alapú logaritmusát adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expression*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="remarks"></a>Megjegyzések
  
  A LOG10 és a POWER függvények fordítottan kapcsolódnak egymáshoz. Például: 10 ^ LOG10 (n) = n.  
  
## <a name="examples"></a>Példák
  
  A következő példa deklarál egy változót, és visszaadja a megadott változó LOG10 értékét (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
