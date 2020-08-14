---
title: Dátum és idő függvények Azure Cosmos DB lekérdezési nyelven
description: A dátum-és időértékek az SQL rendszerfunkcióinak megismerése Azure Cosmos DB a DateTime és az timestamp művelet végrehajtásához.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 460abbc0b2a2f277aaeed57c5b938de530696776
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224951"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Dátum és idő függvények (Azure Cosmos DB)

A dátum-és időfüggvények lehetővé teszik a DateTime és timestamp típusú műveletek végrehajtását Azure Cosmos DBban.

## <a name="functions-to-obtain-the-date-and-time"></a>A dátum és idő beszerzéséhez szükséges függvények

A következő skaláris függvények lehetővé teszik az aktuális UTC-dátum és idő megszerzését két formában: egy olyan karakterláncot, amely megfelel az ISO 8601 formátumnak, vagy egy numerikus időbélyegnek, amelynek értéke a UNIX-kor (ezredmásodperc):

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>Függvények a DateTime értékekkel való munkavégzéshez

A következő függvények lehetővé teszik a dátum-és időértékek egyszerű kezelését:

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)

## <a name="next-steps"></a>További lépések

- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)
- [Összesítések](sql-query-aggregates.md)
