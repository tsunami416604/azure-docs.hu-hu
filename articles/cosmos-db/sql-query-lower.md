---
title: Azure Cosmos DB lekérdezési nyelv alacsonyabb
description: Ismerkedjen meg az Azure Cosmos DB alsó SQL System függvényével, amely egy karakterlánc-kifejezést ad vissza a nagybetűs karakterek kisbetűsre konvertálása után.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f7cd864d90b8ab17a180a00a7a6d4bb683da9be
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873301"
---
# <a name="lower-azure-cosmos-db"></a>ALSÓ (Azure Cosmos DB)
 Egy karakterlánc-kifejezést ad vissza, miután a nagybetűs karaktereket a kisbetűs értékre konvertálta.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan használható a `LOWER` egy lekérdezésben.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
