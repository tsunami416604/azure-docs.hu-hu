---
title: Változók kiosztása Azure SQL Data Warehouseban | Microsoft Docs
description: Tippek a T-SQL-változók Azure SQL Data Warehouse való hozzárendeléséhez a megoldások fejlesztéséhez.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 6c943478f3904aac17a572f012f2b2b69ffa2223
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479562"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Változók hozzárendelésének Azure SQL Data Warehouse

Tippek a T-SQL-változók Azure SQL Data Warehouse való hozzárendeléséhez a megoldások fejlesztéséhez.

## <a name="setting-variables-with-declare"></a>Változók beállítása a DECLARE értékkel

A SQL Data Warehouse változók az `DECLARE` utasítással vagy az `SET` utasítással vannak beállítva. A változók és a bevallások inicializálása az egyik legrugalmasabb módszer a SQL Data Warehouse változó értékének megadására.

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

Nem inicializálható és nem használható változó ugyanabban a DEKLARÁLt utasításban. A pont szemléltetése érdekében a következő példa **nem** engedélyezett, mivel @p1 mindkettő inicializálva van, és ugyanabban a deklarált utasításban van használatban. A következő példa hibát jelez.

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

További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).
