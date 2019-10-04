---
title: PADLÓ Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function EMELETEN Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 94bcb6794dce9ccc22001e6d0e0e3e71d133ac42
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351213"
---
# <a name="floor-azure-cosmos-db"></a>EMELET (Azure Cosmos DB)
 Visszaadja a legnagyobb egész szám kisebb vagy egyenlő a megadott numerikus kifejezés.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa a pozitív numerikus, negatív és nulla értékeket jeleníti meg a `FLOOR` függvénnyel.  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
