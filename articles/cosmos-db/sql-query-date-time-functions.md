---
title: Dátum és idő függvények Azure Cosmos DB lekérdezési nyelven
description: Az SQL rendszerfunkcióinak ismertetése a Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 763cac8736109952fdb32500dc669ace2ae7b585
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351228"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Dátum és idő függvények (Azure Cosmos DB)

A dátum-és időfüggvények lehetővé teszik a DateTime és timestamp típusú műveletek végrehajtását Azure Cosmos DBban.

## <a name="functions"></a>Funkciók

A következő skaláris függvények lehetővé teszik az aktuális UTC dátum és idő lekérését két formában; egy numerikus időbélyeg, amelynek értéke a UNIX-kor ezredmásodpercben, vagy egy olyan karakterlánc, amely megfelel az ISO 8601 formátumnak:

|||
|-|-|
|[GetCurrentDateTime](sql-query-getcurrentdatetime.md)|[GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)||


## <a name="next-steps"></a>További lépések

- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)
- [Aggregátumok](sql-query-aggregates.md)
