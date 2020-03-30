---
title: IS_STRING az Azure Cosmos DB lekérdezési nyelvében
description: Az Azure Cosmos DB IS_STRING SQL-rendszerfunkcióiról.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a717f343b0f46522a3ce2bb56c32e3f15998d777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303766"
---
# <a name="is_string-azure-cosmos-db"></a>IS_STRING (Azure Cosmos DB)
 Logikai értéket ad eredményül, amely azt jelzi, hogy a megadott kifejezés típusa karakterlánc-e.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
IS_STRING(<expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*Kifejezés*  
   Bármilyen kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Logikai kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa a JSON logikai, szám, karakterlánc, null, objektum, tömb `IS_STRING` és nem definiált típusok objektumait ellenőrzi a függvény használatával.  
  
```sql
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció egy [tartományindex](index-policy.md#includeexclude-strategy)előnyeit élvezi .

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB típusellenőrzési függvényei](sql-query-type-checking-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
