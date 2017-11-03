---
title: "Engedélyezze a Stretch Database TSQL - Azure az átlátható adattitkosítást |} Microsoft Docs"
description: "Az SQL Server Stretch Database Azure TSQL átlátható adattitkosítás (TDE) engedélyezése"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: anvang
ms.openlocfilehash: ed26c2b386e08b76f78b4a05e12c46d2b97c20f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>A Stretch Database az Azure (Transact-SQL) átlátható adattitkosítás (TDE) engedélyezése
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transzparens Data Encryption (TDE) abban a kártékony tevékenység fenyegetés valós idejű titkosítási és visszafejtési az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok nyugalmi elvégzésével anélkül, hogy az alkalmazást módosítani kellene.

TDE teljes adatbázis tárterülete az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával titkosítja. Az adatbázis-titkosítási kulcs védi egy beépített kiszolgálói tanúsítványt. A beépített kiszolgálói tanúsítvány egyedi minden Azure-kiszolgálóval rendelkezik. Microsoft legalább 90 naponta automatikusan elforgatja ezeket a tanúsítványokat. TDE általános ismertetését lásd: [átlátszó Data Encryption (TDE)].

## <a name="enabling-encryption"></a>Titkosítás engedélyezése
TDE engedélyezése az Azure adatbázist, az adattárolás átemelt Stretch-kompatibilis SQL Server-adatbázisok, tegye a következőket:

1. Csatlakozás a *fő* adatbázis használata a bejelentkezési azonosítót, amely a rendszergazda vagy egy másik adatbázist üzemeltető Azure kiszolgálón a **dbmanager** a master adatbázisban szerepkör
2. Hajtsa végre a következő utasítást az adatbázis titkosításához.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Titkosításának letiltása
TDE letiltása az Azure adatbázist, az adattárolás átemelt Stretch-kompatibilis SQL Server-adatbázisok, tegye a következőket:

1. Csatlakozás a *fő* adatbázis használata a bejelentkezési azonosítót, amely a rendszergazda vagy egy tagja a **dbmanager** a master adatbázisban szerepkör
2. Hajtsa végre a következő utasítást az adatbázis titkosításához.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Titkosítási ellenőrzése
Egy Azure-adatbázis, amely tárolja az adatokat a titkosítási állapot átemelt Stretch-kompatibilis SQL Server-adatbázis ellenőrzéséhez tegye a következőket:

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

<!--Anchors-->
[átlátszó Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
