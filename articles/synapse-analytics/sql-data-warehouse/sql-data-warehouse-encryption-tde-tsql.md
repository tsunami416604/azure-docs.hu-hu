---
title: Transzparens adattitkosítás (T-SQL)
description: Transzparens adattitkosítás (TDE) az Azure szinapszis Analyticsben (T-SQL)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 81a28773f8f13cfb8dac75f1c4e11fd773c2c8f3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212197"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Ismerkedés a transzparens adattitkosításrel (TDE)

> [!div class="op_single_selector"]
>
> * [Biztonsági áttekintés](sql-data-warehouse-overview-manage-security.md)
> * [Hitelesítés](sql-data-warehouse-authentication.md)
> * [Titkosítás (portál)](sql-data-warehouse-encryption-tde.md)
> * [Titkosítás (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Szükséges engedélyek

A transzparens adattitkosítás (TDE) engedélyezéséhez rendszergazdának vagy a DBManager szerepkör tagjának kell lennie.

## <a name="enabling-encryption"></a>Titkosítás engedélyezése

A TDE engedélyezéséhez kövesse az alábbi lépéseket:

1. Kapcsolódjon a *Master* adatbázishoz az adatbázist futtató kiszolgálón egy olyan bejelentkezéssel, amely a főadatbázisban rendszergazda vagy a **DBManager** szerepkör tagja.
2. A következő utasítás végrehajtásával titkosíthatja az adatbázist.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Titkosítás letiltása

A TDE letiltásához kövesse az alábbi lépéseket:

1. Kapcsolódjon a *Master* adatbázishoz egy olyan bejelentkezéssel, amely a főadatbázisban rendszergazda vagy a **DBManager** szerepkör tagja.
2. A következő utasítás végrehajtásával titkosíthatja az adatbázist.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> A TDE beállítások módosítása előtt a szüneteltetett SQL-készletet újra kell indítani.

## <a name="verifying-encryption"></a>Titkosítás ellenőrzése

A titkosítás állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1. Kapcsolódjon a *Master* vagy a instance adatbázishoz egy olyan bejelentkezéssel, amely a főadatbázisban rendszergazda vagy a **DBManager** szerepkör tagja.
2. A következő utasítás végrehajtásával titkosíthatja az adatbázist.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Egy ```1``` titkosított adatbázist jelez, amely ```0``` egy nem titkosított adatbázist jelez.

## <a name="encryption-dmvs"></a>Titkosítási DMV

* [sys. Databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys. dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
