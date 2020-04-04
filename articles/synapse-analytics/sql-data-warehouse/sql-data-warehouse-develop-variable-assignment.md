---
title: Változók hozzárendelése
description: Ebben a cikkben található alapvető tippeket a T-SQL változók hozzárendelése az SQL-készletben.
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
ms.openlocfilehash: 2dcf706ea59657abc2718a69e59191604dc2849d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633402"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>Változók hozzárendelése a Synapse SQL-készletben

Ebben a cikkben található alapvető tippeket a T-SQL változók hozzárendelése az SQL-készletben.

## <a name="set-variables-with-declare"></a>Változók beállítása DECLARE-lel

Az SQL-készletben lévő `DECLARE` változók `SET` az utasítás vagy az utasítás használatával vannak beállítva. A változók DECLARE-lel való inicializálása az SQL-készletben változóérték beállításának egyik legrugalmasabb módja.

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

Nem inicializálható és nem használhat változót ugyanabban a DECLARE utasításban. A pont szemléltetéséhez a **not** következő @p1 példa nem engedélyezett, mivel az inicializálás és az azonos DECLARE utasításban is szerepel. Mint ilyen, a következő példa ad egy hiba:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Értékek beállítása SET-vel

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

További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)
