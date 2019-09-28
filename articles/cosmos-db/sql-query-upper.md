---
title: FELSŐ Azure Cosmos DB lekérdezési nyelv
description: Ismerkedjen meg az SQL System Function felső Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6b0f025948803a23c5b3c8bb6415c0e111b946b2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349039"
---
# <a name="upper-azure-cosmos-db"></a>FELSŐ (Azure Cosmos DB)
 Egy karakterlánc-kifejezés után kisbetűt adatok nagybetűssé alakításával adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan használható a `UPPER` a lekérdezésekben  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
