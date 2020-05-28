---
title: Adatbázis másolása
description: Hozzon létre tranzakciós szempontból konzisztens másolatot egy meglévő adatbázisról Azure SQL Database ugyanazon a kiszolgálón vagy egy másik kiszolgálón.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 02/24/2020
ms.openlocfilehash: d92882014f66234be8a8b1d7063dae866ec6f230
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045297"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Adatbázis tranzakciós szempontból konzisztens másolatának másolása Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database számos módszert biztosít egy meglévő [adatbázis](single-database-overview.md) tranzakciós szempontból konzisztens másolatának létrehozásához ugyanazon a kiszolgálón vagy egy másik kiszolgálón. Az adatbázist a Azure Portal, a PowerShell vagy a T-SQL használatával másolhatja.

## <a name="overview"></a>Áttekintés

A másolási kérelem időpontját tartalmazó adatbázis-másolat a forrásadatbázis pillanatképe. Ugyanezt a kiszolgálót vagy egy másik kiszolgálót is kiválaszthatja. Azt is megteheti, hogy megtartja a szolgáltatási szintet és a számítási méretet, vagy eltérő számítási méretet használ ugyanazon a szolgáltatási szinten (kiadás) belül. A másolás befejezése után teljesen működőképes, független adatbázis lesz. Ezen a ponton bármely kiadásra frissítheti vagy visszaminősítheti azt. A bejelentkezések, a felhasználók és az engedélyek egymástól függetlenül kezelhetők. A másolás a Geo-replikációs technológiával jön létre, és a kivetés befejezése után a rendszer automatikusan leállítja a Geo-replikálási hivatkozást. A Geo-replikáció használatának összes követelménye az adatbázis-másolási műveletre vonatkozik. Részletekért lásd: az [aktív geo-replikáció áttekintése](active-geo-replication-overview.md) .

> [!NOTE]
> Az adatbázisok [automatikus biztonsági mentése](automated-backups-overview.md) az adatbázis-másolat létrehozásakor használatos.

## <a name="logins-in-the-database-copy"></a>Bejelentkezések az adatbázis-másolatban

Ha ugyanarra a kiszolgálóra másol egy adatbázist, ugyanazokat a bejelentkezéseket is használhatja mindkét adatbázison. Az adatbázis másolásához használt rendszerbiztonsági tag lesz az adatbázis tulajdonosa az új adatbázisban.

Ha egy adatbázist egy másik kiszolgálóra másol, akkor a célkiszolgálón a másolási műveletet kezdeményező rendszerbiztonsági tag lesz az új adatbázis tulajdonosa.

A célkiszolgálótől függetlenül a rendszer az összes adatbázis-felhasználót, az engedélyeiket és a biztonsági azonosítókat (SID-ket) az adatbázis-másolatba másolja. A [tárolt adatbázis-felhasználók](logins-create-manage.md) adathozzáféréshez való használata biztosítja, hogy a másolt adatbázis ugyanazzal a felhasználói hitelesítő adatokkal rendelkezik, így a másolás befejezése után azonnal elérheti ugyanazokkal a hitelesítő adatokkal.

Ha kiszolgálói szintű bejelentkezést használ az adatok eléréséhez, és az adatbázist egy másik kiszolgálóra másolja, előfordulhat, hogy a bejelentkezési alapú hozzáférés nem működik. Ez azért fordulhat elő, mert a bejelentkezési adatok nem léteznek a célkiszolgálón, vagy mert a jelszavuk és a biztonsági azonosítóik (SID) eltérnek. Ha egy adatbázis másik kiszolgálóra való másolásakor szeretné megtudni a bejelentkezések kezelését, olvassa el a [Azure SQL Database biztonság kezelése](active-geo-replication-security-configure.md)a vész-helyreállítás után című témakört. Miután a másolási művelet egy másik kiszolgálóra sikeres, és a többi felhasználó újraleképezése előtt, csak az adatbázis-tulajdonoshoz tartozó bejelentkezési azonosító, vagy a kiszolgáló rendszergazdája jelentkezhet be a másolt adatbázisba. A bejelentkezések feloldásához és az adathozzáférés létrehozásához a másolási művelet befejezése után tekintse meg a [bejelentkezések feloldása](#resolve-logins)című témakört.

## <a name="copy-using-the-azure-portal"></a>Másolás az Azure Portal használatával

Ha a Azure Portal használatával szeretne másolni egy adatbázist, nyissa meg az adatbázis lapját, majd kattintson a **Másolás**gombra.

   ![Adatbázis másolása](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Másolás a PowerShell vagy az Azure CLI használatával

Adatbázis másolásához használja az alábbi példákat.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell esetében használja a [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) parancsmagot.

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager (RM) modult, de a jövőbeli fejlesztés az az. SQL modulhoz kapcsolódik. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Az adatbázis másolása aszinkron művelet, de a céladatbázis közvetlenül a kérelem elfogadása után jön létre. Ha még folyamatban van a másolási művelet megszakítása, a [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) parancsmag használatával dobja el a céladatbázis-adatbázist.

A teljes PowerShell-parancsfájlhoz lásd: [adatbázis másolása új kiszolgálóra](scripts/copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Az adatbázis másolása aszinkron művelet, de a céladatbázis közvetlenül a kérelem elfogadása után jön létre. Ha még folyamatban van a másolási művelet megszakítása, dobja el a célként megadott adatbázist az az [SQL db delete](/cli/azure/sql/db#az-sql-db-delete) paranccsal.

* * *

## <a name="copy-using-transact-sql"></a>Másolás Transact-SQL használatával

Jelentkezzen be a Master adatbázisba a kiszolgáló-rendszergazdai bejelentkezéssel vagy a másolni kívánt adatbázist létrehozó bejelentkezéssel. Az adatbázis sikeres másolásához a kiszolgáló rendszergazdájának nem a szerepkör tagjának kell lennie `dbmanager` . További információ a bejelentkezésekről és a kiszolgálóhoz való csatlakozásról: [bejelentkezések kezelése](logins-create-manage.md).

A forrásadatbázis másolásának megkezdése az [adatbázis létrehozása... AZ utasítás MÁSOLATa](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) . A T-SQL-utasítás addig fut, amíg az adatbázis-másolási művelet be nem fejeződik.

> [!NOTE]
> A T-SQL-utasítás leállítása nem szakítja meg az adatbázis-másolási műveletet. A művelet befejezéséhez dobja el a céladatbázis-adatbázist.
>

### <a name="copy-to-the-same-server"></a>Másolás ugyanarra a kiszolgálóra

Jelentkezzen be a Master adatbázisba a kiszolgáló-rendszergazdai bejelentkezéssel vagy a másolni kívánt adatbázist létrehozó bejelentkezéssel. Az adatbázis sikeres másolásához a kiszolgáló rendszergazdájának nem a szerepkör tagjának kell lennie `dbmanager` .

Ez a parancs egy Adatbázis2 nevű új adatbázisba másolja a Adatbázis1 ugyanazon a kiszolgálón. Az adatbázis méretétől függően a másolási művelet végrehajtása hosszabb időt is igénybe vehet.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-a-different-server"></a>Másolás másik kiszolgálóra

Jelentkezzen be az új adatbázist létrehozó célkiszolgáló főadatbázisába. Használjon olyan bejelentkezési azonosítót, amelynek a neve és jelszava megegyezik a forráskiszolgálón lévő forrásadatbázis adatbázis-tulajdonosával. A célkiszolgálón a szerepkör tagjának kell lennie `dbmanager` , vagy a kiszolgáló-rendszergazdai bejelentkezési azonosítónak kell lennie.

Ez a parancs a Adatbázis1-t a Kiszolgáló1-ről egy új, Adatbázis2 nevű adatbázisra másolja a Kiszolgáló2-on. Az adatbázis méretétől függően a másolási művelet végrehajtása hosszabb időt is igénybe vehet.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Mindkét kiszolgáló tűzfalát úgy kell konfigurálni, hogy engedélyezze a bejövő kapcsolatokat a T-SQL CREATE ADATBÁZIST kiállító ügyfél IP-címéről... A parancs MÁSOLATa.

### <a name="copy-to-a-different-subscription"></a>Másolás másik előfizetésre

A [SQL Database másolása másik kiszolgálóra](#copy-to-a-different-server) szakasz lépéseit követve másolja az adatbázist egy másik előfizetésben található kiszolgálóra a T-SQL használatával. Győződjön meg arról, hogy olyan bejelentkezési azonosítót használ, amelynek a neve és jelszava megegyezik a forrásadatbázis adatbázis-tulajdonosával. Emellett a bejelentkezésnek a szerepkör vagy a kiszolgáló rendszergazdája tagjának kell lennie a `dbmanager` forrás-és a célkiszolgálón is.

> [!NOTE]
> A [Azure Portal](https://portal.azure.com), a PowerShell és az Azure CLI nem támogatja az adatbázis másolását egy másik előfizetésre.

## <a name="monitor-the-progress-of-the-copying-operation"></a>A másolási művelet állapotának figyelése

Figyelje a másolási folyamatot a [sys. Databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys. dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)és [sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) nézetek lekérdezésével. A másolás folyamatban van, az új adatbázis sys. Databases nézetének **state_desc** oszlopa **másolásra**van beállítva.

* Ha a másolás meghiúsul, az új adatbázis sys. Databases nézetének **state_desc** oszlopa **gyanúsnak**van beállítva. Hajtsa végre a DROP utasítást az új adatbázison, majd próbálkozzon újra később.
* Ha a másolás sikeres, az új adatbázis sys. Databases nézetének **state_desc** oszlopa **online**értékre van állítva. A másolás befejeződött, és az új adatbázis egy normál adatbázis, amely a forrás-adatbázistól függetlenül módosítható.

> [!NOTE]
> Ha úgy dönt, hogy megszakítja a másolást, amíg folyamatban van, hajtsa végre a [drop Database](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) utasítást az új adatbázison.

> [!IMPORTANT]
> Ha olyan másolatot kell létrehoznia, amely lényegesen kisebb szolgáltatási céllal rendelkezik, mint a forrás, akkor előfordulhat, hogy a céladatbázis nem rendelkezik elegendő erőforrással a kiindulási folyamat befejezéséhez, és a másolási Opera meghibásodását okozhatja. Ebben a forgatókönyvben egy geo-visszaállítási kérelem használatával hozzon létre egy másolatot egy másik kiszolgálón és/vagy egy másik régióban. További információ: [Azure SQL Database helyreállítása az adatbázis biztonsági mentései segítségével](recovery-using-backups.md#geo-restore) .

## <a name="rbac-roles-to-manage-database-copy"></a>RBAC szerepkörök az adatbázis-másolat kezeléséhez

Adatbázis-másolat létrehozásához a következő szerepköröket kell megadnia

* Előfizetés tulajdonosa vagy
* SQL Server közreműködő szerepkör vagy
* Egyéni szerepkör a forrás-és a célként megadott adatbázisokhoz a következő engedélyekkel:

   Microsoft. SQL/kiszolgálók/adatbázisok/olvasás Microsoft. SQL/kiszolgálók/adatbázisok/írás

Az adatbázis-másolatok megszakításához a következő szerepköröket kell megadnia

* Előfizetés tulajdonosa vagy
* SQL Server közreműködő szerepkör vagy
* Egyéni szerepkör a forrás-és a célként megadott adatbázisokhoz a következő engedélyekkel:

   Microsoft. SQL/kiszolgálók/adatbázisok/olvasás Microsoft. SQL/kiszolgálók/adatbázisok/írás

Az adatbázis-másolat Azure Portal használatával történő kezeléséhez a következő engedélyekre is szüksége lesz:

   Microsoft. Resources/Subscriptions/Resources/Read Microsoft. Resources/Subscriptions/Resources/Write Microsoft. Resources/központi telepítések/olvasás Microsoft. erőforrások/központi telepítések/írás Microsoft. erőforrások/üzembe helyezés/operationstatuses/olvasás

Ha szeretné megtekinteni a központi telepítések alatt lévő műveleteket a portálon található erőforráscsoporthoz, több erőforrás-szolgáltató műveletei, beleértve az SQL-műveleteket, szüksége lesz ezekre a további RBAC szerepkörökre:

   Microsoft. Resources/Subscriptions/resourcegroups/Deployments/Operations/Read Microsoft. Resources/előfizetések/resourcegroups/üzembe helyezés/operationstatuses/olvasás

## <a name="resolve-logins"></a>Bejelentkezések feloldása

Miután az új adatbázis online állapotú a célkiszolgálón, az [Alter User](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) utasítással újra felhasználhatja a felhasználókat az új adatbázisból a célkiszolgálón való bejelentkezéshez. Az árva felhasználók megoldásához tekintse meg az [árva felhasználók hibaelhárítása](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server)című témakört. Lásd még: [Hogyan lehet felügyelni a Azure SQL Database biztonsági mentést a katasztrófa utáni helyreállítás után](active-geo-replication-security-configure.md).

Az új adatbázisban lévő összes felhasználó megőrzi a forrás-adatbázisban lévő engedélyeket. Az adatbázis-példányt kezdeményező felhasználó az új adatbázis adatbázis-tulajdonosa lesz. A másolás sikeres és a többi felhasználó újraleképezése után csak az adatbázis tulajdonosa jelentkezhet be az új adatbázisba.

Ha egy adatbázist egy másik kiszolgálóra másol, a felhasználók és bejelentkezések kezelésével kapcsolatos további információkért lásd: [a Azure SQL Database biztonság kezelése a vész-helyreállítás után](active-geo-replication-security-configure.md).

## <a name="database-copy-errors"></a>Adatbázis-másolási hibák

A következő hibák fordulhatnak elő az adatbázisok Azure SQL Databaseban történő másolása során. További információk az [Azure SQL-adatbázis másolása](database-copy.md) című részben.

| Hibakód | Severity | Leírás |
| ---:| ---:|:--- |
| 40635 |16 |A (z)%. &#x2a;ls IP-címmel rendelkező ügyfél átmenetileg le van tiltva. |
| 40637 |16 |Az adatbázis-másolat létrehozása jelenleg le van tiltva. |
| 40561 |16 |Az adatbázis másolása sikertelen volt. A forrás-vagy a céladatbázis nem létezik. |
| 40562 |16 |Az adatbázis másolása sikertelen volt. A forrásadatbázis el lett dobva. |
| 40563 |16 |Az adatbázis másolása sikertelen volt. A célként megadott adatbázis el lett dobva. |
| 40564 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra. |
| 40565 |16 |Az adatbázis másolása sikertelen volt. Nem engedélyezett több mint 1 egyidejű adatbázis-másolat ugyanabból a forrásból. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra később. |
| 40566 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra. |
| 40567 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra. |
| 40568 |16 |Az adatbázis másolása sikertelen volt. A forrásadatbázis elérhetetlenné vált. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra. |
| 40569 |16 |Az adatbázis másolása sikertelen volt. A céladatbázis elérhetetlenné vált. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra. |
| 40570 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra később. |
| 40571 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra később. |

## <a name="next-steps"></a>További lépések

* További információ a bejelentkezésekről: a [bejelentkezések kezelése](logins-create-manage.md) és [a Azure SQL Database biztonság kezelése a vész-helyreállítás után](active-geo-replication-security-configure.md).
* Az adatbázisok exportálásával kapcsolatban tekintse meg [az adatbázis exportálása BACPAC](database-export.md)című témakört.
