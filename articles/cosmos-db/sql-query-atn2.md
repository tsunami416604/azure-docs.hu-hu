---
title: ATN2 Azure Cosmos DB lekérdezési nyelven
description: Ismerje meg, hogy a Azure Cosmos DB ATN2 SQL System függvénye hogyan adja vissza az y/x arkusz tangensének elsődleges értékét radiánban kifejezve.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 696e14e75998ead04c99fab2b84fc4c742d5f54a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302661"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 Az arkusz tangens / x, y radiánban kifejezett fő értékét adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa alapján számítja ki a ATN2 a megadott x és y összetevőket.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
