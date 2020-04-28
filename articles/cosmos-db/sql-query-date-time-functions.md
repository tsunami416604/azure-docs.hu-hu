---
title: Dátum és idő függvények Azure Cosmos DB lekérdezési nyelven
description: A dátum-és időértékek az SQL rendszerfunkcióinak megismerése Azure Cosmos DB a DateTime és az timestamp művelet végrehajtásához.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 93b3e90deb5e3764cb5402938c5d0e14cadb7883
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873386"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Dátum és idő függvények (Azure Cosmos DB)

A dátum-és időfüggvények lehetővé teszik a DateTime és timestamp típusú műveletek végrehajtását Azure Cosmos DBban.

## <a name="functions"></a>Functions

A következő skaláris függvények lehetővé teszik az aktuális UTC dátum és idő lekérését két formában; egy numerikus időbélyeg, amelynek értéke a UNIX-kor ezredmásodpercben, vagy egy olyan karakterlánc, amely megfelel az ISO 8601 formátumnak:

|||
|-|-|
|[GetCurrentDateTime](sql-query-getcurrentdatetime.md)|[GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)||


## <a name="next-steps"></a>További lépések

- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)
- [Összesítések](sql-query-aggregates.md)
