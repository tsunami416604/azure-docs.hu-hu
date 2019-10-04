---
title: StringToNull Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function StringToNull Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c7ea3ca82cadb1351d8581f3d652ce060d67969b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349248"
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
  
  Az alábbi példa azt szemlélteti, hogyan viselkedik a `StringToNull` a különböző típusok között. 

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

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
