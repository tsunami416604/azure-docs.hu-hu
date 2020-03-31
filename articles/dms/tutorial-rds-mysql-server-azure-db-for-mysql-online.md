---
title: 'Oktatóanyag: Az RDS MySQL áttelepítése online a MySQL Azure-adatbázisába'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan hajthat végre online áttelepítést az RDS MySQL-ről az Azure Database Migration Service használatával az Azure Database Migration Service használatával.
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
ms.openlocfilehash: c34de48d0184057f42d1b779abee56e1fa9ac169
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255170"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Oktatóanyag: Az RDS-mySQL áttelepítése az Azure Database for MySQL-be online a DMS használatával

Az Azure Database Migration Service segítségével adatbázisokat telepíthet át egy RDS MySQL-példányból az [Azure Database for MySQL-be,](https://docs.microsoft.com/azure/mysql/) miközben a forrásadatbázis online marad az áttelepítés során. Más szóval az áttelepítés minimális állásidővel érhető el az alkalmazáshoz. Ebben az oktatóanyagban az **Alkalmazottak** mintaadatbázisát az RDS MySQL egy példányából az Azure Database for MySQL-be telepíti át az Azure Database Migration Service online áttelepítési tevékenységének használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Telepítse át a mintasémát a mysqldump és a mysql segédprogramok használatával.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.

> [!NOTE]
> Az Azure Database Migration Service online áttelepítés végrehajtásához létre kell adnia egy példányt a prémium díjszabási szint alapján. További információkért tekintse meg az Azure Database Migration Service [díjszabási](https://azure.microsoft.com/pricing/details/database-migration/) lap.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a migrálási folyamat optimalizálása érdekében ugyanabban az Azure-régióban hozza létre az Azure Database Migration Service-példányt, mint amelyikben a céladatbázis is található. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk ismerteti, hogyan hajthatja végre az online áttelepítés egy példányát RDS MySQL azure Database for MySQL.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Győződjön meg arról, hogy a forrás MySQL szerver en támogatott MySQL közösségi kiadás fut. A MySQL-példány verziójának meghatározásához a mysql segédprogramban vagy a MySQL Workbench-ben futtassa a következő parancsot:

    ```
    SELECT @@version;
    ```

    További információt a [Támogatott Azure-adatbázis a MySQL-verziókhoz című cikkben](https://docs.microsoft.com/azure/mysql/concepts-supported-versions)talál.

* Töltse le és telepítse a [MySQL **Alkalmazottak** mintaadatbázist.](https://dev.mysql.com/doc/employee/en/employees-installation.html)
* Hozzon létre egy példányt az [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal)számára.
* Hozzon létre egy Microsoft Azure virtuális hálózatot az Azure adatbázis-áttelepítési szolgáltatáshoz az Azure Resource Manager telepítési modelljével, amely az [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával biztosít helyek közötti kapcsolatot a helyszíni forráskiszolgálókhoz. A virtuális hálózat létrehozásáról további információt a [Virtuális hálózati dokumentációban](https://docs.microsoft.com/azure/virtual-network/)és különösen a részletes en című rövid útmutatóban talál.
* Győződjön meg arról, hogy a virtuális hálózati biztonsági csoport szabályai nem blokkolják a következő bejövő kommunikációs portokat az Azure Database Migration Service szolgáltatásba: 443, 53, 9354, 445 és 12000. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részleteket a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal című témakörben olvashat.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)
* Állítsa be a [Windows tűzfalat](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (vagy a Linux tűzfalat) az adatbázis-motor elérésének engedélyezéséhez. MySQL-kiszolgáló esetén engedélyezze a 3306-os portot a kapcsolathoz.

> [!NOTE]
> Az Azure Database for MySQL csak az InnoDB-táblákat támogatja. A MyISAM táblák InnoDB-ra való konvertálásához olvassa el a [Táblák konvertálása MyISAM-ből InnoDB-ba](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) című témakört.

### <a name="set-up-aws-rds-mysql-for-replication"></a>Az AWS RDS MySQL beállítása replikációra

1. Új paramétercsoport létrehozásához kövesse az AWS utasításait a [MySQL Adatbázisnaplófájlok](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html), a Bináris naplózási formátum szakaszban található MySQL Database Log Files ( **Bináris naplózási formátum )** című cikkben.
2. Hozzon létre egy új paramétercsoportot a következő konfigurációval:
    * binlog_format = row
    * binlog_checksum = NINCS
3. Mentse az új paramétercsoportot.
4. Társítsa az új paramétercsoportot az RDS MySQL-példányhoz. Lehet, hogy újra kell indítani.

## <a name="migrate-the-schema"></a>A séma áttelepítése

1. Bontsa ki a sémát a forrásadatbázisból, és alkalmazza a céladatbázisra az összes adatbázis-objektum, például táblasémák, indexek és tárolt eljárások áttelepítésének befejezéséhez.

    Csak a sémát telepítheti át, ha a mysqldump-ot a --no-data paraméterrel használja. A séma áttelepítésére a következő parancsot kell parancsolni:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Ha például az **Alkalmazottak** adatbázissémafájlját szeretné kidobni, használja a következő parancsot:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importálja a sémát a célszolgáltatásba, amely az Azure Database for MySQL. A sémamemória-fájl visszaállításához futtassa a következő parancsot:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Például az **Alkalmazottak** adatbázis sémaimportálásához:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Ha a sémában idegen kulcsok szerepelnek, a migrálás első betöltése és folyamatos szinkronizálása sikertelen lesz. A drop foreign key parancsfájl kibontásához és az idegen kulcsparancsfájl nak a célhelyen (Azure Database for MySQL) való hozzáadásához futtassa a következő parancsfájlt a MySQL Workbench programban:

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

4. Futtassa a drop idegen kulcsot (amely a második oszlop) a lekérdezés eredményében az idegen kulcs eldobásához.

5. Ha az adatokban eseményindítók (beszúrásvagy frissítés) vannak, a forrásból származó adatok replikálása előtt kényszeríti ki a cél adatintegritását. A javaslat az, hogy tiltsa le az eseményindítók a cél összes *táblájában az* áttelepítés során, majd engedélyezze az eseményindítók áttelepítés után befejeződött.

    Az eseményindítók letiltása a céladatbázisban:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Ha az ENUM adattípus példányai vannak bármely táblában, javasoljuk, hogy ideiglenesen frissítsen a céltábla "karakterváltozó" adattípusára. A WHen adatreplikáció befejeződött, majd visszaállítja az adattípust AZ ENUM-ra.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Válassza ki azt az előfizetést, amelyben létre szeretné hozni az Azure Database Migration Service példányát, majd válassza **az Erőforrás-szolgáltatók**lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd jobbra a **Microsoft.DataMigration**programtól, és válassza a **Regisztráció**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Az Azure Database Migration Service példányának létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, ahol létre szeretné hozni az Azure Database Migration Service példányát.

5. Jelöljön ki egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat hozzáférést biztosít az Azure Database Migration Service számára a forrás MySQL-példányhoz és a MySQL-példány cél Azure-adatbázisához.

    A virtuális hálózat Azure Portalon való létrehozásáról további információt a Virtuális hálózat létrehozása az Azure Portal használatával című témakörben [talál.](https://aka.ms/DMSVnet)

6. Válasszon ki egy tarifacsomagot; az online áttelepítéshez válassza ki a Prémium: 4vCores tarifacsomagot.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.

     ![Az Azure Database Migration Service személyes példányának megkeresése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az **Új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **Forráskiszolgáló típusa** mezőben válassza a **MySQL**lehetőséget, majd a **Célkiszolgáló típusa** szövegmezőben válassza az **AzureDbForMySQL**lehetőséget.
5. A **Tevékenység típusának kiválasztása** csoportban válassza az **Online adatáttelepítés**lehetőséget.

    > [!IMPORTANT]
    > Ügyeljen arra, hogy válassza **az online adatok áttelepítése**; offline áttelepítések nem támogatottak ebben a forgatókönyvben.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Másik lehetőségként **választhatja** a Projekt létrehozása csak az áttelepítési projekt most, és az áttelepítés későbbi végrehajtásához lehetőséget.

6. Kattintson a **Mentés** gombra.

7. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

    > [!NOTE]
    > Kérjük, jegyezze fel az online áttelepítés beállításához szükséges előfeltételeket a projektlétrehozási panelen.

## <a name="specify-source-details"></a>Forrás adatainak megadása

* Az **áttelepítési forrás részletei** képernyőn adja meg a kapcsolat részleteit a forrás MySQL-példány.

   ![Forrás részletei](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés**lehetőséget, majd a **Cél részletei** képernyőn adja meg a mySQL-kiszolgálóhoz használt azure-adatbázis kapcsolatának részleteit, amely előre ki van építve, és a **MySQLDump** használatával üzembe helyezett Alkalmazottak sémát telepíti.

    ![Cél kiválasztása](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrásadatbázis, az Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázist.

    ![Leképezés céladatbázisokra](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Inicializálás**.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Fut** értékre.

    ![Tevékenység állapota - futás](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. A **DATABASE NAME csoportban**válasszon ki egy adott adatbázist a **Teljes adatbetöltés** és a **Növekményes adatszinkronizálási** műveletek áttelepítési állapotának megkerüléséhez.

    **A teljes adatterhelés** a kezdeti betöltési áttelepítés állapotát mutatja, míg **a Növekményes adatszinkronizálás** a változásadat-rögzítési (CDC) állapotot mutatja.

    ![Leltár képernyő - teljes adatterhelés](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Leltár képernyő - növekményes adatszinkronizálás](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

A kezdeti teljes terhelés befejezése után az adatbázisok **Ready to Cutover (Ready to Cutover)** jelöléssel vannak ellátva.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

    ![Újravágás](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.
3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázis-áttelepítési állapot **azt mutatja, befejezett,** csatlakoztassa az alkalmazásokat az új cél Azure-adatbázis MySQL-adatbázis.

A MySQL helyszíni példányának online áttelepítése a MySQL Azure Database for MySQL-re befejeződött.

## <a name="next-steps"></a>További lépések

* Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Az Azure Database for MySQL szolgáltatásról a [Mi az Azure Database for MySQL című](https://docs.microsoft.com/azure/mysql/overview)cikkben olvashat.
* További kérdések esetén küldjön e-mailt az [Azure-adatbázis-áttelepítések ask](mailto:AskAzureDatabaseMigrations@service.microsoft.com) aliasának.
