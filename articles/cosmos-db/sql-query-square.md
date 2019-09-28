---
title: NÉGYZET Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function SQUARE Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683db82f2574da9150fb64767047d6ada6d667da
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349416"
---
# <a name="square-azure-cosmos-db"></a>SQUARE (Azure Cosmos DB)
 A megadott számérték négyzetét adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
SQUARE(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa 1-3 szám négyzetének adja vissza.  
  
```sql
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{s1: 1, s2: 4, s3: 9}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
