---
title: Bejelentkezés Azure Cosmos DB lekérdezés nyelve
description: További információ a Azure Cosmos DB LOG SQL System függvényéről a megadott numerikus kifejezés természetes alapú logaritmusának visszaadásához
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9213ef03f383dec7109652246411fac154b4a7f9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302508"
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
  
  A természetes logaritmus az **e**-Base logaritmusa, ahol az **e** egy, körülbelül 2,718281828-as irracionális konstans.  
  
  Az exponenciális egy szám természetes algoritmusát az a szám maga: LOG (EXP (n)) = n. Az exponenciális egy szám természetes alapú logaritmus alapja az a szám, és maga: EXP (napló (n)) = n.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa egy változó deklarálja, és a megadott változó (10) alapú logaritmus értékét adja vissza.  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 Az alábbi példa kiszámítja egy szám kitevőjét `LOG`.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
