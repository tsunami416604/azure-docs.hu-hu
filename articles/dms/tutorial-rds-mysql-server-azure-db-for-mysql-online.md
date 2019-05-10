---
title: 'Oktatóanyag: Azure Database Migration Service használata egy online migrálás RDS MySQL az Azure Database for MySQL-hez |} A Microsoft Docs'
description: Ismerkedjen meg az áttelepítés egy online RDS MySQL az Azure Database for MySQL-hez az Azure Database Migration Service használatával.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: e971fd160a43be088f6d3c4a9fb6fddc7dd769b0
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415673"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Oktatóanyag: RDS MySQL migrálása az Azure Database for MySQL-hez online a DMS használatával

Azure Database Migration Service segítségével adatbázisokat migrálhat egy RDS MySQL-példány [, Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) során az áttelepítés során a forrás-adatbázis online állapotban marad. Más szóval az alkalmazás minimális állásideje érhető el áttelepítési. Ebben az oktatóanyagban áttelepítése a **alkalmazottak** mintaadatbázist az RDS MySQL egy példányát az Azure Database for MySQL-hez az online migrálás tevékenység az Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * A minta séma áttelepítése a mysqldump és a mysql parancssori segédeszközöket.
> * Hozzon létre egy Azure Database Migration Service példányát.
> * Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.

> [!NOTE]
> Az Azure Database Migration Service online áttelepítése használatához hozzon létre egy példányt prémium tarifacsomagban alapján. További információkért tekintse meg az Azure Database Migration Service [díjszabás](https://azure.microsoft.com/pricing/details/database-migration/) lapot.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a migrálási folyamat optimalizálása érdekében ugyanabban az Azure-régióban hozza létre az Azure Database Migration Service-példányt, mint amelyikben a céladatbázis is található. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk azt ismerteti, hogyan hajthat végre egy online migrálás RDS MySQL egy példányból az Azure Database for MySQL.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Győződjön meg arról, hogy a forrás MySQL-kiszolgáló fut-e a támogatott MySQL community Edition kiadását. Annak megállapításához, a MySQL-példány verziója a mysql segédprogram vagy a MySQL Workbench, futtassa a parancsot:

    ```
    SELECT @@version;
    ```

    További információkért tekintse meg a cikket [támogatott, Azure Database for MySQL-verziók](https://docs.microsoft.com/azure/mysql/concepts-supported-versions).

* Töltse le és telepítse a [MySQL **alkalmazottak** mintaadatbázis](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Hozzon létre egy példányt [, Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Hozzon létre egy Azure virtuális hálózaton (VNet) az Azure Resource Manager üzembe helyezési modell, amely lehetővé teszi a helyek közötti kapcsolatot a helyszíni adatforrás-kiszolgálók használatával vagy az Azure Database Migration Service [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). A virtuális hálózatok létrehozásával kapcsolatos további információkért lásd: a [Virtual Network-dokumentáció](https://docs.microsoft.com/azure/virtual-network/), és különösen a témakör részletesen a rövid útmutató cikkek.
* Győződjön meg arról, hogy a virtuális hálózatok közötti hálózati biztonsági csoport szabályai nem blokkolják a következő bejövő kommunikációs portokat, Azure Database Migration Service: 443-as, 53-as és 9354-es, 445-ös, valamint a 12000. Az Azure VNet NSG-forgalom szűrése további részletekért tekintse meg a cikket [hálózati biztonsági csoportokkal a hálózati forgalom szűrése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat az Azure Database Migration Service a forrás MySQL-kiszolgáló, amely alapértelmezés szerint a TCP-port 3306-os eléréséhez.
* Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.
* Hozzon létre egy kiszolgálószintű [tűzfalszabály](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) számára az Azure Database for MySQL-kiszolgálóhoz a céladatbázis Azure Database Migration Service hozzáférésének engedélyezéséhez. Adja meg a virtuális hálózat, Azure Database Migration Service használt alhálózat tartományán.

> [!NOTE]
> Azure Database for MySQL csak a táblák InnoDB támogatja. MyISAM-táblák InnoDB konvertálásához című cikket [konvertálása a MyISAM kívánt táblák InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>Az AWS RDS MySQL beállítása a replikációhoz

1. Egy új paramétercsoport létrehozásához kövesse az utasításokat a cikk az AWS által biztosított [MySQL-adatbázis naplófájljai](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html), a a **bináris naplózási formátum** szakaszban.
2. Hozzon létre egy új paramétercsoport a következő beállításokkal:
    * binlog_format = sor
    * binlog_checksum = NONE
3. Mentse az új paramétercsoportot.

## <a name="migrate-the-schema"></a>A séma migrálása

1. A séma kiolvasása a forrás-adatbázis, és a alkalmazni a céladatbázis minden adatbázis-objektumok, például a táblasémákat, indexek és tárolt eljárások áttelepítésének befejezéséhez.

    Csak a séma migrálása a legegyszerűbb módja az, hogy mysqldump használja a--no-adatok paraméterrel. A parancs a séma áttelepítése a következő:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Ha például egy séma-fájl kiírása a **alkalmazottak** adatbázis, a következő paranccsal:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. A célként megadott szolgáltatás, amely az Azure Database for MySQL importálja a sémát. A memóriakép-fájl visszaállításához futtassa a következő parancsot:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Importálja a sémát a például a **alkalmazottak** adatbázis:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Ha a sémában idegen kulcsok szerepelnek, a migrálás első betöltése és folyamatos szinkronizálása sikertelen lesz. Bontsa ki a legördülő idegen kulcs parancsfájlt, és futtassa a következő szkriptet a MySQL Workbench célhely (az Azure Database for MySQL-hez), külső kulcs parancsfájl hozzáadása:

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

4. Futtassa a lekérdezés eredményét a külső kulcs eldobásához dobja el külső kulcs (amely a második oszlop).

5. Ha az adatok eseményindítók (insert vagy update trigger), azt adatok integritását a cél a előtt replikálja az adatokat a forrás-kényszeríti. Az javasoljuk, hogy tiltsa le az összes tábla eseményindítókat *a cél* és az áttelepítést, majd engedélyezheti azt az eseményindítók a migrálás után végezze el.

    A céladatbázisban eseményindítók letiltása:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Ha valamelyik tábla ENUM adatok típusú példányok, javasoljuk, hogy ideiglenesen frissítse a céloldali tábla karakter különböző adattípusra. Adatreplikálás befejeződése után majd visszatér az adattípus enum.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Válassza ki az előfizetést, amelyben az Azure Database Migration Service példányát létre, és válassza ki a kívánt **erőforrás-szolgáltatók**.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki a helyet, ahol az Azure Database Migration Service példányát létre szeretné.

5. Válasszon ki egy meglévő Vnetet, vagy hozzon létre egy újat.

    A virtuális hálózat az Azure Database Migration Service biztosít hozzáférést a forrás MySQL-példányt, és a cél Azure Database for MySQL-példányt.

    Virtuális hálózat létrehozása az Azure Portalon kapcsolatos további információkért tekintse meg a cikket [hozzon létre egy virtuális hálózatot az Azure portal használatával](https://aka.ms/DMSVnet).

6. Válasszon egy tarifacsomagot; az online áttelepítés ügyeljen arra, hogy válassza ki a prémium szintű: 4vCores tarifacsomagot.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.

     ![Az Azure Database Migration Service személyes példányának megkeresése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az a **új migrálási projekt** lapon adjon meg egy nevet a projektnek a **forráskiszolgáló típusa** szövegbeviteli mezőben válasszon ki **MySQL**, majd a **cél kiszolgáló típusa** szövegbeviteli mezőben válasszon ki **AzureDbForMySQL**.
5. Az a **válassza ki a tevékenység típusát** szakaszban jelölje be **Online adatáttelepítés**.

    > [!IMPORTANT]
    > Ügyeljen arra, hogy válasszon **Online adatáttelepítés**; a kapcsolat nélküli migrálások nem támogatottak ebben a forgatókönyvben.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Választhatja azt is megteheti, **csak a Create project** a migrálási projekt létrehozása, és később a migrálás végrehajtására.

6. Kattintson a **Mentés** gombra.

7. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

    > [!NOTE]
    > Ellenőrizze, hogy jegyezze fel a projekt létrehozása panelen online migrálás beállításához szükséges előfeltételeket.

## <a name="specify-source-details"></a>Forrás adatainak megadása

* Az a **Migrálási forrás részletei** képernyőn, adja meg a kapcsolódási adatok a forrás MySQL-példányt.

   ![Forrás részletei](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza ki **mentése**, majd a a **cél részletei** képernyőn, adja meg a cél Azure Database for MySQL-kiszolgáló, amely előre ki van építve, és a kapcsolódási adatait a **alkalmazottak** MySQLDump használatával telepítette a sémákat.

    ![Cél kiválasztása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis adatbázis neve megegyezik a forrás-adatbázis, Azure Database Migration Service alapértelmezés szerint választja ki a céladatbázisban.

    ![Leképezés céladatbázisokra](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Inicializálás**.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Fut** értékre.

    ![Tevékenység állapota – futó](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. Alatt **ADATBÁZISNÉV**, válasszon ki egy adott adatbázist való migrálás állapota **adatok teljes betöltése** és **adatok növekményes szinkronizálása** műveleteket.

    **Adatok teljes betöltése** jeleníti meg az első terheléselosztó áttelepítési állapotot, amíg **adatok növekményes szinkronizálása** látható adatok rögzítése (CDC) állapotának módosítása.

    ![Készlet képernyő - adatok teljes betöltése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Készlet képernyő - adatok növekményes szinkronizálása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

Miután befejeződött a kezdeti teljes terhelés, az adatbázisok vannak megjelölve az **készen áll a Cutover**.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

    ![Újrakezdés a következő kivágási](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.
3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázisok migrálási állapotát jeleníti meg **befejezve**, az alkalmazások az új cél Azure Database for MySQL-adatbázishoz való csatlakozáshoz.

A MySQL-hez a helyszíni példányát az Azure Database MySQL, online migrálás befejeződött.

## <a name="next-steps"></a>További lépések

* Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Azure Database for MySQL-hez információt lásd: a cikk [Mi az Azure Database for MySQL?](https://docs.microsoft.com/azure/mysql/overview).
* Ha más kérdése van, e-mailben a [kérje meg az Azure adatbázis-Migrálási](mailto:AskAzureDatabaseMigrations@service.microsoft.com) alias.
