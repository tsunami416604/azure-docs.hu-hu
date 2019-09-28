---
title: EXP Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function EXP-vel Azure Cosmos DB-ben.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 21c7ae63f46f2acd961245c59805220174c106f1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351044"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 Az exponenciális a megadott numerikus kifejezés értékét adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="remarks"></a>Megjegyzések
  
  Az állandó **e** (2.718281...), a természetes logaritmusokat alapját.  
  
  A kitevő, egy szám az állandó **e** hatványára szám. Ha például EXP(1.0) = e ^ 1.0-s = 2.71828182845905 és EXP(10) = e ^ 10 = 22026.4657948067.  
  
  Egy szám természetes alapú logaritmusának exponenciális értéke maga a szám: EXP (napló (n)) = n. Egy szám exponenciális értékének természetes logaritmusa maga a szám: NAPLÓ (EXP (n)) = n.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa egy változó deklarálja, és a megadott változó (10) exponenciális értékét adja vissza.  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 Az alábbi példa a natural logarithm legfeljebb 20-at és a természetes alapú logaritmusát az exponenciális 20 exponenciális értékét adja vissza. Mivel ezek a függvények inverz függvények, egy másik, a visszatérési kerekítési lebegőpontos értékek mindkét esetben az értéke 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
