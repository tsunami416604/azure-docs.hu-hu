---
title: GetCurrentTimestamp Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function GetCurrentTimestamp Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c1e0a8d69edab0c01005268ee49c23625236f03a
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606917"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)

 Azon ezredmásodpercek számát adja vissza, amelyek elteltek 00:00:00 csütörtök, 1970. január 1. között.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Visszatérési típusok
  
Egy aláírt numerikus értéket ad vissza, amely a UNIX-kor óta eltelt ezredmásodpercek száma, azaz a 00:00:00 csütörtök és 1970 január 1. között eltelt ezredmásodpercek száma.

## <a name="remarks"></a>Megjegyzések

A GetCurrentTimestamp () egy determinált-függvény. A visszaadott eredmény UTC (egyezményes világidő).

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan kérheti le az aktuális időbélyeget a GetCurrentTimestamp () beépített függvény használatával.
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Íme egy példa az eredményhalmaz értékére.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Következő lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
