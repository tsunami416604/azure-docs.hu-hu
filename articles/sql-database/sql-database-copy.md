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
ms.author: sashan
ms.reviewer: carlrab
ms.date: 09/04/2019
ms.openlocfilehash: de56e66046bb61ac31c1842ae6ce7a9c6720760d
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934205"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Azure SQL Database-adatbázis tranzakciós szempontból konzisztens másolatának másolása

Azure SQL Database számos módszert biztosít egy meglévő Azure SQL Database-adatbázis ([önálló adatbázis](sql-database-single-database.md)) tranzakciós szempontból konzisztens másolatának létrehozásához ugyanazon a kiszolgálón vagy egy másik kiszolgálón. SQL-adatbázist a Azure Portal, a PowerShell vagy a T-SQL használatával másolhat. 

## <a name="overview"></a>Áttekintés

A másolási kérelem időpontját tartalmazó adatbázis-másolat a forrásadatbázis pillanatképe. Ugyanezt a kiszolgálót vagy egy másik kiszolgálót is kiválaszthatja. Azt is megteheti, hogy megtartja a szolgáltatási szintet és a számítási méretet, vagy eltérő számítási méretet használ ugyanazon a szolgáltatási szinten (kiadás) belül. A másolás befejezése után teljesen működőképes, független adatbázis lesz. Ezen a ponton bármely kiadásra frissítheti vagy visszaminősítheti azt. A bejelentkezések, a felhasználók és az engedélyek egymástól függetlenül kezelhetők. A másolás a Geo-replikációs technológiával jön létre, és a kivetés befejezése után a rendszer automatikusan leállítja a Geo-replikálási hivatkozást. A Geo-replikáció használatának összes követelménye az adatbázis-másolási műveletre vonatkozik. Részletekért lásd: az [aktív geo-replikáció áttekintése](sql-database-active-geo-replication.md) .

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

Az adatbázis másolása aszinkron művelet, de a céladatbázis közvetlenül a kérelem elfogadása után jön létre. Ha még folyamatban van a másolási művelet megszakítása, a [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) parancsmag használatával dobja el a céladatbázis-adatbázist.  

## <a name="rbac-roles-to-manage-database-copy"></a>RBAC szerepkörök az adatbázis-másolat kezeléséhez

Adatbázis-másolat létrehozásához a következő szerepköröket kell megadnia

- Előfizetés tulajdonosa vagy
- SQL Server közreműködő szerepkör vagy
- Egyéni szerepkör a forrás-és a célként megadott adatbázisokhoz a következő engedélyekkel:

   Microsoft.Sql/servers/databases/read   
   Microsoft.Sql/servers/databases/write   

Az adatbázis-másolatok megszakításához a következő szerepköröket kell megadnia

- Előfizetés tulajdonosa vagy
- SQL Server közreműködő szerepkör vagy
- Egyéni szerepkör a forrás-és a célként megadott adatbázisokhoz a következő engedélyekkel:

   Microsoft.Sql/servers/databases/read   
   Microsoft.Sql/servers/databases/write   
   
Az adatbázis-másolat Azure Portal használatával történő kezeléséhez a következő engedélyekre is szüksége lesz:

&nbsp;&nbsp; Microsoft.Resources/előfizetések&nbsp; /erőforrások/olvasás   
&nbsp;&nbsp; Microsoft.Resources/Subscriptions/&nbsp; Resources/Write   
&nbsp;&nbsp; Microsoft.Resources/&nbsp; központi telepítések/olvasás   
&nbsp;&nbsp; Microsoft.Resources&nbsp; /üzembe helyezés/írás   
&nbsp;&nbsp; Microsoft.Resources/üzembe&nbsp; helyezés/operationstatuses/olvasás    

Ha szeretné megtekinteni a központi telepítések alatt lévő műveleteket a portálon található erőforráscsoporthoz, több erőforrás-szolgáltató műveletei, beleértve az SQL-műveleteket, szüksége lesz ezekre a további RBAC szerepkörökre: 

&nbsp;&nbsp; Microsoft.erőforrások/előfizetések/resourcegroups/üzembehelyezések&nbsp; /műveletek/olvasás   
&nbsp;&nbsp; Microsoft.Resources/előfizetések/resourcegroups/üzembe&nbsp; helyezés/operationstatuses/olvasás



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

Figyelje a másolási folyamatot a sys. Databases és a sys. DM _database_copies nézetek lekérdezésével. Amíg a másolás folyamatban van, az új adatbázis sys. Databases nézetének **state_desc** oszlopa **másolásra**van beállítva.

* Ha a másolás meghiúsul, az új adatbázis sys. Databases nézetének **state_desc** oszlopa a **gyanús**értékre van állítva. Hajtsa végre a DROP utasítást az új adatbázison, majd próbálkozzon újra később.
* Ha a másolás sikeres, az új adatbázis sys. Databases nézetének **state_desc** oszlopa **online**értékre van állítva. A másolás befejeződött, és az új adatbázis egy normál adatbázis, amely a forrás-adatbázistól függetlenül módosítható.

> [!NOTE]
> Ha úgy dönt, hogy megszakítja a másolást, amíg folyamatban van, hajtsa végre a [drop Database](https://msdn.microsoft.com/library/ms178613.aspx) utasítást az új adatbázison. Azt is megteheti, hogy a forrás-adatbázis DROP DATABASE utasításának végrehajtása megszakítja a másolási folyamatot.

> [!IMPORTANT]
> Ha olyan másolatot kell létrehoznia, amely lényegesen kisebb SLO-val rendelkezik, mint a forrás, akkor előfordulhat, hogy a céladatbázis nem rendelkezik elegendő erőforrással a kiindulási folyamat befejezéséhez, és a másolási Opera meghibásodásához vezethet. Ebben a forgatókönyvben egy geo-visszaállítási kérelem használatával hozzon létre egy másolatot egy másik kiszolgálón és/vagy egy másik régióban. További információ: [Azure SQL Database helyreállítása adatbázis-másolatok használatával](sql-database-recovery-using-backups.md#geo-restore) .


## <a name="resolve-logins"></a>Bejelentkezések feloldása

Miután az új adatbázis online állapotú a célkiszolgálón, az [Alter User](https://msdn.microsoft.com/library/ms176060.aspx) utasítással újra felhasználhatja a felhasználókat az új adatbázisból a célkiszolgálón való bejelentkezéshez. Az árva felhasználók megoldásához tekintse meg az [árva felhasználók hibaelhárítása](https://msdn.microsoft.com/library/ms175475.aspx)című témakört. Lásd még: [Az Azure SQL Database biztonságának kezelése a vész-helyreállítás után](sql-database-geo-replication-security-config.md).

Az új adatbázisban lévő összes felhasználó megőrzi a forrás-adatbázisban lévő engedélyeket. Az adatbázis-másolatot kezdeményező felhasználó az új adatbázis adatbázis-tulajdonosa lesz, és új biztonsági azonosítót (SID) kap. A másolás sikeres és a többi felhasználó újraleképezése után csak a másolást kezdeményező bejelentkezést, az adatbázis tulajdonosát lehet bejelentkezni az új adatbázisba.

Ha egy másik SQL Database-kiszolgálóra másol egy adatbázist a felhasználók és a bejelentkezések kezelésével kapcsolatban, tekintse meg az [Azure SQL Database biztonságának kezelése a vész-helyreállítás után](sql-database-geo-replication-security-config.md)című témakört.

## <a name="next-steps"></a>További lépések

* További információ a bejelentkezésekről: [bejelentkezések kezelése](sql-database-manage-logins.md) és [Az Azure SQL Database biztonságának kezelése a vész-helyreállítás után](sql-database-geo-replication-security-config.md).
* Az adatbázisok exportálásával kapcsolatban tekintse meg [az adatbázis exportálása BACPAC](sql-database-export.md)című témakört.
