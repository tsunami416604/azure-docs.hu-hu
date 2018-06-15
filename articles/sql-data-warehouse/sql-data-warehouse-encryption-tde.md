---
title: Az SQL Data Warehouse (portál) átlátható adattitkosítás |} Microsoft Docs
description: Az SQL Data Warehouse átlátható adattitkosítás (TDE)
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 9c4abb0416acc656a4cfae332377c398260191de
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31524227"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Az átlátszó adatok titkosítás (TDE) az SQL Data Warehouse első lépései
> [!div class="op_single_selector"]
> * [Biztonság – áttekintés](sql-data-warehouse-overview-manage-security.md)
> * [Hitelesítés](sql-data-warehouse-authentication.md)
> * [Titkosítás (portál)](sql-data-warehouse-encryption-tde.md)
> * [Encryption (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>Szükséges engedélyek
Ahhoz, hogy az átlátszó Data Encryption (TDE), a rendszergazda vagy a dbmanager szerepkör tagjának kell lennie.

## <a name="enabling-encryption"></a>Titkosítás engedélyezése
Az SQL Data Warehouse TDE engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az adatbázishoz a [Azure-portálon](https://portal.azure.com)
2. Az adatbázis paneljén kattintson a **beállítások** gomb
3. Válassza ki a **átlátható adattitkosítás** beállítás ![][1]
4. Válassza ki a **a** beállítás ![][2]
5. Válassza ki **mentése**
   ![][3]  

## <a name="disabling-encryption"></a>Titkosításának letiltása
SQL Data Warehouse TDE letiltásához kövesse az alábbi lépéseket:

1. Nyissa meg az adatbázishoz a [Azure-portálon](https://portal.azure.com)
2. Az adatbázis paneljén kattintson a **beállítások** gomb
3. Válassza ki a **átlátható adattitkosítás** beállítás ![][1]
4. Válassza ki a **ki** beállítás ![][4]
5. Válassza ki **mentése**
   ![][5]  

## <a name="encryption-dmvs"></a>Titkosítási dinamikus felügyeleti nézetek
A következő dinamikus felügyeleti nézetek a titkosítási erősíthető meg:

* [sys.Databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.Databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
