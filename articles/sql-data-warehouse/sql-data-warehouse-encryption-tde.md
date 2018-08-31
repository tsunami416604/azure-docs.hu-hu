---
title: Transzparens adattitkosítás az SQL Data Warehouse (portál) |} A Microsoft Docs
description: Az SQL Data Warehouse transzparens adattitkosítás (TDE)
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: e641ddb5360c18b5977fb79de41334d4216c2b90
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306187"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>A transzparens adattitkosítás (TDE) az SQL Data Warehouse első lépései
> [!div class="op_single_selector"]
> * [Biztonsági áttekintés](sql-data-warehouse-overview-manage-security.md)
> * [Hitelesítés](sql-data-warehouse-authentication.md)
> * [Titkosítás (portál)](sql-data-warehouse-encryption-tde.md)
> * [Encryption (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>Szükséges engedélyek
Ahhoz, hogy a transzparens adattitkosítás (TDE), a rendszergazda vagy a dbmanager szerepkör tagjának kell lennie.

## <a name="enabling-encryption"></a>Titkosítás engedélyezése
Engedélyezni szeretné a TDE az SQL Data Warehouse, kövesse az alábbi lépéseket:

1. Nyissa meg az adatbázishoz a [Azure Portalon](https://portal.azure.com)
2. Az adatbázis paneljén kattintson a **beállítások** gomb
3. Válassza ki a **transzparens adattitkosítás** lehetőség ![][1]
4. Válassza ki a **a** beállítás ![][2]
5. Válassza ki **mentése**
   ![][3]  

## <a name="disabling-encryption"></a>Letiltja a titkosítást
TDE SQL Data Warehouse letiltásához kövesse az alábbi lépéseket:

1. Nyissa meg az adatbázishoz a [Azure Portalon](https://portal.azure.com)
2. Az adatbázis paneljén kattintson a **beállítások** gomb
3. Válassza ki a **transzparens adattitkosítás** lehetőség ![][1]
4. Válassza ki a **ki** beállítás ![][4]
5. Válassza ki **mentése**
   ![][5]  

## <a name="encryption-dmvs"></a>Titkosítási DMV-kkel
Titkosítási erősíthető meg a következő DMV-k használatával:

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
