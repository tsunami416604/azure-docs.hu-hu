---
title: PLAFON az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg, hogyan az AZURE Cosmos DB CEILING SQL rendszerfüggvénye a megadott numerikus kifejezésnél nagyobb vagy azzal egyenlő legkisebb egész értéket adja vissza.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 81f113aa51a7f739b506ec7e3eb5bf2cb9f49a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302644"
---
# <a name="ceiling-azure-cosmos-db"></a>MENNYEZET (Azure Cosmos DB)
 A megadott numerikus kifejezésnél nagyobb vagy azzal egyenlő legkisebb egész értéket adja eredményül.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa pozitív numerikus, negatív `CEILING` és nulla értékeket mutat be a függvénnyel.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció egy [tartományindex](index-policy.md#includeexclude-strategy)előnyeit élvezi .

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
