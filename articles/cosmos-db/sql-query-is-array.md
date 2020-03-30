---
title: IS_ARRAY az Azure Cosmos DB lekérdezési nyelvében
description: Az Azure Cosmos DB IS_ARRAY SQL-rendszerfunkcióiról.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f5867850db6eb3d6552bc129cca3708ef7747072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303885"
---
# <a name="is_array-azure-cosmos-db"></a>IS_ARRAY (Azure Cosmos DB)
 Logikai értéket ad eredményül, amely azt jelzi, hogy a megadott kifejezés típusa tömb-e.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
IS_ARRAY(<expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*Kifejezés*  
   Bármilyen kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Logikai kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa a JSON logikai, szám, karakterlánc, null, objektum, tömb `IS_ARRAY` és nem definiált típusok objektumait ellenőrzi a függvény használatával.  
  
```sql
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció egy [tartományindex](index-policy.md#includeexclude-strategy)előnyeit élvezi .

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB típusellenőrzési függvényei](sql-query-type-checking-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
