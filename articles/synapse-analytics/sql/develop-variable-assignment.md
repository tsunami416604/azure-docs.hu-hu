---
title: Változók hozzárendelése a Synapse SQL-rel
description: Ebben a cikkben tippeket talál a T-SQL változók synapse SQL-hez való hozzárendeléshez.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428666"
---
# <a name="assigning-variables-with-synapse-sql"></a>Változók hozzárendelése a Synapse SQL-rel

Ebben a cikkben tippeket talál a T-SQL változók synapse SQL-hez való hozzárendeléshez.

## <a name="setting-variables-with-declare"></a>Változók beállítása DECLARE-lel

A Synapse SQL változói `DECLARE` az utasítás `SET` vagy az utasítás használatával vannak beállítva. A változók DECLARE-lel való inicializálása az egyik legrugalmasabb módja a változóérték beállításának a Synapse SQL-ben.

```sql
DECLARE @v  int = 0
;
```

A DECLARE segítségével egyszerre több változót is beállíthat. A SELECT vagy a UPDATE nem használható a következőkre:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nem inicializálható és nem használhat változót ugyanabban a DECLARE utasításban. Ennek szemléltetésére a következő példa *@p1* nem engedélyezett, mivel inicializálása és ugyanabban a DECLARE utasításban van használva. A következő példa hibát ad.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Értékek beállítása SET-vel

A SET egy gyakori módszer egyetlen változó beállítására.

A következő utasítások mind érvényes módjai a változó készlettel való beállításának:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Egyszerre csak egy változót állíthat be a SET segítségével. Az összetett operátorok azonban megengedettek.

## <a name="limitations"></a>Korlátozások

Változóhozzárendeléshez nem használható az UPDATE.

## <a name="next-steps"></a>További lépések

További fejlesztési tippeket a [Synapse SQL fejlesztési áttekintése](develop-overview.md) című cikkben talál.