---
title: Azure SQL Database-adatbázis másolása | Microsoft Docs
description: Hozzon létre tranzakciós szempontból konzisztens másolatot egy meglévő Azure SQL Database-adatbázisról ugyanazon a kiszolgálón vagy egy másik kiszolgálón.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/03/2019
ms.openlocfilehash: e9cc5aaaf11a799b17cc87b40113e166fcd93afb
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568991"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Azure SQL Database-adatbázis tranzakciós szempontból konzisztens másolatának másolása

Azure SQL Database számos módszert biztosít egy meglévő Azure SQL Database-adatbázis ([önálló adatbázis](sql-database-single-database.md)) tranzakciós szempontból konzisztens másolatának létrehozásához ugyanazon a kiszolgálón vagy egy másik kiszolgálón. SQL-adatbázist a Azure Portal, a PowerShell vagy a T-SQL használatával másolhat. 

## <a name="overview"></a>Áttekintés

A másolási kérelem időpontját tartalmazó adatbázis-másolat a forrásadatbázis pillanatképe. Ugyanezt a kiszolgálót vagy egy másik kiszolgálót is kiválaszthatja. Azt is megteheti, hogy megtartja a szolgáltatási szintet és a számítási méretet, vagy eltérő számítási méretet használ ugyanazon a szolgáltatási szinten (kiadás) belül. A másolás befejezése után teljesen működőképes, független adatbázis lesz. Ezen a ponton bármely kiadásra frissítheti vagy visszaminősítheti azt. A bejelentkezések, a felhasználók és az engedélyek egymástól függetlenül kezelhetők.  

> [!NOTE]
> Az adatbázisok [automatikus biztonsági mentése](sql-database-automated-backups.md) az adatbázis-másolat létrehozásakor használatos.

## <a name="logins-in-the-database-copy"></a>Bejelentkezések az adatbázis-másolatban

Ha ugyanarra a SQL Database kiszolgálóra másol egy adatbázist, ugyanazokat a bejelentkezéseket is használhatja mindkét adatbázison. Az adatbázis másolásához használt rendszerbiztonsági tag lesz az adatbázis tulajdonosa az új adatbázisban. Minden adatbázis-felhasználó, a hozzá tartozó engedélyek és a biztonsági azonosítók (SID-ek) át lesznek másolva az adatbázis-másolatba.  

Ha egy másik SQL Database-kiszolgálóra másol egy adatbázist, az új kiszolgálón lévő rendszerbiztonsági tag lesz az adatbázis tulajdonosa az új adatbázisban. Ha [tárolt adatbázis-felhasználókat](sql-database-manage-logins.md) használ az adatokhoz való hozzáféréshez, győződjön meg arról, hogy mind az elsődleges, mind a másodlagos adatbázis ugyanazokkal a felhasználói hitelesítő adatokkal rendelkezik, így a másolás befejeződése után azonnal elérheti ugyanazokkal a hitelesítő adatokkal. 

Ha [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)használ, teljes mértékben kizárja a hitelesítő adatoknak a másolatban való kezelésének szükségességét. Ha azonban új kiszolgálóra másolja az adatbázist, előfordulhat, hogy a bejelentkezési alapú hozzáférés nem működik, mert a bejelentkezések nem léteznek az új kiszolgálón. Ha más SQL Database-kiszolgálóra másol egy adatbázist, a bejelentkezések kezelésével kapcsolatos további információkért lásd: [Az Azure SQL Database biztonságának kezelése](sql-database-geo-replication-security-config.md)a vész-helyreállítás után. 

A másolás sikeres és a többi felhasználó újraleképezése után csak a másolást kezdeményező bejelentkezést, az adatbázis tulajdonosát lehet bejelentkezni az új adatbázisba. A másolási művelet befejezését követően a bejelentkezések feloldásához [](#resolve-logins)tekintse meg a bejelentkezések feloldása című témakört.

## <a name="copy-a-database-by-using-the-azure-portal"></a>Adatbázis másolása a Azure Portal használatával

Ha a Azure Portal használatával szeretne másolni egy adatbázist, nyissa meg az adatbázis lapját, majd kattintson a **Másolás**gombra. 

   ![Adatbázis másolása](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Adatbázis másolása a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az adatbázis PowerShell használatával történő másolásához használja a [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) parancsmagot. 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

A teljes minta parancsfájlt itt tekintheti [meg: adatbázis másolása új kiszolgálóra](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Adatbázis másolása a Transact-SQL használatával

Jelentkezzen be a Master adatbázisba a kiszolgálói szintű rendszerbiztonsági tag bejelentkezési azonosítójával vagy a másolni kívánt adatbázist létrehozó bejelentkezéssel. Az adatbázis sikeres másolásához a kiszolgáló szintű rendszerbiztonsági tag nem a DBManager szerepkör tagjának kell lennie. További információ a bejelentkezésekről és a kiszolgálóhoz való csatlakozásról: [bejelentkezések kezelése](sql-database-manage-logins.md).

Az [adatbázis létrehozása](https://msdn.microsoft.com/library/ms176061.aspx) utasítással indítsa el a forrásadatbázis másolását. Az utasítás végrehajtása elindítja az adatbázis másolási folyamatát. Mivel az adatbázis másolása aszinkron folyamat, a CREATE DATABASE utasítás adja vissza az adatbázis másolásának befejeződése előtt.

### <a name="copy-a-sql-database-to-the-same-server"></a>SQL-adatbázis másolása ugyanarra a kiszolgálóra

Jelentkezzen be a Master adatbázisba a kiszolgálói szintű rendszerbiztonsági tag bejelentkezési azonosítójával vagy a másolni kívánt adatbázist létrehozó bejelentkezéssel. Az adatbázis sikeres másolásához a kiszolgáló szintű rendszerbiztonsági tag nem a DBManager szerepkör tagjának kell lennie.

Ez a parancs egy Adatbázis2 nevű új adatbázisba másolja a Adatbázis1 ugyanazon a kiszolgálón. Az adatbázis méretétől függően a másolási művelet végrehajtása hosszabb időt is igénybe vehet.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>SQL-adatbázis másolása másik kiszolgálóra

Jelentkezzen be a célkiszolgáló főadatbázisára, az SQL Database-kiszolgálóra, amelyen létre szeretné hozni az új adatbázist. Használjon olyan bejelentkezési azonosítót, amelynek a neve és jelszava megegyezik a forrás-SQL Database kiszolgálón található forrásadatbázis adatbázis-tulajdonosával. A célkiszolgálón a DBManager szerepkör tagjának kell lennie, vagy a kiszolgálói szintű rendszerbiztonsági tag bejelentkezési azonosítójának kell lennie.

Ez a parancs a Adatbázis1-t a Kiszolgáló1-ről egy új, Adatbázis2 nevű adatbázisra másolja a Kiszolgáló2-on. Az adatbázis méretétől függően a másolási művelet végrehajtása hosszabb időt is igénybe vehet.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> Mindkét kiszolgáló tűzfalát úgy kell konfigurálni, hogy engedélyezze a bejövő kapcsolatokat a T-SQL COPY parancsot kiállító ügyfél IP-címéről.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>SQL-adatbázis másolása másik előfizetésre

Az előző szakaszban ismertetett lépéseket követve másolhatja az adatbázist egy másik előfizetésben lévő SQL Database-kiszolgálóra. Győződjön meg arról, hogy olyan bejelentkezési azonosítót használ, amelynek a neve és jelszava megegyezik a forrásadatbázis adatbázis-tulajdonosával, és a DBManager szerepkör tagja, vagy a kiszolgáló szintű rendszerbiztonsági tag. 

> [!NOTE]
> A [Azure Portal](https://portal.azure.com) nem támogatja a másolást egy másik előfizetésre, mert a portál meghívja az ARM API-t, és az előfizetési tanúsítványokat használja a Geo-replikációban érintett mindkét kiszolgáló eléréséhez.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>A másolási művelet állapotának figyelése

Figyelje a másolási folyamatot a sys. Databases és a sys. DM _database_copies nézetek lekérdezésével. Amíg a másolás folyamatban van, az új adatbázis sys. Databases nézetének **state_desc** oszlopa másolásra van beállítva.

* Ha a másolás meghiúsul, az új adatbázis sys. Databases nézetének **state_desc** oszlopa a **gyanús**értékre van állítva. Hajtsa végre a DROP utasítást az új adatbázison, majd próbálkozzon újra később.
* Ha a másolás sikeres, az új adatbázis sys. Databases nézetének **state_desc** oszlopa **online**értékre van állítva. A másolás befejeződött, és az új adatbázis egy normál adatbázis, amely a forrás-adatbázistól függetlenül módosítható.

> [!NOTE]
> Ha úgy dönt, hogy megszakítja a másolást, amíg folyamatban van, hajtsa végre a [drop Database](https://msdn.microsoft.com/library/ms178613.aspx) utasítást az új adatbázison. Azt is megteheti, hogy a forrás-adatbázis DROP DATABASE utasításának végrehajtása megszakítja a másolási folyamatot.

## <a name="resolve-logins"></a>Bejelentkezések feloldása

Miután az új adatbázis online állapotú a célkiszolgálón, az [Alter User](https://msdn.microsoft.com/library/ms176060.aspx) utasítással újra felhasználhatja a felhasználókat az új adatbázisból a célkiszolgálón való bejelentkezéshez. Az árva felhasználók megoldásához tekintse meg az [árva felhasználók hibaelhárítása](https://msdn.microsoft.com/library/ms175475.aspx)című témakört. Lásd még: [Az Azure SQL Database biztonságának kezelése a vész-helyreállítás után](sql-database-geo-replication-security-config.md).

Az új adatbázisban lévő összes felhasználó megőrzi a forrás-adatbázisban lévő engedélyeket. Az adatbázis-másolatot kezdeményező felhasználó az új adatbázis adatbázis-tulajdonosa lesz, és új biztonsági azonosítót (SID) kap. A másolás sikeres és a többi felhasználó újraleképezése után csak a másolást kezdeményező bejelentkezést, az adatbázis tulajdonosát lehet bejelentkezni az új adatbázisba.

Ha egy másik SQL Database-kiszolgálóra másol egy adatbázist a felhasználók és a bejelentkezések kezelésével kapcsolatban, tekintse meg az [Azure SQL Database biztonságának kezelése a vész-helyreállítás után](sql-database-geo-replication-security-config.md)című témakört.

## <a name="next-steps"></a>További lépések

* További információ a bejelentkezésekről: [bejelentkezések kezelése](sql-database-manage-logins.md) és [Az Azure SQL Database biztonságának kezelése a vész-helyreállítás után](sql-database-geo-replication-security-config.md).
* Az adatbázisok exportálásával kapcsolatban tekintse meg [az adatbázis exportálása BACPAC](sql-database-export.md)című témakört.
