---
title: GetCurrentDateTime Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function GetCurrentDateTime Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ec0b8ccaceed4abe3dd2784463f507f3bc76d890
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88606966"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)

Az aktuális UTC (egyezményes világidő) dátumának és időpontjának beolvasása ISO 8601-karakterláncként.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>Visszatérési típusok
  
  Az aktuális UTC dátum és idő ISO 8601 sztring értékét adja vissza abban a formátumban, `YYYY-MM-DDThh:mm:ss.fffffffZ` ahol:
  
  |Formátum|Leírás|
  |-|-|
  |ÉÉÉÉ|négyjegyű év|
  |MM|kétjegyű hónap (01 = Január stb.)|
  |DD|hónap kétjegyű napja (01 – 31)|
  |T|az időelemek kezdetének jelölője|
  |óó|kétjegyű óra (00 – 23)|
  |hh|kétjegyű perc (00 – 59)|
  |mm|kétszámjegyű másodperc (00 – 59)|
  |.fffffff|hét számjegyű tört másodperc|
  |Z|UTC (egyezményes világidő) jelölő||
  
  Az ISO 8601 formátumával kapcsolatos további információkért lásd: [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Megjegyzések

A GetCurrentDateTime () egy determinált-függvény. A visszaadott eredmény UTC. A pontosság 7 számjegy, amelynek pontossága 100 nanoszekundumban.

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="examples"></a>Példák
  
Az alábbi példa azt mutatja be, hogyan kérhető le az aktuális UTC dátum idő a GetCurrentDateTime () beépített függvény használatával.
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Íme egy példa az eredményhalmaz értékére.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>További lépések

- [Dátum és idő függvények Azure Cosmos DB](sql-query-date-time-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
