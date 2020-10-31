---
title: ACO-Azure Cosmos DB lekérdezési nyelv
description: Ismerje meg, hogy az ACO-arccosice (Azure Cosmos DB) SQL System függvénye hogyan adja vissza azt a szöget (radiánban), amelynek koszinusza a megadott numerikus kifejezés
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 99730b4c67adc841a33ea9d60440b00a19f90e48
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090953"
---
# <a name="acos-azure-cosmos-db"></a>ACO-i (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Azt a szöget adja vissza radiánban, amelynek koszinusza a megadott numerikus kifejezés; más néven szám arkusz koszinuszát.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a-1 értéket adja vissza `ACOS` .  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
