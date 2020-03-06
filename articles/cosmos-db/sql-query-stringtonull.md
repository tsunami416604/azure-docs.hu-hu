---
title: StringToNull Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function StringToNull Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78296439"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
 NULL értékre fordított kifejezést ad vissza. Ha a kifejezés nem fordítható le, a nem definiált értéket adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés, amelyet null kifejezésként kell elemezni.
  
## <a name="return-types"></a>Visszatérési típusok
  
  NULL kifejezést vagy nem definiált értéket ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan viselkedik a `StringToNull` különböző típusokban. 

Az alábbi példák érvényes bemenettel rendelkeznek.

 A szóköz csak a "NULL" előtt vagy után engedélyezett.

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

A következő példák érvénytelen bemenettel rendelkeznek.

A null a kis-és nagybetűk megkülönböztetése, és minden kisbetűvel, azaz "NULL" karakterrel kell írni.

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Íme az eredményhalmaz.  
  
```json
[{}]
```  

Az átadott kifejezést a rendszer null kifejezésként értelmezi. Ezek a bemenetek nem értékelik a null értéket, így a nem definiált értéket adja vissza.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{}]
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
