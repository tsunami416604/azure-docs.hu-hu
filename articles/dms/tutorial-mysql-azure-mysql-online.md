---
title: 'Oktatóanyag: A MySQL áttelepítése online a MySQL Azure-adatbázisába'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan hajthat végre online áttelepítést a helyszíni MySQL-ből az Azure Database for MySQL szolgáltatásra az Azure Database Migration Service használatával.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 7c8087a01bb71657e816be89b6a562dd4783b271
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240740"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Oktatóanyag: MySQL online migrálása az Azure Database for MySQL-be a DMS használatával

Az Azure Database Migration Service segítségével áttelepítheti az adatbázisokat egy helyszíni MySQL-példányból az [Azure Database for MySQL-be](https://docs.microsoft.com/azure/mysql/) minimális állásidővel. Ez azt jelenti, hogy a migrálás az alkalmazás minimális ideig tartó leállásával végezhető el. Ebben az oktatóanyagban az **Alkalmazottak** mintaadatbázisát a MySQL 5.7 helyszíni példányából az Azure Database for MySQL-be telepíti át egy online áttelepítési tevékenység használatával az Azure Database Migration Service-ben.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * A mintaséma migrálása a mysqldump segédprogrammal.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.

> [!NOTE]
> Az Azure Database Migration Service online áttelepítés végrehajtásához létre kell adnia egy példányt a prémium díjszabási szint alapján.

> [!IMPORTANT]
> Az optimális áttelepítési élmény érdekében a Microsoft azt javasolja, hogy hozzon létre egy példányt az Azure Database Migration Service ugyanabban az Azure-régióban, mint a cél-adatbázis. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Töltse le és telepítse a [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6-os vagy 5.7-es verzióját. A helyszíni MySQL-verziónak egyeznie kell az Azure Database for MySQL verziójával. Például a MySQL 5.6 csak az Azure Database for MySQL 5.6-ba migrálható, az 5.7-es verzióra nem frissíthető.
* [Példány létrehozása az Azure Database for MySQL-ben](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal). Az adatbázisok az Azure Portal használatával való csatlakoztatásának és létrehozásának részleteiért tekintse meg az [Azure Database for MySQL: Csatlakozás és adatlekérdezés a MySQL Workbench használatával](https://docs.microsoft.com/azure/mysql/connect-workbench) című cikket.  
* Hozzon létre egy Microsoft Azure virtuális hálózatot az Azure adatbázis-áttelepítési szolgáltatáshoz az Azure Resource Manager telepítési modelljével, amely az [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával biztosít helyek közötti kapcsolatot a helyszíni forráskiszolgálókhoz. A virtuális hálózat létrehozásáról további információt a [Virtuális hálózati dokumentációban](https://docs.microsoft.com/azure/virtual-network/)és különösen a részletes en című rövid útmutatóban talál.

    > [!NOTE]
    > A virtuális hálózatinet telepítése során, ha az ExpressRoute szolgáltatást hálózati társviszony-létesítéssel használja a Microsofthoz, adja hozzá a következő [szolgáltatásvégpontokat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) ahhoz az alhálózathoz, amelyben a szolgáltatás ki lesz építve:
    >
    > * Cél adatbázis-végpont (például SQL-végpont, Cosmos DB-végpont és így tovább)
    > * Tárolási végpont
    > * Szolgáltatásbusz végpontja
    >
    > Erre a konfigurációra azért van szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózati biztonsági csoport szabályai nem blokkolják a következő bejövő kommunikációs portokat az Azure Database Migration Service szolgáltatásba: 443, 53, 9354, 445, 12000. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részleteket a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal című témakörben olvashat.](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat, hogy az Azure Database Migration Service hozzáférhessen a Forrás MySQL Server szolgáltatáshoz, amely alapértelmezés szerint a 3306-os TCP-port.
* Ha a forrásadatbázis(ok) előtt tűzfalberendezést használ, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia ahhoz, hogy az Azure Database Migration Service hozzáférhessen a forrásadatbázis(ok)hoz az áttelepítéshez.
* Hozzon létre egy kiszolgálószintű [tűzfalszabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure Database for MySQL számára, amely lehetővé teszi az Azure Database Migration Service hozzáférését a céladatbázisokhoz. Adja meg az Azure Database Migration Service használt virtuális hálózat alhálózati tartományát.
* A forrásként szolgáló MySQL-nek a MySQL közösségi kiadásának kell lennie. A MySQL-példány verziójának megállapításához futtassa a következő parancsot a MySQL segédprogramban vagy a MySQL Workbenchben:

    ```
    SELECT @@version;
    ```

* Az Azure Database for MySQL csak az InnoDB-táblákat támogatja. Ha a MyISAM-táblákat InnoDB-táblákká szeretné alakítani, tekintse meg a [táblák MyISAM-ból InnoDB-vé konvertálását ismertető](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) részt

* Engedélyezze a bináris naplózást a forrásadatbázisban szereplő my.ini (Windows) vagy a my.cnf (Unix) fájlban az alábbi konfiguráció használatával:

  * **server_id** = 1 vagy nagyobb (csak a MySQL 5.6-os verziójára vonatkozik)
  * **log-bin** = \<path> (csak a MySQL 5.6-ra vonatkozik) Például: log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (javasoljuk, hogy ne használjon nullát; csak a MySQL 5.6 esetében releváns)
  * **Binlog_row_image** = full (csak a MySQL 5.6-os verziójára vonatkozik)
  * **log_slave_updates** = 1

* A felhasználónak a ReplicationAdmin szerepkörrel kell rendelkeznie az alábbi jogosultságokkal:

  * **REPLICATION CLIENT** – Csak változásfeldolgozási feladatokhoz szükséges. Vagyis a csak teljes betöltési feladatokhoz nincs szükség erre a jogosultságra.
  * **REPLICATION REPLICA** – Csak változásfeldolgozási feladatokhoz szükséges. Vagyis a csak teljes betöltési feladatokhoz nincs szükség erre a jogosultságra.
  * **SUPER** – Kizárólag a MySQL 5.6.6-os verziójánál korábbi verziókban szükséges.

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása

Ahhoz, hogy az összes adatbázis-objektumot táblasémaként, indexekként és tárolt eljárásokként egészíthessük ki, ki kell bontanunk a forrásadatbázis sémáját, és alkalmaznunk kell az adatbázisra. A séma kibontásához használhatja a mysqldump segédprogramot a `--no-data` paraméterrel.

Feltételezve, hogy a MySQL **Alkalmazottak** mintaadatbázis a helyszíni rendszerben, a parancs, hogy nem séma áttelepítés a mysqldump a következő:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Példa:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Ha sémát szeretne importálni az Azure Database for MySQL célba, futtassa a következő parancsot:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Példa:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Ha a sémában idegen kulcsok szerepelnek, a migrálás első betöltése és folyamatos szinkronizálása sikertelen lesz.  Hajtsa végre a következő parancsfájlt a MySQL Workbench programban a drop foreign key parancsfájl kibontásához és az idegen kulcsparancsfájl hozzáadásához.

```
SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
    KCU.TABLE_NAME,
    KCU.COLUMN_NAME,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
    WHERE
      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
  AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
  GROUP BY SchemaName;
 ```

Az idegen kulcs elvetéséhez futtassa a drop foreign key (második oszlop) szkriptet a lekérdezési eredményekben.

> [!IMPORTANT]
> Ha biztonsági másolat használatával importál adatokat, távolítsa el a CREATE DEFINER parancsokat manuálisan vagy a --skip-definer paranccsal mysqldump végrehajtásakor. DefineR létrehozásához szuper jogosultságokat kell létrehozni, és korlátozott az Azure Database for MySQL.

Ha van egy eseményindító az adatokban (beszúrása vagy frissítése eseményindító), akkor kényszeríti az adatok integritását a cél előtt a replikált adatokat a forrásból. Javasoljuk, hogy migráláskor tiltsa le a triggereket a cél minden táblájában, majd a migrálás végeztével engedélyezze őket ismét.

Az eseményindítók letiltásához használja a következő parancsot:

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyben létre szeretné hozni az Azure Database Migration Service példányát, majd válassza **az Erőforrás-szolgáltatók**lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd jobbra a **Microsoft.DataMigration**programtól, és válassza a **Regisztráció**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Jelöljön ki egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat hozzáférést biztosít az Azure Database Migration Service számára a forrás SQL Server és a cél Azure SQL Database-példány eléréséhez.

    A virtuális hálózat Azure Portalon való létrehozásáról további információt a Virtuális hálózat létrehozása az Azure Portal használatával című témakörben [talál.](https://aka.ms/DMSVnet)

5. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Az Azure Database Migration Service összes példányának megkeresése](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, majd válassza ki a példányt.

     ![Az Azure Database Migration Service példányának megkeresése](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az **Új migrálási projekt** képernyőn nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza ki a **MySQL**, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **AzureDbForMySQL** elemet.
5. A **Tevékenység típusának kiválasztása** szakaszban válassza az **Online adatok migrálása** lehetőséget.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > Másik lehetőségként **választhatja** a Projekt létrehozása csak az áttelepítési projekt most, és az áttelepítés későbbi végrehajtásához lehetőséget.

6. Kattintson a **Mentés** gombra, jegyezze fel a DMS-sel történő sikeres adatmigráláshoz szükséges követelményeket, majd válassza a **Tevékenység létrehozása és futtatása** lehetőséget.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Forrás adatainak hozzáadása** képernyőn adja meg a forrásként szolgáló MySQL-példány kapcsolati adatait.

    ![A Forrás adatainak hozzáadása képernyő](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés** lehetőséget, majd a **Cél részletei** képernyőn adja meg a célul szolgáló Azure Database for MySQL-kiszolgáló kapcsolati adatait. Ez a cél az Azure Database for MySQL azon példánya, amelyen üzembe helyezte az **Employees** sémát a mysqldump segédprogrammal.

    ![A részleteket tartalmazó képernyő](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrásadatbázis, az Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázist.

    ![Leképezés céladatbázisokra](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)
   > [!NOTE] 
   > Bár ebben a lépésben több adatbázist is kijelölhet, az Azure Database Migration Service minden példánya legfeljebb négy adatbázist támogat az egyidejű áttelepítéshez. Emellett az Azure Database Migration Service régiónként legfeljebb két példánya lehet egy előfizetésben. Ha például 40 adatbázist telepíthet át, ezek közül csak nyolcat telepíthet át egyidejűleg, és csak akkor, ha az Azure Database Migration Service két példányát hozta létre.

3. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik az áttelepítési tevékenység ablak, és a tevékenység **állapota** **inicializálódik.**

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Befejezve** értékre.

     ![Tevékenység állapota: Befejezve](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. Az **Adatbázis neve** mezőben válassza ki az adatbázist, az **Adatok teljes betöltése** és a **Növekményes adatszinkronizálás** műveletek migrálási állapotának megtekintéséhez.

    Az Adatok teljes betöltésénél az első betöltés migrálási állapota jelenik meg, a Növekményes adatszinkronizálásnál pedig az Adatváltozások rögzítése (CDC) állapot látható.

     ![Tevékenység állapota – Teljes betöltés kész](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![Tevékenység állapota – Növekményes adatszinkronizálás](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

Az első teljes betöltés elkészültével az adatbázisok **Átállásra kész** állapotúak lesznek.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

    ![Átállás indítása](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.
3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázis migrálási állapota **Befejezve** értékre vált, csatlakoztassa alkalmazásait az új Azure SQL-céladatbázishoz.

## <a name="next-steps"></a>További lépések

* Az Azure Database for MySQL-be történő online migrálás végrehajtásakor felmerülő ismert hibákhoz és korlátozásokhoz kapcsolódó információk: [Az Azure Database for MySQL online migrálásával kapcsolatos ismert hibák és kerülő megoldások](known-issues-azure-mysql-online.md).
* Az Azure Database Migration Service szolgáltatásról a [Mi az Azure-adatbázis-áttelepítési szolgáltatás?](https://docs.microsoft.com/azure/dms/dms-overview)cikkben olvashat.
* Az Azure Database for MySQL szolgáltatásról a [Mi az Azure Database for MySQL című](https://docs.microsoft.com/azure/mysql/overview)cikkben olvashat.
