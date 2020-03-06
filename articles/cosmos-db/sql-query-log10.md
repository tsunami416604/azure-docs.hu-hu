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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
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
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="remarks"></a>Megjegyzések
  
  A LOG10 és a POWER függvény intenzitásfokozatok kapcsolódnak egymáshoz. Ha például 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa egy változó deklarálja, és a megadott változó (100) LOG10 értékét adja vissza.  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
