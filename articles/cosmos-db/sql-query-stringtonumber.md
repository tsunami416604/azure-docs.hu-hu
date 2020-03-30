---
title: StringToNumber az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL rendszer funkció StringToNumber az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296422"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 Számra fordított kifejezést ad vissza. Ha a kifejezés nem fordítható le, akkor nem definiált értéket ad vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   JSON-szám kifejezésként elemezendő karakterlánc-kifejezés. A JSON-ban lévő számoknak egész számnak vagy lebegőpontosnak kell lenniük. A JSON formátumról a [json.org](https://json.org/)  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Szám kifejezést ad eredményül, vagy nem definiált.  
  
## <a name="examples"></a>Példák
  
  A következő példa `StringToNumber` bemutatja, hogyan viselkedik a különböző típusok között. 

A szóközszó közterület csak a Szám előtt vagy után engedélyezett.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Itt van az eredményhalmaz.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

A JSON-ban az érvényes számnak egész számnak vagy lebegőpontos számnak kell lennie.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Itt van az eredményhalmaz.  
  
```json
{{}}
```  

Az átadott kifejezés számkifejezésként lesz elemezve; ezek a bemenetek nem értékelik a típus szám, és így vissza meghatározatlan. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Itt van az eredményhalmaz.  
  
```json
{{}}
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
