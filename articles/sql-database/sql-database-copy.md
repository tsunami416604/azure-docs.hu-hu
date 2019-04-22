---
title: Az Azure SQL database másolása |} A Microsoft Docs
description: Hozzon létre egy meglévő Azure SQL database tranzakciós szempontból konzisztens másolatot ugyanarra a kiszolgálóra vagy egy másik kiszolgálóra.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: sahsan
ms.reviewer: carlrab
manager: craigg
ms.date: 04/11/2019
ms.openlocfilehash: 363803e9276a8356b52438f251391378c54f1655
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678938"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Az Azure SQL database tranzakciós szempontból konzisztens másolatot másolása

Az Azure SQL Database egy meglévő Azure SQL database tranzakciós szempontból konzisztens másolatot létrehozni ugyanazon a kiszolgálón vagy egy másik kiszolgálóra több módszert is biztosít. SQL-adatbázis másolhatja az Azure portal, PowerShell vagy a T-SQL használatával. 

## <a name="overview"></a>Áttekintés

Adatbázis-másolat a Másolás kérés időpontjában a forrásadatbázis pillanatképét. Kiválaszthatja, hogy ugyanazon a kiszolgálón vagy egy másik kiszolgálóra. Is lehet váltani, tartsa szolgáltatásszintet és számítási méret vagy ugyanazon a szolgáltatásszinten (kiadás) belül különböző számítási méret. A Másolás befejezése után egy teljesen működőképes, független adatbázis válik. Ezen a ponton frissítse vagy Visszaléptetés a bármely verzióra. A bejelentkezések, felhasználók és engedélyek egymástól függetlenül is felügyelhetők.  

> [!NOTE]
> [Adatbázisok biztonsági mentése automatikus](sql-database-automated-backups.md) adatbázis-másolat létrehozásakor használt.

## <a name="logins-in-the-database-copy"></a>Az adatbázis-másolat a bejelentkezések

Egy adatbázis az SQL Database ugyanarra a kiszolgálóra való másolásakor a azonos bejelentkezések használható mindkét adatbázison. A rendszerbiztonsági tag használatával másolja át az adatbázist az új adatbázis az adatbázis tulajdonosa lesz. Minden adatbázis-felhasználó, az engedélyeit és a biztonsági azonosítók (SID) az adatbázis-másolat lesz másolva.  

Ha egy adatbázist egy másik SQL-adatbázis-kiszolgálóra másolja, a rendszerbiztonsági tag az új kiszolgálón válik az új adatbázis az adatbázis tulajdonosa. Ha [tartalmazott adatbázis felhasználóit](sql-database-manage-logins.md) adatelérés, győződjön meg arról, hogy az elsődleges és másodlagos adatbázisok mindig rendelkezzenek-e az azonos felhasználói hitelesítő adatok, úgy, hogy a másolás után azonnal hozzáférhet a azonos hitelesítő adatokkal . 

Ha [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), teljesen többé nem kell hitelesítő adatokat, a Másolás kezeléséhez. Azonban az adatbázis másolása új kiszolgálóra esetén a bejelentkezés-alapú hozzáférés nem működik, mert a bejelentkezések nem léteznek az új kiszolgálón. Bejelentkezések kezelése, ha az adatbázis másolása egy másik SQL-adatbáziskiszolgáló kapcsolatos további információkért lásd: [kezelése az Azure SQL database biztonsági után vész-helyreállítási](sql-database-geo-replication-security-config.md). 

Sikeres másolását követően, és mielőtt más felhasználók újra vannak társítva, csak a bejelentkezés által kezdeményezett, a másolás, az adatbázis tulajdonosa, bejelentkezhet az új adatbázisba. A másolási művelet befejezése után a bejelentkezések elhárításához lásd: [bejelentkezések megoldásához](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Adatbázis másolása az Azure portal használatával

Adatbázis másolása az Azure portal használatával, nyissa meg az adatbázishoz tartozó lap, és kattintson **másolási**. 

   ![Az adatbázis másolása](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Adatbázis másolása egy PowerShell-lel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés Az.Sql modul. Ezeket a parancsmagokat lásd: [azurerm.SQL-hez](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). A parancsok a Az modul, és az AzureRm-modulok argumentumainak lényegében megegyeznek.

Adatbázis másolása egy PowerShell-lel, használja a [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) parancsmagot. 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Teljes minta parancsfájl, lásd: [adatbázis másolása új kiszolgálóra](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Adatbázis másolása a Transact-SQL használatával

Jelentkezzen be a kiszolgálószintű fő bejelentkező vagy a bejelentkezés által létrehozott szeretné másolni az adatbázist a master adatbázisban. Az adatbázis másolása sikeres, a bejelentkezések, amelyek nem a kiszolgálószintű rendszerbiztonsági tagnak a dbmanager szerepkör tagjának kell lennie. Bejelentkezések és a kiszolgálóhoz való kapcsolódás kapcsolatos további információkért lásd: [bejelentkezések kezelése](sql-database-manage-logins.md).

Indítsa el a forrás-adatbázis-Másolás a [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) utasítást. Ez az utasítás végrehajtása indítja el az adatbázis másolása folyamat. Mivel az adatbázis másolása egy aszinkron folyamat, a CREATE DATABASE utasítás adja vissza, az adatbázis-Másolás befejezése előtt.

### <a name="copy-a-sql-database-to-the-same-server"></a>SQL-adatbázis másolása ugyanarra a kiszolgálóra

Jelentkezzen be a kiszolgálószintű fő bejelentkező vagy a bejelentkezés által létrehozott szeretné másolni az adatbázist a master adatbázisban. Az adatbázis másolása sikeres, a bejelentkezések, amelyek nem a kiszolgálószintű rendszerbiztonsági tagnak a dbmanager szerepkör tagjának kell lennie.

Ezzel a paranccsal egy új adatbázist 2. adatbázis ugyanazon a kiszolgálón Adatbázis1 másolja. Az adatbázis méretétől függően a másolási művelet eltarthat egy ideig.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>SQL-adatbázis másolása egy másik kiszolgálóra

Jelentkezzen be a célkiszolgáló, ahol az új adatbázis hozható létre az SQL Database-kiszolgáló a master adatbázisban. Használja ugyanazt a nevet és jelszót a forrásadatbázis a forrás SQL Database-kiszolgálón az adatbázis tulajdonosaként rendelkező bejelentkezési adatokat. A bejelentkezés a célkiszolgálón csak is a dbmanager szerepkör és a kiszolgálószintű fő bejelentkezéssel.

Ezzel a paranccsal az 1. kiszolgálón Adatbázis1 egy új adatbázisra, 2. adatbázis neve a 2. kiszolgálón másolja. Az adatbázis méretétől függően a másolási művelet eltarthat egy ideig.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> Mindkét kiszolgálók tűzfalak bejövő kapcsolat engedélyezéséhez a parancsot a T-SQL-PÉLDÁNYT az ügyfél az IP-címről kell konfigurálni.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>SQL-adatbázis másolása egy másik előfizetésbe

A lépések descrbed használhatja az előző szakaszban az adatbázis másolása egy SQL Database-kiszolgáló egy másik előfizetésben található. Ellenőrizze, hogy használjon olyan bejelentkezési azonosítót, amely rendelkezik a forrásadatbázis adatbázis tulajdonosa ugyanazt a nevet és jelszót, és azt a dbmanager szerepkör tagjának, vagy a kiszolgálószintű fő bejelentkezéssel. 

> [!NOTE]
> A [az Azure portal](https://portal.azure.com) nem támogatja másik előfizetésbe való másolás, mert a portál meghívja az ARM API-t, és az előfizetés-tanúsítványok használatával mindkét georeplikációs részt vevő kiszolgálók eléréséhez.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>A másolási művelet állapotának figyelése

A másolási folyamat figyeléséhez a sys.databases és a sys.dm_database_copies nézet lekérdezésével. A Másolás folyamatban van, amíg a **state_desc** a sys.databases nézetet az új adatbázis oszlop értéke **MÁSOLÁSA**.

* Ha a Másolás sikertelen, a **state_desc** a sys.databases nézetet az új adatbázis oszlop értéke **FELTÉTELEZHETŐ**. Hajtsa végre a DROP utasítást az új adatbázis, és próbálkozzon újra később.
* Ha a másolása sikeres volt, a **state_desc** a sys.databases nézetet az új adatbázis oszlop értéke **ONLINE**. A másolás befejeződése, és az új adatbázis rendszeres adatbázis, amely független a forrásadatbázis is módosítható.

> [!NOTE]
> Ha úgy dönt, hogy megszakítja a másolás, amíg folyamatban van, hajtsa végre a [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) utasítás az új adatbázis. Másik lehetőségként a DROP DATABASE utasítás végrehajtása a forrásadatbázison is megszakítja a másolási folyamat.

## <a name="resolve-logins"></a>Oldja meg a bejelentkezések

Miután az új adatbázis online, a célkiszolgálón, használja a [ALTER felhasználói](https://msdn.microsoft.com/library/ms176060.aspx) utasítás újramegfeleltetése bejelentkezések a célkiszolgálón az új adatbázis a felhasználók. Árva felhasználók megoldásához olvassa el [árva felhasználók hibaelhárítása](https://msdn.microsoft.com/library/ms175475.aspx). Lásd még: [kezelése az Azure SQL database biztonsági után vész-helyreállítási](sql-database-geo-replication-security-config.md).

Az új adatbázisban található összes felhasználó őrzik meg az engedélyeket, a forrás-adatbázis volt. Az adatbázis-másolat kezdeményező felhasználó lesz az új adatbázis adatbázis tulajdonosa, és hozzá van rendelve egy új biztonsági azonosítóval (SID). Sikeres másolását követően, és mielőtt más felhasználók újra vannak társítva, csak a bejelentkezés által kezdeményezett, a másolás, az adatbázis tulajdonosa, bejelentkezhet az új adatbázisba.

Felhasználók és bejelentkezések kezelése, ha az adatbázis másolása egy másik SQL-adatbáziskiszolgáló kapcsolatos további információkért lásd: [kezelése az Azure SQL database biztonsági után vész-helyreállítási](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>További lépések

* Bejelentkezések kapcsolatos információkért lásd: [bejelentkezések kezelése](sql-database-manage-logins.md) és [kezelése az Azure SQL database biztonsági után vész-helyreállítási](sql-database-geo-replication-security-config.md).
* Adatbázis exportálása, lásd: [az adatbázis exportálása BACPAC](sql-database-export.md).
