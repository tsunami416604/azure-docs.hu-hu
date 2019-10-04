---
title: IS_DEFINED Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function IS_DEFINED Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2c3193262a41b3c6772d4fe29c78a132bc51bbd8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349870"
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

## <a name="next-steps"></a>További lépések

- [Type Check functions Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
