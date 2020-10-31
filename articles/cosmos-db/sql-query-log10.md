---
title: LOG10 Azure Cosmos DB lekérdezési nyelven
description: További információ a Azure Cosmos DB LOG10 SQL System függvényéről a megadott numerikus kifejezés 10-es alapú logaritmusának visszaadásához
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 003479e4b9afaa3895bf18b7d2fc33979197d635
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075296"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
  A LOG10 és a POWER függvények fordítottan kapcsolódnak egymáshoz. Például: 10 ^ LOG10 (n) = n. Ez a rendszerfüggvény nem fogja használni az indexet.
  
## <a name="examples"></a>Példák
  
  A következő példa deklarál egy változót, és visszaadja a megadott változó LOG10 értékét (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
