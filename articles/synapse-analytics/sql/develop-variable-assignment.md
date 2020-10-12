---
title: Változók társítása a szinapszis SQL-sel
description: Ebben a cikkben tippeket talál a T-SQL-változók a szinapszis SQL-sel való hozzárendeléséhez.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: abc0a9f6fa6baefc7cc4b29c84ff179f0851dc30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90028916"
---
# <a name="assign-variables-with-synapse-sql"></a>Változók társítása a szinapszis SQL-sel

Ebben a cikkben tippeket talál a T-SQL-változók a szinapszis SQL-sel való hozzárendeléséhez.

## <a name="set-variables-with-declare"></a>Változók beállítása a DECLARE értékkel

A szinapszis SQL-változók az `DECLARE` utasítás vagy az utasítás használatával vannak beállítva `SET` . A DEKLARÁLt változók inicializálása az egyik legrugalmasabb módszer egy változó értékének megadására a szinapszis SQL-ben.

```sql
DECLARE @v  int = 0
;
```

A DECLARE paranccsal egyszerre több változót is beállíthat. A SELECT vagy a UPDATE művelettel nem végezheti el a következőket:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nem inicializálható és nem használható változó ugyanabban a DEKLARÁLt utasításban. A következő példa nem engedélyezett, mert a * \@ P1* egyszerre inicializálva van, és ugyanabban a deklarált utasításban van használatban. A következő példa hibát jelez.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Értékek beállítása a SET értékkel

A SET egy általános módszer egyetlen változó beállítására.

A következő utasítások minden érvényes módszert határoznak meg a KÉSZLETtel rendelkező változók beállításához:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Egyszerre csak egy változót lehet beállítani. Az összetett operátorok azonban megengedettek.

## <a name="limitations"></a>Korlátozások

A változó hozzárendeléshez nem használható frissítés.

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippekért tekintse meg a [SZINAPSZIS SQL-fejlesztés áttekintése](develop-overview.md) című cikket.
