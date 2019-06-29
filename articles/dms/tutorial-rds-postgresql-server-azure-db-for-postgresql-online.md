---
title: 'Oktatóanyag: PostgreSQL-hez készült Azure Database Migration Service használata egy online migrálás az Azure-adatbázishoz a távoli asztali szolgáltatások PostgreSQL |} A Microsoft Docs'
description: Ismerje meg, áttelepítése online a távoli asztali szolgáltatások PostgreSQL az Azure Database for postgresql-hez az Azure Database Migration Service használatával.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: 17fb83bc845de61f7ec0e674f09c0dc73537f2fd
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461582"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Oktatóanyag: A távoli asztali szolgáltatások PostgreSQL migrálása az Azure Database for postgresql-hez online a DMS használatával

Azure Database Migration Service segítségével adatbázisokat migrálhat egy távoli asztali szolgáltatások PostgreSQL-példány [, Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) során az áttelepítés során a forrás-adatbázis online állapotban marad. Más szóval az alkalmazás minimális állásideje érhető el áttelepítési. Ebben az oktatóanyagban áttelepítése a **DVD bérleti** mintaadatbázis egy távoli asztali szolgáltatások PostgreSQL 9.6-példányból, Azure Database for PostgreSQL, az online migrálás tevékenység az Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * A minta séma migrálása a pg_dump segédprogram használatával.
> * Hozzon létre egy Azure Database Migration Service példányát.
> * Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.

> [!NOTE]
> Az Azure Database Migration Service online áttelepítése használatához hozzon létre egy példányt prémium tarifacsomagban alapján. További információkért tekintse meg az Azure Database Migration Service [díjszabás](https://azure.microsoft.com/pricing/details/database-migration/) lapot.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a migrálási folyamat optimalizálása érdekében ugyanabban az Azure-régióban hozza létre az Azure Database Migration Service-példányt, mint amelyikben a céladatbázis is található. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk azt ismerteti, hogyan hajthat végre egy online migrálás PostgreSQL helyszíni példányát az Azure-adatbázis PostgreSQL-hez készült.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Töltse le és telepítse [PostgreSQL közösségi kiadását](https://www.postgresql.org/download/) 9,5, 9.6 vagy 10. A PostgreSQL-kiszolgáló verziója forrás 9.5.11, 9.6.7, 10, kell lennie. vagy újabb. További információkért tekintse meg a cikket [PostgreSQL-adatbázis verziója támogatott](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Emellett a távoli asztali szolgáltatások PostgreSQL-verzió meg kell egyeznie az Azure Database for PostgreSQL-verzió. Például a távoli asztali szolgáltatások PostgreSQL 9.5.11.5 csak áttelepítheti, Azure Database for PostgreSQL 9.5.11 és nem 9.6.7 verzió.

    > [!NOTE]
    > PostgreSQL 10-es verzió, a jelenleg DMS csak támogatja az áttelepítést verzió 10.3 az Azure Database for postgresql-hez.

* Hozzon létre egy példányt [, Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal). Ebben [szakasz](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin) a dokumentum részletes hogyan csatlakozhat a PostgreSQL kiszolgálóhoz a pgAdmin segítségével.
* Hozzon létre egy Azure virtuális hálózaton (VNet) az Azure Resource Manager üzembe helyezési modell, amely lehetővé teszi a helyek közötti kapcsolatot a helyszíni adatforrás-kiszolgálók használatával vagy az Azure Database Migration Service [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). A virtuális hálózatok létrehozásával kapcsolatos további információkért lásd: a [Virtual Network-dokumentáció](https://docs.microsoft.com/azure/virtual-network/), és különösen a témakör részletesen a rövid útmutató cikkek.
* Győződjön meg arról, hogy a virtuális hálózat hálózati biztonsági csoportjának (NSG) szabályai nem blokkolják az Azure Database Migration Service bejövő kommunikációs portokat: 443-as, 53-as és 9354-es, 445-ös, valamint a 12000. Az Azure VNet NSG-forgalom szűrése további részletekért tekintse meg a cikket [hálózati biztonsági csoportokkal a hálózati forgalom szűrése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat az Azure Database Migration Service eléréséhez a forrás PostgreSQL-kiszolgálót, amely alapértelmezés szerint az 5432-es TCP-porton.
* Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.
* Hozzon létre egy kiszolgálószintű [tűzfalszabály](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) számára az Azure Database for PostgreSQL-kiszolgálóhoz a céladatbázis Azure Database Migration Service hozzáférésének engedélyezéséhez. Adja meg a virtuális hálózat, Azure Database Migration Service használt alhálózat tartományán.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Replikációs beállítása az AWS RDS PostgreSQL

1. Egy új paramétercsoport létrehozásához kövesse az utasításokat a cikk az AWS által biztosított [DB paraméter a csoportok kezelése](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. A fő felhasználónév használata az Azure Database Migration Service csatlakozni a forrás. A fő felhasználó aktuálistól eltérő fiókkal használja, ha a fióknak rendelkeznie kell a rds_superuser szerepkör és a rds_replication szerepkör. A rds_replication szerepkör engedélyt ad logikai pontjainak kezelése és az adatok logikai tárhelyek használatával.
3. Hozzon létre egy új paramétercsoport a következő beállításokkal: egy. Állítsa a rds.logical_replication paramétert a DB paraméter 1 csoportban.
    b. max_wal_senders = [egyidejű feladatok számát] – a max_wal_senders paraméter állítja be, amely képes futtatni, javasoljuk 10 feladatok egyidejű feladatok számát.
    c. max_replication_slots – = 5 tárhelyek [helyek száma], ajánlott állítson be.
4. A távoli asztali szolgáltatások PostgreSQL példányhoz létrehozott paraméter csoport hozzárendelése.

## <a name="migrate-the-schema"></a>A séma migrálása

1. A séma kiolvasása a forrás-adatbázis, és a alkalmazni a céladatbázis minden adatbázis-objektumok, például a táblasémákat, indexek és tárolt eljárások áttelepítésének befejezéséhez.

    Csak a séma migrálása a legegyszerűbb módja, hogy pg_dump használja az -s kapcsolóval. További információkért lásd: a [példák](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) a Postgres pg_dump oktatóanyagban.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Ha például egy séma-fájl kiírása a **dvdrental** adatbázis, a következő paranccsal:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. A célként megadott szolgáltatás, amely, Azure Database for PostgreSQL egy üres adatbázis létrehozása. Csatlakozhat, és hozzon létre egy adatbázist, tekintse meg a következő cikkek valamelyikét:

    * [Egy Azure Database for PostgreSQL-kiszolgáló létrehozása az Azure portal használatával](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [Azure Database for PostgreSQL létrehozása az Azure CLI használatával](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-azure-cli)

3. Importálja a sémát a célként megadott szolgáltatás, amely az Azure Database for postgresql-hez. A memóriakép-fájl visszaállításához futtassa a következő parancsot:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Példa:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Ha a sémában idegen kulcsok szerepelnek, a migrálás első betöltése és folyamatos szinkronizálása sikertelen lesz. Bontsa ki a legördülő idegen kulcs parancsfájlt, és adja hozzá a külső kulcs szkriptet a célhelyen (Azure Database for postgresql-hez), a pgadmin alkalmazásban vagy a psql-jének futtassa a következő szkriptet:
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. Futtassa a lekérdezés eredményét a külső kulcs eldobásához dobja el külső kulcs (amely a második oszlop).

6. Ha az adatok eseményindítók (insert vagy update trigger), azt adatok integritását a cél a előtt replikálja az adatokat a forrás-kényszeríti. Az javasoljuk, hogy tiltsa le az összes tábla eseményindítókat *a cél* és az áttelepítést, majd engedélyezheti azt az eseményindítók a migrálás után végezze el.

    A céladatbázisban eseményindítók letiltása:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Válassza ki az előfizetést, amelyben az Azure Database Migration Service példányát létre, és válassza ki a kívánt **erőforrás-szolgáltatók**.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki a helyet, ahol az Azure Database Migration Service példányát létre szeretné.

5. Válasszon ki egy meglévő Vnetet, vagy hozzon létre egy újat.

    A virtuális hálózat Azure Database Migration Service biztosít hozzáférést a forrás PostgreSQL-példány és a cél Azure-adatbázis PostgreSQL-példány.

    Virtuális hálózat létrehozása az Azure Portalon kapcsolatos további információkért tekintse meg a cikket [hozzon létre egy virtuális hálózatot az Azure portal használatával](https://aka.ms/DMSVnet).

6. Válasszon egy tarifacsomagot; az online áttelepítés ügyeljen arra, hogy válassza ki a prémium szintű: 4vCores tarifacsomagot.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings4.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.

     ![Az Azure Database Migration Service személyes példányának megkeresése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az a **új migrálási projekt** lapon adjon meg egy nevet a projektnek a **forráskiszolgáló típusa** szövegbeviteli mezőben válasszon ki **PostgreSQL-hez az AWS RDS**, majd a az**Célkiszolgáló típusa** szövegbeviteli mezőben válasszon ki **, Azure Database for PostgreSQL**.
5. Az a **válassza ki a tevékenység típusát** szakaszban jelölje be **Online adatáttelepítés**.

    > [!IMPORTANT]
    > Ügyeljen arra, hogy válasszon **Online adatáttelepítés**; a kapcsolat nélküli migrálások nem támogatottak ebben a forgatókönyvben.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Választhatja azt is megteheti, **csak a Create project** a migrálási projekt létrehozása, és később a migrálás végrehajtására.

6. Kattintson a **Mentés** gombra.

7. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

    > [!NOTE]
    > Ellenőrizze, hogy jegyezze fel a projekt létrehozása panelen online migrálás beállításához szükséges előfeltételeket.

## <a name="specify-source-details"></a>Forrás adatainak megadása

* Az a **Migrálási forrás részletei** képernyőn, adja meg a forrás PostgreSQL példány kapcsolati adatait.

   ![Forrás részletei](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details4.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza ki **mentése**, majd a a **cél részletei** képernyőn, adja meg a cél Azure Database for PostgreSQL-kiszolgálót, amely előre ki van építve, és a kapcsolódási adatait a **DVD-ről Bérlését** pg_dump használatával telepítette a sémákat.

    ![Cél kiválasztása](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-select-target4.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis adatbázis neve megegyezik a forrás-adatbázis, Azure Database Migration Service alapértelmezés szerint választja ki a céladatbázisban.

    ![Leképezés céladatbázisokra](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-targets-activity5.png)

3. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Inicializálás**.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Fut** értékre.

    ![Tevékenység állapota – futó](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. Alatt **ADATBÁZISNÉV**, válasszon ki egy adott adatbázist való migrálás állapota **adatok teljes betöltése** és **adatok növekményes szinkronizálása** műveleteket.

    **Adatok teljes betöltése** jeleníti meg az első terheléselosztó áttelepítési állapotot, amíg **adatok növekményes szinkronizálása** látható adatok rögzítése (CDC) állapotának módosítása.

    ![Készlet képernyő - adatok teljes betöltése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Készlet képernyő - adatok növekményes szinkronizálása](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

Miután befejeződött a kezdeti teljes terhelés, az adatbázisok vannak megjelölve az **készen áll a Cutover**.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

    ![Újrakezdés a következő kivágási](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-start-cutover.png)

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.
3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázisok migrálási állapotát jeleníti meg **befejezve**, az alkalmazások az új cél Azure Database for postgresql-hez való csatlakozáshoz.

A PostgreSQL-hez a helyszíni példányát az Azure Database-PostgreSQL online migrálás befejeződött.

## <a name="next-steps"></a>További lépések

* Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Azure Database for postgresql-hez információt lásd: a cikk [Mi az Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
* Ha más kérdése van, e-mailben a [kérje meg az Azure adatbázis-Migrálási](mailto:AskAzureDatabaseMigrations@service.microsoft.com) alias.
