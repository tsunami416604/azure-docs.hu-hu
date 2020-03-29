---
title: StringToNull az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL rendszer függvényStringToNull az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296439"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
 Null-ra fordított kifejezést ad eredményül. Ha a kifejezés nem fordítható le, akkor nem definiált értéket ad vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Null kifejezésként elemezendő karakterlánc-kifejezés.
  
## <a name="return-types"></a>Visszatérési típusok
  
  Null kifejezést vagy nem definiált kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa `StringToNull` bemutatja, hogyan viselkedik a különböző típusok között. 

Az alábbiakban érvényes bemenettel rendelkező példákat mutatunk be.

 A szóközszó közterület csak a "null" előtt vagy után engedélyezett.

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Az alábbi példák érvénytelen bemenettel rendelkeznek.

A null a kis- és nagybetűket, és az összes kisbetűvel, azaz "null" karakterrel kell írni.

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{}]
```  

Az átadott kifejezés null kifejezésként lesz elemezve; ezek a bemenetek nem nullás típusúnak értékelik, és így nem definiált értéket adnak vissza.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{}]
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
