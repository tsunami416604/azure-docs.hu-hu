---
title: TAN Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function TAN szolgáltatással Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 70636dbc00307ced8ad0cf84f41fe025c759997e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349161"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 A megadott szög tangensét adja vissza radiánban, a megadott kifejezésben.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa PI () tangensét számítja ki / 2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
