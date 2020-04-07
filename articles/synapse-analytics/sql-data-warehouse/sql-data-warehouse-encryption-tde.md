---
title: Transzparens adattitkosítás (portál)
description: Átlátszó adattitkosítás (TDE) az Azure Synapse Analytics szolgáltatásban
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
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745226"
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

1. Az adatbázis megnyitása az [Azure Portalon](https://portal.azure.com)
2. Az adatbázis panelen kattintson a **Beállítások** gombra
3. Válassza az **Adattitkosítási** beállítás portál ![beállításainak kiválasztása](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Válassza **On** a ![Portál beállításainak beállítása a](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Válassza **a**
   ![Portálbeállításainak mentése lehetőséget](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Titkosítás letiltása

A TDE letiltásához kövesse az alábbi lépéseket:

1. Az adatbázis megnyitása az [Azure Portalon](https://portal.azure.com)
2. Az adatbázis panelen kattintson a **Beállítások** gombra
3. Válassza az **Adattitkosítási** beállítás portál ![beállításainak kiválasztása](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Válassza a ![Portál beállításainak **kikapcsolása** lehetőséget](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Válassza a Portál **mentése**
   ![beállítás mentése 2 lehetőséget.](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>Titkosítási DMV-k

A titkosítás a következő DMV-kkel igazolható:

* [sys.adatbázisok](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
