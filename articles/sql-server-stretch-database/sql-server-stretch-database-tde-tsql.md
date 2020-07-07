---
title: Stretch Database (T-SQL) transzparens adattitkosítás engedélyezése
description: Transzparens adattitkosítás (TDE) engedélyezése SQL Server Stretch Database Azure-beli TSQL
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6f1f5f55348069dbfe11b4d5857d93f8ba8c9b19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74033958"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Transzparens adattitkosítás (TDE) engedélyezése az Azure-beli Stretch Database (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure Portalra](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

A transzparens adattitkosítás (TDE) segít megvédeni a kártékony tevékenységek fenyegetését azáltal, hogy valós idejű titkosítást és visszafejtést végez az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok számára, anélkül, hogy módosítani kellene az alkalmazást.

A TDE egy teljes adatbázis tartalmát titkosítja egy szimmetrikus kulcs – az adatbázis-titkosítási kulcs – használatával. Az adatbázis-titkosítási kulcsot egy beépített kiszolgálótanúsítvány védi. A beépített kiszolgáló tanúsítványa minden egyes Azure-kiszolgáló esetében egyedi. A Microsoft automatikusan elforgatja ezeket a tanúsítványokat legalább 90 naponta. A TDE általános ismertetését lásd: [transzparens adattitkosítás (TDE)].

## <a name="enabling-encryption"></a>Titkosítás engedélyezése
Ha engedélyezni szeretné a TDE egy olyan Azure-adatbázishoz, amely a stretch-kompatibilis SQL Server-adatbázisból áttelepített adatok tárolására szolgál, tegye a következőket:

1. Kapcsolódjon az adatbázist futtató Azure-kiszolgálón található *Master* adatbázishoz egy olyan bejelentkezéssel, amely a főadatbázisban rendszergazda vagy a **DBManager** szerepkör tagja.
2. A következő utasítás végrehajtásával titkosíthatja az adatbázist.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Titkosítás letiltása
Ha le szeretné tiltani egy olyan Azure-adatbázis TDE, amely a stretch-kompatibilis SQL Server-adatbázisból áttelepített adatok tárolására szolgál, tegye a következőket:

1. Kapcsolódjon a *Master* adatbázishoz egy olyan bejelentkezéssel, amely a főadatbázisban rendszergazda vagy a **DBManager** szerepkör tagja.
2. A következő utasítás végrehajtásával titkosíthatja az adatbázist.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Titkosítás ellenőrzése
Egy olyan Azure-adatbázis titkosítási állapotának ellenőrzéséhez, amely a stretch-kompatibilis SQL Server-adatbázisból áttelepített adatok tárolására szolgál, tegye a következőket:

1. Kapcsolódjon a *Master* vagy a instance adatbázishoz egy olyan bejelentkezéssel, amely a főadatbázisban rendszergazda vagy a **DBManager** szerepkör tagja.
2. A következő utasítás végrehajtásával titkosíthatja az adatbázist.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Egy ```1``` titkosított adatbázist jelez, amely ```0``` egy nem titkosított adatbázist jelez.

<!--Anchors-->
[Transzparens adattitkosítás (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
