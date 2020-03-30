---
title: Változók hozzárendelése
description: Tippek a T-SQL változók hozzárendeléséhez az Azure SQL Data Warehouse-ban megoldások fejlesztéséhez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0adcd9bdf92b7ec649b7d91ca0e655fc006b3549
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351667"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Változók hozzárendelése az Azure SQL Data Warehouse-ban

Tippek a T-SQL változók hozzárendeléséhez az Azure SQL Data Warehouse-ban megoldások fejlesztéséhez.

## <a name="setting-variables-with-declare"></a>Változók beállítása DECLARE-lel

Az SQL Data Warehouse változói `DECLARE` az `SET` utasítás vagy a kimutatás használatával vannak beállítva. A változók DECLARE-lel való inicializálása az SQL Data Warehouse egyik legrugalmasabb módja a változóérték beállítására.

```sql
DECLARE @v  int = 0
;
```

A DECLARE segítségével egyszerre több változót is beállíthat. A SELECT vagy az UPDATE nem használható a következő kreált módon:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nem inicializálható és nem használhat változót ugyanabban a DECLARE utasításban. A pont szemléltetéséhez a **not** következő @p1 példa nem engedélyezett, mivel az inicializálás és az azonos DECLARE utasításban is szerepel. A következő példa hibát ad.

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

Az UPDATE nem használható változóhozzárendeléshez.

## <a name="next-steps"></a>További lépések

További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)
