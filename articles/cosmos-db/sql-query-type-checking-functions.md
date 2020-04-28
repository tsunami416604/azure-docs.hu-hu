---
title: Típus-ellenőrzési függvények Azure Cosmos DB lekérdezési nyelven
description: Tudnivalók az SQL rendszerfunkcióinak ellenőrzéséről Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "71349082"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Type Check functions (Azure Cosmos DB)

A típus-ellenőrzési függvények lehetővé teszik egy kifejezés típusának ellenőrzését egy SQL-lekérdezésen belül. A Type-Check függvények segítségével meghatározhatja, hogy milyen típusú tulajdonságok vannak a menet közben, ha változó vagy ismeretlen. 

## <a name="functions"></a>Functions

Az alábbi táblázat a támogatott beépített típus-ellenőrzési funkciókat tartalmazza:

A következő függvények támogatják a bemeneti értékek típusának ellenőrzését, és mindegyik logikai értéket ad vissza.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>További lépések

- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)
- [Összesítések](sql-query-aggregates.md)
