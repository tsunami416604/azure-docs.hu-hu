---
title: RAND Azure Cosmos DB lekérdezési nyelven
description: Ismerje meg az SQL System Function RAND Azure Cosmos DB-ben való megismerését.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302219"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Egy véletlenszerűen generált numerikus értéket ad vissza [0, 1).
 
## <a name="syntax"></a>Szintaxis
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Visszatérési típusok

  A numerikus kifejezést ad vissza.

## <a name="remarks"></a>Megjegyzések

  `RAND` egy determinált függvény. A `RAND` ismétlődő hívásai nem adják vissza ugyanazt az eredményt.

## <a name="examples"></a>Példák
  
  A következő példa egy véletlenszerűen generált numerikus értéket ad vissza.
  
```sql
SELECT RAND() AS rand 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
