---
title: Adatbázis másolása
description: Hozzon létre egy tranzakcióskonzisztens másolatot egy meglévő Azure SQL-adatbázisról ugyanazon a kiszolgálón vagy egy másik kiszolgálón.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 02/24/2020
ms.openlocfilehash: 7e4744627cfd08874e07bb126df048ea3e644f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473744"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Azure SQL-adatbázis tranzakciós konzisztens példányának másolása

Az Azure SQL Database számos módszert biztosít egy meglévő Azure SQL-adatbázis ([egyetlen adatbázis)](sql-database-single-database.md)tranzakciós konzisztens másolatának létrehozásához ugyanazon a kiszolgálón vagy egy másik kiszolgálón. Az SQL-adatbázisok másolása az Azure Portalon, a PowerShellen vagy a T-SQL-en keresztül.

## <a name="overview"></a>Áttekintés

Az adatbázis másolata a forrásadatbázis pillanatképe a másolási kérelem időpontjától. Kiválaszthatja ugyanazt a kiszolgálót vagy egy másik kiszolgálót. Is választhat, hogy megtartja a szolgáltatási szint és a számítási méret, vagy használjon egy másik számítási méretet ugyanazon a szolgáltatási szinten (kiadás). A másolat befejezése után teljesen működőképes, független adatbázissá válik. Ezen a ponton bármely kiadásra frissítheti vagy visszaminősítheti azt. A bejelentkezések, a felhasználók és az engedélyek egymástól függetlenül kezelhetők. A másolás a georeplikációs technológiával jön létre, és a vetés befejezése után a georeplikációs kapcsolat automatikusan megszakad. A georeplikáció használatának minden követelménye az adatbázis másolási műveletére vonatkozik. A részleteket az [Aktív georeplikáció áttekintése](sql-database-active-geo-replication.md) című témakörben találja.

> [!NOTE]
> [Az automatikus adatbázis-biztonsági mentések](sql-database-automated-backups.md) adatbázis-másolat létrehozásakor használatosak.

## <a name="logins-in-the-database-copy"></a>Bejelentkezések az adatbázisban másoló

Ha ugyanarra az SQL Database-kiszolgálóra másol egy adatbázist, mindkét adatbázisban ugyanazok a bejelentkezések használhatók. Az adatbázis másolásához használt rendszerbiztonsági tag lesz az új adatbázis adatbázis-tulajdonosa. 

Amikor egy adatbázist egy másik SQL Database-kiszolgálóra másol, a célkiszolgálón a másolási műveletet kezdeményező rendszerbiztonsági tag lesz az új adatbázis tulajdonosa. 

A célkiszolgálótól függetlenül az adatbázis minden felhasználója, engedélyei és biztonsági azonosítói (BIZTONSÁGI AZONOSÍTÓK) átkerülnek az adatbázis-másolatba. A [tartalmazott adatbázis-felhasználók](sql-database-manage-logins.md) adateléréshez való használata biztosítja, hogy a másolt adatbázis ugyanazokkal a felhasználói hitelesítő adatokkal rendelkezzen, így a másolat befejezése után azonnal hozzáférhet ugyanazzal a hitelesítő adattal.

Ha kiszolgálószintű bejelentkezéseket használ az adatok eléréséhez, és az adatbázist egy másik kiszolgálóra másolja, előfordulhat, hogy a bejelentkezési alapú hozzáférés nem működik. Ez azért fordulhat elő, mert a bejelentkezések nem léteznek a célkiszolgálón, vagy mert a jelszavaik és a biztonsági azonosítóik (SID-k) eltérőek. Ha tudni szeretné, hogy miként kezelheti a bejelentkezéseket, amikor egy másik SQL-adatbázis-kiszolgálóra másol egy adatbázist, olvassa el [az Azure SQL-adatbázis biztonságának kezelése a vészhelyreállítás után című témakört.](sql-database-geo-replication-security-config.md) Miután a másolási művelet egy másik kiszolgálóra sikeres, és mielőtt más felhasználók at újraleképeznék, csak az adatbázis tulajdonosához társított bejelentkezés vagy a kiszolgáló rendszergazdája jelentkezhet be a másolt adatbázisba. A bejelentkezések feloldásához és az adathozzáférés létrehozásához a másolási művelet befejezése után, olvassa el [a Bejelentkezések feloldása című témakört.](#resolve-logins)

## <a name="copy-a-database-by-using-the-azure-portal"></a>Adatbázis másolása az Azure Portal használatával

Ha az Azure Portalon keresztül szeretne adatbázist másolni, nyissa meg az adatbázis lapját, majd kattintson a **Másolás gombra.**

   ![Adatbázis másolni](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell-or-azure-cli"></a>Adatbázis másolása powershell vagy Azure CLI használatával

Adatbázis másolásához használja az alábbi példákat.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

A PowerShell, használja a [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) parancsmag.

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Az AzureRM-modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak. A kompatibilitásukról az [Új Azure PowerShell Az modul bemutatása](/powershell/azure/new-azureps-module-az)című témakörben lehet további további további információkért.

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Az adatbázis-másolat aszinkron művelet, de a céladatbázis a kérelem elfogadása után azonnal létrejön. Ha meg kell szakítania a másolási műveletet, miközben még folyamatban van, dobja el a céladatbázist az [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) parancsmag használatával.

A PowerShell-parancsfájl teljes mintájáról az [Adatbázis másolása új kiszolgálóra](scripts/sql-database-copy-database-to-new-server-powershell.md)( Teljes minta ) témakörben található.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Az adatbázis-másolat aszinkron művelet, de a céladatbázis a kérelem elfogadása után azonnal létrejön. Ha meg kell szakítania a másolási műveletet, miközben még folyamatban van, dobja el a céladatbázist az [az sql db delete](/cli/azure/sql/db#az-sql-db-delete) paranccsal.

* * *

## <a name="rbac-roles-to-manage-database-copy"></a>Az adatbázis-másolás kezelésére szolgáló RBAC-szerepkörök

Adatbázis-másolat létrehozásához a következő szerepkörökben kell lennie

- Előfizetés tulajdonosa vagy
- SQL Server közreműködői szerepkör vagy
- Egyéni szerepkör a forrás- és céladatbázisokon a következő engedélyekkel:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Az adatbázis-másolat megszakításához a következő szerepkörökben kell lennie

- Előfizetés tulajdonosa vagy
- SQL Server közreműködői szerepkör vagy
- Egyéni szerepkör a forrás- és céladatbázisokon a következő engedélyekkel:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Az Azure Portalon történő adatbázis-másolás kezeléséhez a következő engedélyekre is szüksége lesz:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

Ha meg szeretné tekinteni a műveleteket az erőforráscsoportban a portálon, műveletek több erőforrás-szolgáltatók, beleértve az SQL-műveleteket, szüksége lesz ezekre a további RBAC-szerepkörök:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="copy-a-database-by-using-transact-sql"></a>Adatbázis másolása a Transact-SQL használatával

Jelentkezzen be a fő adatbázisba a kiszolgáló rendszergazdájának bejelentkezésével vagy a másolni kívánt adatbázist létrehozó bejelentkezéssel. Az adatbázis-másolás sikeressedéséhez a nem kiszolgálórendszergazdának `dbmanager` tartozó bejelentkezéseknek a szerepkör tagjainak kell lenniük. A bejelentkezésekről és a kiszolgálóhoz való csatlakozásról a [Bejelentkezések kezelése című](sql-database-manage-logins.md)témakörben talál további információt.

A forrásadatbázis másolásának megkezdése a [CREATE DATABASE ...-val MINT A NYILATKOZAT MÁSOLATA.](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) A T-SQL utasítás az adatbázis-másolási művelet befejezéséig folytatódik.

> [!NOTE]
> A T-SQL utasítás leállítása nem szünteti meg az adatbázis-másolási műveletet. A művelet leállításához dobja el a céladatbázist.
>

### <a name="copy-a-sql-database-to-the-same-server"></a>SQL-adatbázis másolása ugyanarra a kiszolgálóra

Jelentkezzen be a fő adatbázisba a kiszolgáló rendszergazdájának bejelentkezésével vagy a másolni kívánt adatbázist létrehozó bejelentkezéssel. Az adatbázis másolásának sikeresvégrehajtásához a nem kiszolgálórendszergazdának tartozó `dbmanager` bejelentkezéseknek a szerepkör tagjainak kell lenniük.

Ez a parancs a Database1 adatbázist egy adatbázisba másolja, amelynek neve Database2 ugyanazon a kiszolgálón. Az adatbázis méretétől függően a másolási művelet befejezése eltarthat egy ideig.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>SQL-adatbázis másolása másik kiszolgálóra

Jelentkezzen be annak a célkiszolgálónak a fő adatbázisába, amelyen az új adatbázist létre kell hozni. Használjon olyan bejelentkezési nevet és jelszót, amely nek ugyanaz a neve és a jelszava, mint a forráskiszolgálón lévő forrásadatbázis-tulajdonosnak. A célkiszolgálón lévő bejelentkezésnek a `dbmanager` szerepkör tagjának kell lennie, vagy a kiszolgáló rendszergazdájának kell lennie.

Ez a parancs a Database1 kiszolgálón1-et egy adatbázisba másolja, amelynek neve Database2 a kiszolgálón2. Az adatbázis méretétől függően a másolási művelet befejezése eltarthat egy ideig.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Mindkét kiszolgáló tűzfalait úgy kell konfigurálni, hogy a T-SQL CREATE DATABASE-t kibocsátó ügyfél IP-címéről bejövő kapcsolat legyen... Mint a parancs másolata.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>SQL-adatbázis másolása másik előfizetésbe

Az [SQL-adatbázis másolása egy másik kiszolgálóra](#copy-a-sql-database-to-a-different-server) szakasz lépéseisegítségével másolhatja az adatbázist egy másik előfizetésben lévő SQL Database-kiszolgálóra a T-SQL használatával. Győződjön meg arról, hogy olyan bejelentkezési nevet és jelszót használ, amelynek neve és jelszava megegyezik a forrásadatbázis adatbázis-tulajdonosával. Ezenkívül a bejelentkezésnek a szerepkör `dbmanager` vagy a kiszolgáló rendszergazdájának kell lennie mind a forrás-, mind a célkiszolgálókon.

> [!NOTE]
> Az [Azure Portalon](https://portal.azure.com), a PowerShell és az Azure CLI nem támogatja az adatbázis-másolást egy másik előfizetéshez.

### <a name="monitor-the-progress-of-the-copying-operation"></a>A másolási művelet előrehaladásának figyelése

A [sys.databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)és [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) nézetek lekérdezésével figyelheti a másolási folyamatot. Amíg a másolás folyamatban van, az új adatbázis sys.databases nézetének **state_desc** oszlopa másolásra van **állítva.**

* Ha a másolás sikertelen, az új adatbázis sys.databases.databases nézetének **state_desc** oszlopa **GYANÚS**. Hajtsa végre a DROP utasítást az új adatbázisban, majd próbálkozzon később.
* Ha a másolás sikeres, az új adatbázis sys.databases nézetének **state_desc** oszlopa **online**lesz állítva. A másolás befejeződött, és az új adatbázis egy rendszeres adatbázis, amely a forrásadatbázistól függetlenül módosítható.

> [!NOTE]
> Ha úgy dönt, hogy megszakítja a másolást, miközben az folyamatban van, hajtsa végre a [DROP DATABASE](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) utasítást az új adatbázison.

> [!IMPORTANT]
> Ha a forrásnál lényegesen kisebb szolgáltatáscélkitűzéssel rendelkező másolatot kell létrehoznia, előfordulhat, hogy a céladatbázis nem rendelkezik elegendő erőforrással a vetési folyamat befejezéséhez, és a másolási művelet sikertelensítheti le. Ebben az esetben használjon geo-visszaállítási kérelmet egy másolatot egy másik kiszolgálón és/vagy egy másik régióban. További információkért [lásd: Azure SQL-adatbázis helyreállítása adatbázis-biztonsági mentések használatával.](sql-database-recovery-using-backups.md#geo-restore)

## <a name="resolve-logins"></a>Bejelentkezések feloldása

Miután az új adatbázis online állapotba került a célkiszolgálón, az [ALTER USER](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) utasítás segítségével rendelje át a felhasználókat az új adatbázisból a célkiszolgálón lévő bejelentkezésekhez. Az árva felhasználók feloldásáról az [Árva felhasználók – Hibaelhárítás](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server)című témakörben van. Lásd [még: Az Azure SQL-adatbázis biztonságának kezelése a vészhelyreállítás után.](sql-database-geo-replication-security-config.md)

Az új adatbázis minden felhasználója megőrzi a forrásadatbázisban lévő engedélyeket. Az adatbázis-másolatot kezdeményező felhasználó lesz az új adatbázis tulajdonosa. Miután a másolás sikeres volt, és mielőtt a többi felhasználót újra leképezne, csak az adatbázis tulajdonosa jelentkezhet be az új adatbázisba.

Ha tudni szeretné, hogy miként kezelheti a felhasználókat és a bejelentkezéseket, amikor egy másik SQL-adatbázis-kiszolgálóra másol egy adatbázist, olvassa el [az Azure SQL-adatbázis biztonságának kezelése a vészhelyreállítás után című témakört.](sql-database-geo-replication-security-config.md)

## <a name="database-copy-errors"></a>Adatbázis másolási hibái

A következő hibák merülhetnek fel az Azure SQL Database-ben lévő adatbázis másolása közben. További információk az [Azure SQL-adatbázis másolása](sql-database-copy.md) című részben.

| Hibakód | Severity | Leírás |
| ---:| ---:|:--- |
| 40635 |16 |A(z) '%.&#x2a;ls' IP-címmel rendelkező ügyfél ideiglenesen le van tiltva. |
| 40637 |16 |Az adatbázis-másolat létrehozása jelenleg le van tiltva. |
| 40561 |16 |Az adatbázis másolása nem sikerült. A forrás- vagy céladatbázis nem létezik. |
| 40562 |16 |Az adatbázis másolása nem sikerült. A forrásadatbázis el lett dobva. |
| 40563 |16 |Az adatbázis másolása nem sikerült. A céladatbázis el lett dobva. |
| 40564 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. A céladatbázist dobja el, majd próbálkozzon újra. |
| 40565 |16 |Az adatbázis másolása nem sikerült. Ugyanabból a forrásból legfeljebb 1 egyidejű adatbázis-másolás engedélyezett. A céladatbázist eldobja, majd próbálkozzon később. |
| 40566 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. A céladatbázist dobja el, majd próbálkozzon újra. |
| 40567 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. A céladatbázist dobja el, majd próbálkozzon újra. |
| 40568 |16 |Az adatbázis másolása nem sikerült. A forrásadatbázis elérhetetlenné vált. A céladatbázist dobja el, majd próbálkozzon újra. |
| 40569 |16 |Az adatbázis másolása nem sikerült. A céladatbázis elérhetetlenné vált. A céladatbázist dobja el, majd próbálkozzon újra. |
| 40570 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. A céladatbázist eldobja, majd próbálkozzon később. |
| 40571 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. A céladatbázist eldobja, majd próbálkozzon később. |

## <a name="next-steps"></a>További lépések

- A bejelentkezésekről a [Bejelentkezések kezelése](sql-database-manage-logins.md) és [az Azure SQL-adatbázis biztonságának kezelése a vészhelyreállítás után című](sql-database-geo-replication-security-config.md)témakörben talál.
- Adatbázis exportálásához olvassa el [Az adatbázis exportálása BACPAC-fájlba](sql-database-export.md)című témakört.
