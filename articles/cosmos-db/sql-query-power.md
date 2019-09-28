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
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349638"
---
# <a name="power-azure-cosmos-db"></a>TELJESÍTMÉNY (Azure Cosmos DB)
 A megadott hatványra a megadott kifejezés értékét adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr1*  
   A numerikus kifejezés.  
  
*numeric_expr2*  
   A *numeric_expr1*növelésének ereje.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa bemutatja egy számot (a szám adatkockájának) 3 hatékonyságát előléptetése.  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
