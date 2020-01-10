---
title: 'Oktatóanyag: az RDS PostgreSQL online átmigrálása a Azure Database for PostgreSQLba'
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan hajthat végre egy online áttelepítést az RDS PostgreSQL-ből Azure Database for PostgreSQL a Azure Database Migration Service használatával.
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
ms.openlocfilehash: 0930afeb02c79c9b3cf1da791e8cc5cda83c2820
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751268"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Oktatóanyag: az RDS PostgreSQL migrálása Azure Database for PostgreSQL online állapotba a DMS használatával

A Azure Database Migration Service segítségével telepítheti át az adatbázisokat egy RDS PostgreSQL-példányból, hogy [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) , amíg a forrásadatbázis online állapotban marad az áttelepítés során. Más szóval a Migrálás az alkalmazás minimális állásidővel is elérhető. Ebben az oktatóanyagban áttelepíti a **DVD-kölcsönzési** minta adatbázisát az RDS PostgreSQL 9,6 egy példányáról, hogy Azure Database for PostgreSQL a Azure Database Migration Service Online áttelepítési tevékenységének használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Telepítse át a minta sémát a pg_dump segédprogram használatával.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.

> [!NOTE]
> Az online áttelepítés végrehajtásához a Azure Database Migration Service használatával egy példányt kell létrehozni a prémium szintű díjszabás alapján. További információkért tekintse meg a Azure Database Migration Service [díjszabását](https://azure.microsoft.com/pricing/details/database-migration/) ismertető oldalt.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a migrálási folyamat optimalizálása érdekében ugyanabban az Azure-régióban hozza létre az Azure Database Migration Service-példányt, mint amelyikben a céladatbázis is található. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk bemutatja, hogyan végezhető el az online áttelepítés a PostgreSQL helyszíni példányáról a Azure Database for PostgreSQLba.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Töltse le és telepítse a [PostgreSQL Community edition](https://www.postgresql.org/download/) 9,5, 9,6 vagy 10 verziót. A forrás PostgreSQL-kiszolgáló verziójának 9.5.11, 9.6.7, 10 vagy újabbnak kell lennie. További információkért lásd a [PostgreSQL-adatbázisok támogatott verzióit](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)ismertető cikket.

    Emellett az RDS PostgreSQL-verziónak meg kell egyeznie a Azure Database for PostgreSQL verziójával. Például az RDS PostgreSQL 9.5.11.5 csak Azure Database for PostgreSQL 9.5.11, és nem a 9.6.7 verzióra tud áttérni.

* [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)-példány létrehozása. A pgAdmin segítségével a PostgreSQL-kiszolgálóhoz való kapcsolódással kapcsolatos részletekért tekintse meg a dokumentum ezen [szakaszát](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin) .
* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Service számára a Azure Resource Manager üzemi modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával. A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg a [Virtual Network dokumentációt](https://docs.microsoft.com/azure/virtual-network/), és különösen a gyors üzembe helyezési cikkeket részletesen ismerteti.
* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport szabályai nem gátolják meg a következő bejövő kommunikációs portokat a Azure Database Migration Service: 443, 53, 9354, 445 és 12000. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)című cikket.
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat, hogy a Azure Database Migration Service hozzáférhessen a forrás PostgreSQL-kiszolgálóhoz, amely alapértelmezés szerint a 5432-es TCP-port.
* Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.
* Hozzon létre egy kiszolgálói szintű [Tűzfalszabály](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a Azure Database for PostgreSQL kiszolgáló számára, amely lehetővé teszi Azure Database Migration Service hozzáférést a célként megadott adatbázisokhoz. Adja meg a Azure Database Migration Service használt virtuális hálózat alhálózati tartományát.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>AWS RDS PostgreSQL beállítása replikáláshoz

1. Új paraméterérték létrehozásához kövesse az AWS által az [adatbázis-paraméter-csoportok használata](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html)című cikkben ismertetett utasításokat.
2. A fő felhasználónevet használva kapcsolódjon a forráshoz Azure Database Migration Service. Ha a fő felhasználói fióktól eltérő fiókot használ, a fióknak a rds_superuser szerepkörrel és a rds_replication szerepkörrel kell rendelkeznie. A rds_replication szerepkör a logikai tárolóhelyek kezeléséhez és az adatstreamek logikai tárolóhelyek használatával történő továbbításához biztosít engedélyeket.
3. Hozzon létre egy új paraméter-csoportot a következő konfigurációval: a. Állítsa az RDS. logical_replication paramétert az DB-paraméter csoportjába az 1 értékre.
    b. max_wal_senders = [egyidejű feladatok száma] – a max_wal_senders paraméter a futtatott egyidejű feladatok számát állítja be, 10 feladatot ajánl fel.
    c. max_replication_slots – = [bővítőhelyek száma], ajánlott öt bővítőhelyre beállítani.
4. Rendelje hozzá a létrehozott típusparaméter-csoportot az RDS PostgreSQL-példányhoz.

## <a name="migrate-the-schema"></a>A séma migrálása

1. Bontsa ki a sémát a forrás-adatbázisból, és alkalmazza a célként megadott adatbázisra az összes adatbázis-objektum, például a táblázat sémái, indexek és tárolt eljárások áttelepítésének befejezéséhez.

    Csak a séma áttelepítésének legegyszerűbb módja a pg_dump használata az-s kapcsolóval. További információ: [példák](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) a postgres pg_dump oktatóanyagban.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Például a **dvdrental** -adatbázishoz tartozó sémafájl kiírásához használja a következő parancsot:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Hozzon létre egy üres adatbázist a cél szolgáltatásban, amely Azure Database for PostgreSQL. Az adatbázisok összekapcsolásához és létrehozásához tekintse meg az alábbi cikkek egyikét:

    * [Azure Database for PostgreSQL-kiszolgáló létrehozása a Azure Portal használatával](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [Azure Database for PostgreSQL létrehozása az Azure CLI használatával](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-azure-cli)

3. Importálja a sémát a cél szolgáltatásba, amely Azure Database for PostgreSQL. A séma memóriaképfájl-fájljának visszaállításához futtassa a következő parancsot:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Példa:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Ha a sémában idegen kulcsok szerepelnek, a migrálás első betöltése és folyamatos szinkronizálása sikertelen lesz. A drop Foreign Key parancsfájl kibontásához és a külső kulcsú parancsfájl hozzáadásához a célhelyen (Azure Database for PostgreSQL) futtassa a következő parancsfájlt a PgAdmin vagy a psql:
  
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

5. A külső kulcs eldobásához futtassa a legördülő idegen kulcsot (amely a második oszlop) a lekérdezési eredményben.

6. Ha az adatokban eseményindítók (INSERT vagy Update trigger) szerepelnek, akkor az adatok a forrásból való replikálása előtt kényszerítve lesznek az adatok integritására a célhelyen. Javasoljuk, hogy az áttelepítés során tiltsa le az eseményindítókat a *célhelyen* lévő összes táblában, majd engedélyezze az eseményindítókat az áttelepítés befejeződése után.

    Eseményindítók letiltása a céladatbázisben:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók**lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, amelyben létre szeretné hozni a Azure Database Migration Service példányát.

5. Válasszon egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat Azure Database Migration Service hozzáférést biztosít a forrás PostgreSQL-példányhoz és a cél Azure Database for PostgreSQL példányhoz.

    Ha további információt szeretne arról, hogyan hozhat létre virtuális hálózatot a Azure Portalban, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](https://aka.ms/DMSVnet)című cikket.

6. Válasszon árképzési szintet; az online áttelepítés esetében ügyeljen arra, hogy a prémium: 4vCores díjszabási szintet válassza.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings4.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.

     ![Az Azure Database Migration Service személyes példányának megkeresése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az **új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **forráskiszolgáló típusa** szövegmezőben válassza ki az **AWS RDS for PostgreSQL**elemet, majd a **célkiszolgáló típusa** szövegmezőben válassza a **Azure Database for PostgreSQL**lehetőséget.
5. A **tevékenység típusának** kiválasztása szakaszban válassza az **online adatáttelepítés**lehetőséget.

    > [!IMPORTANT]
    > Ügyeljen arra, hogy **online adatáttelepítést**válasszon; az offline áttelepítések nem támogatottak ebben a forgatókönyvben.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Másik lehetőségként választhatja a **projekt létrehozása** lehetőséget az áttelepítési projekt létrehozásához, és később végrehajthatja az áttelepítést.

6. Kattintson a **Mentés** gombra.

7. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

    > [!NOTE]
    > Jegyezze fel az online áttelepítés beállításához szükséges előfeltételeket a projekt létrehozása panelen.

## <a name="specify-source-details"></a>Forrás adatainak megadása

* Az **áttelepítési forrás részletei** képernyőn válassza ki a forrás PostgreSQL-példány kapcsolati adatait.

   ![Forrás részletei](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details4.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés**lehetőséget, majd a **cél részletei** képernyőn adja meg a cél Azure Database for PostgreSQL-kiszolgáló kapcsolati adatait, amely előre kiépített, és a DVD- **kölcsönzési** sémát a pg_dump használatával telepíti.

    ![Cél kiválasztása](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-select-target4.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrás-adatbázis, akkor a Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázis-adatbázist.

    ![Leképezés céladatbázisokra](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-targets-activity5.png)

3. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Inicializálás**.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Fut** értékre.

    ![Tevékenység állapota – fut](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. Az **adatbázis neve**területen válasszon ki egy adott adatbázist, amely a **teljes adatterhelés** és a **növekményes adatszinkronizálási** műveletek áttelepítési állapotát adja meg.

    A **teljes adatterhelés** megjeleníti a kezdeti betöltési áttelepítés állapotát, míg a **növekményes adatszinkronizálás** megjeleníti az adatváltozások rögzítésére (CDC) vonatkozó állapotot.

    ![Leltár képernyő – teljes adatterhelés](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Leltár képernyő – növekményes adatszinkronizálás](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

A kezdeti teljes terhelés befejezése után az adatbázisok **készen**állnak a átváltás.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

    ![Kivágás megkezdése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-start-cutover.png)

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.
3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázis-áttelepítési **állapot megjelenik,** az alkalmazásokat az új cél Azure Database for PostgreSQL adatbázishoz kapcsolja.

A PostgreSQL helyszíni példányának Azure Database for PostgreSQL való online áttelepítése már befejeződött.

## <a name="next-steps"></a>Következő lépések

* Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* További információ a Azure Database for PostgreSQLről: mi a [Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
* Ha további kérdése van, küldjön e-mailt az [Azure Database áttelepítési](mailto:AskAzureDatabaseMigrations@service.microsoft.com) aliasra.
