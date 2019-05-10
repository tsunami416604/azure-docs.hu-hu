---
title: 'Oktatóanyag: Azure Database Migration Service segítségével áttelepítése egy online Oracle az Azure Database for postgresql-hez |} A Microsoft Docs'
description: Megtanulhatja, hogyan hajtsa végre egy online migrálás a helyszíni Oracle vagy a virtuális gépek Azure-adatbázis PostgreSQL-hez készült Azure Database Migration Service használatával.
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
ms.openlocfilehash: b73249a9f72e4616f6d36e16f110913278f04590
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415605"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Oktatóanyag: Oracle migrálása az Azure Database for PostgreSQL használatával online DMS (előzetes verzió)

Azure Database Migration Service segítségével az adatbázisok migrálása, Oracle-adatbázisok védelmét a helyszíni vagy virtuális gépeken [, Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) minimális állásidővel. Más szóval hajthatja végre az áttelepítést, az alkalmazás minimális állásideje. Ebben az oktatóanyagban áttelepítése a **HR** mintaadatbázis egy helyszíni vagy virtuálisgép-példány az Oracle 11 g, Azure Database for PostgreSQL, az online migrálás tevékenység az Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Felmérheti a migrálás sikeresebbé az ora2pg eszközzel.
> * A minta séma ora2pg eszközével migrálhatja.
> * Hozzon létre egy Azure Database Migration Service példányát.
> * Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.

> [!NOTE]
> Azure Database Migration Service egy online migrálás végrehajtásához használatához hozzon létre egy példányt prémium tarifacsomagban alapján.

> [!IMPORTANT]
> Egy optimális migrálási folyamatba, a Microsoft azt javasolja, létrehozása az Azure Database Migration Service példányát és a céladatbázis Azure ugyanabban a régióban. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk bemutatja, hogyan áttelepítése online Oracle az Azure Database for postgresql-hez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Töltse le és telepítse [Oracle 11g Release 2 (Standard Edition, Standard Edition egy, vagy Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Töltse le a mintát **HR** adatbázist [Itt](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Töltse le és telepítse vagy ora2pg [Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf) vagy [Linux](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Linux.pdf).
* [Példány létrehozása Azure Database for PostgreSQL-ben](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Kapcsolódjon a példányhoz, és hozzon létre egy adatbázist, az utasítás használatával a jelen [dokumentum](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Hozzon létre egy Azure virtuális hálózaton (VNet) az Azure Resource Manager üzembe helyezési modell, amely lehetővé teszi a helyek közötti kapcsolatot a helyszíni adatforrás-kiszolgálók használatával vagy az Azure Database Migration Service [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). A virtuális hálózatok létrehozásával kapcsolatos további információkért lásd: a [Virtual Network-dokumentáció](https://docs.microsoft.com/azure/virtual-network/), és különösen a témakör részletesen a rövid útmutató cikkek.

  > [!NOTE]
  > Virtuális hálózathoz a telepítés során, ha az ExpressRoute hálózati a Microsoft társviszony-létesítés használja, hozzá a következő szolgáltatás [végpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) az alhálózathoz, amelyben üzembe fogja helyezni a szolgáltatást:
  > * Cél adatbázis végpont (például SQL-végpont, Cosmos-DB végpont, és így tovább)
  > * Storage-végpont
  > * Service bus-végpont
  >
  > Ez a konfiguráció szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózatok közötti hálózati biztonsági csoport (NSG) szabályai nem blokkolják a következő bejövő kommunikációs portokat, Azure Database Migration Service: 443, 53, 9354, 445, 12000. Az Azure VNet NSG-forgalom szűrése további részletekért tekintse meg a cikket [hálózati biztonsági csoportokkal a hálózati forgalom szűrése](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat az Azure Database Migration Service a forrás Oracle kiszolgálón, amely alapértelmezés szerint az 1521-es TCP-port.
* A forrásadatbázis (ok) elé egy tűzfalkészülék használata esetén előfordulhat, hogy hozzá kell tűzfalszabályokban, Azure Database Migration Service az áttelepítéshez a forrásadatbázis (ok) eléréséhez.
* Hozzon létre egy kiszolgálószintű [tűzfalszabály](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure Database for PostgreSQL, Azure Database Migration Service férhessenek hozzá a céladatbázisok. Adja meg a virtuális hálózat, Azure Database Migration Service használt alhálózat tartományán.
* Engedélyezze a forrás Oracle-adatbázisokhoz való hozzáférést.

  > [!NOTE]
  > Az adatbázis-szerepkör szükség egy felhasználóhoz szeretne csatlakozni az Oracle-forrás.

  * Archív ismétlése naplók szükség a növekményes szinkronizálás az Azure Database Migration Service adatváltozások rögzítése. Kövesse az alábbi lépéseket az Oracle-forrás konfigurálása:
    * Jelentkezzen be a SYSDBA csoport jogosultsággal a következő parancs futtatásával:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Állítsa le az adatbázis-példány a következő parancs futtatásával.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Várjon, amíg a megerősítés `'ORACLE instance shut down'`.

    * Indítsa el az új példány és a csatlakoztatási (de nem nyitnak meg) az adatbázis engedélyezése vagy letiltása az archiválási üzleti egység, futtassa a következő parancsot:

      ```
      STARTUP MOUNT;
      ```

      Az adatbázis csatlakoztatva kell lenniük; kis türelmet, a "Oracle-példány indítása" megerősítésig.

    * Módosítsa az adatbázis archiválás módban a következő parancs futtatásával:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Nyissa meg az adatbázis a normál működést a következő parancs futtatásával:

      ```
      ALTER DATABASE OPEN;
      ```

      Szükség lehet újraindítani a ÍV fájl jelenik meg.

    * Győződjön meg arról, hogy futtassa a következő parancsot:

      ```
      SELECT log_mode FROM v$database;
      ```

      Választ kell kapnia `'ARCHIVELOG'`. Ha a válasz `'NOARCHIVELOG'`, majd a követelmény nem teljesül.

  * Engedélyezze a kiegészítő naplózási replikáció használatával a következő lehetőségek közül.

    * **1. lehetőség**.
      Módosítsa az adatbázis szintű kiegészítő naplózási ahhoz, hogy biztosítsák a PK és egyedi index összes tábla. Az észlelési lekérdezés által visszaadott `'IMPLICIT'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      A naplózás módosítása a kiegészítő táblázatok szintjén. Futtatás csak a táblákat, adatkezelés, és nem rendelkeznek PKs vagy egyedi indexeket.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **2. lehetőség**.
      Módosítsa az adatbázis szintű kiegészítő naplózási ahhoz, hogy biztosítsák az összes táblát, és az észlelési lekérdezés visszaadja az `'YES'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      A naplózás módosítása a kiegészítő táblázatok szintjén. Minden táblához csak egy utasítás futtatása az alábbi logikát kövesse.

      Ha a tábla elsődleges kulccsal rendelkezik:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Ha a tábla rendelkezik egy egyedi index:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      Ellenkező esetben futtassa a következő parancsot:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Győződjön meg arról, hogy futtassa a következő parancsot:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Választ kell kapnia `'YES'`.

> [!IMPORTANT]
> Az ebben a forgatókönyvben nyilvános előzetes kiadását, Azure Database Migration Service támogatja az Oracle-verzió 10g vagy 11g. Ügyfeleink az Oracle-verzió 12 c vagy újabb verzióját kell vegye figyelembe, hogy a minimális hitelesítési protokoll engedélyezett szeretne csatlakozni az Oracle ODBC-illesztő 8 kell lennie. Az Oracle-forrás, amely verziója 12c vagy újabb, konfigurálnia kell a hitelesítési protokoll a következő:
>
> * Update SQLNET.ORA:
>
>    ```
>    SQLNET.ALLOWED_LOGON_VERSION_CLIENT = 8
>    SQLNET.ALLOWED_LOGON_VERSION_SERVER = 8
>    ```
>
> * Indítsa újra a számítógépet az új beállítások érvénybe léptetéséhez.
> * A meglévő felhasználók jelszavának módosítása:
>
>    ```
>    ALTER USER system IDENTIFIED BY {pswd}
>    ```
>
>   További információkért tekintse meg az oldal [Itt](http://www.dba-oracle.com/t_allowed_login_version_server.htm).
>
> Végezetül ne feledje, hogy a hitelesítési protokoll módosítása befolyásolhatja az ügyfél-hitelesítéshez.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Részéről az erőfeszítés mérje fel az Oracle, Azure database for PostgreSQL az áttelepítéshez

Annak ellenőrzéséhez, az Oracle migrálása az Azure Database for postgresql-hez szükséges munkát ora2pg használatát javasoljuk. Használja a `ora2pg -t SHOW_REPORT` irányelv jelentést készít azokról az Oracle-objektumok, a migrálás becsült költség (a fejlesztői nap) és az átalakítás részeként külön figyelmet igényelhetnek egyes adatbázis-objektumok létrehozásához.

A legtöbb ügyfél egy jelentős időt az értékelési jelentés megtekintésével és az automatikus és manuális átalakítás tevékenységi mérlegelése fogják tölteni.

Állíthat be és futtathat ora2pg egy értékelési jelentés létrehozására, tekintse meg a **Premigration: Értékelés** szakaszában a [Oracle-, Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). Egy mintául szolgáló ora2pg értékelési jelentés érhető el a hivatkozási [Itt](http://ora2pg.darold.net/report.html).

## <a name="export-the-oracle-schema"></a>Az Oracle-séma exportálása

Azt javasoljuk, hogy az Oracle-sémát, és más Oracle-objektumok (típusok, eljárások, függvények, stb.) átalakítása egy sémát, amely kompatibilis az Azure Database for PostgreSQL ora2pg használja. ora2pg előre a bizonyos adattípusokkal meghatározásához számos irányelveket tartalmaz. Használhatja például a `DATA_TYPE` irányelv lecseréli az összes NUMBER(*,0) bigint NUMERIC(38) helyett.

Exportálandó .sql fájlokat az adatbázis-objektumok mindegyike ora2pg futtathatja. Majd áttekintheti a .sql fájlok előtt importálja azokat az Azure database for postgresql-hez psql használatával, vagy futtathatja a. SQL-parancsfájlt a pgadmin alkalmazásban.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Példa:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Konfigurálja és futtassa a séma átalakítását ora2pg: a **áttelepítés: Séma és adatok** szakaszában a [Oracle-, Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Állítsa be a sémát az Azure Database for postgresql-hez

Alapértelmezés szerint a Oracle a schema.table.column felső kapcsolatos minden esetben megőrzi, amíg PostgreSQL schema.table.column tartja a kisbetűs be a számítógépre. Azure Database Migration Service indítása adatáthelyezés Oracle az Azure Database for postgresql-hez a schema.table.column Oracle forrásaként megkülönbözteti a kis ugyanazt a formátumot kell lennie.

Ha például az Oracle-forrás "HR" sémája megegyezik." AZ ALKALMAZOTTAK"." EMPLOYEE_ID", majd a PostgreSQL-séma ugyanazt a formátumot kell használnia.

Győződjön meg arról, hogy a schema.table.column megkülönbözteti a kis formátuma megegyezik a Oracle és az Azure Database for PostgreSQL, azt javasoljuk, hogy használhatja-e az alábbi lépéseket.

> [!NOTE]
> Egy másik módszer segítségével származtatni a nagybetűs sémát. Folyamatban van, és automatizálhatja ezt a lépést.

1. Exportálja a sémák ora2pg használata alacsonyabb esetekben. Az a tábla létrehozása sql-parancsfájlt hozzon létre egy sémát a "SCHEMA" nagybetűs manuálisan.
2. Azure Database for postgresql-hez az Oracle-objektum – például az eseményindítók, feladatütemezések, eljárások, típusok és funkciók, a többi importálja.
3. Ahhoz, hogy a tábla és oszlop felső eset, futtassa a következő szkriptet:

   ```
   -- INPUT: schema name
   set schema.var = “HR”;

   -- Generate statements to rename tables and columns
   SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
   UNION ALL 
   SELECT 2, 'alter table "'||c.relname||'" rename '||a.attname||' to "'||upper(a.attname)||'";'
   FROM pg_class c
   JOIN pg_attribute a ON a.attrelid = c.oid
   JOIN pg_type t ON a.atttypid = t.oid
   LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
    AND r.conname = a.attname
   WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
   UNION ALL
   SELECT 3, 'alter table '||c.relname||' rename to "'||upper(c.relname)||'";'
   FROM pg_catalog.pg_class c
    LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
   WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
   ORDER BY 1;
   ```

* A teljes betöltés futtatásához a céladatbázis dobja el a külső kulcs. Tekintse meg a **a minta séma Migrálása** cikkének [Itt](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) egy parancsfájlt, dobja el a külső kulcs használatával.
* Azure Database Migration Service használatával a teljes terhelés és a Szinkronizáló futtathat.
* Ha a cél Azure Database for PostgreSQL-példány adatait a forrás van naprakész, hajtsa végre az átváltás az Azure Database Migration Service adatbázis.
* SÉMA, tábla és oszlop kisbetű (Ha a sémát az Azure Database for PostgreSQL ezzel a módszerrel az alkalmazás lekérdezés kell lennie), hogy futtassa a következő szkriptet:

  ```
  -- INPUT: schema name
  set schema.var = hr;
  
  -- Generate statements to rename tables and columns
  SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
  UNION ALL
  SELECT 2, 'alter table "'||c.relname||'" rename "'||a.attname||'" to '||lower(a.attname)||';'
  FROM pg_class c
  JOIN pg_attribute a ON a.attrelid = c.oid
  JOIN pg_type t ON a.atttypid = t.oid
  LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
     AND r.conname = a.attname
  WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
  UNION ALL
  SELECT 3, 'alter table "'||c.relname||'" rename to '||lower(c.relname)||';'
  FROM pg_catalog.pg_class c
     LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
  WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
  ORDER BY 1;
  ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válasszon ki egy meglévő Vnetet, vagy hozzon létre egy újat.

    A virtuális hálózat biztosít az Azure Database Migration Service a forrás Oracle-hozzáféréssel rendelkező és a cél Azure Database for PostgreSQL-példány.

    Virtuális hálózat létrehozása az Azure Portalon kapcsolatos további információkért tekintse meg a cikket [hozzon létre egy virtuális hálózatot az Azure portal használatával](https://aka.ms/DMSVnet).

5. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.

    ![Az Azure Database Migration Service személyes példányának megkeresése](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az a **új migrálási projekt** lapon adjon meg egy nevet a projektnek a **forráskiszolgáló típusa** szövegbeviteli mezőben válasszon ki **Oracle**, a a **célkiszolgáló típusa**  szövegbeviteli mezőben válasszon ki **, Azure Database for PostgreSQL**.
5. Az a **válassza ki a tevékenység típusát** szakaszban jelölje be **Online adatáttelepítés**.

   ![Azure Database Migration Service-projekt létrehozása](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Választhatja azt is megteheti, **csak a Create project** a migrálási projekt létrehozása, és később a migrálás végrehajtására.

6. Válassza ki **mentése**, vegye figyelembe a követelmények sikeres használatához online áttelepítése, és válassza ki az Azure Database Migration Service **létrehozása és a futási tevékenységet**.

## <a name="specify-source-details"></a>Forrás adatainak megadása

* Az a **forrás adatainak hozzáadása** képernyőn, adja meg a forrás Oracle-példány kapcsolati adatait.

  ![A Forrás adatainak hozzáadása képernyő](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Töltse fel az Oracle OCI illesztőprogram

1. Válassza ki **mentése**, majd a a **telepítése OCI illesztőprogram** képernyőn jelentkezzen be az Oracle-fiókjába, töltse le az illesztőprogram **instantclient-basiclite-windows.x64-12.2.0.1.0.zip**(37,128,586 Byte(s)) (SHA1 ellenőrzőösszeg: 865082268) származó [Itt](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Töltse le az illesztőprogram egy megosztott mappába.

   Ellenőrizze, hogy a mappa a felhasználónév, amelyet a megadott minimális olvasási hozzáféréssel rendelkező van megosztva. Azure Database Migration Service fér hozzá, és beolvassa a megosztást a OCI illesztőprogramjának feltölthet az Azure-bA a megszemélyesítése a megadott felhasználónév.

   A megadott felhasználónevet egy Windows felhasználói fiókot kell lennie.

   ![OCI illesztőprogram telepítése](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza ki **mentése**, majd a a **cél részletei** képernyőn, adja meg a cél Azure Database for PostgreSQL-kiszolgáló kapcsolati adatait Ez az előre kiépített példányt, Azure Database for PostgreSQL, amelyhez a **HR** séma üzembe lett helyezve.

    ![A részleteket tartalmazó képernyő](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis adatbázis neve megegyezik a forrás-adatbázis, Azure Database Migration Service alapértelmezés szerint választja ki a céladatbázisban.

    ![Leképezés céladatbázisokra](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

  Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Inicializálás**.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Fut** értékre.

     ![Tevékenység állapota – futó](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Alatt **adatbázisnév**, válasszon ki egy adott adatbázist való migrálás állapota **adatok teljes betöltése** és **adatok növekményes szinkronizálása** műveleteket.

    Az Adatok teljes betöltésénél az első betöltés migrálási állapota jelenik meg, a Növekményes adatszinkronizálásnál pedig az Adatváltozások rögzítése (CDC) állapot látható.

     ![Tevékenység állapota – Teljes betöltés kész](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Tevékenység állapota – Növekményes adatszinkronizálás](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

Az első teljes betöltés elkészültével az adatbázisok **Átállásra kész** állapotúak lesznek.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.

   ![Átállás indítása](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázisok migrálási állapotát jeleníti meg **befejezve**, csatlakozzon az alkalmazásokat az új cél Azure Database for PostgreSQL-példány.

 > [!NOTE]
 > Mivel PostgreSQL alapértelmezés szerint rendelkezik schema.table.column kisbetű szerepel, visszatérhet nagybetű, kisbetű a parancsfájl használatával a **állítsa be a sémát az Azure Database for postgresql-hez** Ez a cikk korábbi szakaszában.

## <a name="next-steps"></a>További lépések

* Az Azure Database for PostgreSQL-be történő online migrálás végrehajtásakor felmerülő ismert hibákhoz és korlátozásokhoz kapcsolódó információk: [Az Azure Database for PostgreSQL online migrálásával kapcsolatos ismert hibák és kerülő megoldások](known-issues-azure-postgresql-online.md).
* Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Azure Database for postgresql-hez információt lásd: a cikk [Mi az Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
