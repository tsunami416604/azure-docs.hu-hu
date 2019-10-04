---
title: StringToNumber Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function StringToNumber Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b9596738d9b02fa26f9c363287323b905654a1f
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349221"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 Egy számra fordított kifejezést ad vissza. Ha a kifejezés nem fordítható le, a nem definiált értéket adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés, amelyet JSON-szám kifejezésként kell elemezni. A JSON-számnak egész számnak vagy lebegőpontos pontnak kell lennie. A JSON formátumával kapcsolatos részletekért lásd: [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy szám kifejezést vagy nem definiált értéket ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan viselkedik a `StringToNumber` a különböző típusok között. 

A szóköz csak a szám előtt vagy után engedélyezett.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Íme az eredményhalmaz.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

A JSON-ban érvényes számnak kell lennie vagy egész számnak vagy lebegőpontos számnak kell lennie.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Íme az eredményhalmaz.  
  
```json
{{}}
```  

Az átadott kifejezést szám típusú kifejezésként értelmezi a rendszer. Ezek a bemenetek nem értékelik ki a számot, és így nem definiált értéket adnak vissza. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Íme az eredményhalmaz.  
  
```json
{{}}
```  

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
