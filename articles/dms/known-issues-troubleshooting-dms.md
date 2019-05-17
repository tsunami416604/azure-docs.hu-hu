---
title: Kapcsolatos ismert gyakori problémák/hibák az Azure Database Migration Service segítségével társított hibaelhárítási cikk |} A Microsoft Docs
description: Ismerje meg hogyan háríthatók el a gyakori ismert problémák/hibák társított Azure Database Migration Service segítségével.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: dc8ba315d08f3a130ff0adf91afc90f545baf4e4
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604435"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Gyakori Azure Database Migration Service-problémák és hibák elhárítása

Ez a cikk azt ismerteti, néhány gyakori problémák és hibák, amelyek az Azure Database Migration Service-felhasználók között. A cikk ezeket a problémákat és hibákat megoldásával kapcsolatos információkat is tartalmaz.

## <a name="migration-activity-in-queued-state"></a>Az áttelepítési tevékenység várólistára

Az Azure Database Migration Service-projekt létrehozásakor új tevékenységek a tevékenységek várólistára helyezett állapot maradnak.

| Ok         | Feloldás |
| ------------- | ------------- |
| Ez a hiba akkor fordul elő, amikor az Azure Database Migration Service-példány elérte a maximális kapacitás, a folyamatban lévő feladatok, amelyek egy időben futnak. Minden olyan új tevékenység várólistára van állítva, a kapacitás amíg elérhetővé nem válik. | Ellenőrizze a Data Migration Service példányhoz tartozik a tevékenységek futtatása a projektek között. Továbbra is hozhat létre új tevékenységeket, amelyek automatikusan hozzáadja a végrehajtási várólistára. Amint a meglévő futó tevékenységek bármelyike befejezéséhez, a következő sorban álló tevékenység elindul, és állapotra vált, automatikusan futó állapotban. Nincs szükségünk, indítsa el áttelepítési sorban álló tevékenységek további teendője.<br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Migrálásra kijelölt adatbázisok maximális száma

A következő hiba akkor fordul elő, amikor a tevékenység áthelyezése az Azure SQL Database vagy az Azure SQL Database egy adatbázis-migrálási projektje létrehozása felügyelt példány:

* **Hiba**: Ellenőrzési hiba a migrálási beállítások","errorDetail":"több mint maximális száma "4" objektum "Adatbázis" kiválasztva migrálásra."

| Ok         | Feloldás |
| ------------- | ------------- |
| Ez a hiba jeleníti meg, ha több mint négy adatbázis egy egyszeri áttelepítést tevékenység kiválasztott. Jelenleg minden migrálási tevékenység négy adatbázis korlátozva. | Válassza ki a négy vagy kevesebb adatbázisok száma migrálási tevékenységet. Ha négynél több párhuzamosan az adatbázisokat át van szüksége, üzembe helyezése az Azure Database Migration Service egy másik példánya. Minden egyes előfizetés jelenleg támogatja az akár két Azure Database Migration Service-példányt.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>A MySQL a MySQL-hez Azure-ba való migrálás sikertelen helyreállítási hibák

Telepít át MySQL az Azure Database for MySQL, Azure Database Migration Service segítségével, az áttelepítési tevékenység sikertelen lesz, a következő hibával:

* **Hiba**: Adatbázis-migrálási hiba – a feladat "TaskID" [n] helyreállítási egymást követő hibák miatt fel lett függesztve.

| Ok         | Feloldás |
| ------------- | ------------- |
| Ez a hiba akkor fordulhat elő, ha a felhasználó elvégezni a migrálást hiányzik a ReplicationAdmin szerepkörrel és/vagy jogosultságokkal replikációs ügyfél, a REPLIKÁCIÓ REPLIKA és a SUPER (MySQL 5.6.6 rendszernél korábbi verzió esetén).<br> <br><br><br> <br> <br> <br> <br> <br> <br> | Győződjön meg arról, hogy a [előfeltételként jogosultságokkal](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) a felhasználóhoz tartozó fiókot kell konfigurálni, pontosan az Azure Database for MySQL-példányt. Például a következő lépések szükséges jogosultságokkal rendelkező "migrateuser" nevű felhasználó létrehozásához:<br>1. FELHASZNÁLÓ létrehozása migrateuser@'% "azonosított szerint"secret"; <br>2. A "secret"; által azonosított migrateuser'@'% db_name.* az összes jogosultságok engedélyezése Ismételje meg ezt a lépést hozzáférést biztosít a több adatbázis <br>3. A Grant replikációs alárendelt *.* a "secret"; által azonosított migrateuser'@'%<br>4. Engedélyezés replikációs ügyfél *.* a "secret"; által azonosított migrateuser'@'%<br>5. Jogosultságok; ürítése |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Azure Database Migration Service leállítására tett kísérlet közben hiba

Hiba történt a következő amikor megjelenik az Azure Database Migration Service-példány leállítása:

* **Hiba**: Szolgáltatás leállítása nem sikerült. Hiba: {"error": {"code": "InvalidRequest", "message": "egy vagy több tevékenység jelenleg futnak. Állítsa le a szolgáltatást, várjon, amíg a tevékenység befejeződött, vagy manuálisan állítsa le ezeket a tevékenységeket, és próbálkozzon újra. "}}

| Ok         | Feloldás |
| ------------- | ------------- |
| Ez a hiba a szolgáltatáspéldány leállítani a végrehajtani kívánt olyan tevékenységeket foglal magába, amelyek továbbra is futnak, vagy jelenleg a migrálási projekt jelenít meg. <br><br><br><br><br><br> | Győződjön meg arról, hogy nincsenek-e az Azure Database Migration Service leállítani kívánt példányát futtató tevékenységek. Törölheti is a tevékenységek vagy projektekhez előtt állítsa le a szolgáltatást. A következő lépések bemutatják, hogyan törlésével az összes futó feladatot az áttelepítés szolgáltatáspéldány karbantartása projektek eltávolítása:<br>1. Install-Module -Name AzureRM.DataMigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription -SubscriptionName "<subName>" <br> 4. Remove-AzureRmDataMigrationProject -Name <projectName> -ResourceGroupName <rgName> -ServiceName <serviceName> -DeleteRunningTask |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Adatbázis visszaállítása közben az Azure SQL-adatbázis áttelepítése SQL felügyelt példánya hiba

Amikor egy online migrálás SQL Serverről az Azure SQL Database felügyelt példány végez, az átállás meghiúsul a következő hiba:

* **Hiba**: A művelet azonosítója "műveletazonosító:" nem sikerült a visszaállítási művelet. Kód "AuthorizationFailed", Message "a"clientId","objectId"objektumazonosítójú ügyfél nem rendelkezik hatókörben"Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read"művelet végrehajtására" /subscriptions/ előfizetés-azonosító ".".

| Ok         | Feloldás    |
| ------------- | ------------- |
| Ez a hiba azt jelzi, hogy az alkalmazás egyszerű használt online migrálás SQL Serverről az Azure SQL Database felügyelt példánya nem közreműködés engedélye az előfizetésben. Bizonyos API-hívások a felügyelt példány jelenleg ezt az engedélyt az előfizetés szükséges, a visszaállítási művelethez. <br><br><br><br><br><br><br><br><br><br> | Használja a `Get-AzureADServicePrincipal` PowerShell-parancsmagot `-ObjectId` érhető el a hibaüzenetben használt azonosító megjelenítendő nevét.<br><br> Ellenőrizze az engedélyeket ehhez az alkalmazáshoz, és ellenőrizze, rendelkezik-e a [közreműködői szerepkört](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) az előfizetés szintjén. <br><br> Az Azure Database áttelepítési Service mérnöki csapata dolgozik azon, hogy korlátozza a szükséges hozzáférést a jelenlegi közreműködés szerepkör az előfizetésben. Ha rendelkezik egy üzleti követelményt, nem engedélyezi a használatát közreműködés szerepkör, további segítségért forduljon az Azure ügyfélszolgálatához. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Hiba történt, amikor az Azure Database Migration Service társított hálózati adapter törlése

Ha egy hálózati kártyát, Azure Database Migration Service társított törölni próbál, a törlési kísérlet a hibaüzenettel meghiúsul:

* **Hiba**: Azure Database Migration Service miatt a DMS-szolgáltatás használatával a hálózati Adapterhez társított hálózati adapter nem lehet törölni.

| Ok         | Feloldás    |
| ------------- | ------------- |
| A hiba akkor fordul elő, amikor az Azure Database Migration Service-példány azonban továbbra is megtalálhatók és a hálózati adaptert. <br><br><br><br><br><br> | A hálózati adapter törléséhez törölje a DMS szolgáltatás példánya, amely automatikusan törli a szolgáltatás által használt hálózati Adapterhez.<br><br> **Fontos**: Ellenőrizze, hogy az Azure Database Migration Service-példány törlése folyamatban nem tartoznak futó tevékenységek.<br><br> A projektek és az Azure Database Migration Service-példányhoz tartozó tevékenységek törlését követően törölheti a szolgáltatáspéldányt. A szolgáltatáspéldány által használt hálózati Adapterhez automatikusan törlődik a szolgáltatás törlése során. |

## <a name="connection-error-when-using-expressroute"></a>Kapcsolódási hiba ExpressRoute használata során

Próbál csatlakozni, az Azure Database Migration service-projekt varázsló a forráson, ha a kapcsolat hosszabb időkorlát után meghiúsul, ha a forrás ExpressRoute használatával a hálózati kapcsolatot.

| Ok         | Feloldás    |
| ------------- | ------------- |
| Használata esetén [ExpressRoute](https://azure.microsoft.com/services/expressroute/), Azure Database Migration Service [igényel](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) három szolgáltatásvégpontokat a virtuális hálózat alhálózatához társított a szolgáltatás kiépítése:<br> --Service Bus-végpont<br> – Storage-végpont<br> --Cél adatbázis végpont (például SQL-végpont, Cosmos-DB végpont)<br><br><br><br> | [Engedélyezése](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) a szükséges Szolgáltatásvégpontok az ExpressRoute-kapcsolat forrás- és az Azure Database Migration Service között. <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-mysql"></a>Ha a MySQL-adatbázis áttelepítése az Azure-beli MySQL időtúllépési hiba

Amikor egy MySQL-adatbázist telepít át egy Azure Database for MySQL-példányt, Azure Database Migration Service-n keresztül, az áttelepítés sikertelen lesz, a következő időtúllépési hiba:

* **Hiba**: Adatbázis-migrálási hiba – nem sikerült betölteni a fájl – nem sikerült elindítani a betöltési folyamat fájl n RetCode: SQL_ERROR SqlState: HY000 NativeError: 1205 üzenetet: [MySQL] [ODBC-illesztő] [mysqld] zárolás várakozási időkorlátja lejárt; Próbálja meg újraindítani a tranzakció

| Ok         | Feloldás    |
| ------------- | ------------- |
| Ez a hiba akkor fordul elő, amikor az áttelepítés meghiúsul, a zárolás várakozási időtúllépés miatt áttelepítés során.<br><br> | Érdemes növelni a kiszolgáló paraméterének értéke **"innodb_lock_wait_timeout"**. A legnagyobb megengedett értéke 1073741824. |

## <a name="additional-known-issues"></a>További ismert problémák

* [Ismert problémák és a migrálás korlátozások az online migrálást az Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Ismert problémák és a migrálás korlátozások az online migrálást az Azure Database MySQL-hez](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Ismert problémák és a migrálás az Azure Database for postgresql-hez online áttelepítések vonatkozó korlátozások](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>További lépések

* A cikk [Azure Database Migrálási szolgáltatás PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* A cikk [kiszolgáló paramétereinek konfigurálása az Azure Database for MySQL-hez az Azure portal segítségével hogyan](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* A cikk [Azure Database Migration Service használatára vonatkozó előfeltételek áttekintése](https://docs.microsoft.com/azure/dms/pre-reqs).
* Tekintse meg a [Azure Database Migration Service használatával kapcsolatos gyakori kérdések](https://docs.microsoft.com/azure/dms/faq).
