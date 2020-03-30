---
title: Transzparens adattitkosítás engedélyezése stretch adatbázishoz (T-SQL)
description: Transzparens adattitkosítás (TDE) engedélyezése az SQL Server Stretch Database számára az Azure TSQL-en
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033958"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Transzparens adattitkosítás (TDE) engedélyezése az Azure stretch-adatbázisához (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure-portál](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Az átlátszó adattitkosítás (TDE) az adatbázis, a kapcsolódó biztonsági mentések és az inaktív tranzakciós naplófájlok valós idejű titkosításával és visszafejtésével az alkalmazás módosítása nélkül nyújt védelmet a rosszindulatú tevékenységek fenyegetése ellen.

A TDE egy teljes adatbázis tartalmát titkosítja egy szimmetrikus kulcs – az adatbázis-titkosítási kulcs – használatával. Az adatbázis-titkosítási kulcsot beépített kiszolgálói tanúsítvány védi. A beépített kiszolgálói tanúsítvány minden Egyes Azure-kiszolgálóesetében egyedi. A Microsoft legalább 90 naponta automatikusan elforgatja ezeket a tanúsítványokat. A TDE általános leírását az Átlátszó adattitkosítás (TDE) című témakörben [tetszetős en]olvassa el.

## <a name="enabling-encryption"></a>Titkosítás engedélyezése
Ha engedélyezni szeretné a TDE-t egy olyan Azure-adatbázishoz, amely a rugalmasan kezelhető SQL Server-adatbázisból áttelepített adatokat tárolja, tegye a következőket:

1. Csatlakozás az adatbázist üzemeltető Azure-kiszolgáló *fő* adatbázisához rendszergazdai vagy a **főadatbázisban** lévő dbmanager-szerepkör tagjaként történő bejelentkezéssel
2. Az adatbázis titkosításához hajtsa végre a következő utasítást.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Titkosítás letiltása
Ha le szeretné tiltani a TDE-t egy olyan Azure-adatbázisesetében, amely a rugalmasan engedélyezett SQL Server-adatbázisból áttelepített adatokat tárolja, tegye a következőket:

1. Csatlakozás a *főadatbázishoz* rendszergazdai vagy a **főadatbázis dbmanager-szerepkörének** tagjaként történő csatlakozással
2. Az adatbázis titkosításához hajtsa végre a következő utasítást.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Titkosítás ellenőrzése
A rugalmasan elérhető SQL Server-adatbázisból áttelepített adatokat tároló Azure-adatbázis titkosítási állapotának ellenőrzéséhez tegye a következőket:

1. Csatlakozás a *főkiszolgáló-* vagy példányadatbázishoz rendszergazdai vagy a **főadatbázis dbmanager-szerepkörének** tagja ként történő bejelentkezéssel
2. Az adatbázis titkosításához hajtsa végre a következő utasítást.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Az eredmény ```1``` titkosított adatbázist ```0``` jelez, nem titkosított adatbázist jelöl.

<!--Anchors-->
[Transzparens adattitkosítás (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
