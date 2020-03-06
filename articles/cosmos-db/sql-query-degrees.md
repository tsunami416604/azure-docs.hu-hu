---
title: FOK Azure Cosmos DB lekérdezési nyelven
description: Ismerje meg az Azure Cosmos DB fok SQL System függvényét, hogy a megfelelő szöget adja vissza fokban a radiánban megadott szögben.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d175ba53a71998fc8e7812a1b761f9cd264c38a9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299470"
---
# <a name="degrees-azure-cosmos-db"></a>FOK (Azure Cosmos DB)
 A megfelelő szöget adja vissza fokban a egy radiánban megadott szög.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példában a-PI/2 radián szöge fok számát adja vissza.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
