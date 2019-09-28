---
title: ABS Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function ABS Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 480c5ffcfaccfac5061e53612a2ee57235cfa626
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348759"
---
# <a name="abs-azure-cosmos-db"></a>ABS (Azure Cosmos DB)
 A megadott numerikus kifejezés (pozitív) abszolút értékét adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa a `ABS` függvény használatának eredményeit jeleníti meg három különböző számon.  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
