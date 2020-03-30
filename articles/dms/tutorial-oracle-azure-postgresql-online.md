---
title: 'Oktatóanyag: Az Oracle áttelepítése online a PostgreSQL Azure-adatbázisába'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan hajthat végre online áttelepítést az Oracle helyszíni vagy virtuális gépeken az Azure Database for PostgreSQL szolgáltatásba az Azure Database Migration Service használatával.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/24/2020
ms.openlocfilehash: 956523e2b51795a4bc97c653dab8b408b06061f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255564"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Oktatóanyag: Az Oracle áttelepítése az Azure Database for PostgreSQL-be online a DMS (előzetes verzió) használatával

Az Azure Database Migration Service segítségével áttelepítheti az adatbázisokat a helyszíni vagy virtuális gépeken tárolt Oracle-adatbázisokból az [Azure Database for PostgreSQL-be](https://docs.microsoft.com/azure/postgresql/) minimális állásidővel. Más szóval az áttelepítést minimális állásidővel befejezheti az alkalmazásba. Ebben az oktatóanyagban **HR** a HR-mintaadatbázist az Oracle 11g helyszíni vagy virtuálisgép-példányából az Azure Database for PostgreSQL-be telepíti át az Azure Database Migration Service online áttelepítési tevékenységének használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Mérje fel az átállási erőfeszítéseket az ora2pg eszközzel.
> * Telepítse át a mintasémát az ora2pg eszközzel.
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.

> [!NOTE]
> Az Azure Database Migration Service online áttelepítés végrehajtásához létre kell adnia egy példányt a prémium díjszabási szint alapján.

> [!IMPORTANT]
> Az optimális áttelepítési élmény érdekében a Microsoft azt javasolja, hogy hozzon létre egy példányt az Azure Database Migration Service ugyanabban az Azure-régióban, mint a cél-adatbázis. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk bemutatja, hogyan hajthatja végre az oracle-ről a PostgreSQL-adatbázisra való online áttelepítést.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Töltse le és telepítse [az Oracle 11g Release 2 (Standard Edition, Standard Edition One vagy Enterprise Edition) rendszert.](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)
* Töltse le a minta **HR** adatbázis [innen](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Töltse le és [telepítse ora2pg windows os vagy Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf).
* [Példány létrehozása Azure Database for PostgreSQL-ben](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Csatlakozzon a példányhoz, és hozzon létre egy adatbázist a [dokumentumban](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal)található utasítás használatával.
* Hozzon létre egy Microsoft Azure virtuális hálózatot az Azure adatbázis-áttelepítési szolgáltatáshoz az Azure Resource Manager telepítési modelljével, amely az [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával biztosít helyek közötti kapcsolatot a helyszíni forráskiszolgálókhoz. A virtuális hálózat létrehozásáról további információt a [Virtuális hálózati dokumentációban](https://docs.microsoft.com/azure/virtual-network/)és különösen a részletes en című rövid útmutatóban talál.

  > [!NOTE]
  > A virtuális hálózat beállítása során, ha az ExpressRoute szolgáltatást hálózati társviszony-létesítéssel használja a Microsofthoz, adja hozzá a következő [szolgáltatásvégpontokat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) ahhoz az alhálózathoz, amelyben a szolgáltatás ki lesz építve:
  >
  > * Cél adatbázis-végpont (például SQL-végpont, Cosmos DB-végpont és így tovább)
  > * Tárolási végpont
  > * Szolgáltatásbusz végpontja
  >
  > Erre a konfigurációra azért van szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózati biztonsági csoport (NSG) szabályai nem blokkolják a következő bejövő kommunikációs portokat az Azure Database Migration Service szolgáltatásba: 443, 53, 9354, 445, 12000. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részleteket a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal című témakörben olvashat.](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat, hogy az Azure Database Migration Service hozzáférhessen a forrás Oracle-kiszolgálóhoz, amely alapértelmezés szerint az 1521-es TCP-port.
* Ha a forrásadatbázis(ok) előtt tűzfalberendezést használ, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia ahhoz, hogy az Azure Database Migration Service hozzáférhessen a forrásadatbázis(ok)hoz az áttelepítéshez.
* Hozzon létre egy kiszolgálószintű [tűzfalszabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure Database for PostgreSQL számára, amely lehetővé teszi az Azure Database Migration Service hozzáférését a céladatbázisokhoz. Adja meg az Azure Database Migration Service használt virtuális hálózat alhálózati tartományát.
* Hozzáférés engedélyezése a forrás Oracle adatbázisokhoz.

  > [!NOTE]
  > A DBA szerepkör szükséges ahhoz, hogy a felhasználó az Oracle-forráshoz csatlakozzon.

  * Archív ismétlésnaplók szükséges növekményes szinkronizálás az Azure Database Migration Service az adatok változásának rögzítéséhez. Az Oracle forrásának konfigurálásához kövesse az alábbi lépéseket:
    * Jelentkezzen be SYSDBA jogosultsággal a következő parancs futtatásával:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Állítsa le az adatbázispéldányt a következő parancs futtatásával.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Várja meg `'ORACLE instance shut down'`a megerősítést .

    * Indítsa el az új példányt, és csatlakoztassa (de ne nyissa meg) az adatbázist a következő parancsot futtató bu archiválás engedélyezéséhez vagy letiltásához:

      ```
      STARTUP MOUNT;
      ```

      Az adatbázist csatlakoztatni kell; várjon az "Oracle példány indítása" megerősítésére.

    * Módosítsa az adatbázis-archiválási módot a következő parancs futtatásával:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Nyissa meg az adatbázist a normál műveletekhez a következő parancs futtatásával:

      ```
      ALTER DATABASE OPEN;
      ```

      Előfordulhat, hogy újra kell indítania az ARC-fájl megjelenítését.

    * Az ellenőrzéshez futtassa a következő parancsot:

      ```
      SELECT log_mode FROM v$database;
      ```

      Meg kell kapnia a választ `'ARCHIVELOG'`. Ha a `'NOARCHIVELOG'`válasz , akkor a követelmény nem teljesül.

  * Engedélyezze a replikáció kiegészítő naplózását az alábbi lehetőségek egyikével.

    * **1. lehetőség.**
      Módosítsa az adatbázis szintű kiegészítő naplózást, hogy lefedje az összes táblát pk-vel és egyedi indexszel. Az észlelési `'IMPLICIT'`lekérdezés a d.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Módosítsa a táblaszintű kiegészítő naplózást. Csak olyan táblákhoz futtasson, amelyek adatmanipulációval rendelkeznek, és nem rendelkeznek pk-ekkel vagy egyedi indexekkel.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **2.**
      Módosítsa az adatbázis szintű kiegészítő naplózást az összes tábla `'YES'`lefedéséhez, és az észlelési lekérdezés visszaadja.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Módosítsa a táblaszintű kiegészítő naplózást. Kövesse az alábbi logikát, hogy minden táblához csak egy utasítást futtasson.

      Ha a tábla elsődleges kulccsal rendelkezik:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Ha a tábla egyedi indexszel rendelkezik:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      Ellenkező esetben futtassa a következő parancsot:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Az ellenőrzéshez futtassa a következő parancsot:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Meg kell kapnia a választ `'YES'`.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Az Oracle postgreSQL-áttelepítéshez való erőfeszítésének felmérése az Azure Database számára

Javasoljuk, hogy az ora2pg használatával felmérheti az Oracle-ről a PostgreSQL-hez készült Azure Database-re való áttelepítéshez szükséges erőfeszítéseket. Az `ora2pg -t SHOW_REPORT` irányelv segítségével hozzon létre egy jelentést, amely felsorolja az összes Oracle-objektumot, a becsült áttelepítési költséget (fejlesztői napokban), valamint bizonyos adatbázis-objektumokat, amelyek az átalakítás részeként különös figyelmet igényelhetnek.

A legtöbb ügyfél jelentős időt tölt az értékelő jelentés áttekintésével, valamint az automatikus és manuális konverziós erőfeszítések figyelembevételével.

Az ora2pg konfigurálásához és futtatásához értékelési jelentés létrehozásához tekintse meg az [Oracle for Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) **Előáttelepítés: Értékelés** szakaszát. A minta ora2pg értékelő jelentés [itt](https://ora2pg.darold.net/report.html)áll rendelkezésre .

## <a name="export-the-oracle-schema"></a>Az Oracle séma exportálása

Azt javasoljuk, hogy az ora2pg használatával konvertálja az Oracle sémát és más Oracle-objektumokat (típusokat, eljárásokat, függvényeket stb.) egy olyan sémává, amely kompatibilis az Azure Database for PostgreSQL-lel. Az ora2pg számos direktívát tartalmaz, amelyek segítenek előre meghatározni bizonyos adattípusokat. Az irányelv segítségével például az `DATA_TYPE` összes NUMBER(*,0) helyett bigint(NUMERIC(38) helyett bigint helyett.

Az ora2pg futtatásával exportálhatja az adatbázis-objektumokat .sql fájlokban. Ezután áttekintheti az .sql fájlokat, mielőtt importálná őket a PostgreSQL Azure Database for PostgreSQL-be a psql használatával, vagy végrehajthatja a . SQL-parancsfájl a PgAdminban.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Példa:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Az ora2pg sémakonvertáláshoz való konfigurálásához és futtatásához olvassa el az [Oracle for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)adatbázisának **Áttelepítés: Séma és adatok** szakaszát.

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>A séma beállítása a PostgreSQL Azure Database szolgáltatásában

Az Oracle táblasémák, tárolt eljárások, csomagok és egyéb adatbázis-objektumok konvertálása az ora2pg használatával a Postgres kompatibilitást az Azure Database Migration Service áttelepítési folyamatának indítása előtt. Lásd az alábbi linkeket, hogyan kell dolgozni ora2pg:

* [Az ora2pg telepítése Windows rendszerre](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Oracle az Azure PostgreSQL migrációs szakácskönyvéhez](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Az Azure Database Migration Service is létrehozhatja a PostgreSQL táblaséma. A szolgáltatás hozzáfér a táblasémához a csatlakoztatott Oracle-forrásban, és létrehoz egy kompatibilis táblasémát az Azure Database for PostgreSQL-ben. Győződjön meg arról, hogy érvényesíti és ellenőrzi a séma formátumot az Azure Database for PostgreSQL-ben, miután az Azure Database Migration Service befejezte a séma létrehozását és az adatok áthelyezését.

> [!IMPORTANT]
> Az Azure Database Migration Service csak a táblasémát hozza létre; más adatbázis-objektumok, például a tárolt eljárások, csomagok, indexek stb.

Is győződjön meg róla, hogy dobja el az idegen kulcsot a céladatbázisban a teljes terhelés futtatásához. Tekintse meg **a mintaséma áttelepítése** szakasza a cikk [itt](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) egy parancsfájlt, amely segítségével eldobja az idegen kulcsot. Az Azure Database Migration Service használatával fut a teljes terhelés és szinkronizálás.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Ha a PostgreSQL táblaséma már létezik

Ha egy PostgreSQL sémát hoz létre olyan eszközökkel, mint például az ora2pg az adatok mozgatásának megkezdése előtt az Azure Database Migration Service használatával, rendelje hozzá a forrástáblákat az Azure Database Migration Service céltábláihoz.

1. Amikor létrehoz egy új Oracle for Azure Database for PostgreSQL migrálási projektet, a rendszer kéri, hogy válassza ki a céladatbázist és a célsémát a Sémák kiválasztása lépésben. Töltse ki a céladatbázist és a célsémát.

   ![Portál-előfizetések megtekintése](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. Az **Áttelepítési beállítások** képernyőn megjelenik az Oracle forrástábláinak listája. Az Azure Database Migration Service megpróbálja egyeztetni a táblákat a forrás- és a céltábláktábla neve alapján. Ha több, különböző burkolatú céltábla létezik, kiválaszthatja, hogy melyik céltáblát szeretné leképezni.

    ![Portál-előfizetések megtekintése](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Ha a forrástábla neveket különböző nevű táblákhoz kell hozzákell képeznie, e-mailt [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) küld, és a folyamat automatizálásához egy parancsfájlt tudunk biztosítani.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Ha a PostgreSQL táblaséma nem létezik

Ha a cél PostgreSQL-adatbázis nem tartalmaz táblaséma-információkat, az Azure Database Migration Service konvertálja a forrássémát, és újra létrehozza azt a céladatbázisban. Ne feledje, hogy az Azure Database Migration Service csak a táblasémát hozza létre, más adatbázis-objektumokat, például a tárolt eljárásokat, csomagokat és indexeket nem.
Ha azt szeretné, hogy az Azure Database Migration Service hozza létre a sémát, győződjön meg arról, hogy a célkörnyezet tartalmaz egy sémát meglévő táblák nélkül. Ha az Azure Database Migration Service felderít egy táblát, a szolgáltatás feltételezi, hogy a sémát egy külső eszköz, például ora2pg hozta létre.

> [!IMPORTANT]
> Az Azure Database Migration Service megköveteli, hogy az összes tábla ugyanúgy jön létre, az Azure Database Migration Service vagy egy eszköz, például ora2pg használatával, de nem mindkettő használatával.

Első lépések:

1. Hozzon létre egy sémát a céladatbázisban az alkalmazás követelményei alapján. Alapértelmezés szerint a PostgreSQL táblaséma és az oszlopok neve kisbetűs. Az Oracle táblaséma és az oszlopok viszont alapértelmezés szerint minden nagybetűs esetben jelen vannak.
2. A Sémák kiválasztása lépésben adja meg a céladatbázist és a célsémát.
3. Az Azure Database for PostgreSQL rendszerben létrehozott séma alapján az Azure Database Migration Service a következő átalakítási szabályokat használja:

    Ha a séma neve az Oracle forrásban, és megegyezik az Azure Database for PostgreSQL, majd az Azure Database Migration Service *létrehozza a tábla séma használatával ugyanazt az esetet, mint a cél.*

    Példa:

    | Forrás Oracle séma | PostgreSQL-adatbázis cél.Séma | A DMS létrehozott séma.table.column |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | célHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR". országokban". COUNTRY_ID" |
    | HR | targetHR.HR | "HR"." országokban". COUNTRY_ID" |
    | HR | targetHR.Hr | *Nem lehet feltérképezni a vegyes eseteket |

    *Ha vegyes esetsémát és táblaneveket szeretne [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com)létrehozni a cél PostgreSQL-ben, lépjen kapcsolatba a programmal. A cél PostgreSQL-adatbázisban beállíthatjuk a vegyes esettábla-sémát.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd jobbra a **Microsoft.DataMigration**programtól, és válassza a **Regisztráció**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Jelöljön ki egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat hozzáférést biztosít az Azure Database Migration Service számára az Oracle forráshoz és a PostgreSQL-példány cél Azure-adatbázisához.

    A virtuális hálózat Azure Portalon való létrehozásáról további információt a Virtuális hálózat létrehozása az Azure Portal használatával című témakörben [talál.](https://aka.ms/DMSVnet)

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
4. Az **Új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **Forráskiszolgáló típusa** mezőben válassza az **Oracle**lehetőséget a **Célkiszolgáló típusa** mezőben, és válassza az Azure Database **for PostgreSQL**lehetőséget.
5. A **Tevékenység típusának kiválasztása** csoportban válassza az **Online adatáttelepítés**lehetőséget.

   ![Azure Database Migration Service-projekt létrehozása](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Másik lehetőségként **választhatja** a Projekt létrehozása csak az áttelepítési projekt most, és az áttelepítés későbbi végrehajtásához lehetőséget.

6. Válassza a **Mentés**lehetőséget, jegyezze fel az Azure Database Migration Service online áttelepítéshez való sikeres használatának követelményeit, majd válassza **a Tevékenység létrehozása és futtatása**lehetőséget.

## <a name="specify-source-details"></a>Forrás adatainak megadása

* A **Forrás részleteinek hozzáadása** képernyőn adja meg a forrás Oracle-példány kapcsolatának részleteit.

  ![A Forrás adatainak hozzáadása képernyő](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Oracle OCI illesztőprogram feltöltése

1. Válassza a **Mentés**lehetőséget, majd az **OCI illesztőprogram telepítése** képernyőn jelentkezzen be Oracle-fiókjába, és töltse le innen az **instantclient-basiclite-windows.x64-12.0.1.0.zip** (37 128 586 bájt) [here](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst)(SHA1 Checksum: 865082268) illesztőprogramot.
2. Töltse le az illesztőprogramot egy megosztott mappába.

   Győződjön meg arról, hogy a mappa a megadott felhasználónévvel van megosztva, minimális írásvédett hozzáféréssel. Az Azure Database Migration Service a megosztásról való hozzáférést és olvasást a megadott felhasználónév megszemélyesítésével teszi elérhetőé az OCI-illesztőprogram Azure-ba való feltöltéséhez.

   A megadott felhasználónévnek Windows felhasználói fióknak kell lennie.

   ![OCI illesztőprogram telepítése](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés**lehetőséget, majd a **Cél részletei** képernyőn adja meg a postgreSQL-kiszolgálóhoz szükséges azure-adatbázis kapcsolatának részleteit, amely a PostgreSQL-adatbázis előre kiépített példánya, amelyre a **HR-sémát** telepítették.

    ![A részleteket tartalmazó képernyő](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrásadatbázis, az Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázist.

    ![Leképezés céladatbázisokra](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

  Megjelenik az áttelepítési tevékenység ablak, és a tevékenység **állapota** **inicializálódik.**

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Fut** értékre.

     ![Tevékenység állapota - futás](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Az **Adatbázis neve területen**válasszon ki egy adott adatbázist a Teljes **adatbetöltés** és a **Növekményes adatszinkronizálási** műveletek áttelepítési állapotának megkerüléséhez.

    Az Adatok teljes betöltésénél az első betöltés migrálási állapota jelenik meg, a Növekményes adatszinkronizálásnál pedig az Adatváltozások rögzítése (CDC) állapot látható.

     ![Tevékenység állapota – Teljes betöltés kész](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Tevékenység állapota – Növekményes adatszinkronizálás](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

Az első teljes betöltés elkészültével az adatbázisok **Átállásra kész** állapotúak lesznek.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.

   ![Átállás indítása](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázis-áttelepítési állapot **azt mutatja, befejezett,** csatlakoztassa az alkalmazásokat az új cél Azure Database for PostgreSQL-példány.

 > [!NOTE]
 > Mivel a PostgreSQL alapértelmezés szerint schema.table.column kisbetűvel rendelkezik, a cikk korábbi részében a **séma beállítása a PostgreSQL-ben** című szakaszban található parancsfájl használatával visszaállíthatja a nagybetűs ről a kisbetűsre.

## <a name="next-steps"></a>További lépések

* Az Azure Database for PostgreSQL-be történő online migrálás végrehajtásakor felmerülő ismert hibákhoz és korlátozásokhoz kapcsolódó információk: [Az Azure Database for PostgreSQL online migrálásával kapcsolatos ismert hibák és kerülő megoldások](known-issues-azure-postgresql-online.md).
* Az Azure database migration service szolgáltatásról a [Mi az Azure-adatbázis-áttelepítési szolgáltatás?](https://docs.microsoft.com/azure/dms/dms-overview)
* A PostgreSQL Azure Database for-ról a [Mi az Azure Database for PostgreSQL című](https://docs.microsoft.com/azure/postgresql/overview)cikkben olvashat.
