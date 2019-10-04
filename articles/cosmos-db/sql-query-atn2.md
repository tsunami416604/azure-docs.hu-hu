---
title: ATN2 Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function ATN2 Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 43bf2f6e27d093b72560b87349150268e0f58a60
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350199"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 Az arkusz tangens / x, y radiánban kifejezett fő értékét adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa alapján számítja ki a ATN2 a megadott x és y összetevőket.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
