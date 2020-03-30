---
title: GetCurrentDateTime az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL rendszer funkció GetCurrentDateTime az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d50b08ab85c7e299c465c3eb6f34e867d6634006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303902"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 Az aktuális UTC (egyezményes világidő) dátumot és időt adja eredményül ISO 8601 karakterláncként.
  
## <a name="syntax"></a>Szintaxis
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Visszatérési típusok
  
  Az aktuális UTC-dátum és idő ISO 8601 karakterlánc-értéket adja eredményül abban a formátumban, akövetkező: `YYYY-MM-DDThh:mm:ss.fffffffZ`
  
  |||
  |-|-|
  |Éééé|négyjegyű év|
  |MM|kétjegyű hónap (01 = január stb.)|
  |Dd|a hónap kétjegyű napja (01-től 31-ig)|
  |T|jelző az időelemek kezdetére|
  |óó|kétjegyű óra (00-tól 23-ig)|
  |hh|kétjegyű perc (00-tól 59-ig)|
  |mm|kétjegyű másodperc (00 és 59 között)|
  |.fffffff|hétjegyű tört másodpercek|
  |Z|UTC (koordinált világidő) jelölés||
  
  Az ISO 8601 formátumról további információt [a ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Megjegyzések

  A GetCurrentDateTime() egy nem determinisztikus függvény. 
  
  A visszaadott eredmény UTC.

  A pontosság 7 számjegy, 100 nanoszekundumos pontossággal.

## <a name="examples"></a>Példák
  
  A következő példa bemutatja, hogyan lehet leadni az aktuális UTC dátumidő a GetCurrentDateTime() beépített függvény használatával.
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Íme egy példa eredményhalmaz.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB dátum- és időfüggvényei](sql-query-date-time-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
