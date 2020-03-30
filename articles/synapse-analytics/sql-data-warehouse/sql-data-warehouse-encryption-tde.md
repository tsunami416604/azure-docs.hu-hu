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
ms.openlocfilehash: 669a02bb45ef61d1e66d719850defcfaa19b838d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350535"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Az átlátszó adattitkosítás (TDE) első lépései
> [!div class="op_single_selector"]
> * [Biztonság – áttekintés](sql-data-warehouse-overview-manage-security.md)
> * [Hitelesítés](sql-data-warehouse-authentication.md)
> * [Titkosítás (portál)](sql-data-warehouse-encryption-tde.md)
> * [Titkosítás (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

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

* [sys.adatbázisok]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.adatbázisok]: https://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
