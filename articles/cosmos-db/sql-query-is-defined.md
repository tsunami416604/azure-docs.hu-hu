---
title: Azure Cosmos DB lekérdezési nyelv IS_DEFINED
description: Ismerkedjen meg a Azure Cosmos DB SQL System Function IS_DEFINEDával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5fcaf52d8e9e6b942a95f0b0c43f3f654c5d5d2
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303851"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 Jelzi, ha a tulajdonság hozzá lett rendelve egy érték logikai érték beolvasása.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*kifejezés*  
   Bármely kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa ellenőrzi, hogy a megadott JSON-dokumentum-tulajdonság. Az első igaz értéket ad vissza, mert "a", de a második hamis értéket ad vissza, mert hiányzik a "b".  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Következő lépések

- [Type Check functions Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
