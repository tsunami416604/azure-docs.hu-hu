---
title: FOK Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function DEGREEs szolgáltatással Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d8c2109c2c11f137b1966741a95d1d0c02895016
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351202"
---
# <a name="degrees-azure-cosmos-db"></a>FOK (Azure Cosmos DB)
 A megfelelő szöget adja vissza fokban a egy radiánban megadott szög.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példában a-PI/2 radián szöge fok számát adja vissza.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"degrees": 90}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
