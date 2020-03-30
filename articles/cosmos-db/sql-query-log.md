---
title: LOG az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg a LOG SQL rendszer függvényaz Azure Cosmos DB a megadott numerikus kifejezés természetes logaritmusának visszaadásához
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9213ef03f383dec7109652246411fac154b4a7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302508"
---
# <a name="log-azure-cosmos-db"></a>LOG (Azure Cosmos DB)
 A megadott numerikus kifejezés természetes logaritmusát számítja ki.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
*base*  
   Nem kötelező numerikus argumentum, amely a logaritmus alapját állítja be.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="remarks"></a>Megjegyzések
  
  Alapértelmezés szerint a LOG() függvény a természetes logaritmust adja vissza. A logaritmus alapját módosíthatja egy másik értékre a választható alapparaméter használatával.  
  
  A természetes logaritmus az **e**alap logaritmusa, ahol **e** egy irracionális állandó, amely körülbelül 2,718281828.  
  
  Egy szám exponenciális természetes logaritmusa maga a szám: LOG( EXP( n ) = n. És egy szám természetes logaritmusának exponenciális száma maga a szám: EXP( LOG( n ) ) = n.  
  
## <a name="examples"></a>Példák
  
  A következő példa deklarál egy változót, és a megadott változó (10) logaritmusértékét adja vissza.  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 A következő példa `LOG` egy szám kitevőjének kitevője.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
