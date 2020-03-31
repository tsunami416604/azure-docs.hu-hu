---
title: ARRAY_SLICE az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg, hogyan adja vissza a tömbösSQL-kifejezés tömbszelet-SQL-rendszerfüggvénye egy tömbkifejezés egy részét
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a98cb17d22f41776ff788d12ced6aa988ad0b10e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303324"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
 Tömbkifejezés egy részét adja vissza.
  
## <a name="syntax"></a>Szintaxis
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*arr_expr*  
   Bármely tömbkifejezés.  
  
*num_expr*  
   Nulla alapú numerikus index, amelynél a tömb ötóra kezdődik. Negatív értékek használhatók a kezdő index megadására a tömb utolsó eleméhez képest, azaz -1 a tömb utolsó elemére hivatkozik.  

*num_expr* Nem kötelező numerikus kifejezés, amely az eredményül kapott tömb elemeinek maximális számát állítja be.    

## <a name="return-types"></a>Visszatérési típusok
  
  Tömbkifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa bemutatja, hogyan szerezhet `ARRAY_SLICE`be különböző szeleteket egy tömbből a használatával.  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Tömbfüggvények Az Azure Cosmos DB](sql-query-array-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
