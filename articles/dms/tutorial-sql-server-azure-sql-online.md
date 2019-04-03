---
title: 'Oktatóanyag: Azure Database Migration Service segítségével online áttelepítése az SQL Server adatbázis egyetlen vagy készletezett Azure SQL Database-ben |} A Microsoft Docs'
description: Megtanulhatja, hogyan hajtható végre egy online migrálás a helyszíni SQL Server egy önálló adatbázis vagy készletezett Azure SQL Database-adatbázishoz az Azure Database Migration Service használatával.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c01eccb63639a3838c9f726bc48400a76aba8cf0
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883496"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-online-using-dms"></a>Oktatóanyag: SQL Server migrálása az önálló adatbázis vagy készletezett Azure SQL Database-adatbázis online állapotba a DMS használatával

Az Azure Database Migration Service segítségével minimális állásidővel migrálhatja egy helyszíni SQL Server-példány adatbázisait az [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/)-be. Ebben az oktatóanyagban áttelepítése a **Adventureworks2012** adatbázis helyreállítása az SQL Server 2016 (vagy újabb) helyszíni példányát egy önálló adatbázis vagy készletezett adatbázis az Azure SQL Database az Azure Database Migration használatával A szolgáltatás.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - A helyszíni adatbázis felmérése a Data Migration Assistant szolgáltatás használatával.
> - A mintaséma migrálása a Data Migration Assistant szolgáltatás használatával.
> - Egy Azure Database Migration Service-példány létrehozása.
> - Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> - A migrálás futtatása.
> - A migrálás monitorozása.
> - Migrálási jelentés letöltése.

> [!NOTE]
> Az Azure Database Migration Service online áttelepítése használatához hozzon létre egy példányt prémium tarifacsomagban alapján. További információkért tekintse meg az Azure Database Migration Service [díjszabás](https://azure.microsoft.com/pricing/details/database-migration/) lapot.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a migrálási folyamat optimalizálása érdekében ugyanabban az Azure-régióban hozza létre az Azure Database Migration Service-példányt, mint amelyikben a céladatbázis is található. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk ismerteti egy online migrálás SQL Serverről egy önálló adatbázis vagy készletezett Azure SQL Database-adatbázishoz. Az offline migrálással kapcsolatban tekintse meg az [SQL Server migrálása felügyelt Azure SQL Database-példányra kapcsolat nélküli üzemmódban, a DMS használatával](tutorial-sql-server-to-azure-sql.md) című cikket.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Töltse le és telepítse az [SQL Server 2012-es vagy újabb verzióját](https://www.microsoft.com/sql-server/sql-server-downloads) (bármelyik kiadást).
- Engedélyezze a TCP/IP protokollt, amely az SQL Server Express telepítése során alapértelmezés szerint le van tiltva – kövesse a [Kiszolgálói hálózati protokoll engedélyezése vagy letiltása](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) cikk utasításait.
- Egyetlen (vagy készletezett) adatbázis létrehozása az Azure SQL Database, amelyeket a következő a részletek a cikkben [egy önálló adatbázis létrehozása az Azure SQL Database az Azure portal használatával](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Ha az SQL Server Integration Services (SSIS) használ, és szeretné áttelepíteni a katalógus-adatbázis, az SSIS-projektek/csomagok (SSISDB) az SQL Serverről az Azure SQL Database, a cél SSISDB létrehozott és kezelt automatikusan az Ön nevében amikor, SSIS az Azure Data Factory (ADF) üzembe helyezése. SSIS-csomagok áttelepítése kapcsolatos további információkért tekintse meg a cikket [áttelepítése az SQL Server Integration Services-csomagok az Azure-bA](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

- Töltse le a [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) 3.3-as vagy újabb verzióját.
- Hozzon létre egy Azure Virtual Network (VNET) használatával az Azure Resource Manager üzembe helyezési modell, amely lehetővé teszi a helyek közötti kapcsolatot a helyszíni adatforrás-kiszolgálók használatával vagy az Azure Database Migration Service [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Virtuális hálózathoz a telepítés során, ha az ExpressRoute hálózati a Microsoft társviszony-létesítés használja, hozzá a következő szolgáltatás [végpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) az alhálózathoz, amelyben üzembe fogja helyezni a szolgáltatást:
    > - Cél adatbázis végpont (például SQL-végpont, Cosmos-DB végpont, és így tovább)
    > - Storage-végpont
    > - Service bus-végpont
    >
    > Ez a konfiguráció szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal.

- Győződjön meg arról, hogy a virtuális hálózatok közötti hálózati biztonsági csoport szabályai nem blokkolják a következő bejövő kommunikációs portokat, Azure Database Migration Service: 443, 53, 9354, 445, 12000. További részletek az Azure VNET NSG-forgalom szűréséről: [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse az SQL-kiszolgáló forrását, amely alapértelmezés szerint az 1433-as TCP-port.
- Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
- Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.
- Hozzon létre egy kiszolgálószintű [tűzfalszabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure), hogy az Azure SQL Database Migration Service hozzáférhessen a céladatbázisokhoz. Adja meg az Azure Database Migration Service-hez használt virtuális hálózat alhálózati tartományát.
- Gondoskodjon róla, hogy a forrásként szolgáló SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedélyekkel.
- Gondoskodjon róla, hogy a forrásként szolgáló Azure SQL Database-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek CONTROL DATABASE engedéllyel a célként szolgáló Azure SQL adatbázisokban.
- A forrás SQL Servernek 2005-ös vagy újabb verziójúnak kell lennie. A futó SQL Server-példány verziójának megállapításához lásd [az SQL Server és összetevői verziójának, kiadásának és frissítési szintjének megállapításával](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an) foglalkozó cikket.
- Az adatbázis(ok)nak tömeges naplózási vagy teljes helyreállítási módban kell lenniük. Az SQL Server-példány helyreállítási modelljének megállapításához lásd az [adatbázis (SQL Server) helyreállítási modelljének megtekintésével vagy módosításával](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server?view=sql-server-2017) foglalkozó cikket.
- Mindenképpen készítsen teljes biztonsági mentést az adatbázisokról. Teljes biztonsági mentés létrehozásához lásd: a cikk [hogyan: Hozzon létre egy teljes biztonsági mentés (Transact-SQL)](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms191304(v=sql.105)).
- Ha valamelyik táblának nincs elsődleges kulcsa, engedélyezze az adatváltozások rögzítését (Change Data Capture, CDC) az adatbázisban és az adott táblán.
    > [!NOTE]
    > Az elsődleges kulccsal nem rendelkező táblákat az alábbi szkripttel keresheti meg.

    ```sql
    USE <DBName>;
    go
    SELECT is_tracked_by_cdc, name AS TableName
    FROM sys.tables WHERE type = 'U' and is_ms_shipped = 0 AND
    OBJECTPROPERTY(OBJECT_ID, 'TableHasPrimaryKey') = 0;
     ```
    >Ha az eredmények között van egy vagy több olyan tábla, amelynél az „is_tracked_by_cdc” értéke „0”, engedélyezze a változások rögzítését az adatbázisban és az adott táblákon. Ezzel kapcsolatban lásd az [adatváltozások rögzítésének engedélyezésével és letiltásával (SQL Server)](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-2017) foglalkozó cikket.

- Konfigurálja a forrásként szolgáló SQL Server terjesztői szerepkörét.

    >[!NOTE]
    > Azt, hogy a replikációs összetevők telepítve vannak-e, az alábbi lekérdezéssel állapíthatja meg.

    ```sql
    USE master;
    DECLARE @installed int;
    EXEC @installed = sys.sp_MS_replication_installed;
    SELECT @installed as installed;
    ```
    Ha az eredmény egy hibaüzenet, amely a replikációs összetevők telepítését javasolja, akkor telepítse az SQL Server replikációs összetevőit. Ezzel kapcsolatban lásd az [SQL Server-replikáció telepítésével](https://docs.microsoft.com/sql/database-engine/install-windows/install-sql-server-replication?view=sql-server-2017) foglalkozó cikket.

    Ha a replikáció már telepítve van, az alábbi T-SQL-paranccsal ellenőrizze, hogy a terjesztői szerepkör konfigurálva van-e a forrásként szolgáló SQL Server-kiszolgálón.

    ```sql
    EXEC sp_get_distributor;
    ```

    Ha a terjesztés nincs beállítva, tehát ha a fenti parancs kimenetében a terjesztői kiszolgálónál NULL érték jelenik meg, akkor konfigurálja a terjesztést a [terjesztés konfigurálását](https://docs.microsoft.com/sql/relational-databases/replication/configure-publishing-and-distribution?view=sql-server-2017) ismertető cikk útmutatása alapján.

- Tiltsa le az adatbázistriggereket az Azure SQL Database-céladatbázisban.
    >[!NOTE]
    > Az Azure SQL Database-céladatbázis triggereit az alábbi lekérdezéssel keresheti meg:

    ```sql
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    További információk: [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017). 

## <a name="assess-your-on-premises-database"></a>A helyszíni adatbázis felmérése

Áttelepítheti az adatokat a helyszíni SQL Server-példány egyetlen adatbázishoz vagy készletezett Azure SQL Database-adatbázishoz, mielőtt kell az SQL Server-adatbázis az olyan problémák elhárítását, amelyek megakadályozhatják a migrálás értékeléséhez. A Data Migration Assistant 3.3-as vagy újabb verzióját használva kövesse az [SQL Server migrálási felmérés végzése](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) cikkben leírt lépéseket, és végezze el a helyszíni adatbázis felmérését.

A helyszíni adatbázis felméréséhez hajtsa végre a következő lépéseket:

1. A DMA-ban válassza az Új (+) ikont, majd a **Felmérés** projekttípust.
2. Nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza az **SQL Servert**, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database** lehetőséget. Ezután a **Létrehozás** lehetőséggel hozza létre a projektet.

    Ha az értékelni kívánt a forrás SQL Server adatbázis migrálása az önálló adatbázis vagy készletezett Azure SQL Database-adatbázishoz, választhat egyikét vagy mindkettőt a következő értékelési jelentéstípusok:

   - Adatbázis-kompatibilitás ellenőrzése
   - Szolgáltatásparitás ellenőrzése

     Alapértelmezés szerint mindkét jelentéstípus ki van választva.

3. A DMA **Beállítások** ablakában válassza a **Tovább** lehetőséget.
4. A **Források kiválasztása** képernyőn, a **Kapcsolódás kiszolgálóhoz** párbeszédablakban adja meg az SQL-kiszolgálója adatait, majd válassza a **Csatlakozás** lehetőséget.
5. A **Források hozzáadása** párbeszédablakban válassza az **AdventureWorks2012** elemet, majd a **Hozzáadás**, végül a **Felmérés indítása** lehetőséget.

    > [!NOTE]
    > Az SSIS használata, ha a DMA jelenleg nem támogatja a forrás SSISDB értékelését. Azonban SSIS-projektek/csomagok mérni/érvényesíti, azokat a cél Azure SQL Database által üzemeltetett SSISDB rendszer újratelepítése. SSIS-csomagok áttelepítése kapcsolatos további információkért tekintse meg a cikket [áttelepítése az SQL Server Integration Services-csomagok az Azure-bA](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

    A felmérés befejeztével az eredmények a következőképpen jelennek meg:

    ![Adatmigrálás felmérése](media/tutorial-sql-server-to-azure-sql-online/dma-assessments.png)

    A különálló adatbázisokat vagy készletezett adatbázisok az Azure SQL Database az értékelések határozza meg kapcsolatos funkcióparitási problémák és a egy önálló adatbázis üzembe helyezése és készletezett adatbázisok áttelepítése hátráltató.

    - Az **SQL Server szolgáltatásparitás** kategória átfogó javaslatokat ad, az Azure-ban elérhető alternatív megközelítéseket javasol, valamint kockázatcsökkentő lépéseket ajánl fel a migrálási projektek megtervezéséhez.
    - A **Kompatibilitási problémák** kategória azonosítja a csak részben támogatott vagy nem támogatott funkciókat, amelyek olyan kompatibilitási problémákat okozhatnak, amelyek akadályozhatják az SQL Server-adatbázis(ok) migrálását az Azure SQL Database szolgáltatásba. A felmerülő problémák megoldására a program javaslatokat is tesz.

6. Tekintse át a felmérés eredményeit, és az egyes lehetőségek kiválasztásával ellenőrizze, fennáll-e bármilyen akadályozó, illetve paritási probléma.

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása

Miután nem okoz gondot az értékelés az és DMA meggyőződött arról, hogy a kijelölt adatbázis egy önálló adatbázis vagy készletezett Azure SQL Database-adatbázishoz való migrálásának egy működőképes jelölt, használja a séma migrálása az Azure SQL Database.

> [!NOTE]
> Mielőtt létrehozna egy migrálási projektet a DMA-ban, győződjön meg arról, hogy az előfeltételekben meghatározottak alapján már létrehozott egy Azure SQL Database-adatbázist. Ebben az oktatóanyagban az Azure SQL Database neve **AdventureWorksAzure**, de megadhat bármilyen másik nevet is.
> [!IMPORTANT]
> Az SSIS használata, ha a DMA jelenleg nem támogatja az áttelepítés forrás SSISDB, de, ugyanakkor az SSIS-projektek/csomagok a cél Azure SQL Database által üzemeltetett SSISDB. SSIS-csomagok áttelepítése kapcsolatos további információkért tekintse meg a cikket [áttelepítése az SQL Server Integration Services-csomagok az Azure-bA](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Áttelepíteni a **AdventureWorks2012** séma egy önálló adatbázis vagy készletezett Azure SQL Database-adatbázishoz, hajtsa végre az alábbi lépéseket:

1. A Data Migration Assistant programban válassza az Új (+) ikont, majd a **Projekt típusa** alatt válassza a **Migrálás** lehetőséget.
2. Nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza az **SQL Server** elemet, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database** lehetőséget.
3. A **Migrálási hatókör** alatt válassza a **Csak a séma** lehetőséget.

    A fent leírt lépések elvégzése után megjelenik a DMA felhasználói felülete, ahogy az a következő képen látható:

    ![Data Migration Assistant-projekt létrehozása](media/tutorial-sql-server-to-azure-sql-online/dma-create-project.png)

4. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.
5. A DMA-ban adja meg az SQL Server forráskapcsolati adatait, válassza a **Csatlakozás** lehetőséget, majd válassza ki az **AdventureWorks2012** adatbázist.

    ![Data Migration Assistant forráskapcsolati adatok](media/tutorial-sql-server-to-azure-sql-online/dma-source-connect.png)

6. Válassza a **Tovább** lehetőséget. A **Csatlakozás célkiszolgálóhoz** területen alatt adja meg az Azure SQL Database célkapcsolati adatait, válassza a **Csatlakozás** lehetőséget, majd válassza ki azt az **AdventureWorksAzure** adatbázist, amelyet az Azure SQL Database-ben előzetesen kiépített.

    ![Data Migration Assistant célkapcsolati adatok](media/tutorial-sql-server-to-azure-sql-online/dma-target-connect.png)

7. A **Tovább** lehetőséggel lépjen tovább az **Objektumok kiválasztása** képernyőre, ahol megadhatja azokat a sémaobjektumokat az **AdventureWorks2012** adatbázisban, amelyeket üzembe kell helyezni az Azure SQL Database-ben.

    Alapértelmezés szerint az összes objektum ki van jelölve.

    ![SQL-szkriptek létrehozása](media/tutorial-sql-server-to-azure-sql-online/dma-assessment-source.png)

8. Az **SQL-szkript létrehozása** lehetőséggel hozza létre az SQL-szkripteket, majd tekintse át azokat, hogy észrevehesse az esetleges hibákat.

    ![Sémaszkript](media/tutorial-sql-server-to-azure-sql-online/dma-schema-script.png)

9. Válassza a **Séma üzembe helyezése** lehetőséget a séma üzembe helyezéséhez az Azure SQL Database-ben. Az üzembe helyezés után ellenőrizze a célt az esetleges hibákért.

    ![Séma üzembe helyezése](media/tutorial-sql-server-to-azure-sql-online/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-sql-server-to-azure-sql-online/portal-select-subscription1.png)

2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-sql-server-to-azure-sql-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-sql-server-to-azure-sql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-sql-server-to-azure-sql-online/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, amelyen az Azure Database Migration Service példányát létre szeretné hozni. 

5. Válasszon ki egy meglévő virtuális hálózatot (VNET), vagy hozzon létre egy újat.

    A VNET biztosítja az Azure Database Migration Service számára a forrásként szolgáló SQL Serverhez és az Azure SQL Database-célpéldányhoz való hozzáférést.

    További információk a virtuális hálózatok létrehozásáról az Azure Portallal: [Virtuális hálózat létrehozása az Azure Portallal](https://aka.ms/DMSVnet).

6. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    Ha a megfelelő Azure Database Migration Service-csomag kiválasztása segítségre van szüksége, tekintse meg a javaslatok, az a könyvelési [Itt](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-sql-server-to-azure-sql-online/dms-settings2.png)

7 válassza **létrehozás** szolgáltatás létrehozásához.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-sql-server-to-azure-sql-online/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.

    ![Az Azure Database Migration Service személyes példányának megkeresése](media/tutorial-sql-server-to-azure-sql-online/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az **Új migrálási projekt** képernyőn nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza ki az **SQL Servert**, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database-t**.
5. Az a **válassza ki a tevékenység típusát** szakaszban jelölje be **Online adatáttelepítés**.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-sql-server-to-azure-sql-online/dms-create-project3.png)

    > [!NOTE]
    > Alternatív megoldásként választhatja a **csak a projektet létrehozó** lehetőséget, ha most csak a migrálási projektet szeretné létrehozni, és csak később szeretné elindítani a tényleges migrálást.

6. Kattintson a **Mentés** gombra.

7. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

    ![Database Migration Service-tevékenység létrehozása és futtatása](media/tutorial-sql-server-to-azure-sql-online/dms-create-and-run-activity.png)

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server-példány kapcsolati adatait.

    Gondoskodjon róla, hogy az SQL Server-példány neveként teljes tartománynevet (FQDN) használjon. Amikor a DNS-névfeloldás nem lehetséges, az IP-címet is használhatja.

2. Ha nem telepített megbízható tanúsítványt a forráskiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítványokkal titkosított SSL-kapcsolatok nem biztosítanak erős védelmet. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Éles környezetben vagy az internethez csatlakozó kiszolgálók esetén az önaláírt tanúsítványokkal működő SSL nem megbízható.

   ![Forrás részletei](media/tutorial-sql-server-to-azure-sql-online/dms-source-details3.png)

    > [!IMPORTANT]
    > Az SSIS használata, ha a DMS jelenleg nem támogatja az áttelepítés forrás SSISDB, de az SSIS-projektek/csomagok a cél Azure SQL Database által üzemeltetett SSISDB, ugyanakkor. SSIS-csomagok áttelepítése kapcsolatos további információkért tekintse meg a cikket [áttelepítése az SQL Server Integration Services-csomagok az Azure-bA](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés** lehetőséget, majd a **Migrálási cél részletei** képernyőn adja meg a célul szolgáló Azure SQL Database Server kapcsolati adatait. Ez a cél az az Azure SQL Database, amelyen üzembe helyezte az **AdventureWorks2012** sémát a DMA-val.

    ![Cél kiválasztása](media/tutorial-sql-server-to-azure-sql-online/dms-select-target3.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis neve megegyezik a forrásadatbázis nevével, az Azure Database Migration Service alapértelmezés szerint a céladatbázist választja ki.

    ![Leképezés céladatbázisokra](media/tutorial-sql-server-to-azure-sql-online/dms-map-targets-activity3.png)

3. Válassza a **Mentés**, lehetőséget, majd a **Táblák kiválasztása** képernyőn bontsa ki a táblák listáját, és tekintse át az érintett mezőket.

    Az Azure Database Migration Service automatikusan kiválasztja az összes üres forrástáblát, amely a célként szolgáló Azure SQL Database-példányon megtalálható. Ha újra kíván migrálni olyan táblákat, amelyek már tartalmaznak adatokat, azokat ezen a panelen külön kikell választania.

    ![Select tables (Táblák kiválasztása)](media/tutorial-sql-server-to-azure-sql-online/dms-configure-setting-activity3.png)

4. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-sql-server-to-azure-sql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

- Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Inicializálás**.

    ![Tevékenység állapota: Inicializálás](media/tutorial-sql-server-to-azure-sql-online/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Fut** értékre.

2. Kattintson egy adatbázisra az **Adatok teljes betöltése** és a **Növekményes adatszinkronizálás** műveletek migrálási állapotának megtekintéséhez.

    ![Tevékenység állapota: Folyamatban](media/tutorial-sql-server-to-azure-sql-online/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása

Az első teljes betöltés elkészültével az adatbázisok **Átállásra kész** állapotúak lesznek.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

    ![Átállás indítása](media/tutorial-sql-server-to-azure-sql-online/dms-start-cutover.png)

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.
3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázis migrálási állapota **Befejezve** értékre vált, csatlakoztassa alkalmazásait az új Azure SQL Database-céladatbázishoz.

    ![Tevékenység állapota: Befejezve](media/tutorial-sql-server-to-azure-sql-online/dms-activity-completed.png)

## <a name="next-steps"></a>További lépések

- [SQL-migrálás az Azure Database Migration Service (DMS) használatával](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) – gyakorlati labor.
- Ismert problémák és korlátozások online migrálást az Azure SQL Database végrehajtásakor kapcsolatos információkért lásd: a cikk [ismert problémák és megoldások az Azure SQL Database online áttelepítések](known-issues-azure-sql-online.md).
- Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Azure SQL Database kapcsolatos információkért tekintse meg a cikket [Mi az az Azure SQL Database szolgáltatás?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
