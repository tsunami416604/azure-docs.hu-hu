---
title: NÉGYZET Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function SQUARE Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a22c4daaf9df889f2256bc78f2175c966d4841f7
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303443"
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

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
