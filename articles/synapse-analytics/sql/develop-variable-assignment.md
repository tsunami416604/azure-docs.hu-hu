---
title: Változók társítása a szinapszis SQL-sel
description: Ebben a cikkben tippeket talál a T-SQL-változók a szinapszis SQL-sel való hozzárendeléséhez.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428666"
---
# <a name="assigning-variables-with-synapse-sql"></a>Változók kiosztása a szinapszis SQL-sel

Ebben a cikkben tippeket talál a T-SQL-változók a szinapszis SQL-sel való hozzárendeléséhez.

## <a name="setting-variables-with-declare"></a>Változók beállítása a DECLARE értékkel

A szinapszis SQL-változók az `DECLARE` utasítás vagy az `SET` utasítás használatával vannak beállítva. A DEKLARÁLt változók inicializálása az egyik legrugalmasabb módszer egy változó értékének megadására a szinapszis SQL-ben.

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

Nem inicializálható és nem használható változó ugyanabban a DEKLARÁLt utasításban. A következő példa nem engedélyezett, mivel *@p1* a egyszerre van inicializálva, és UGYANABBAN a deklarált utasításban van használatban. A következő példa hibát jelez.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Értékek beállítása a SET értékkel

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

## <a name="next-steps"></a>További lépések

További fejlesztési tippekért tekintse meg a [SZINAPSZIS SQL-fejlesztés áttekintése](develop-overview.md) című cikket.