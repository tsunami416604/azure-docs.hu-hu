---
title: Típusellenőrző függvények az Azure Cosmos DB lekérdezési nyelvén
description: Ismerje meg az SQL-rendszerfüggvények az Azure Cosmos DB típusellenőrzéséről.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349082"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Típusellenőrzési függvények (Azure Cosmos DB)

A típusellenőrzési függvények segítségével ellenőrizheti az SQL-lekérdezésen belüli kifejezés típusát. A típusellenőrzési függvények segítségével meghatározhatja, hogy milyen típusú tulajdonságok vannak menet közben az elemeken belül, ha változóak vagy ismeretlenek. 

## <a name="functions"></a>Functions

Az alábbiakban a támogatott beépített típusellenőrzési funkciókat táblázatban olvashatja:

A következő függvények támogatják a típusellenőrzést a bemeneti értékekkel szemben, és mindegyik logikai értéket ad vissza.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>További lépések

- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)
- [Összesítések](sql-query-aggregates.md)
