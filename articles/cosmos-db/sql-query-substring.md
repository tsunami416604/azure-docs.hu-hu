---
title: Alsztring Azure Cosmos DB lekérdezési nyelven
description: További információ az SQL System Function alsztringről Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bf14bda9bd1acc62820bf07f83ac074a8d1b691c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349188"
---
# <a name="substring-azure-cosmos-db"></a>Alsztring (Azure Cosmos DB)
 Már a megadott karakter számolt helyzetét megadja egy karakterlánc-kifejezés részét adja vissza, és továbbra is fennáll, a megadott időtartam, illetve a karakterlánc végén.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.
  
*num_expr1*  
   Egy numerikus kifejezés, amely a kezdő karaktert jelöli. A 0 érték a *str_expr*első karaktere.
  
*num_expr2*  
   Egy numerikus kifejezés, amely azt jelzi, hogy a visszaadott *str_expr* legfeljebb hány karakterből állhat. A 0 vagy kevesebb érték üres karakterláncot eredményez.

## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa részét adja vissza, az "abc" kezdődően: 1 és 1 karakter hosszúságú.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"substring": "b"}]  
```  

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
