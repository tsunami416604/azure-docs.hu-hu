---
title: A Stretch Database TSQL - Azure transzparens adattitkosítás engedélyezése |} A Microsoft Docs
description: Az SQL Server Stretch Database az Azure TSQL transzparens adattitkosításának (TDE) engedélyezése
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.author: douglasl
ms.openlocfilehash: 634e1b097f2ca16a8c52da02445b3f5f2af7ff3d
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744785"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Transzparens adattitkosítás (TDE) engedélyezze a Stretch Database az Azure-ban (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transzparens adattitkosítás (TDE) segítségével elleni védelemhez kártevő szándékú tevékenységek valós idejű titkosítási és visszafejtési az adatbázis, azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra inaktív elvégzésével az alkalmazás módosítása nélkül.

TDE titkosítja a tárolót a teljes adatbázisra az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával. Az adatbázis-titkosítási kulcs egy beépített kiszolgálói tanúsítvány védi. A beépített kiszolgálói tanúsítvány egy egyedülálló megoldás minden egyes Azure-kiszolgáló. A Microsoft legalább 90 naponként automatikusan elforgatja ezeket a tanúsítványokat. TDE általános ismertetését lásd: [Transzparens adattitkosítás (TDE)].

## <a name="enabling-encryption"></a>Titkosítás engedélyezése
TDE engedélyezése az Azure-adatbázis, amely tárolja az adatokat át a Stretch engedélyezve van az SQL Server-adatbázisból, tegye a következőket:

1. Csatlakozás a *fő* adatbázis az Azure használatával olyan bejelentkezési azonosítót, a rendszergazda vagy egy tagja az adatbázist futtató kiszolgálón a **dbmanager** szerepkör a master adatbázisban
2. Futtassa a következő utasítást az adatbázis titkosításához.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Letiltja a titkosítást
TDE letiltása az Azure-adatbázis, amely tárolja az adatokat át a Stretch engedélyezve van az SQL Server-adatbázisból, tegye a következőket:

1. Csatlakozás a *fő* használatával olyan bejelentkezési azonosítót, a rendszergazda vagy egy tagja a **dbmanager** szerepkör a master adatbázisban
2. Futtassa a következő utasítást az adatbázis titkosításához.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Titkosítási ellenőrzése
Annak ellenőrzéséhez, hogy a Stretch engedélyezve van az SQL Server-adatbázisból áttelepítése titkosítási állapotát az Azure-adatbázis, amely tárolja az adatokat, tegye a következőket:

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

<!--Anchors-->
[Transzparens adattitkosítás (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
