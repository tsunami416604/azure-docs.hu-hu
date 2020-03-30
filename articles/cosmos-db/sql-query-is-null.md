---
title: IS_NULL az Azure Cosmos DB lekérdezési nyelvében
description: Az Azure Cosmos DB IS_NULL SQL-rendszerfunkcióiról.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4dbf21c3052ddd5ebdd62925e65a854c47f59017
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303834"
---
# <a name="is_null-azure-cosmos-db"></a>IS_NULL (Azure Cosmos DB)
 Logikai értéket ad eredményül, amely azt jelzi, hogy a megadott kifejezés típusa null értékű-e.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
IS_NULL(<expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*Kifejezés*  
   Bármilyen kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Logikai kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa a JSON logikai, szám, karakterlánc, null, objektum, tömb `IS_NULL` és nem definiált típusok objektumait ellenőrzi a függvény használatával.  
  
```sql
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció egy [tartományindex](index-policy.md#includeexclude-strategy)előnyeit élvezi .

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB típusellenőrzési függvényei](sql-query-type-checking-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
