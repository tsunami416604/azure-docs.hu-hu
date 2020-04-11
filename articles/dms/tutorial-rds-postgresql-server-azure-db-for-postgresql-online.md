---
title: 'Oktatóanyag: Az RDS PostgreSQL áttelepítése online a PostgreSQL Azure-adatbázisába'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan hajthat végre online áttelepítést az RDS PostgreSQL-ről a PostgreSQL-adatbázisba az Azure Database Migration Service használatával.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 04/11/2020
ms.openlocfilehash: be6f0cd734d31f43557b49f8e9314e925b383899
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113949"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Oktatóanyag: Az RDS PostgreSQL áttelepítése az Azure DB-be a PostgreSQL-hez online a DMS használatával

Az Azure Database Migration Service segítségével adatbázisokat telepíthet át egy RDS PostgreSQL-példányból az [Azure Database for PostgreSQL-be,](https://docs.microsoft.com/azure/postgresql/) miközben a forrásadatbázis online marad az áttelepítés során. Más szóval az áttelepítés minimális állásidővel érhető el az alkalmazáshoz. Ebben az oktatóanyagban az RDS PostgreSQL 9.6 egy példányából telepíti át a **DVD-kölcsönzési** mintaadatbázist az Azure Database Migration Service online áttelepítési tevékenységének használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Telepítse át a mintasémát a pg_dump segédprogrammal.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.
> * Az áttelepítés átállásának végrehajtása.

> [!NOTE]
> Az Azure Database Migration Service online áttelepítés végrehajtásához létre kell adnia egy példányt a prémium díjszabási szint alapján. További információkért tekintse meg az Azure Database Migration Service [díjszabási](https://azure.microsoft.com/pricing/details/database-migration/) lap. Titkosítjuk a lemezt, hogy megakadályozzuk az adatlopást az áttelepítés során.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a migrálási folyamat optimalizálása érdekében ugyanabban az Azure-régióban hozza létre az Azure Database Migration Service-példányt, mint amelyikben a céladatbázis is található. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk ismerteti, hogyan hajthatja végre az online áttelepítés egy helyszíni példányát a PostgreSQL az Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Töltse le és telepítse a PostgreSQL 9.5, 9.6 vagy 10 [közösségi kiadását.](https://www.postgresql.org/download/) A forrás PostgreSQL Server verziónak 9.5.11, 9.6.7, 10 vagy újabb lehet. További információt a [Támogatott PostgreSQL adatbázis-verziók](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)című cikkben talál.

   Azt is vegye figyelembe, hogy a cél Azure-adatbázis PostgreSQL-verzió egyenlőnek kell lennie, vagy később, mint az RDS PostgreSQL verzió. Az RDS PostgreSQL 9.6 például csak a PostgreSQL 9.6-os, 10-es vagy 11-es Azure Database adatbázisába tud áttérni, de a PostgreSQL 9.5-höz készült Azure Database adatbázisba nem.

* Hozzon létre egy példányt az [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) vagy [az Azure Database for PostgreSQL - Hyperscale (Citus)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)számára. A cikk ezen [részében](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin) részletesen tájékot megtudhatja, hogyan csatlakozhat a PostgreSQL Kiszolgálóhoz a pgAdmin használatával.
* Hozzon létre egy Microsoft Azure virtuális hálózatot az Azure adatbázis-áttelepítési szolgáltatáshoz az Azure Resource Manager telepítési modelljével, amely az [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával biztosít helyek közötti kapcsolatot a helyszíni forráskiszolgálókhoz. A virtuális hálózat létrehozásáról további információt a [Virtuális hálózati dokumentációban](https://docs.microsoft.com/azure/virtual-network/)és különösen a részletes en című rövid útmutatóban talál.
* Győződjön meg arról, hogy a virtuális hálózati biztonsági csoport szabályai nem blokkolják a következő bejövő kommunikációs portokat az Azure Database Migration Service szolgáltatásba: 443, 53, 9354, 445 és 12000. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részleteket a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal című témakörben olvashat.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat, hogy az Azure Database Migration Service hozzáférhessen a Forrás PostgreSQL-kiszolgálóhoz, amely alapértelmezés szerint az 5432-es TCP-port.
* Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.
* Hozzon létre egy kiszolgálószintű [tűzfalszabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure Database for PostgreSQL-kiszolgálóhoz, amely lehetővé teszi az Azure Database Migration Service hozzáférését a céladatbázisokhoz. Adja meg az Azure Database Migration Service használt virtuális hálózat alhálózati tartományát.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Az AWS RDS PostgreSQL beállítása replikációra

1. Új paramétercsoport létrehozásához kövesse az AWS utasításait a [Db parameter groups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html)című cikkben.
2. A fő felhasználónév segítségével csatlakozzon a forráshoz az Azure Database Migration Service.Use the master user name to connect to the source from Azure Database Migration Service. Ha a fő felhasználói fióktól eltérő fiókot használ, a fióknak rds_superuser szerepkörrel és rds_replication szerepkörrel kell rendelkeznie. A rds_replication szerepkör engedélyeket ad a logikai tárolóhelyek kezeléséhez és az adatok logikai tárolóhelyek használatával történő streameléséhez.
3. Hozzon létre egy új paramétercsoportot a következő konfigurációval:

    a. Állítsa az rds.logical_replication paramétert a DB paramétercsoportban 1-re.

    b. max_wal_senders =[egyidejű feladatok száma] - A max_wal_senders paraméter beállítja a futtatható egyidejű feladatok számát, 10 feladatot ajánl.

    c. max_replication_slots – = [bővítőhelyek száma], javasoljuk, hogy öt bővítőhely.

4. Társítsa a létrehozott paramétercsoportot az RDS PostgreSQL-példányhoz.

## <a name="migrate-the-schema"></a>A séma áttelepítése

1. Bontsa ki a sémát a forrásadatbázisból, és alkalmazza a céladatbázisra az összes adatbázis-objektum, például táblasémák, indexek és tárolt eljárások áttelepítésének befejezéséhez.

    Csak a sémát telepítheti át, ha pg_dump használja az -s kapcsolóval. További információkért tekintse meg a [példákat](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) a Postgres pg_dump oktatóanyag.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Ha például a **dvdrental** adatbázis sémafájljának kiírásához a következő parancsot használja:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Hozzon létre egy üres adatbázist a célszolgáltatásban, amely a PostgreSQL Azure Database. Adatbázis csatlakoztatásához és létrehozásához olvassa el az alábbi cikkek egyikét:

    * [Azure-adatbázis létrehozása postgreSQL-kiszolgálóhoz az Azure Portal használatával](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [Azure-adatbázis létrehozása a PostgreSQL - Hyperscale (Citus) kiszolgálóhoz az Azure Portal használatával](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)

3. Importálja a sémát a célszolgáltatásba, amely a PostgreSQL Azure Database. A sémamemória-fájl visszaállításához futtassa a következő parancsot:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Például:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Ha a sémában idegen kulcsok szerepelnek, a migrálás első betöltése és folyamatos szinkronizálása sikertelen lesz. A drop foreign key parancsfájl kibontásához és az idegen kulcs parancsfájljának a célállomáson való hozzáadásához (Azure Database for PostgreSQL) futtassa a következő parancsfájlt a PgAdmin vagy a psql rendszerben:
  
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

5. Futtassa a drop idegen kulcsot (amely a második oszlop) a lekérdezés eredményében az idegen kulcs eldobásához.

6. Ha az adatokban eseményindítók (beszúrásvagy frissítés) vannak, a forrásból származó adatok replikálása előtt kényszeríti ki a cél adatintegritását. A javaslat az, hogy tiltsa le az eseményindítók a cél összes *táblájában az* áttelepítés során, majd engedélyezze az eseményindítók áttelepítés után befejeződött.

    Az eseményindítók letiltása a céladatbázisban:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Válassza ki azt az előfizetést, amelyben létre szeretné hozni az Azure Database Migration Service példányát, majd válassza **az Erőforrás-szolgáltatók**lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd jobbra a **Microsoft.DataMigration**programtól, és válassza a **Regisztráció**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Az Azure Database Migration Service példányának létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, ahol létre szeretné hozni az Azure Database Migration Service példányát.

5. Jelöljön ki egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat hozzáférést biztosít az Azure Database Migration Service számára a forrás PostgreSQL-példányhoz és a PostgreSQL-példány cél Azure-adatbázisához.

    A virtuális hálózat Azure Portalon való létrehozásáról további információt a Virtuális hálózat létrehozása az Azure Portal használatával című témakörben [talál.](https://aka.ms/DMSVnet)

6. Válasszon ki egy tarifacsomagot; az online áttelepítéshez válassza ki a Prémium: 4vCores tarifacsomagot.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, jelölje ki a példányt, majd válassza a + **Új áttelepítési projekt**lehetőséget.
3. Az **Új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **Forráskiszolgáló típusa** mezőben válassza az **AWS RDS for PostgreSQL**lehetőséget, majd a **Célkiszolgáló típusa** mezőbe válassza az Azure Database for **PostgreSQL**lehetőséget.
4. A **Tevékenység típusának kiválasztása** csoportban válassza az **Online adatáttelepítés**lehetőséget.

    > [!IMPORTANT]
    > Ügyeljen arra, hogy válassza **az online adatok áttelepítése**; offline áttelepítések nem támogatottak ebben a forgatókönyvben.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Másik lehetőségként **választhatja** a Projekt létrehozása csak az áttelepítési projekt most, és az áttelepítés későbbi végrehajtásához lehetőséget.

5. Kattintson a **Mentés** gombra.

6. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

    > [!NOTE]
    > Kérjük, jegyezze fel az online áttelepítés beállításához szükséges előfeltételeket a projektlétrehozási panelen.

## <a name="specify-source-details"></a>Forrás adatainak megadása

* A **Forrás részleteinek hozzáadása** képernyőn adja meg a postgreSQL-példány kapcsolatának részleteit.

   ![Forrás részletei](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés**lehetőséget, majd a **Cél részletei** képernyőn adja meg a postgreSQL-kiszolgálóhoz használt azure-adatbázis kapcsolatának részleteit, amely előre ki van építve, és a **DVD Rentals** sémát pg_dump használatával telepíti.

    ![Cél részletei](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrásadatbázis, az Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázist.

    ![Leképezés céladatbázisokra](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Inicializálás**.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Fut** értékre.

    ![Tevékenység állapota - futás](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. A **DATABASE NAME csoportban**válasszon ki egy adott adatbázist a **Teljes adatbetöltés** és a **Növekményes adatszinkronizálási** műveletek áttelepítési állapotának megkerüléséhez.

    **A teljes adatterhelés** a kezdeti betöltési áttelepítés állapotát mutatja, míg **a Növekményes adatszinkronizálás** a változásadat-rögzítési (CDC) állapotot mutatja.

    ![Leltár képernyő - teljes adatterhelés](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Leltár képernyő - növekményes adatszinkronizálás](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

A kezdeti teljes terhelés befejezése után az adatbázisok **Ready to Cutover (Ready to Cutover)** jelöléssel vannak ellátva.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

2. Várjon, amíg a **Függőben lévő módosítások** számláló **0-t** jelenít meg annak érdekében, hogy a forrásadatbázisba beérkező összes tranzakció lelegyen állítva, jelölje be a **Megerősítés** jelölőnégyzetet, majd kattintson az **Alkalmaz gombra.**

    ![Teljes átvágási képernyő](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. Ha az adatbázis-áttelepítésállapota **befejezett,** csatlakoztassa az alkalmazásokat az új cél Azure Database for PostgreSQL adatbázishoz.

Az RDS PostgreSQL helyszíni példányának online áttelepítése a PostgreSQL Azure Database for PostgreSQL-re befejeződött.

## <a name="next-steps"></a>További lépések

* Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* A PostgreSQL Azure Database for-ról a [Mi az Azure Database for PostgreSQL című](https://docs.microsoft.com/azure/postgresql/overview)cikkben olvashat.
* További kérdések esetén küldjön e-mailt az [Azure-adatbázis-áttelepítések ask](mailto:AskAzureDatabaseMigrations@service.microsoft.com) aliasának.
