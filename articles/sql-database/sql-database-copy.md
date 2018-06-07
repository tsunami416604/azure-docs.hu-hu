---
title: Azure SQL-adatbázis másolása |} Microsoft Docs
description: Hozzon létre egy meglévő Azure SQL adatbázis tranzakciós úton konzisztens példányát ugyanarra a kiszolgálóra vagy egy másik kiszolgálóra.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 04/01/2018
ms.author: carlrab
ms.topic: conceptual
ms.openlocfilehash: 2217df046cf95ddcd12f6dcaa41b2c3f8b0090f6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646201"
---
# <a name="copy-an-azure-sql-database"></a>Azure SQL-adatbázis másolása

Az Azure SQL-adatbázis létrehozásához egy meglévő Azure SQL adatbázis tranzakciós úton konzisztens másolatot ugyanarra a kiszolgálóra vagy egy másik kiszolgálóra több módszert biztosít. SQL-adatbázis másolása az Azure-portálon, a PowerShell vagy a T-SQL. 

## <a name="overview"></a>Áttekintés

Egy adatbázis-példány a source adatbázis időpontjában a a másolási kérés. Kiválaszthatja, hogy ugyanazon a kiszolgálón vagy egy másik kiszolgáló, a szolgáltatási rétegben és teljesítményszintet vagy egy másik teljesítményszintet belül az azonos szolgáltatási réteg (kiadás). A másolás után egy teljesen működőképes, független adatbázis válik. Ezen a ponton frissítése vagy visszaminősítését a bármely verzióra. A bejelentkezési adatok, a felhasználók és az engedélyek függetlenül is kezelhetők.  

## <a name="logins-in-the-database-copy"></a>Az adatbázis-másolat bejelentkezésekre

Egy adatbázis ugyanazon a logikai kiszolgálón történő másolásakor a azonos bejelentkezések mindkét adatbázissal is használhatók. A rendszerbiztonsági tag használatával másolja az adatbázist az új adatbázis az adatbázis tulajdonosa lesz. Minden adatbázis-felhasználó, az engedélyek és a biztonsági azonosítók (SID) az adatbázis-másolat lesz másolva.  

Adatbázis másolása egy másik logikai kiszolgálóhoz, ha a rendszerbiztonsági tag az új kiszolgáló lesz az adatbázis tulajdonosának az új adatbázis. Ha [tartalmazott adatbázis-felhasználók](sql-database-manage-logins.md) adatelérés, biztosítja, hogy az elsődleges és másodlagos adatbázisok mindig a felhasználói hitelesítő adatokkal, így a másolás után azonnal elérhetőségét azokkal a hitelesítő adatokkal . 

Ha [Azure Active Directory](../active-directory/active-directory-whatis.md), akkor is teljesen szükségtelenné teszik a hitelesítő adatokat, a Másolás kezelése. Azonban az adatbázis új kiszolgálóra történő másolásakor a bejelentkezés-alapú hozzáférés előfordulhat, hogy nem működik, mert a bejelentkezési adatok nem léteznek az új kiszolgálón. Bejelentkezések kezelése, ha az adatbázis másolása egy másik logikai kiszolgáló kapcsolatos további tudnivalókért lásd: [kezelése az Azure SQL database biztonsági katasztrófa utáni helyreállítás után](sql-database-geo-replication-security-config.md). 

A másolási sikeres követően, és mielőtt más felhasználók újra vannak társítva, csak a bejelentkezési azonosítót, a másolás, az adatbázis tulajdonosának kezdeményezett bejelentkezhet az új adatbázishoz. A másolási művelet befejezése után bejelentkezések megoldását lásd [bejelentkezések megoldásához](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Adatbázis másolása az Azure portál használatával

Adatbázis másolása az Azure portál használatával, nyissa meg az adatbázis lapját, és kattintson **másolási**. 

   ![Az adatbázis másolása](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Adatbázis másolása a PowerShell használatával

Adatbázis másolása a PowerShell használatával, használja a [New-AzureRmSqlDatabaseCopy](/powershell/module/azurerm.sql/new-azurermsqldatabasecopy) parancsmag. 

```PowerShell
New-AzureRmSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Tekintse meg a teljes minta parancsfájlt [adatbázis másolása egy új kiszolgálót](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Adatbázis másolása Transact-SQL használatával

Jelentkezzen be a kiszolgálószintű fő bejelentkezéssel vagy a bejelentkezés által létrehozott át kívánja másolni az adatbázist a fő adatbázist. Az adatbázis másolása sikeres, bejelentkezési adatok, amelyek nincsenek a kiszolgálói szintű rendszerbiztonsági tag a dbmanager szerepkör tagjának kell lennie. Bejelentkezéseket és a kiszolgálóhoz való kapcsolódás kapcsolatos további információkért lásd: [bejelentkezések kezelése](sql-database-manage-logins.md).

A forrásadatbázis másolásának megkezdése a [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) utasítást. Az utasítás végrehajtása indít el az adatbázis másolása folyamat. Mivel az adatbázis másolása egy aszinkron folyamat, a CREATE DATABASE utasítás adja vissza, az adatbázis-Másolás befejezése előtt.

### <a name="copy-a-sql-database-to-the-same-server"></a>Egy SQL-adatbázis másolása ugyanarra a kiszolgálóra
Jelentkezzen be a kiszolgálószintű fő bejelentkezéssel vagy a bejelentkezés által létrehozott át kívánja másolni az adatbázist a fő adatbázist. Az adatbázis másolása sikeres, bejelentkezési adatok, amelyek nincsenek a kiszolgálói szintű rendszerbiztonsági tag a dbmanager szerepkör tagjának kell lennie.

Ez a parancs egy új adatbázist ugyanazon a kiszolgálón Database2 nevű Database1 másolja. Az adatbázis méretétől függően a másolási művelet eltarthat egy ideig.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Egy SQL-adatbázis másolása egy másik kiszolgálóra

Jelentkezzen be a célkiszolgáló, ahol az új adatbázisnak van-e létrehozni SQL-adatbáziskiszolgáló a fő adatbázist. Használjon olyan bejelentkezési azonosítót, amely rendelkezik az adatbázis tulajdonosának a forráskiszolgálón SQL adatbázis a forrásadatbázis ugyanazt a nevet és jelszót. A bejelentkezés a célkiszolgálón is kell a dbmanager szerepkör tagjának vagy a kiszolgálószintű fő bejelentkezéssel kell.

Ez a parancs átmásolja Database1 kiszolgáló1 kiszolgáló2 Database2 nevű új adatbázis. Az adatbázis méretétől függően a másolási művelet eltarthat egy ideig.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


### <a name="monitor-the-progress-of-the-copying-operation"></a>A másolási művelet előrehaladásának figyeléséhez

A másolási folyamat figyelése a sys.databases és sys.dm_database_copies nézet lekérdezésével. Miközben folyamatban van. a Másolás a **state_desc** a sys.databases nézetben az új adatbázis oszlop értéke **MÁSOLÁSA**.

* Ha a másolás nem sikerül, a **state_desc** a sys.databases nézetben az új adatbázis oszlop értéke **FELTÉTELEZHETŐ**. Hajtsa végre a DROP utasítást az új adatbázis, és próbálkozzon újra később.
* Ha a másolás sikeres, a **state_desc** a sys.databases nézetben az új adatbázis oszlop értéke **ONLINE**. A másolási befejeződött, és az új adatbázis rendszeres adatbázis a forrásadatbázis független módosítható.

> [!NOTE]
> Ha úgy dönt, hogy megszakítja a a másolást, amíg folyamatban van, a végrehajtást a [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) utasítás az új adatbázishoz. Alternatív megoldásként a DROP DATABASE utasítás végrehajtása a forrásadatbázison is visszavonja a másolási folyamat.
> 

## <a name="resolve-logins"></a>Oldja meg a bejelentkezési adatok

Miután az új adatbázis a célkiszolgálón online állapotban, a [ALTER felhasználói](https://msdn.microsoft.com/library/ms176060.aspx) adja meg a felhasználók az új adatbázis bejelentkezések a célkiszolgálón újból az utasítást. Árva felhasználók megoldása érdekében tekintse meg a [árva felhasználók hibaelhárítása](https://msdn.microsoft.com/library/ms175475.aspx). Lásd még: [kezelése az Azure SQL database biztonsági katasztrófa utáni helyreállítás után](sql-database-geo-replication-security-config.md).

Minden felhasználó az új adatbázis a forrásadatbázis rendelkeztek engedélyeket megőrzése mellett. Az adatbázis-másolat elindító felhasználó az új adatbázis tulajdonosa lesz, és hozzá van rendelve egy új biztonsági azonosítóval (SID). A másolási sikeres követően, és mielőtt más felhasználók újra vannak társítva, csak a bejelentkezési azonosítót, a másolás, az adatbázis tulajdonosának kezdeményezett bejelentkezhet az új adatbázishoz.

Felhasználók és bejelentkezések kezelése, ha az adatbázis másolása egy másik logikai kiszolgáló kapcsolatos további tudnivalókért lásd: [kezelése az Azure SQL database biztonsági katasztrófa utáni helyreállítás után](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>További lépések

* Bejelentkezések kapcsolatos információkért lásd: [bejelentkezések kezelése](sql-database-manage-logins.md) és [kezelése az Azure SQL database biztonsági katasztrófa utáni helyreállítás után](sql-database-geo-replication-security-config.md).
* Egy adatbázis-exportálási, lásd: [exportálja az adatbázis egy BACPAC](sql-database-export.md).
