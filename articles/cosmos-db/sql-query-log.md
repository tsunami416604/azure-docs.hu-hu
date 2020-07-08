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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
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
   Egy numerikus kifejezés.  
  
*base*  
   Nem kötelező numerikus argumentum, amely a logaritmus alapját állítja be.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="remarks"></a>Megjegyzések
  
  Alapértelmezés szerint a LOG () függvény a természetes alapú logaritmust adja vissza. A logaritmus alapjait másik értékre módosíthatja a választható alap paraméter használatával.  
  
  A természetes logaritmus az **e**-Base logaritmusa, ahol az **e** egy, körülbelül 2,718281828-as irracionális konstans.  
  
  Egy szám exponenciális értékének természetes alapú logaritmusa maga a szám: LOG (EXP (n)) = n. Egy szám természetes alapú logaritmusának exponenciális értéke maga a szám: EXP (LOG (n)) = n.  
  
## <a name="examples"></a>Példák
  
  A következő példa deklarál egy változót, és visszaadja a megadott változó (10) logaritmus értékét.  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 A következő példa `LOG` egy szám kitevőjét számítja ki.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
