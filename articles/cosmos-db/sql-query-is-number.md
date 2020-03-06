---
title: Azure Cosmos DB lekérdezési nyelv IS_NUMBER
description: Ismerkedjen meg a Azure Cosmos DB SQL System Function IS_NUMBERával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c019424241bc07d5a5d2cc19a64685c476f94548
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303817"
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
  
  A következő példa a JSON logikai, szám, karakterlánc, null, objektum, tömb és nem definiált típusok objektumait ellenőrzi a `IS_NUMBER` függvénnyel.  
  
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

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Következő lépések

- [Type Check functions Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
