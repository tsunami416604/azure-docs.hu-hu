---
title: StringToBoolean az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL rendszer funkció StringToBoolean az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296541"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 Logikai kifejezésre fordított kifejezést ad vissza. Ha a kifejezés nem fordítható le, akkor nem definiált értéket ad vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Logikai kifejezésként elemezendő karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Logikai kifejezést ad eredményül, vagy nem definiált.  
  
## <a name="examples"></a>Példák
  
  A következő példa `StringToBoolean` bemutatja, hogyan viselkedik a különböző típusok között. 
 
 Az alábbiakban érvényes bemenettel rendelkező példákat mutatunk be.

A szóközszó csak az "igaz"/"false" előtt vagy után engedélyezett.

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Az alábbi példák érvénytelen bemenettel rendelkeznek.

 A logikai értékek a kis- és nagybetűket is érzékenyen tartják, és az összes kisbetűvel, azaz az "igaz" és a "hamis" karakterrel kell írni őket.

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Itt van az eredményhalmaz.  
  
```json
[{}]
``` 

Az átadott kifejezés logikai kifejezésként lesz elemezve; ezek a bemenetek nem értékeli konklúpia írja be a logikai értéket, és így nem definiált értéket adnak vissza.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
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
