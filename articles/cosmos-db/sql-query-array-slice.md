---
title: Azure Cosmos DB lekérdezési nyelv ARRAY_SLICE
description: Ismerje meg, hogyan adja vissza a tömbbeli szeletek SQL System függvényét Azure Cosmos DB egy Array kifejezés egy részét.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a98cb17d22f41776ff788d12ced6aa988ad0b10e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303324"
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
  
  Az alábbi példa bemutatja, hogyan lehet egy tömb különböző szeleteit `ARRAY_SLICE`használatával beolvasni.  
  
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

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Array függvények Azure Cosmos DB](sql-query-array-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
