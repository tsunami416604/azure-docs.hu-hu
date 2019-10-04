---
title: Bejelentkezés Azure Cosmos DB lekérdezés nyelve
description: Ismerkedjen meg az SQL rendszerfunkciós bejelentkezés Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 16f2321eb48afacbc9636b5c0588b1ea3a01a284
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349741"
---
# <a name="log-azure-cosmos-db"></a>NAPLÓ (Azure Cosmos DB)
 A megadott numerikus kifejezés természetes alapú logaritmusát adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
*alap*  
   Olyan nem kötelező numerikus argumentum, amely beállítja a logaritmus alapjának a.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="remarks"></a>Megjegyzések
  
  Alapértelmezés szerint LOG() a természetes alapú logaritmusát adja vissza. A logaritmus alapja a választható alap paraméter használatával módosíthatja egy másik értéket.  
  
  A természetes alapú logaritmus alapja az alap-es alapú logaritmusa **e**, ahol **e** megegyezik egy irrational állandó körülbelül 2.718281828.  
  
  A szám exponenciális értékének természetes logaritmusa maga a szám: LOG( EXP( n ) ) = n. Egy szám természetes alapú logaritmusának exponenciális értéke maga a szám: EXP( LOG( n ) ) = n.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa egy változó deklarálja, és a megadott változó (10) alapú logaritmus értékét adja vissza.  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 A következő példa egy szám kitevője `LOG` értéket számítja ki.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
