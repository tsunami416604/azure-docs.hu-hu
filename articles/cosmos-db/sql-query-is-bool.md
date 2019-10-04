---
title: IS_BOOL Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function IS_BOOL Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cb928558483a703a554d3eb6eb049af544f72eb1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349890"
---
# <a name="is_bool-azure-cosmos-db"></a>IS_BOOL (Azure Cosmos DB)
 Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy logikai érték visszaadása.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
IS_BOOL(<expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*kifejezés*  
   Bármely kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy logikai kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a JSON logikai, szám, karakterlánc, null, objektum, tömb és nem definiált típusok objektumait ellenőrzi a `IS_BOOL` függvény használatával.  
  
```sql
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  

## <a name="next-steps"></a>További lépések

- [Type Check functions Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
