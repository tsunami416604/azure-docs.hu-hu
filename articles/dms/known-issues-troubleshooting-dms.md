---
title: Gyakori problémák - Azure adatbázis-áttelepítési szolgáltatás
description: További információ az Azure Database Migration Service használatával kapcsolatos gyakori ismert problémák és hibák elhárításáról.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: c5d2ad481124f5ae048d010cdf632ee661bbd6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649107"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Gyakori Azure-adatbázis-áttelepítési szolgáltatással kapcsolatos problémák és hibák elhárítása

Ez a cikk néhány gyakori problémát és hibát ismertet, amelyekkel az Azure Database Migration Service felhasználói találkozhatnak. A cikk a problémák és hibák megoldásával kapcsolatos információkat is tartalmaz.

## <a name="migration-activity-in-queued-state"></a>Áttelepítési tevékenység várakozási sorba állított állapotban

Amikor új tevékenységeket hoz létre egy Azure Database Migration Service projektben, a tevékenységek várólistára helyezett állapotban maradnak.

| Ok         | Megoldás: |
| ------------- | ------------- |
| Ez a probléma akkor fordul elő, ha az Azure Database Migration Service-példány elérte az egyidejűleg futó folyamatban lévő feladatok maximális kapacitását. Minden új tevékenység várólistára kerül, amíg a kapacitás elérhetővé nem válik. | Ellenőrizze, hogy az Adatáttelepítési szolgáltatás példánya több projekten keresztül futtatott tevékenységeket. Folytathatja az új tevékenységek létrehozását, amelyek automatikusan hozzáadnak a várólistához a végrehajtáshoz. Amint a meglévő futó tevékenységek bármelyike befejeződik, a következő várólistára helyezett tevékenység elindul, és az állapot automatikusan futó állapotra változik. A várólistás tevékenységek áttelepítésének megkezdéséhez nem kell további műveleteket végrehajtania.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Az áttelepítésre kijelölt adatbázisok maximális száma

A következő hiba akkor fordul elő, amikor egy adatbázis-áttelepítési projekthez létrehozott tevékenységet az Azure SQL Database vagy egy Azure SQL Database felügyelt példánya számára:

* **Hiba**: Áttelepítési beállítások érvényesítési hibája", "errorDetail":"Az "Adatbázisok" több mint maximális számú "4" objektuma lett kiválasztva az áttelepítéshez."

| Ok         | Megoldás: |
| ------------- | ------------- |
| Ez a hiba akkor jelenik meg, ha egy áttelepítési tevékenységhez négynél több adatbázist jelölt ki. Jelenleg minden áttelepítési tevékenység négy adatbázisra korlátozódik. | Áttelepítési tevékenységenként négy vagy kevesebb adatbázist jelöljön ki. Ha több mint négy adatbázist kell párhuzamosan áttelepítenie, az Azure Database Migration Service egy másik példányát. Jelenleg minden előfizetés legfeljebb két Azure Database Migration Service-példányt támogat.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Hibák a MySQL Azure MySQL-be való áttelepítéséhez helyreállítási hibákkal

Amikor az Azure Database Migration Service használatával áttér a MySQL-adatbázisból a MySQL Azure-adatbázisába, az áttelepítési tevékenység a következő hibával sikertelen:

* **Hiba**: Adatbázis-áttelepítési hiba - A "TaskID" feladat [n] egymást követő helyreállítási hibák miatt lett felfüggesztve.

| Ok         | Megoldás: |
| ------------- | ------------- |
| Ez a hiba akkor fordulhat elő, ha az áttelepítést lebonyolító felhasználóból hiányzik a ReplicationAdmin szerepkör és/vagy a REPLIKÁCIÓS ÜGYFÉL, a REPLICATION REPLICA és a SUPER (a MySQL 5.6.6-nál korábbi verziók) jogosultságai.<br><br><br><br><br><br><br><br><br><br><br><br><br> | Győződjön meg arról, hogy a felhasználói fiók [előfeltételi jogosultságai](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) pontosan vannak konfigurálva az Azure Database for MySQL-példányban. A következő lépések például a szükséges jogosultságokkal rendelkező "áttelepítésfelhasználó" nevű felhasználó létrehozásához a következő lépések követhetők:<br>1. CREATE USER migrateuser@'%' "titkos" azonosítója; <br>2. Adjon meg minden jogosultságot db_name.* a "titkos" által azonosított "migrateuser"@'%" számára; ismételje meg ezt a lépést, hogy hozzáférést biztosítson több adatbázishoz <br>3. Adjon replikációs szolga a *.* a "titkos" által azonosított "migrateuser'@'%"-ra;<br>4. Replikációs ügyfél megadása a *programon.* a "titkos" által azonosított "migrateuser'@'%"-ra;<br>5. Flush jogosultságok; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Hiba az Azure database migration service leállításakor

Az Azure Database Migration Service-példány leállításakor a következő hibaüzenet jelenik meg:

* **Hiba**: A szolgáltatás nem tudta leállítani. Hiba: {'error':{'code':'InvalidRequest','message':'Egy vagy több tevékenység jelenleg is fut. A szolgáltatás leállításához várjon, amíg a tevékenységek befejeződnek, vagy állítsa le ezeket a tevékenységeket manuálisan, majd próbálkozzon újra.'}}

| Ok         | Megoldás: |
| ------------- | ------------- |
| Ez a hiba akkor jelenik meg, ha a leállítani kívánt szolgáltatáspéldány olyan tevékenységeket tartalmaz, amelyek még futnak vagy jelen vannak az áttelepítési projektekben. <br><br><br><br><br><br> | Győződjön meg arról, hogy nincsenek futó tevékenységek az Azure Database Migration Service leállítani kívánt példányában. A szolgáltatás leállítása előtt törölheti a tevékenységeket vagy projekteket is. Az alábbi lépések bemutatják, hogyan távolíthatja el az áttelepítési szolgáltatáspéldány törléséhez szükséges projekteket az összes futó feladat törlésével:<br>1. Install-Module -Név AzureRM.DataMigration <br>2. Bejelentkezés-AzureRmAccountAccount <br>3. Select-AzureRmSubscription -SubscriptionName "\<alnév>" <br> 4. Remove-AzureRmDataMigrationProject \<-Name projectName> \<-ResourceGroupName \<rgName> -ServiceName szolgáltatásnév> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Hiba történt az Azure Database Migration Service indításakor

Az Azure Database Migration Service-példány indításakor a következő hibaüzenet jelenik meg:

* **Hiba**: A szolgáltatás nem indul el. Hiba: {'errorDetail':'A szolgáltatás nem tudott elindulni, forduljon a Microsoft támogatási szolgálatához'}

| Ok         | Megoldás: |
| ------------- | ------------- |
| Ez a hiba akkor jelenik meg, ha az előző példány belsőleg meghibásodott. Ez a hiba ritkán fordul elő, és a mérnöki csapat tisztában van vele. <br> | Törölje a szolgáltatás azon példányát, amelyet nem tud elindítani, majd újat kell kiépítenie a lecseréléséhez. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Hiba az adatbázis visszaállításakor az SQL Azure SQL DB felügyelt példányába való áttelepítése közben

Amikor online áttelepítést hajt végre az SQL Server kiszolgálóról egy Azure SQL Database által felügyelt példányra, az átállás a következő hibával sikertelen:

* **Hiba:** A visszaállítási művelet nem sikerült a "operationId" műveletazonosítóhoz. "AuthorizationFailed" kód, "Az "Ügyfélazonosító" "Az ügyfélazonosító" objektumazonosítóval nem rendelkezik felhatalmazással a "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read" művelet végrehajtására a '/subscriptions/subscriptionId' hatókörön keresztül.'.'.

| Ok         | Megoldás:    |
| ------------- | ------------- |
| Ez a hiba azt jelzi, hogy az SQL Serverről egy Azure SQL Database által felügyelt példányba történő online áttelepítéshez használt egyszerű alkalmazás nem rendelkezik hozzájárulási engedéllyel az előfizetéshez. Bizonyos API-hívások felügyelt példány jelenleg ehhez az engedélyhez az előfizetésa a visszaállítási művelethez. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Használja `Get-AzureADServicePrincipal` a PowerShell-parancsmag elérhető `-ObjectId` a hibaüzenetben, hogy sorolja fel a használt alkalmazásazonosító megjelenítendő nevét.<br><br> Ellenőrizze az alkalmazás engedélyeit, és győződjön meg arról, hogy az előfizetés szintjén rendelkezik a [közreműködői szerepkörsel.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) <br><br> Az Azure Database Migration Service Engineering Team azon dolgozik, hogy korlátozza a szükséges hozzáférést a jelenlegi közreműködési szerepkör előfizetés. Ha olyan üzleti követelménye van, amely nem teszi lehetővé a közreműködési szerepkör használatát, további segítségért forduljon az Azure-támogatáshoz. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Hiba az Azure Database Migration Service szolgáltatáshoz társított hálózati adapter törlésekor

Az Azure Database Migration Service szolgáltatáshoz társított hálózati illesztőkártya törlésekor a törlési kísérlet sikertelen lesz a következő hibával:

* **Hiba:** Az Azure Database Migration Service szolgáltatáshoz társított hálózati adapter nem törölhető a hálózati adaptert használó DMS-szolgáltatás miatt

| Ok         | Megoldás:    |
| ------------- | ------------- |
| Ez a probléma akkor fordul elő, ha az Azure Database Migration Service példány továbbra is jelen van, és a hálózati adapter t. <br><br><br><br><br><br><br><br> | A hálózati adapter törléséhez törölje azt a DMS-szolgáltatáspéldányt, amely automatikusan törli a szolgáltatás által használt hálózati adaptert.<br><br> **Fontos:** Győződjön meg arról, hogy az Azure Database Migration Service-példány törlése nem rendelkezik futó tevékenységekkel.<br><br> Miután az Azure Database Migration Service-példányhoz társított összes projekt és tevékenység törlődik, törölheti a szolgáltatáspéldányt. A szolgáltatáspéldány által használt hálózati adapter automatikusan törlődik a szolgáltatás törlésének részeként. |

## <a name="connection-error-when-using-expressroute"></a>Csatlakozási hibák az ExpressRoute használata esetében

Amikor az Azure Database Migration Service projektvarázslóban próbál csatlakozni a forráshoz, a csatlakozás időtúllépés miatt meghiúsul, ha a forrás az ExpressRoute-ot használja a csatlakozáshoz.

| Ok         | Megoldás:    |
| ------------- | ------------- |
| Az [ExpressRoute](https://azure.microsoft.com/services/expressroute/)használatakor az Azure Database Migration Service három szolgáltatásvégpont kiépítését [igényli](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) a szolgáltatáshoz társított virtuális hálózat alhálózaton:<br> -- Szolgáltatásbusz végpontja<br> -- Tárolási végpont<br> -- Cél adatbázis végpontja (pl. SQL végpont, Cosmos DB végpont)<br><br><br><br><br> | [Engedélyezze](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) a szükséges szolgáltatásvégpontokat a forrás és az Azure Database Migration Service közötti ExpressRoute-kapcsolathoz. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>A várakozási időmegtorolási hiba zárolása mySQL-adatbázisazure DB-re történő áttelepítésekor

Amikor egy MySQL-adatbázist az Azure Database Migration Service szolgáltatáson keresztül egy Azure Database for MySQL-példányba telepít át, az áttelepítés sikertelen lesz a következő zárolási várakozási időmeghagyási hibával:

* **Hiba**: Adatbázis-áttelepítési hiba - Nem sikerült betölteni a fájlt - Nem sikerült elindítani az 'n' RetCode fájl betöltési folyamatát: SQL_ERROR SqlState: HY000 NativeError: 1205 Üzenet: [MySQL][ODBC Driver][mysqld] Lock wait timeout exceeded; tranzakció újraindításának megkísérlése

| Ok         | Megoldás:    |
| ------------- | ------------- |
| Ez a hiba akkor fordul elő, ha az áttelepítés sikertelen, mert a zárolásvárakozási időmegtorás az áttelepítés során. | Fontolja meg a "innodb_lock_wait_timeout" kiszolgálóparaméter értékének **növelését.** A legnagyobb megengedett érték 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Hiba a forrás SQL Server kiszolgálóhoz való csatlakozáskor dinamikus port vagy elnevezett példány használatakor

Amikor az Azure Database Migration Service-t névvel ellátott példányon vagy dinamikus porton futó SQL Server-forráshoz próbálja csatlakoztatni, a kapcsolat a következő hibával sikertelen:

* **Hiba**: -1 - Az SQL-kapcsolat nem sikerült. Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el. Ellenőrizze, hogy a példányneve helyes-e, és hogy az SQL Server úgy van-e beállítva, hogy engedélyezze a távoli kapcsolatokat. (szolgáltató: SQL hálózati illesztők, hiba: 26 - Hiba a kiszolgáló/példány keresése)

| Ok         | Megoldás:    |
| ------------- | ------------- |
| Ez a probléma akkor fordul elő, ha a forrás SQL Server-példány, amely az Azure Database Migration Service megpróbál csatlakozni, vagy egy dinamikus port, vagy egy elnevezett példányt használ. Az SQL Server Browser szolgáltatás az 1434-es UDP-portot figyeli egy elnevezett példányhoz bejövő kapcsolatokra vagy dinamikus port használatakor. A dinamikus port az SQL Server szolgáltatás minden újraindításakor változhat. Az SQL Server Configuration Manager rendszerben az SQL Server Configuration Manager rendszerben hálózati konfiguráción keresztül egy példányhoz rendelt dinamikus portot ellenőrizheti.<br><br><br> |Ellenőrizze, hogy az Azure Database Migration Service képes-e csatlakozni a forrás SQL Server Browser szolgáltatáshoz az 1434-es UDP-porton és az SQL Server-példányon keresztül a dinamikusan hozzárendelt TCP-porton keresztül. |

## <a name="additional-known-issues"></a>További ismert problémák

* [Az Azure SQL Database-be való online áttelepítéssel kapcsolatos ismert problémák/áttelepítési korlátozások](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Ismert problémák/áttelepítési korlátozások az online áttelepítéssel kapcsolatban a MySQL Azure-adatbázisába](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Ismert problémák/áttelepítési korlátozások az online áttelepítéssel kapcsolatban a PostgreSQL Azure Database szolgáltatásba](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>További lépések

* Tekintse meg a cikket [az Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Tekintse meg a [kiszolgálóparamétereinek konfigurálása az Azure Database for MySQL-ben az Azure Portal használatával című cikket.](https://docs.microsoft.com/azure/mysql/howto-server-parameters)
* Tekintse meg a cikk [az Azure Database Migration Service használatának előfeltételeit](https://docs.microsoft.com/azure/dms/pre-reqs).
* Tekintse meg az [Azure Database Migration Service használatával kapcsolatos gyakori kérdéseket.](https://docs.microsoft.com/azure/dms/faq)
