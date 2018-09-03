---
title: MySQL online migrálása az Azure Database for MySQL-be, az Azure Database Migration Service használatával | Microsoft Docs
description: Megtudhatja, hogyan végezhet online migrálást a helyszíni MySQL-ből az Azure Database for MySQL-be, az Azure Database Migration Service használatával.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 08/27/2018
ms.openlocfilehash: 88cd390e37273c95304dab5ba3153e8a63270ab1
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042686"
---
# <a name="migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>MySQL online migrálása az Azure Database for MySQL-be a DMS használatával
Az Azure Database Migration Service használatával minimális szolgáltatáskieséssel migrálhatja egy helyszíni MySQL-példány adatbázisait az [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/)-be. Ez azt jelenti, hogy a migrálás az alkalmazás minimális ideig tartó leállásával végezhető el. Ebben az oktatóanyagban az **Employees** mintaadatbázist fogja migrálni a MySQL 5.7 egy helyszíni példányáról az Azure Database for MySQL-be az Azure Database Migration Service online migrálási tevékenységének használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * A mintaséma migrálása a mysqldump segédprogrammal.
> * Egy Azure Database Migration Service-példány létrehozása.
> * Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Töltse le és telepítse a [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6-os vagy 5.7-es verzióját. A helyszíni MySQL-verziónak egyeznie kell az Azure Database for MySQL verziójával. Például a MySQL 5.6 csak az Azure Database for MySQL 5.6-ba migrálható, az 5.7-es verzióra nem frissíthető.
- [Példány létrehozása az Azure Database for MySQL-ben](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal). Az adatbázisok az Azure Portal használatával való csatlakoztatásának és létrehozásának részleteiért tekintse meg az [Azure Database for MySQL: Csatlakozás és adatlekérdezés a MySQL Workbench használatával](https://docs.microsoft.com/azure/mysql/connect-workbench) című cikket.  
- Hozzon létre egy virtuális hálózatot az Azure Database Migration Service-hez az Azure Resource Manager-alapú üzemi modell használatával, amely a hálózat helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgálóknak [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) használatával.
- Győződjön meg arról, hogy az Azure Virtual Network (VNET) hálózati biztonsági szabályok nem blokkolják a következő kommunikációs portokat: 443, 53, 9354, 445, 12000. További részletek az Azure VNET NSG-forgalom szűréséről: [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
- Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a MySQL-kiszolgáló forrását, amely alapértelmezés szerint a 3306-os TCP-port.
- Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.
- Hozzon létre egy kiszolgálószintű [tűzfalszabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure Database for MySQL-hez, hogy az Azure Database Migration Service hozzáférhessen a céladatbázisokhoz. Adja meg az Azure Database Migration Service-hez használt virtuális hálózat alhálózati tartományát.
- A forrásként szolgáló MySQL-nek a MySQL közösségi kiadásának kell lennie. A MySQL-példány verziójának megállapításához futtassa a következő parancsot a MySQL segédprogramban vagy a MySQL Workbenchben:
    ```
    SELECT @@version;
    ```
- Az Azure Database for MySQL csak az InnoDB-táblákat támogatja. Ha a MyISAM-táblákat InnoDB-táblákká szeretné alakítani, tekintse meg a [táblák MyISAM-ból InnoDB-vé konvertálását ismertető](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) részt 

- Engedélyezze a bináris naplózást a forrásadatbázisban szereplő my.ini (Windows) vagy a my.cnf (Unix) fájlban az alábbi konfiguráció használatával:
- A felhasználónak a ReplicationAdmin szerepkörrel kell rendelkeznie az alábbi jogosultságokkal:
    - **REPLICATION CLIENT** – Csak változásfeldolgozási feladatokhoz szükséges. Vagyis a csak teljes betöltési feladatokhoz nincs szükség erre a jogosultságra.
    - **REPLICATION REPLICA** – Csak változásfeldolgozási feladatokhoz szükséges. Vagyis a csak teljes betöltési feladatokhoz nincs szükség erre a jogosultságra.
    - **SUPER** – Kizárólag a MySQL 5.6.6-os verziójánál korábbi verziókban szükséges.

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása
Ahhoz, hogy az összes adatbázis-objektumot táblasémaként, indexekként és tárolt eljárásokként egészíthessük ki, ki kell bontanunk a forrásadatbázis sémáját, és alkalmaznunk kell az adatbázisra. A séma kibontásához használhatja a mysqldump segédprogramot - - no-data paraméterrel.
 
Feltételezve, hogy rendelkezik egy alkalmazotti MySQL-mintaadatbázissal a helyszíni rendszerben, a mysqldumppal történő sémamigráláshoz szükséges parancs a következő:
```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```
Például:
```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Ha sémát szeretne importálni az Azure Database for MySQL célba, futtassa a következő parancsot:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Például:
```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Ha a sémában idegen kulcsok szerepelnek, a migrálás első betöltése és folyamatos szinkronizálása sikertelen lesz.  Futtassa az alábbi szkriptet a MySQL Workbenchben a drop foreign key szkript (idegen kulcs elvetése) és az add foreign key (idegen kulcs hozzáadása) szkript kibontásához.
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
  AND KCU.REFERENCED_TABLE_SCHEMA = ['SchemaName') Queries
  GROUP BY SchemaName;
 ```
        
Az idegen kulcs elvetéséhez futtassa a drop foreign key (második oszlop) szkriptet a lekérdezési eredményekben.

Ha az adatok között trigger is szerepel (beszúrási vagy frissítési trigger), az kikényszeríti az adatok integritását a célban az adatok forrásból való replikálása előtt. Javasoljuk, hogy migráláskor tiltsa le a triggereket a cél minden táblájában, majd a migrálás végeztével engedélyezze őket ismét.

A céladatbázisban szereplő triggerek letiltásához használja az alábbi parancsot:
```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```
## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása
1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.
 
   ![Portál-előfizetések megtekintése](media\tutorial-mysql-to-azure-mysql-online\portal-select-subscriptions.png)
       
2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.
 
    ![Erőforrás-szolgáltatók megtekintése](media\tutorial-mysql-to-azure-mysql-online\portal-select-resource-provider.png)
    
3.  Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.
 
    ![Erőforrás-szolgáltató regisztrálása](media\tutorial-mysql-to-azure-mysql-online\portal-register-resource-provider.png)    

## <a name="create-a-dms-instance"></a>DMS-példány létrehozása
1.  Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media\tutorial-mysql-to-azure-mysql-online\portal-marketplace.png)

2.  Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.
 
    ![Azure Database Migration Service-példány létrehozása](media\tutorial-mysql-to-azure-mysql-online\dms-create1.png)
  
3.  **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válasszon ki egy meglévő virtuális hálózatot (VNET), vagy hozzon létre egy újat.

    A VNET biztosítja az Azure Database Migration Service számára a forrásként szolgáló SQL Serverhez és az Azure SQL Database-célpéldányhoz való hozzáférést.

    További információk a virtuális hálózatok létrehozásáról az Azure Portallal: [Virtuális hálózat létrehozása az Azure Portallal](https://aka.ms/DMSVnet).

5. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    Ha segítségre van szüksége a megfelelő szintű Azure Database Migration Service kiválasztásában, tekintse meg az [Azure Database Migration Service- (Azure DMS-) szint kiválasztásáról](https://go.microsoft.com/fwlink/?linkid=861067) szóló blogbejegyzésben közzétett javaslatokat. 

     ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media\tutorial-mysql-to-azure-mysql-online\dms-settings3.png)

7.  A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása
A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.
 
      ![Az Azure Database Migration Service minden példányának megkeresése](media\tutorial-mysql-to-azure-mysql-online\dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.
 
     ![Az Azure Database Migration Service személyes példányának megkeresése](media\tutorial-mysql-to-azure-mysql-online\dms-instance-search.png)
 
3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az **Új migrálási projekt** képernyőn nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza ki a **MySQL**, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **AzureDbForMySQL** elemet.
5. A **Tevékenység típusának kiválasztása** szakaszban válassza az **Online adatok migrálása** lehetőséget.

    ![Azure Database Migration Service-projekt létrehozása](media\tutorial-mysql-to-azure-mysql-online\dms-create-project4.png)

    > [!NOTE]
    > Alternatív megoldásként választhatja a **csak a projektet létrehozó** lehetőséget, ha most csak a migrálási projektet szeretné létrehozni, és csak később szeretné elindítani a tényleges migrálást.

6. Kattintson a **Mentés** gombra, jegyezze fel a DMS-sel történő sikeres adatmigráláshoz szükséges követelményeket, majd válassza a **Tevékenység létrehozása és futtatása** lehetőséget.

## <a name="specify-source-details"></a>Forrás adatainak megadása
1. A **Forrás adatainak hozzáadása** képernyőn adja meg a forrásként szolgáló MySQL-példány kapcsolati adatait.
 
    ![A Forrás adatainak hozzáadása képernyő](media\tutorial-mysql-to-azure-mysql-online\dms-add-source-details.png)   

## <a name="specify-target-details"></a>Cél adatainak megadása
1. Válassza a **Mentés** lehetőséget, majd a **Cél részletei** képernyőn adja meg a célul szolgáló Azure Database for MySQL-kiszolgáló kapcsolati adatait. Ez a cél az Azure Database for MySQL azon példánya, amelyen üzembe helyezte az **Employees** sémát a mysqldump segédprogrammal.

    ![A részleteket tartalmazó képernyő](media\tutorial-mysql-to-azure-mysql-online\dms-add-target-details.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis neve megegyezik a forrásadatbázis nevével, az Azure Database Migration Service alapértelmezés szerint a céladatbázist választja ki.

    ![Leképezés céladatbázisokra](media\tutorial-mysql-to-azure-mysql-online\dms-map-target-details.png)

3.  Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media\tutorial-mysql-to-azure-mysql-online\dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása
- Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Inicializálás**.

## <a name="monitor-the-migration"></a>A migrálás monitorozása
1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Befejezve** értékre.

     ![Tevékenység állapota: Befejezve](media\tutorial-mysql-to-azure-mysql-online\dms-activity-completed.png)

2. Az **Adatbázis neve** mezőben válassza ki az adatbázist, az **Adatok teljes betöltése** és a **Növekményes adatszinkronizálás** műveletek migrálási állapotának megtekintéséhez.

    Az Adatok teljes betöltésénél az első betöltés migrálási állapota jelenik meg, a Növekményes adatszinkronizálásnál pedig az Adatváltozások rögzítése (CDC) állapot látható.
   
     ![Tevékenység állapota – Teljes betöltés kész](media\tutorial-mysql-to-azure-mysql-online\dms-activity-full-load-completed.png)

     ![Tevékenység állapota – Növekményes adatszinkronizálás](media\tutorial-mysql-to-azure-mysql-online\dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása
Az első teljes betöltés elkészültével az adatbázisok **Átállásra kész** állapotúak lesznek.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

    ![Átállás indítása](media\tutorial-mysql-to-azure-mysql-online\dms-start-cutover.png)
 
2.  Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.
3.  Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázis migrálási állapota **Befejezve** értékre vált, csatlakoztassa alkalmazásait az új Azure SQL Database-céladatbázishoz.
 
## <a name="next-steps"></a>További lépések
- Az Azure Database for MySQL-be történő online migrálás végrehajtásakor felmerülő ismert hibákhoz és korlátozásokhoz kapcsolódó információk: [Az Azure Database for MySQL online migrálásával kapcsolatos ismert hibák és kerülő megoldások](known-issues-azure-mysql-online.md).
- Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Az Azure Database for MySQL szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database for MySQL?](https://docs.microsoft.com/azure/mysql/overview).