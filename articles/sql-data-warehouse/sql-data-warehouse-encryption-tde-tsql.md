---
title: "Az SQL Data Warehouse (T-SQL) átlátható adattitkosítás |} Microsoft Docs"
description: "Az SQL Data Warehouse (T-SQL) átlátható adattitkosítás (TDE)"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: barbkess
editor: 
ms.assetid: 8ccefef3-1308-41ee-b336-5e491d1098ae
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: 74c9032aababdce91ed617cd7a4c628915b42504
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Ismerkedés a transzparens adatok titkosítás (TDE)
> [!div class="op_single_selector"]
> * [Biztonság – áttekintés](sql-data-warehouse-overview-manage-security.md)
> * [Hitelesítés](sql-data-warehouse-authentication.md)
> * [Titkosítás (portál)](sql-data-warehouse-encryption-tde.md)
> * [Titkosítás (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>Szükséges engedélyek
Ahhoz, hogy az átlátszó Data Encryption (TDE), a rendszergazda vagy a dbmanager szerepkör tagjának kell lennie.

## <a name="enabling-encryption"></a>Titkosítás engedélyezése
Kövesse az alábbi lépéseket az SQL Data Warehouse TDE engedélyezéséhez:

1. Csatlakozás a *fő* adatbázis használata a bejelentkezési azonosítót, amely a rendszergazda vagy egy másik adatbázist futtató kiszolgálón a **dbmanager** a master adatbázisban szerepkör
2. Hajtsa végre a következő utasítást az adatbázis titkosításához.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Titkosításának letiltása
Kövesse az alábbi lépéseket az SQL Data Warehouse TDE letiltása:

1. Csatlakozás a *fő* adatbázis használata a bejelentkezési azonosítót, amely a rendszergazda vagy egy tagja a **dbmanager** a master adatbázisban szerepkör
2. Hajtsa végre a következő utasítást az adatbázis titkosításához.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Felfüggesztett SQL Data Warehouse a TDE beállítások módosítása előtt kell folytatni.
> 
> 

## <a name="verifying-encryption"></a>Titkosítási ellenőrzése
SQL Data Warehouse titkosítási állapot ellenőrzéséhez kövesse az alábbi lépéseket:

1. Csatlakozás a *fő* vagy egy bejelentkezési azonosítót, amely a rendszergazda vagy egy másik példány adatbázist a **dbmanager** a master adatbázisban szerepkör
2. Hajtsa végre a következő utasítást az adatbázis titkosításához.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Eredménye ```1``` jelzi egy titkosított adatbázis ```0``` nem titkosított adatbázis jelzi.

## <a name="encryption-dmvs"></a>Titkosítási dinamikus felügyeleti nézetek
* [sys.Databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
