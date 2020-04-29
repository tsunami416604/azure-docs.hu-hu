---
title: Transzparens adattitkosítás (portál)
description: Transzparens adattitkosítás (TDE) az Azure szinapszis Analyticsben
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
ms.openlocfilehash: 247691326e3aa2c8027dd0318b23a2cbfcba1efe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745226"
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

1. Nyissa meg az adatbázist a [Azure Portal](https://portal.azure.com)
2. Az adatbázis panelen kattintson a **Beállítások** gombra.
3. Válassza ki az **transzparens adattitkosítási** lehetőség ![portáljának beállításait](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Válassza az **on** Setting ![portál Settings on](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. 
   ![Válassza **a portál beállításainak mentése mentés**lehetőséget.](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Titkosítás letiltása

A TDE letiltásához kövesse az alábbi lépéseket:

1. Nyissa meg az adatbázist a [Azure Portal](https://portal.azure.com)
2. Az adatbázis panelen kattintson a **Beállítások** gombra.
3. Válassza ki az **transzparens adattitkosítási** lehetőség ![portáljának beállításait](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Válassza ki **Off** az off ![Setting portál beállításait kikapcsolva](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Válassza a portál **mentési**
   ![beállítások mentés 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>Titkosítási DMV

A titkosítást a következő DMV lehet megerősíteni:

* [sys. Databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys. dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
