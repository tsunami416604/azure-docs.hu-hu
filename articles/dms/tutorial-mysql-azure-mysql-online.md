---
title: 'Oktatóanyag: Azure Database Migration Service segítségével áttelepítése egy online MySQL az Azure Database for MySQL-hez |} A Microsoft Docs'
description: Megtanulhatja, hogyan hajtsa végre egy online migrálás a helyszíni MySQL az Azure Database MySQL-hez készült Azure Database Migration Service használatával.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5a35df5b72f51f4ef725b3d764e7dc2c80c19ec2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240752"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Oktatóanyag: MySQL online migrálása az Azure Database for MySQL-be a DMS használatával

Azure Database Migration Service segítségével az adatbázisokat át egy helyszíni MySQL-példányt a [, Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) minimális állásidővel. Ez azt jelenti, hogy a migrálás az alkalmazás minimális ideig tartó leállásával végezhető el. Ebben az oktatóanyagban áttelepítése a **alkalmazottak** mintaadatbázis MySQL 5.7-es helyszíni példányát az Azure database for MySQL egy online migrálás tevékenység az Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * A mintaséma migrálása a mysqldump segédprogrammal.
> * Hozzon létre egy Azure Database Migration Service példányát.
> * Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.

> [!NOTE]
> Azure Database Migration Service egy online migrálás végrehajtásához használatához hozzon létre egy példányt prémium tarifacsomagban alapján.

> [!IMPORTANT]
> Egy optimális migrálási folyamatba, a Microsoft azt javasolja, létrehozása az Azure Database Migration Service példányát és a céladatbázis Azure ugyanabban a régióban. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Töltse le és telepítse a [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6-os vagy 5.7-es verzióját. A helyszíni MySQL-verziónak egyeznie kell az Azure Database for MySQL verziójával. Például a MySQL 5.6 csak az Azure Database for MySQL 5.6-ba migrálható, az 5.7-es verzióra nem frissíthető.
* [Példány létrehozása az Azure Database for MySQL-ben](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal). Az adatbázisok az Azure Portal használatával való csatlakoztatásának és létrehozásának részleteiért tekintse meg az [Azure Database for MySQL: Csatlakozás és adatlekérdezés a MySQL Workbench használatával](https://docs.microsoft.com/azure/mysql/connect-workbench) című cikket.  
* Az Azure Resource Manager üzembe helyezési modell, amely biztosítja, hogy a helyek közötti kapcsolatot a helyszíni adatforrás-kiszolgálók használatával hozzon létre egy Azure virtuális hálózaton (VNet) az Azure Database Migration Service [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). A virtuális hálózatok létrehozásával kapcsolatos további információkért lásd: a [Virtual Network-dokumentáció](https://docs.microsoft.com/azure/virtual-network/), és különösen a témakör részletesen a rövid útmutató cikkek.

    > [!NOTE]
    > Virtuális hálózathoz a telepítés során, ha az ExpressRoute hálózati a Microsoft társviszony-létesítés használja, hozzá a következő szolgáltatás [végpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) az alhálózathoz, amelyben üzembe fogja helyezni a szolgáltatást:
    > * Cél adatbázis végpont (például SQL-végpont, Cosmos-DB végpont, és így tovább)
    > * Storage-végpont
    > * Service bus-végpont
    >
    > Ez a konfiguráció szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózatok közötti hálózati biztonsági csoport szabályai nem blokkolják a következő bejövő kommunikációs portokat, Azure Database Migration Service: 443, 53, 9354, 445, 12000. Az Azure VNet NSG-forgalom szűrése további részletekért tekintse meg a cikket [hálózati biztonsági csoportokkal a hálózati forgalom szűrése](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat az Azure Database Migration Service a MySQL-kiszolgáló, amely alapértelmezés szerint a TCP-port 3306-os forrás eléréséhez.
* A forrásadatbázis (ok) elé egy tűzfalkészülék használata esetén előfordulhat, hogy hozzá kell tűzfalszabályokban, Azure Database Migration Service az áttelepítéshez a forrásadatbázis (ok) eléréséhez.
* Hozzon létre egy kiszolgálószintű [tűzfalszabály](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure Database for MySQL, Azure Database Migration Service a céladatbázisokhoz való hozzáférést. Adja meg a virtuális hálózat, Azure Database Migration Service használt alhálózat tartományán.
* A forrásként szolgáló MySQL-nek a MySQL közösségi kiadásának kell lennie. A MySQL-példány verziójának megállapításához futtassa a következő parancsot a MySQL segédprogramban vagy a MySQL Workbenchben:

    ```
    SELECT @@version;
    ```

* Az Azure Database for MySQL csak az InnoDB-táblákat támogatja. Ha a MyISAM-táblákat InnoDB-táblákká szeretné alakítani, tekintse meg a [táblák MyISAM-ból InnoDB-vé konvertálását ismertető](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) részt

* Engedélyezze a bináris naplózást a forrásadatbázisban szereplő my.ini (Windows) vagy a my.cnf (Unix) fájlban az alábbi konfiguráció használatával:

  * **server_id** = 1 vagy nagyobb (csak a MySQL 5.6-os verziójára vonatkozik)
  * **log-bin** = \<elérési útja > (csak a MySQL 5.6-os vonatkozik) például: log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (azt javasoljuk, hogy nem használja a nulla; csak a MySQL 5.6-os vonatkozik)
  * **Binlog_row_image** = full (csak a MySQL 5.6-os verziójára vonatkozik)
  * **log_slave_updates** = 1

* A felhasználónak a ReplicationAdmin szerepkörrel kell rendelkeznie az alábbi jogosultságokkal:

  * **REPLICATION CLIENT** – Csak változásfeldolgozási feladatokhoz szükséges. Vagyis a csak teljes betöltési feladatokhoz nincs szükség erre a jogosultságra.
  * **REPLICATION REPLICA** – Csak változásfeldolgozási feladatokhoz szükséges. Vagyis a csak teljes betöltési feladatokhoz nincs szükség erre a jogosultságra.
  * **SUPER** – Kizárólag a MySQL 5.6.6-os verziójánál korábbi verziókban szükséges.

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása

Ahhoz, hogy az összes adatbázis-objektumot táblasémaként, indexekként és tárolt eljárásokként egészíthessük ki, ki kell bontanunk a forrásadatbázis sémáját, és alkalmaznunk kell az adatbázisra. A séma kibontásához használhatja a mysqldump segédprogramot a `--no-data` paraméterrel.

Feltételezve, hogy MySQL **alkalmazottak** mintaadatbázist a helyszíni rendszerben, ehhez a séma migrálása mysqldump használatával a parancs a következő:

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

Ha a sémában idegen kulcsok szerepelnek, a migrálás első betöltése és folyamatos szinkronizálása sikertelen lesz.  Hajtsa végre a következő szkriptet a MySQL Workbench használatával csomagolja ki a legördülő idegen kulcs parancsfájl, és adja hozzá az idegen kulcs parancsfájl.

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
> Ha egy biztonsági mentés használatával az adatok importálása távolítsa el a létrehozása DEFINER parancsok manuálisan vagy a--skip-definer parancs egy mysqldump végrehajtásakor. DEFINER létrehozása felügyelői jogosultságokat igényel, és az Azure Database for MySQL-hez korlátozott.

Ha az adatok (insert vagy update trigger) egy eseményindítót, adatok integritását a modern, a replikált adatokat a forrásból a célként megadott érvényesíti. Javasoljuk, hogy migráláskor tiltsa le a triggereket a cél minden táblájában, majd a migrálás végeztével engedélyezze őket ismét.

A céladatbázis eseményindítók letiltásához használja a következő parancsot:

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Válassza ki az előfizetést, amelyben az Azure Database Migration Service példányát létre, és válassza ki a kívánt **erőforrás-szolgáltatók**.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válasszon ki egy meglévő Vnetet, vagy hozzon létre egy újat.

    A virtuális hálózat biztosít, Azure Database Migration Service a forrás SQL Server-hozzáféréssel rendelkező és a cél Azure SQL Database-példányban.

    Virtuális hálózat létrehozása az Azure Portalon kapcsolatos további információkért tekintse meg a cikket [hozzon létre egy virtuális hálózatot az Azure portal használatával](https://aka.ms/DMSVnet).

5. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Keresse meg az Azure Database Migration Service összes példánya](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. Az a **Azure Database Migration Service** képernyőn, keresse meg a nevet, Azure Database Migration Service-példány létrehozott, majd válassza ki a példányt.

     ![Keresse meg az Azure Database Migration Service példányát](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az **Új migrálási projekt** képernyőn nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza ki a **MySQL**, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **AzureDbForMySQL** elemet.
5. A **Tevékenység típusának kiválasztása** szakaszban válassza az **Online adatok migrálása** lehetőséget.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > Választhatja azt is megteheti, **csak a Create project** a migrálási projekt létrehozása, és később a migrálás végrehajtására.

6. Kattintson a **Mentés** gombra, jegyezze fel a DMS-sel történő sikeres adatmigráláshoz szükséges követelményeket, majd válassza a **Tevékenység létrehozása és futtatása** lehetőséget.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Forrás adatainak hozzáadása** képernyőn adja meg a forrásként szolgáló MySQL-példány kapcsolati adatait.

    ![A Forrás adatainak hozzáadása képernyő](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés** lehetőséget, majd a **Cél részletei** képernyőn adja meg a célul szolgáló Azure Database for MySQL-kiszolgáló kapcsolati adatait. Ez a cél az Azure Database for MySQL azon példánya, amelyen üzembe helyezte az **Employees** sémát a mysqldump segédprogrammal.

    ![A részleteket tartalmazó képernyő](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis adatbázis neve megegyezik a forrás-adatbázis, Azure Database Migration Service alapértelmezés szerint választja ki a céladatbázisban.

    ![Leképezés céladatbázisokra](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)

3. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Inicializálás**.

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
* Azure Database Migration Service kapcsolatos információkért tekintse meg a cikket [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Azure Database for MySQL-hez információt lásd: a cikk [Mi az Azure Database for MySQL?](https://docs.microsoft.com/azure/mysql/overview).
