---
title: INDEX_OF az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL-rendszer funkció INDEX_OF az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 50e489fdf57398d486b07944782ecbb3fd1d6a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71350982"
---
# <a name="index_of-azure-cosmos-db"></a>INDEX_OF (Azure Cosmos DB)
 Az első megadott karakterlánc-kifejezésen belül a második karakterlánc-kifejezés első előfordulásának kezdő helyét adja eredményül, vagy -1 értéket, ha a karakterlánc nem található.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
INDEX_OF(<str_expr1>, <str_expr2> [, <numeric_expr>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr1*  
   A megkeresni a karakterlánc-kifejezés.  
  
*str_expr2*  
   A megkeresni keresett karakterlánc-kifejezés.  

*numeric_expr* Nem kötelező numerikus kifejezés, amely beállítja a keresés kezdetét. Az első pozíció *str_expr1* 0. 
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa az "abc" különböző részkarakterláncainak indexét adja vissza.  
  
```sql
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"i1": 0, "i2": 1, "i3": -1}]  
```  

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
