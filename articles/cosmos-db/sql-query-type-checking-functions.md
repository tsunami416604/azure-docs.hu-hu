---
title: Típus-ellenőrzési függvények Azure Cosmos DB lekérdezési nyelven
description: Tudnivalók az SQL rendszerfunkcióinak ellenőrzéséről Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3ffe8fa9286c8594dc72d78582f8c7b0a3d05c78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563330"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Type Check functions (Azure Cosmos DB)

A típus-ellenőrzési függvények lehetővé teszik egy kifejezés típusának ellenőrzését egy SQL-lekérdezésen belül. A Type-Check függvények segítségével meghatározhatja, hogy milyen típusú tulajdonságok vannak a menet közben, ha változó vagy ismeretlen. 

## <a name="functions"></a>Functions

Az alábbi táblázat a támogatott beépített típus-ellenőrzési funkciókat tartalmazza:

A következő függvények támogatják a bemeneti értékek típusának ellenőrzését, és mindegyik logikai értéket ad vissza.  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>További lépések

- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)
- [Összesítések](sql-query-aggregates.md)
