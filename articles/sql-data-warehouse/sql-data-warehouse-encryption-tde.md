---
title: Transzparens adattitkosítás (portál)
description: Transzparens adattitkosítás (TDE) a SQL Data Warehouse
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
ms.openlocfilehash: 4435c5f246d6dd20fd0b2c560634f6980d6aa2ff
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685783"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Ismerkedés a transzparens adattitkosítás (TDE) szolgáltatással SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Biztonsági áttekintés](sql-data-warehouse-overview-manage-security.md)
> * [Hitelesítés](sql-data-warehouse-authentication.md)
> * [Titkosítás (portál)](sql-data-warehouse-encryption-tde.md)
> * [Encryption (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Szükséges engedélyek
A transzparens adattitkosítás (TDE) engedélyezéséhez rendszergazdának vagy a DBManager szerepkör tagjának kell lennie.

## <a name="enabling-encryption"></a>Titkosítás engedélyezése
SQL Data Warehouse TDE engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az adatbázist a [Azure Portal](https://portal.azure.com)
2. Az adatbázis panelen kattintson a **Beállítások** gombra.
3. Válassza ki az **transzparens adattitkosítási** beállítást ![][1]
4. Válassza ki **a beállítást ![][2]**
5. Válassza a **mentés**
   ![][3]  

## <a name="disabling-encryption"></a>Titkosítás letiltása
SQL Data Warehouse TDE letiltásához kövesse az alábbi lépéseket:

1. Nyissa meg az adatbázist a [Azure Portal](https://portal.azure.com)
2. Az adatbázis panelen kattintson a **Beállítások** gombra.
3. Válassza ki az **transzparens adattitkosítási** beállítást ![][1]
4. Válassza ki az **off** beállítást ![][4]
5. Válassza a **mentés**
   ![][5]  

## <a name="encryption-dmvs"></a>Titkosítási DMV
A titkosítást a következő DMV lehet megerősíteni:

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
