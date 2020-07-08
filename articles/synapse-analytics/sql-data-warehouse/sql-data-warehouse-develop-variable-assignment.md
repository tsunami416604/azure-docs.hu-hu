---
title: Változók kiosztása
description: Ebben a cikkben alapvető tippeket talál a T-SQL-változók SQL-készletben való hozzárendeléséhez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0cbadc10fb817c70bd259397c840aae68abc2d54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213329"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>Változók társítása a szinapszis SQL-készletben

Ebben a cikkben alapvető tippeket talál a T-SQL-változók SQL-készletben való hozzárendeléséhez.

## <a name="set-variables-with-declare"></a>Változók beállítása a DECLARE értékkel

Az SQL-készletben lévő változók az `DECLARE` utasítással vagy az utasítással állíthatók be `SET` . A DEKLARÁLt változók inicializálása az egyik legrugalmasabb módszer az SQL-készletben lévő változó értékének megadására.

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

Nem inicializálható és nem használható változó ugyanabban a DEKLARÁLt utasításban. A pont szemléltetése érdekében a következő példa **nem** engedélyezett, mivel @p1 mindkettő inicializálva van, és ugyanabban a deklarált utasításban van használatban. A következő példa hibát jelez:

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

## <a name="next-steps"></a>További lépések

További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).
