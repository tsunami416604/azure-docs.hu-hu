---
title: Transzparens adattitkosítás (T-SQL)
description: Transzparens adattitkosítás (TDE) az Azure Synapse Analytics (T-SQL)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: ae751cc5b8e3ab67f3e65757724d0ebae1c45e02
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745250"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Az átlátszó adattitkosítás (TDE) első lépései

> [!div class="op_single_selector"]
>
> * [Biztonság – áttekintés](sql-data-warehouse-overview-manage-security.md)
> * [Hitelesítés](sql-data-warehouse-authentication.md)
> * [Titkosítás (portál)](sql-data-warehouse-encryption-tde.md)
> * [Titkosítás (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Szükséges engedélyek

Az átlátszó adattitkosítás (TDE) engedélyezéséhez rendszergazdának vagy a dbmanager szerepkör tagjának kell lennie.

## <a name="enabling-encryption"></a>Titkosítás engedélyezése

A TDE engedélyezéséhez kövesse az alábbi lépéseket:

1. Csatlakozás az adatbázist tároló kiszolgáló *főadatbázisához* rendszergazdai vagy a **főadatbázis dbmanager-szerepkörének** tagja ként történő bejelentkezéssel
2. Az adatbázis titkosításához hajtsa végre a következő utasítást.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Titkosítás letiltása

A TDE letiltásához kövesse az alábbi lépéseket:

1. Csatlakozás a *főadatbázishoz* rendszergazdai vagy a **főadatbázis dbmanager-szerepkörének** tagjaként történő csatlakozással
2. Az adatbázis titkosításához hajtsa végre a következő utasítást.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> A szüneteltetett SQL-készletet a TDE-beállítások módosítása előtt újra kell indítani.

## <a name="verifying-encryption"></a>Titkosítás ellenőrzése

A titkosítási állapot ellenőrzéséhez kövesse az alábbi lépéseket:

1. Csatlakozás a *főkiszolgáló-* vagy példányadatbázishoz rendszergazdai vagy a **főadatbázis dbmanager-szerepkörének** tagja ként történő bejelentkezéssel
2. Az adatbázis titkosításához hajtsa végre a következő utasítást.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Az eredmény ```1``` titkosított adatbázist ```0``` jelez, nem titkosított adatbázist jelöl.

## <a name="encryption-dmvs"></a>Titkosítási DMV-k

* [sys.adatbázisok](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
