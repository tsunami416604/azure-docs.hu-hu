---
title: EXP az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az Exponent (EXP) SQL rendszer függvényt az Azure Cosmos DB-ben a megadott numerikus kifejezés exponenciális értékének visszaadásához
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76d614264124e1ce4138663b702ff6d899b3aa4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873318"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 A megadott numerikus kifejezés exponenciális értékét adja eredményül.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Ez egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="remarks"></a>Megjegyzések
  
  Az **e** állandó (2,718281...) a természetes logaritmus alapja.  
  
  A szám kitevője a szám erejére emelt **e** állandó. Például EXP(1.0) = e^1.0 = 2,71828182845905 és EXP(10) = e^10 = 22026.4657948067.  
  
  Egy szám természetes logaritmusának exponenciális száma maga a szám: EXP (LOG (n)) = n. És egy szám exponenciális természetes logaritmusa maga a szám: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Példák
  
  A következő példa deklarál egy változót, és a megadott változó (10) exponenciális értékét adja vissza.  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 A következő példa a 20 természetes logaritmus és a 20-as természetes logaritmus exponenciális értékét adja eredményül. Mivel ezek a függvények egymás inverz függvényei, a lebegőpontos matematikai matematika kerekítésével rendelkező visszatérési érték mindkét esetben 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
