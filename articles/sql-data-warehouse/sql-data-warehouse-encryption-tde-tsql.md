---
title: Transzparens adattitkosítás (T-SQL)
description: Transzparens adattitkosítás (TDE) az Azure szinapszis Analyticsben (T-SQL)
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: bd6f40b8389284c1932a2f16a70060cd56e412fb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195805"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Ismerkedés a transzparens adattitkosításrel (TDE)
> [!div class="op_single_selector"]
> * [Biztonsági áttekintés](sql-data-warehouse-overview-manage-security.md)
> * [Hitelesítés](sql-data-warehouse-authentication.md)
> * [Titkosítás (portál)](sql-data-warehouse-encryption-tde.md)
> * [Titkosítás (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

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
> 
> 

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

```1``` eredménye titkosított adatbázist jelez, ```0``` nem titkosított adatbázist jelez.

## <a name="encryption-dmvs"></a>Titkosítási DMV
* [sys. Databases][sys.databases] 
* [sys. dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
