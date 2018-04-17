---
title: Rendelje hozzá az Azure SQL Data Warehouse változók |} Microsoft Docs
description: Tippek a T-SQL változókat az Azure SQL Data Warehouse adattárházzal történő, megoldások hozzárendelése.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 012bc76950f212d69d26607c666e878b22015e70
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse változók hozzárendelése
Tippek a T-SQL változókat az Azure SQL Data Warehouse adattárházzal történő, megoldások hozzárendelése.

## <a name="setting-variables-with-declare"></a>A DECLARE változók beállítása
Az SQL Data Warehouse változók vannak beállítva, használja a `DECLARE` utasítást vagy a `SET` utasítást. Változók DECLARE inicializálása egyike a leginkább rugalmas módja egy változó értéke az SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

DECLARE segítségével egyszerre több változó értéke. Válassza ki vagy a frissítés nem használható a következőket teheti:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nem inicializálható, és használhat egy változót az azonos DECLARE utasítást. Mutatja be a pont, a következő példa egy **nem** óta engedélyezett @p1 van inicializálva és az azonos DECLARE utasításban való használathoz. A következő példa olyan hibaüzenetet ad.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>A beállítás értéke beállítva
Gyakori módja egy változó beállításához lesz.

A következő utasításokat az összes érvényes módon beállítva egy változó beállítása:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Egy változó egyszerre beállítva csak beállítani. Azonban az összetett operátorok nem megengedett.

## <a name="limitations"></a>Korlátozások
Válassza ki vagy frissítés változó-hozzárendelés nem használható.

## <a name="next-steps"></a>További lépések
További fejlesztési tippek, lásd: [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).

