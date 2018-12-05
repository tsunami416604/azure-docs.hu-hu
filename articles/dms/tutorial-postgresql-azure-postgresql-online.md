---
title: 'Oktatóanyag: PostgreSQL online migrálása az Azure Database for MySQL-be az Azure Database Migration Service használatával | Microsoft Docs'
description: Megtudhatja, hogyan végezhet online migrálást a helyszíni PostgreSQL-ből az Azure Database for PostgreSQL-be az Azure Database Migration Service használatával.
services: dms
author: HJToland3
ms.author: scphang
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: ba27ceb784cf139c288a89f3191282fb9b364ddc
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52864376"
---
# <a name="tutorial-migrate-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Oktatóanyag: PostgreSQL online migrálása az Azure Database for PostgreSQL-be DMS használatával
Az Azure Database Migration Service használatával minimális szolgáltatáskieséssel migrálhatja egy helyszíni PostgreSQL-példány adatbázisait az [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/)-be. Ez azt jelenti, hogy a migrálás az alkalmazás minimális ideig tartó leállásával végezhető el. Ebben az oktatóanyagban a **DVD Rental** mintaadatbázist fogja migrálni a PostgreSQL 9.6 egy helyszíni példányáról az Azure Database for PostgreSQL-be az Azure Database Migration Service online migrálási tevékenységének használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * A mintaséma migrálása a pgdump segédprogrammal.
> * Egy Azure Database Migration Service-példány létrehozása.
> * Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.

> [!NOTE]
> Az Azure Database Migration Service egy online migrálás végrehajtásához használatához hozzon létre egy példányt (előzetes verzió) prémium tarifacsomag alapján.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a migrálási folyamat optimalizálása érdekében ugyanabban az Azure-régióban hozza létre az Azure Database Migration Service-példányt, mint amelyikben a céladatbázis is található. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Töltse le és telepítse a [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.5-ös, 9.6-os vagy 10.3-as verzióját. A forrásul szolgáló PostgreSQL-kiszolgáló verziójának a következőnek kell lennie: 9.5.11, 9.6.7, 10.3 vagy újabb. További információkért tekintse meg [A támogatott PostgreSQL-adatbázis verziók](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) című cikket.

    Emellett a helyi PostgreSQL verziójának meg kell egyeznie az Azure Database for PostgreSQL verziójával. Például a PostgreSQL 9.5.11.5 csak az Azure Database for PostgreSQL 9.5.11-es verziójába migrálható, a 9.6.7-es verzióba nem.

- [Példány létrehozása Azure Database for PostgreSQL-ben](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).  
- Hozzon létre egy virtuális hálózatot az Azure Database Migration Service-hez az Azure Resource Manager-alapú üzemi modell használatával, amely a hálózat helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgálóknak [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) használatával.
- Győződjön meg arról, hogy az Azure Virtual Network (VNET) hálózati biztonsági szabályok nem blokkolják a következő kommunikációs portokat: 443, 53, 9354, 445, 12000. További részletek az Azure VNET NSG-forgalom szűréséről: [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
- Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a PostgreSQL-kiszolgáló forrását, amely alapértelmezés szerint az 5432-es TCP-port.
- Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.
- Hozzon létre egy kiszolgálószintű [tűzfalszabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure Database for PostgreSQL-hez, hogy az Azure Database Migration Service hozzáférhessen a céladatbázisokhoz. Adja meg az Azure Database Migration Service-hez használt virtuális hálózat alhálózati tartományát.
- A CLI meghívásának két módja van:
    - Az Azure portál jobb felső sarkában kattintson a Cloud Shell gombra:
 
       ![Cloud Shell gomb a Microsoft Azure Portal-on](media\tutorial-postgresql-to-azure-postgresql-online\cloud-shell-button.png)
 
    - Telepítse és futtassa a CLI-t helyileg. A CLI 2.0 egy parancssori eszköz az Azure-erőforrások kezeléséhez.
     
       A CLI letöltéséhez kövesse [Az Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) című cikkben lévő utasításokat. A cikk a CLI 2.0 verzióját támogató platformokat is felsorolja.
         
       Windows Subsystem for Linux (WSL) beállításához kövesse a [Windows 10 telepítési útmutatójának](https://docs.microsoft.com/windows/wsl/install-win10) utasításait
 
- Engedélyezze a logikai replikálást a postgresql.config fájlban, és állítsa be a következő paramétereket:
    - wal_level = **logical**
    - max_replication_slots = [tárhelyek száma], az ajánlott beállítás **5 tárhely**
    - max_wal_senders = [párhuzamos feladatok száma] – a max_wal_senders paraméter megadja a párhuzamosan futtatható feladatok számát, az ajánlott beállítás **10 feladat**

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása
Ahhoz, hogy az összes adatbázis-objektumot táblasémaként, indexekként és tárolt eljárásokként egészíthessük ki, ki kell bontanunk a forrásadatbázis sémáját, és alkalmaznunk kell az adatbázisra.

1. Pg_dump -s parancs segítségével hozzon létre egy séma-memóriaképfájlt az adatbázishoz. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Például: sémafájl kiírása a dvdrental adatbázishoz:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```
 
    A pg_dump segédprogram használatával kapcsolatos további információkért lásd a [pg-memóriakép](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) oktatóanyagban szereplő példákat.
 
2. Hozzon létre egy üres adatbázist a célkörnyezetben, amely az Azure Database for PostgreSQL.

    Az adatbázis létrehozásáról és hozzárendeléséről az [Azure Database for PostgreSQL kiszolgáló létrehozása a Microsoft Azure Portal-on ](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) című cikkben olvashat.

3. Importálja a sémát a létrehozott céladatbázisba a séma-memóriaképfájl visszaállításával.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    Példa:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```
4. Ha a sémában idegen kulcsok szerepelnek, a migrálás első betöltése és folyamatos szinkronizálása sikertelen lesz. Hajtsa végre a következő szkriptet a PgAdmin alkalmazásban vagy a psql-ben a ’drop foreign key’ és az ’add foreign key’ szkript kibontásához a célhelyen (Azure Database for PostgreSQL).
    
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

    Futtassa a ’drop foreign key’-t (ez a második oszlop) a lekérdezési eredményben.

5.  Az adatok között szereplő trigger (beszúrási vagy frissítési trigger) kikényszeríti az adatok integritását a célban az adatok forrásból való replikálása előtt. Javasoljuk, hogy migráláskor tiltsa le a triggereket **a cél** minden táblájában, majd a migrálás végeztével engedélyezze őket ismét.

    A céladatbázisban szereplő triggerek letiltásához használja az alábbi parancsot:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6.  Ha valamelyik táblában ENUM adattípus van, javasoljuk, hogy ideiglenesen frissítse azt ’character varying’ adattípusra a céltáblában. Miután elkészült az adatreplikáció, állítsa vissza az adattípust ENUM-ra.

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>A DMS egy példányának kiépítése a CLI használatával

1.  A dms szinkronizálási bővítmény telepítéséhez:
    - Jelentkezzen be az Azure-ba az alábbi parancs futtatásával:        
        ```
        az login
        ```

    - Amikor a rendszer kéri, nyisson meg egy webböngészőt, és adja meg a kódot az eszköz hitelesítéséhez. Kövesse az utasításokat a megjelenésük sorrendjében.
    - A dms bővítmény hozzáadásához:
        - Az elérhető bővítmények listájának megjelenítéséhez futtassa a következő parancsot:

            ```
            az extension list-available –otable
            ```
        - A bővítmény telepítéséhez futtassa a következő parancsot:

            ```
            az extension add –n dms-preview
            ```

    - A dms bővítmény helyes telepítésének ellenőrzéséhez futtassa a következő parancsot:
 
        ```
        az extension list -otable
        ```
        A következő kimenetnek kell megjelennie:     

        ```
        ExtensionType    Name
        ---------------  ------
        whl              dms
        ```

    - A következő futtatásával bármikor megjelenítheti a DMS-ben támogatott összes parancsot:
        ```
        az dms -h
        ```
    - Ha több Azure-előfizetéssel is rendelkezik, a DMS-szolgáltatás egy példányának kiépítéséhez használni kívánt előfizetés beállításához futtassa a következő parancsot.

         ```
        az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
         ```

2.  A DMS egy példányának kiépítése a következő parancs futtatásával:

    ```
    az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name BusinessCritical_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
    ```

    Például a következő parancs szolgáltatást hoz létre az alábbi helyen:
    - Hely: az USA 2. keleti régiója
    - Előfizetés: 97181df2-909d-420b-ab93-1bff15acb6b7
    - Erőforráscsoport neve: PostgresDemo
    - DMS-szolgáltatás neve: PostgresCLI

    ```
    az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name BusinessCritical_4vCores
    ```
    A DMS-szolgáltatás példányának létrehozása nagyjából 10–12 percet vesz igénybe.

3. A DMS-ügynök IP-címének azonosításához, így a Postgres pg_hba.conf fájlba való felvételéhez futtassa a következő parancsot:

    ```
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```
    Példa:

    ```
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    A következő címhez hasonló eredményt kell kapnia: 

    ```
    [
      "172.16.136.18"
    ]
    ```

4. Vegye fel a DMS-ügynök IP-címét a Postgres pg_hba.conf fájlba.
    - A DMS kiépítése után jegyezze fel a DMS IP-címét.
    - Vegye fel az IP-címet a pg_hba.conf fájlba a forráson a következő bejegyzéshez hasonló módon:

        ```
        host    all     all     172.16.136.18/10    md5
        host    replication     postgres    172.16.136.18/10    md5
        ```

5. Ezután hozzon létre egy PostgreSQL-migrálási projektet a következő parancs futtatásával:
    
    ```
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```
    Például a következő parancs létrehoz egy projektet az alábbi paraméterek használatával:

      - Hely: az USA nyugati középső régiója
      - Erőforráscsoport neve: PostgresDemo
      - Szolgáltatás neve: PostgresCLI
      - Projekt neve: PGMigration
      - Forrásplatform: PostgreSQL
      - Célplatform: AzureDbForPostgreSql
 
    ```
    az dms project create -l eastus2 -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
    ```
                
6. Az alábbi lépéseket követve hozzon létre egy PostgreSQL-migrálási feladatot.

    Ez a lépés magában foglalja a forrás IP-címének, felhasználói azonosítójának és jelszavának, a cél IP-címének, felhasználói azonosítójának és jelszavának, és a feladattípusnak a megadását a kapcsolat létrehozásához.

    - A lehetőségek teljes listájának megtekintéséhez futtassa a parancsot:
        ```
        az dms project task create -h
        ```

        A forrás és cél kapcsolata esetében is a bemeneti paraméter egy az objektumlistát tartalmazó json-fájlra hivatkozik.
 
        A kapcsolat JSON-objektumának formátuma a PostgreSQL-kapcsolatokhoz.
        
        ```
        {
                    "userName": "user name",    // if this is missing or null, you will be prompted
                    "password": null,           // if this is missing or null (highly recommended) you will
                be prompted
                    "serverName": "server name",
                    "databaseName": "database name", // if this is missing, it will default to the 'postgres'
                server
                    "port": 5432                // if this is missing, it will default to 5432
                }
        ```

    - Emellett van egy adatbázis-beállítási json-fájl is, amely a json-objektumokat sorolja fel. A PostgreSQL esetében az adatbázis-beállítási JSON-objektum formátuma az alábbi:

        ```
        [
            {
                "name": "source database",
                "target_database_name": "target database",
            },
            ...n
        ]
        ```

    - Hozzon létre egy json-fájlt a Jegyzettömbben, másolja ki a következő parancsokat, és illessze be őket a fájlba, majd mentse a fájlt a C:\DMS\source.json helyre.
         ```
        {
                    "userName": "postgres",    
                    "password": null,           
                be prompted
                    "serverName": "13.51.14.222",
                    "databaseName": "dvdrental", 
                    "port": 5432                
                }
         ```
    - Hozzon létre egy másik, target.json elnevezésű fájlt, és mentse másként a C:\DMS\target.json helyre. Foglalja bele az alábbi parancsokat:
        ```
        {
                "userName": " dms@builddemotarget",    
                "password": null,           
                "serverName": " builddemotarget.postgres.database.azure.com",
                "databaseName": "inventory", 
                "port": 5432                
            }
        ```
    - Hozzon létre egy adatbázis-beállítási json-fájlt, amely migrálandó adatbázisként kilistázza a készletet:
        ``` 
        [
            {
                "name": "dvdrental",
                "target_database_name": "dvdrental",
            }
        ]
        ```
    - Futtassa a következő parancsot, amely egybefoglalja a forrás, cél és a DB-beállítási json-fájlokat.

        ``` 
        az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
        ``` 

    Ezen a ponton sikeresen beküldött egy migrálási feladatot.

7.  A feladat előrehaladásának megjelenítéséhez futtassa a következő parancsot:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

    VAGY

     ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
     ```

8. A kibontott kimenetből futtathatja a migrationState lekérdezését is:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

## <a name="understanding-migration-task-status"></a>A migrálási feladat állapotának ismertetése
A kimeneti fájlban számos, a migrálási folyamat előrehaladását jelző paraméter van. Például lásd az alábbi kimeneti fájlt:

    ```
    "output": [                                 Database Level
          {
            "appliedChanges": 0,        //Total incremental sync applied after full load
            "cdcDeleteCounter": 0       // Total delete operation  applied after full load
            "cdcInsertCounter": 0,      // Total insert operation applied after full load
            "cdcUpdateCounter": 0,      // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0, //Number of tables that contain migration error
            "fullLoadLoadingTables": 0, //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,  //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,       //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",  //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                     Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",  //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                    //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,               //Volume in Bytes in full load
            "id": "or|inventory|public|actor",          
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                   //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                //Total sync changes that applied after full load
          },
          {                                     Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                          DMS migration task state
        "state": "Running", //Migration task state – Running means it is still listening to any changes that might come in                  
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
    ```

## <a name="cutover-migration-task"></a>Átállásos migrálási feladat
Amikor a teljes betöltés kész, az adatbázis készen áll az átállásra. Attól függően, mennyire foglalt a forráskiszolgáló a beérkező új tranzakciók miatt, lehet, hogy a teljes betöltés befejeződése után a DMS-feladat még módosításokat alkalmaz.

Annak érdekében, hogy az összes adat szerepeljen, ellenőrizze a forrás- és a céladatbázis sorainak számát. Ezt például a következő paranccsal teheti meg:

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0,  //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0,  //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112,  //full load for table 2
```

1.  Hajtsa végre az átállásos adatbázis-migrálási feladatot a következő paranccsal:

    ```
    az dms project task cutover -h
    ```

    Példa:

    ```
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --database-name Inventory
    ```

2.  Az átállás előrehaladásának monitorozásához futtassa a következő parancsot:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

## <a name="service-project-task-cleanup"></a>Szolgáltatás, projekt, feladat törlése
Ha valamelyik DMS-feladat, -projekt vagy -szolgáltatás megszakítására vagy törlésére van szükség, hajtsa végre a megszakítást a következő sorrendben:
- Az adott futó feladat megszakítása
- A feladat törlése
- A projekt törlése 
- A DMS-szolgáltatás törlése

1.  A futó feladat megszakításához használja a következő parancsot:
    ```
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2.  A futó feladat törléséhez használja a következő parancsot:
    ```
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3.  A futó projekt megszakításához használja a következő parancsot:
     ```
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4.  A futó projekt törléséhez használja a következő parancsot:
    ```
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5.  A DMS-szolgáltatás törléséhez használja a következő parancsot:

     ```
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>További lépések
- Az Azure Database for PostgreSQL-be történő online migrálás végrehajtásakor felmerülő ismert hibákhoz és korlátozásokhoz kapcsolódó információk: [Az Azure Database for PostgreSQL online migrálásával kapcsolatos ismert hibák és kerülő megoldások](known-issues-azure-postgresql-online.md).
- Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Az Azure Database for MySQL szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).