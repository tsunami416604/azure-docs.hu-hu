---
title: Transzparens adattitkosítás az SQL Data Warehouse (T-SQL) |} A Microsoft Docs
description: Transzparens adattitkosítás (TDE) az SQL Data Warehouse (T-SQL)
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 50b6077687da2ed24a3a90b227d129c43f70bfc9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465809"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Ismerkedés a transzparens adattitkosítás (TDE)
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
Kövesse az alábbi lépéseket, hogy engedélyezni szeretné a TDE az SQL Data Warehouse:

1. Csatlakozás a *fő* adatbázis olyan bejelentkezési azonosítót, a rendszergazda vagy egy tagja az adatbázist üzemeltető kiszolgálón a **dbmanager** szerepkör a master adatbázisban
2. Futtassa a következő utasítást az adatbázis titkosításához.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Letiltja a titkosítást
Kövesse az alábbi lépéseket, tiltsa le a TDE az SQL Data Warehouse:

1. Csatlakozás a *fő* használatával olyan bejelentkezési azonosítót, a rendszergazda vagy egy tagja a **dbmanager** szerepkör a master adatbázisban
2. Futtassa a következő utasítást az adatbázis titkosításához.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Felfüggesztett SQL Data Warehouse a TDE-beállítások módosítása előtt kell folytatni.
> 
> 

## <a name="verifying-encryption"></a>Titkosítási ellenőrzése
Az SQL Data Warehouse adattitkosítási állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1. Csatlakozás a *fő* vagy példány adatbázishoz olyan bejelentkezési azonosítót, a rendszergazda vagy egy tagja az a **dbmanager** szerepkör a master adatbázisban
2. Futtassa a következő utasítást az adatbázis titkosításához.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Eredménye ```1``` azt jelzi, hogy egy titkosított adatbázis ```0``` azt jelzi, hogy a nem titkosított adatbázis.

## <a name="encryption-dmvs"></a>Titkosítási DMV-kkel
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
