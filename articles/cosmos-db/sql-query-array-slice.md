---
title: ARRAY_SLICE Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function ARRAY_SLICE Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1df4177bb8b56bc98977af0f5180e8df5affb257
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348543"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
 Egy tömböt megadó kifejezést részét adja vissza.
  
## <a name="syntax"></a>Szintaxis
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*arr_expr*  
   Bármely tömb kifejezés.  
  
*num_expr*  
   A tömb kezdőpontját nulláról induló numerikus indexszel. Adjon meg a kezdő indexet az utolsó elem a tömbben – azaz a 1 hivatkozások viszonyítva az utolsó elem a tömbben negatív értékeket is használható.  

*num_expr* Nem kötelező numerikus kifejezés, amely az eredményül kapott tömbben lévő elemek maximális számát állítja be.    

## <a name="return-types"></a>Visszatérési típusok
  
  Egy tömböt megadó kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan lehet egy tömb különböző szeleteit beolvasni `ARRAY_SLICE` használatával.  
  
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
  
 Íme az eredményhalmaz.  
  
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

## <a name="next-steps"></a>További lépések

- [Array függvények Azure Cosmos DB](sql-query-array-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
