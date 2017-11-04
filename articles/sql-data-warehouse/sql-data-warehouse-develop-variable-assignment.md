---
title: "Rendelje hozzá az SQL Data Warehouse változók |} Microsoft Docs"
description: "Tippek a Transact-SQL változókat az Azure SQL Data Warehouse adattárházzal történő, megoldások hozzárendelése."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 81ddc7cf-a6ba-4585-91a3-b6ea50f49227
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 045d5148cd3f12dac63c961ccf7c953d355ed725
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="assign-variables-in-sql-data-warehouse"></a>Rendelje hozzá az SQL Data Warehouse változók
Az SQL Data Warehouse változók vannak beállítva, használja a `DECLARE` utasítást vagy a `SET` utasítást.

Az alábbi módon tökéletesen érvényes egy változó értékének beállítására:

## <a name="setting-variables-with-declare"></a>A DECLARE változók beállítása
Változók DECLARE inicializálása egyike a leginkább rugalmas módja egy változó értéke az SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

DECLARE segítségével egyszerre több változó értéke. Nem használhat `SELECT` vagy `UPDATE` ehhez:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nem következő és használhat egy változót az azonos DECLARE utasítást. A pont, az alábbi példa mutatja be **nem** engedélyezett @p1 van inicializálva és az azonos DECLARE utasításban való használathoz. Ez hibát eredményez.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>A beállítás értéke beállítva
Egy változó beállításához nagyon gyakori módja lesz.

Az alábbi példák összes beállítását egy változó beállítva érvényes módon:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Egy változó egyszerre beállítva csak beállítani. Azonban, a fent látható összetett operátorok nem megengedett.

## <a name="limitations"></a>Korlátozások
Válassza ki vagy frissítés változó-hozzárendelés nem használható.

## <a name="next-steps"></a>Következő lépések
További fejlesztési tippek, lásd: [fejlesztői áttekintés][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
