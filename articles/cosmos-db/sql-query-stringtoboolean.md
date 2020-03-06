---
title: StringToBoolean Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function StringToBoolean Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78296541"
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
  
  Az alábbi példa bemutatja, hogyan viselkedik a `StringToBoolean` különböző típusokban. 
 
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

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
