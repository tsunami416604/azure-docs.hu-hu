---
title: Azure Cosmos DB lekérdezési nyelv IS_OBJECT
description: Ismerkedjen meg a Azure Cosmos DB SQL System Function IS_OBJECTával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 43d8237d859249317d7b581e25ed9a2b34d39d00
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303800"
---
# <a name="is_object-azure-cosmos-db"></a>IS_OBJECT (Azure Cosmos DB)
 Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy JSON-objektumot ad vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
IS_OBJECT(<expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*kifejezés*  
   Bármely kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a JSON logikai, szám, karakterlánc, null, objektum, tömb és nem definiált típusok objektumait ellenőrzi a `IS_OBJECT` függvénnyel.  
  
```sql
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció kihasználja a [tartomány indexét](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Következő lépések

- [Type Check functions Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
