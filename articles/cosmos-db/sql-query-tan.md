---
title: TAN Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function TAN szolgáltatással Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d7187ba116067445e835769fc33aa70677ef80b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78301981"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Megadja a megadott szög tangensét (radiánban) a megadott kifejezésben.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a PI ()/2 tangensét számítja ki.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
