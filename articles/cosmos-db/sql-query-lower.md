---
title: Azure Cosmos DB lekérdezési nyelv alacsonyabb
description: Ismerkedjen meg az SQL System függvénnyel Azure Cosmos DB ALACSONYABBan.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3a9c122ef65772458b832d3b1651e7e63e42985e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349703"
---
# <a name="lower-azure-cosmos-db"></a>ALSÓ (Azure Cosmos DB)
 Egy karakterlánc-kifejezés nagybetűt adatok átalakítása kisbetűvé után adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan használható a `LOWER` a lekérdezésekben.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
