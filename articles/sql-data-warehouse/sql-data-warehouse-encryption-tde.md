---
title: Transzparens adattitkosítás (portál)
description: Transzparens adattitkosítás (TDE) az Azure szinapszis Analyticsben
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
ms.openlocfilehash: c121f9c16895a749922525d1ba85ee2c2e60cfb0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195856"
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

1. Nyissa meg az adatbázist a [Azure Portal](https://portal.azure.com)
2. Az adatbázis panelen kattintson a **Beállítások** gombra.
3. Válassza ki az **transzparens adattitkosítási** beállítást ![][1]
4. Válassza ki **a beállítást ![][2]**
5. Válassza a **mentés**
   ![][3]  

## <a name="disabling-encryption"></a>Titkosítás letiltása
A TDE letiltásához kövesse az alábbi lépéseket:

1. Nyissa meg az adatbázist a [Azure Portal](https://portal.azure.com)
2. Az adatbázis panelen kattintson a **Beállítások** gombra.
3. Válassza ki az **transzparens adattitkosítási** beállítást ![][1]
4. Válassza ki az **off** beállítást ![][4]
5. Válassza a **mentés**
   ![][5]  

## <a name="encryption-dmvs"></a>Titkosítási DMV
A titkosítást a következő DMV lehet megerősíteni:

* [sys. Databases]
* [sys. dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys. Databases]: https://msdn.microsoft.com/library/ms178534.aspx
[sys. dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
