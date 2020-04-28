---
title: Azure Cosmos DB lekérdezés nyelvének bekapcsolása
description: Ismerkedjen meg az SQL System Function POWER szolgáltatással Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "71349638"
---
# <a name="power-azure-cosmos-db"></a>TELJESÍTMÉNY (Azure Cosmos DB)
 A megadott kifejezés értékét adja vissza a megadott hatványra.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr1*  
   Egy numerikus kifejezés.  
  
*numeric_expr2*  
   A *numeric_expr1*növelésének a célja.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt mutatja be, hogyan lehet egy számot a 3 (a szám adatkockája) hatványára emelni.  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
