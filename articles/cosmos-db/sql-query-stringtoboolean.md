---
title: StringToBoolean Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function StringToBoolean Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8719857dca16585a045f8174dbac8df455f38f38
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349271"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 Egy logikai értékre fordított kifejezést ad vissza. Ha a kifejezés nem fordítható le, a nem definiált értéket adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Logikai kifejezésként értelmezhető karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezést ad vissza, vagy nincs meghatározva.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan viselkedik a `StringToBoolean` a különböző típusok között. 
 
 Az alábbi példák érvényes bemenettel rendelkeznek.

A szóköz csak az "igaz"/"false" előtt vagy után engedélyezett.

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

A következő példák érvénytelen bemenettel rendelkeznek.

 A logikai értékek megkülönböztetik a kis-és nagybetűket, és az összes kisbetűs karaktert, azaz "true" és "false" karaktereket kell írni.

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Íme az eredményhalmaz.  
  
```json
[{}]
``` 

Az átadott kifejezés logikai kifejezésként lesz értelmezve; Ezek a bemenetek nem értékelik a logikai típust, így a nem definiált értéket adja vissza.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Íme az eredményhalmaz.  
  
```json
[{}]
```  

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
