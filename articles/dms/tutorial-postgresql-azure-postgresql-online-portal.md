---
title: 'Oktatóanyag: A PostgreSQL áttelepítése az Azure DB-be a PostgreSQL-hez az Azure portalon keresztül'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan hajthat végre online áttelepítést a PostgreSQL-ről a helyszíni Azure Database for PostgreSQL szolgáltatásba az Azure Database Migration Service használatával az Azure Portalon keresztül.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 4985c492c8ca71da87cf1a519ebc658c203d3952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246976"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Oktatóanyag: A PostgreSQL áttelepítése az Azure DB-be a PostgreSQL-hez online a DMS használatával az Azure portalon keresztül

Az Azure Database Migration Service segítségével áttelepítheti az adatbázisokat egy helyszíni PostgreSQL-példányból az [Azure Database for PostgreSQL-be,](https://docs.microsoft.com/azure/postgresql/) minimális állásidővel az alkalmazásba. Ebben az oktatóanyagban a **DVD-kölcsönzési** mintaadatbázist a PostgreSQL 9.6 helyszíni példányából az Azure Database for PostgreSQL-be telepíti át az Azure Database Migration Service online áttelepítési tevékenységének használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Telepítse át a mintasémát a pg_dump segédprogrammal.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet az Azure Database Migration Service szolgáltatásban.
> * A migrálás futtatása.
> * A migrálás monitorozása.
> * Az áttelepítés átállásának végrehajtása.

> [!NOTE]
> Az Azure Database Migration Service online áttelepítés végrehajtásához létre kell adnia egy példányt a prémium díjszabási szint alapján. Titkosítjuk a lemezt, hogy megakadályozzuk az adatlopást az áttelepítés folyamata során

> [!IMPORTANT]
> Az optimális áttelepítési élmény érdekében a Microsoft azt javasolja, hogy hozzon létre egy példányt az Azure Database Migration Service ugyanabban az Azure-régióban, mint a cél-adatbázis. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Töltse le és telepítse a PostgreSQL 9.4- es, 9.5-ös, 9.6-os vagy 10-es [kiadását.](https://www.postgresql.org/download/) A forrás PostgreSQL Server verzió nak 9.4, 9.5, 9.6, 10 vagy 11 lehet. További információt a [Támogatott PostgreSQL adatbázis-verziók](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)című cikkben talál.

    Emellett a helyi PostgreSQL verziójának meg kell egyeznie az Azure Database for PostgreSQL verziójával. A PostgreSQL 9.6 például csak a PostgreSQL 9.6- vagy 11-es Azure Database for PostgreSQL 9.6-ra telepíthető át, de a PostgreSQL 9.5-höz készült Azure Database for PostgreSQL 9.5-re nem.

* [Hozzon létre egy Azure-adatbázist a PostgreSQL-kiszolgálóhoz,](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) vagy [hozzon létre egy Azure-adatbázist a PostgreSQL - Hyperscale (Citus) kiszolgálóhoz.](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)
* Hozzon létre egy Microsoft Azure virtuális hálózatot az Azure adatbázis-áttelepítési szolgáltatáshoz az Azure Resource Manager telepítési modelljével, amely az [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával biztosít helyek közötti kapcsolatot a helyszíni forráskiszolgálókhoz. A virtuális hálózat létrehozásáról további információt a [Virtuális hálózati dokumentációban](https://docs.microsoft.com/azure/virtual-network/)és különösen a részletes en című rövid útmutatóban talál.

    > [!NOTE]
    > A virtuális hálózat beállítása során, ha az ExpressRoute szolgáltatást hálózati társviszony-létesítéssel használja a Microsofthoz, adja hozzá a következő [szolgáltatásvégpontokat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) ahhoz az alhálózathoz, amelyben a szolgáltatás ki lesz építve:
    >
    > * Cél adatbázis-végpont (például SQL-végpont, Cosmos DB-végpont és így tovább)
    > * Tárolási végpont
    > * Szolgáltatásbusz végpontja
    >
    > Erre a konfigurációra azért van szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a hálózati biztonsági csoport (NSG) szabályai a virtuális hálózat nem blokkolja a következő bejövő kommunikációs portok az Azure Database Migration Service: 443, 53, 9354, 445, 12000. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részleteket a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal című témakörben olvashat.](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat, hogy az Azure Database Migration Service hozzáférhessen a Forrás PostgreSQL Server, amely alapértelmezés szerint a TCP-port 5432.
* Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.
* Hozzon létre egy kiszolgálószintű [tűzfalszabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure Database for PostgreSQL számára, amely lehetővé teszi az Azure Database Migration Service számára a céladatbázisokhoz való hozzáférést. Adja meg az Azure Database Migration Service használt virtuális hálózat alhálózati tartományát.
* Engedélyezze a logikai replikálást a postgresql.config fájlban, és állítsa be a következő paramétereket:

  * wal_level = **logical**
  * max_replication_slots = [bővítőhelyek száma], javasoljuk, hogy **öt bővítőhelyre**
  * max_wal_senders = [párhuzamos feladatok száma] – a max_wal_senders paraméter megadja a párhuzamosan futtatható feladatok számát, az ajánlott beállítás **10 feladat**

> [!IMPORTANT]
> A meglévő adatbázis összes táblájának elsődleges kulcsra van szüksége, hogy a módosítások szinkronizálhatók legyenek a céladatbázissal.

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása

Ahhoz, hogy az összes adatbázis-objektumot táblasémaként, indexekként és tárolt eljárásokként egészíthessük ki, ki kell bontanunk a forrásadatbázis sémáját, és alkalmaznunk kell az adatbázisra.

1. Pg_dump -s parancs segítségével hozzon létre egy séma-memóriaképfájlt az adatbázishoz.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Például hozzon létre egy sémamemóriakép-fájlt a **dvdrental** adatbázishoz:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    A pg_dump segédprogram használatával kapcsolatos további információkért lásd a [pg-memóriakép](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) oktatóanyagban szereplő példákat.

2. Hozzon létre egy üres adatbázist a célkörnyezetben, amely az Azure Database for PostgreSQL.

    Az adatbázis-csatlakozással és adatbázis-létrehozással kapcsolatos további részletekért tekintse meg az [Azure Database for PostgreSQL-kiszolgáló létrehozása az Azure portalon](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) vagy [az Azure Database for PostgreSQL – Hyperscale (Citus) kiszolgáló létrehozása az Azure portalon című témakört.](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)

    > [!NOTE]
    > A PostgreSQL - Hyperscale (Citus) Azure Database for PostgreSQL ( Hyperscale (Citus) egy példánya csak egyetlen adatbázissal rendelkezik: **citus**.

3. Importálja a sémát a létrehozott céladatbázisba a séma-memóriaképfájl visszaállításával.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Példa:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. A drop foreign key script kibontásához és a célhoz (Azure Database for PostgreSQL) való hozzáadásához a PgAdminban vagy a psql-ben futtassa a következő parancsfájlt.

   > [!IMPORTANT]
   > A sémában lévő idegen kulcsok az áttelepítés kezdeti terhelését és folyamatos szinkronizálását eredményezik.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Futtassa a ’drop foreign key’-t (ez a második oszlop) a lekérdezési eredményben.

6. Az eseményindítók letiltásához futtassa az alábbi parancsfájlt.

   > [!IMPORTANT]
   > Eseményindítók (beszúrása vagy frissítése) az adatok kényszerítése adatintegritás a cél előtt az adatok replikálása a forrásból. Ennek eredményeképpen ajánlott letiltani az eseményindítókat **a cél** összes táblájában az áttelepítés során, majd az áttelepítés befejezése után újra engedélyezni az eseményindítókat.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyben létre szeretné hozni az Azure Database Migration Service példányát, majd válassza **az Erőforrás-szolgáltatók**lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd jobbra a **Microsoft.DataMigration**programtól, és válassza a **Regisztráció**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Az **Áttelepítési szolgáltatás létrehozása** képernyőn adja meg a nevét, az előfizetést, az új vagy meglévő erőforráscsoportot és a szolgáltatás helyét.

4. Jelöljön ki egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat hozzáférést biztosít az Azure Database Migration Service számára a forrás PostgreSQL-kiszolgálóhoz és a cél Azure Database for PostgreSQL-példányhoz.

    A virtuális hálózat Azure Portalon való létrehozásáról további információt a Virtuális hálózat létrehozása az Azure Portal használatával című témakörben [talál.](https://aka.ms/DMSVnet)

5. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. A szolgáltatás létrehozásához válassza a **Véleményezés + létrehozás** lehetőséget.

   A szolgáltatás létrehozása körülbelül 10-15 percen belül befejeződik.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Az Azure Database Migration Service összes példányának megkeresése](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, jelölje ki a példányt, majd válassza a + **Új áttelepítési projekt**lehetőséget.

3. Az **Új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **Forráskiszolgáló típusa** mezőben válassza a **PostgresSQL**lehetőséget a **Célkiszolgáló típusa** szövegmezőben, és válassza az Azure Database for **PostgreSQL**lehetőséget.

4. A **Tevékenység típusának kiválasztása** csoportban válassza az **Online adatáttelepítés**lehetőséget.

    ![Azure-adatbázis-áttelepítési szolgáltatás projekt létrehozása](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Másik lehetőségként **választhatja** a Projekt létrehozása csak az áttelepítési projekt most, és az áttelepítés későbbi végrehajtásához lehetőséget.

5. Válassza a **Mentés**lehetőséget, jegyezze fel az Azure Database Migration Service sikeres használatának követelményeit az adatok áttelepítéséhez, majd válassza **a Tevékenység létrehozása és futtatása**lehetőséget.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Forrás részleteinek hozzáadása** képernyőn adja meg a postgreSQL-példány kapcsolatának részleteit.

    ![A Forrás adatainak hozzáadása képernyő](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. A **Cél részletei** képernyőn adja meg a cél szintű hipermérleges (Citus) kiszolgáló kapcsolatának részleteit, amely a Nagykapacitás (Citus) előre kiépített példánya, amelyre a **DVD Rentals** sémát pg_dump használatával telepítették.

    ![A részleteket tartalmazó képernyő](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrásadatbázis, az Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázist.

    ![Leképezés a céladatbázisokképernyőre](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Válassza a **Mentés**lehetőséget, majd az **Áttelepítési beállítások** képernyőn fogadja el az alapértelmezett értékeket.

    ![Áttelepítési beállítások képernyő](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![Áttelepítés összefoglaló képernyője](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik az áttelepítési tevékenység ablak, és a tevékenység **állapota** a **Folyamatban lévő biztonsági mentés**jelenik meg.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Befejezve** értékre.

     ![Áttelepítési folyamat figyelése](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Amikor az áttelepítés befejeződött, az **Adatbázis neve**területen válasszon ki egy adott adatbázist a **Teljes adatbetöltés** és a **Növekményes adatszinkronizálási** műveletek áttelepítési állapotának megkerüléséhez.

   > [!NOTE]
   > **A teljes adatterhelés** a kezdeti betöltési áttelepítés állapotát mutatja, míg **a Növekményes adatszinkronizálás** a változásadat-rögzítési (CDC) állapotot mutatja.

     ![Teljes adatbetöltési részletek](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Növekményes adatszinkronizálásrészletei](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

Az első teljes betöltés elkészültével az adatbázisok **Átállásra kész** állapotúak lesznek.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

2. Várjon, amíg a **Függőben lévő módosítások** számláló **0-t** jelenít meg annak érdekében, hogy a forrásadatbázisba beérkező összes tranzakció lelegyen állítva, jelölje be a **Megerősítés** jelölőnégyzetet, majd kattintson az **Alkalmaz gombra.**

    ![Teljes átvágási képernyő](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Ha az adatbázis-áttelepítésállapota **befejezett,** csatlakoztassa az alkalmazásokat az Azure Database for PostgreSQL új célpéldányához.

## <a name="next-steps"></a>További lépések

* Az Azure Database for PostgreSQL-be történő online migrálás végrehajtásakor felmerülő ismert hibákhoz és korlátozásokhoz kapcsolódó információk: [Az Azure Database for PostgreSQL online migrálásával kapcsolatos ismert hibák és kerülő megoldások](known-issues-azure-postgresql-online.md).
* Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* A PostgreSQL Azure Database for-ról a [Mi az Azure Database for PostgreSQL című](https://docs.microsoft.com/azure/postgresql/overview)cikkben olvashat.
