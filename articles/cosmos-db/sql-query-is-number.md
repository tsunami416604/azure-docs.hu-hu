---
title: IS_NUMBER Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function IS_NUMBER Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 624f5c91a9d2a0eb4744d310120050d0d5ccef4e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349849"
---
# <a name="is_number-azure-cosmos-db"></a>IS_NUMBER (Azure Cosmos DB)
 Egy logikai értéket, amely azt jelzi, ha a megadott kifejezés típusa egy számot ad vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
IS_NUMBER(<expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*kifejezés*  
   Bármely kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a JSON logikai, szám, karakterlánc, null, objektum, tömb és nem definiált típusok objektumait ellenőrzi a `IS_NUMBER` függvény használatával.  
  
```sql
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  

## <a name="next-steps"></a>További lépések

- [Type Check functions Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
