---
title: StartsWith Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function STARTSWITH Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c1ef1a815469e7fb2dd10311b92a28b828772be7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079903"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Olyan logikai érték beolvasása, amely azt jelzi, hogy az első karakterlánc-kifejezés a másodikval kezdődik-e.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
STARTSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   Egy karakterlánc-kifejezés.
  
*str_expr2*  
   Egy olyan karakterlánc-kifejezés, amelyet a rendszer a *str_expr1* elejéhez hasonlít.

*bool_expr* Nem kötelező érték az eset figyelmen kívül hagyásával. Ha igaz értékre van állítva, a STARTSWITH kis-és nagybetűket nem megkülönböztető keresést hajt végre. Ha nincs megadva, ez az érték hamis.

## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
A következő példa ellenőrzi, hogy az "ABC" karakterlánc "b" és "A" karakterrel kezdődik-e.  
  
```sql
SELECT STARTSWITH("abc", "b", false) AS s1, STARTSWITH("abc", "A", false) AS s2, STARTSWITH("abc", "A", true) AS s3
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[
    {
        "s1": false,
        "s2": false,
        "s3": true
    }
]
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
