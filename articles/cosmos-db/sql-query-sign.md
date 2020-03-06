---
title: Bejelentkezés Azure Cosmos DB lekérdezés nyelve
description: Ismerkedjen meg az SQL System függvény bejelentkezési Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 84335acbee1e4166e5346f5c5cac6ef8dae11e99
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302066"
---
# <a name="sign-azure-cosmos-db"></a>ALÁÍRÁS (Azure Cosmos DB)
 A pozitív (+ 1), a nulla (0) vagy a megadott numerikus kifejezés mínuszjel (-1) adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
SIGN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a-2 és 2 közötti számok `SIGN` értékét adja vissza.  
  
```sql
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
