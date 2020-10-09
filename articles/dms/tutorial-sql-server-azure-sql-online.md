---
title: 'Oktatóanyag: SQL Server online migrálása az SQL önálló adatbázisba'
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan hajthat végre online áttelepítést SQL Serverról Azure SQL Databasere a Azure Database Migration Service használatával.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/21/2020
ms.openlocfilehash: 88aaa9ccf3d0c1319637036373463e2a93ccb649
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291316"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-online-using-dms"></a>Oktatóanyag: SQL Server migrálása önálló adatbázisba vagy készletezett adatbázisba Azure SQL Database online-ban a DMS használatával

A Azure Database Migration Service segítségével telepítheti át az adatbázisokat egy SQL Server-példányról [Azure SQL Databasere](https://docs.microsoft.com/azure/sql-database/) minimális állásidővel. Ebben az oktatóanyagban a **Adventureworks2012** -adatbázist egy SQL Server 2016 (vagy újabb) helyszíni példányára telepíti át, amely a Azure Database Migration Service használatával egyetlen adatbázishoz vagy készletezett adatbázishoz lett visszaállítva Azure SQL Database.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> - A helyszíni adatbázis felmérése a Data Migration Assistant szolgáltatás használatával.
> - A mintaséma migrálása a Data Migration Assistant szolgáltatás használatával.
> - Egy Azure Database Migration Service-példány létrehozása.
> - Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> - A migrálás futtatása.
> - Az áttelepítés monitorozása.
> - Migrálási jelentés letöltése.

> [!NOTE]
> Az online áttelepítés végrehajtásához a Azure Database Migration Service használatával egy példányt kell létrehozni a prémium szintű díjszabás alapján. További információkért tekintse meg a Azure Database Migration Service [díjszabását](https://azure.microsoft.com/pricing/details/database-migration/) ismertető oldalt.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a migrálási folyamat optimalizálása érdekében ugyanabban az Azure-régióban hozza létre az Azure Database Migration Service-példányt, mint amelyikben a céladatbázis is található. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk SQL Server áttelepítését mutatja be a Azure SQL Database egy önálló adatbázisba vagy készletezett adatbázisba. Az offline migrálással kapcsolatban tekintse meg az [SQL Server migrálása felügyelt Azure SQL Database-példányra kapcsolat nélküli üzemmódban, a DMS használatával](tutorial-sql-server-to-azure-sql.md) című cikket.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Töltse le és telepítse a [SQL Server 2012-es vagy újabb verzióját](https://www.microsoft.com/sql-server/sql-server-downloads).
- Engedélyezze a TCP/IP protokollt, amely az SQL Server Express telepítése során alapértelmezés szerint le van tiltva – kövesse a [Kiszolgálói hálózati protokoll engedélyezése vagy letiltása](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) cikk utasításait.
- Hozzon létre egy (vagy készletezett) adatbázist a Azure SQL Databaseban, amelyet a következő cikkben ismertetett részletességgel [hozhat létre a Azure SQL Database a Azure Portal használatával: önálló adatbázis létrehozása](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Ha SQL Server Integration Servicest (SSIS) használ, és a katalógus-adatbázist át szeretné telepíteni a SSIS-projektekhez/csomagokhoz (SSISDB) SQL Serverról Azure SQL Databasere, akkor a rendszer automatikusan létrehozza és felügyeli a célként megadott SSISDB, amikor a SSIS-t Azure Data Factory (ADF) kiépíti. A SSIS-csomagok áttelepítésével kapcsolatos további információkért tekintse [meg SQL Server Integration Services csomagok migrálása az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című cikket.

- Töltse le a [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) 3.3-as vagy újabb verzióját.
- Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Service számára a Azure Resource Manager üzemi modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával. A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg a [Virtual Network dokumentációt](https://docs.microsoft.com/azure/virtual-network/), és különösen a gyors üzembe helyezési cikkeket részletesen ismerteti.

    > [!NOTE]
    > Ha a virtuális hálózat beállítása során ExpressRoute használ a Microsoft számára, adja hozzá a következő szolgáltatási [végpontokat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti:
    > - Céladatbázis végpontja (például SQL-végpont, Cosmos DB végpont stb.)
    > - Tárolási végpont
    > - Service Bus-végpont
    >
    > Erre a konfigurációra azért van szükség, mert a Azure Database Migration Service nem rendelkezik internetkapcsolattal.

- Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport szabályai nem gátolják meg a következő bejövő kommunikációs portok Azure Database Migration Service: 443, 53, 9354, 445, 12000. Az Azure Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)című cikket.
- Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse az SQL-kiszolgáló forrását, amely alapértelmezés szerint az 1433-as TCP-port.
- Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
- Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.
- Hozzon létre egy kiszolgálói szintű [Tűzfalszabály-szabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a Azure SQL Database számára, hogy lehetővé tegye a Azure Database Migration Service hozzáférést a célként megadott adatbázisokhoz. Adja meg a Azure Database Migration Servicehoz használt virtuális hálózat alhálózati tartományát.
- Gondoskodjon róla, hogy a forrásként szolgáló SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedélyekkel.
- Győződjön meg arról, hogy a cél Azure SQL Database-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkeznek-e a cél Azure SQL Database példányokon a VEZÉRLÉSi adatbázis engedélyeivel.
- A forrás SQL Servernek 2005-ös vagy újabb verziójúnak kell lennie. A futó SQL Server-példány verziójának megállapításához lásd [az SQL Server és összetevői verziójának, kiadásának és frissítési szintjének megállapításával](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an) foglalkozó cikket.
- Az adatbázis(ok)nak tömeges naplózási vagy teljes helyreállítási módban kell lenniük. Az SQL Server-példány helyreállítási modelljének megállapításához lásd az [adatbázis (SQL Server) helyreállítási modelljének megtekintésével vagy módosításával](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server?view=sql-server-2017) foglalkozó cikket.
- Mindenképpen készítsen teljes biztonsági mentést az adatbázisokról. Az adatbázisok teljes biztonsági mentésével kapcsolatban lásd az [adatbázis teljes biztonsági mentésének létrehozását (Transact-SQL)](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms191304(v=sql.105)) ismertető cikket.
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

    Ha az eredmények között van egy vagy több olyan tábla, amelynél az „is_tracked_by_cdc” értéke „0”, engedélyezze a változások rögzítését az adatbázisban és az adott táblákon. Ezzel kapcsolatban lásd az [adatváltozások rögzítésének engedélyezésével és letiltásával (SQL Server)](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-2017) foglalkozó cikket.

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

- Tiltsa le az adatbázistriggereket az Azure SQL-céladatbázisban.
    >[!NOTE]
    > Az Azure SQL-céladatbázis triggereit az alábbi lekérdezéssel keresheti meg:

    ```sql
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```

    További információk: [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017). 

## <a name="assess-your-on-premises-database"></a>A helyszíni adatbázis felmérése

Mielőtt áttelepít egy SQL Server-példány adatait a Azure SQL Databaseba, fel kell mérnie az SQL Server-adatbázist az áttelepítést megakadályozó esetleges blokkolási problémákra. A Data Migration Assistant 3.3-as vagy újabb verzióját használva kövesse az [SQL Server migrálási felmérés végzése](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) cikkben leírt lépéseket, és végezze el a helyszíni adatbázis felmérését.

Helyszíni adatbázis értékeléséhez hajtsa végre a következő lépéseket:

1. A DMA-ban válassza az Új (+) ikont, majd a **Felmérés** projekttípust.
2. Nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza az **SQL Servert**, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database** lehetőséget. Ezután a **Létrehozás** lehetőséggel hozza létre a projektet.

   Ha kiértékeli a forrás SQL Server-adatbázist, és a Azure SQL Database egy különálló adatbázisba vagy készletezett adatbázisba telepíti át, akkor a következő értékelési jelentések közül választhat:

   - Adatbázis-kompatibilitás ellenőrzése
   - Szolgáltatásparitás ellenőrzése

   Alapértelmezés szerint mindkét jelentéstípus ki van választva.

3. A DMA **Beállítások** ablakában válassza a **Tovább** lehetőséget.
4. A **Források kiválasztása** képernyőn, a **Kapcsolódás kiszolgálóhoz** párbeszédablakban adja meg az SQL-kiszolgálója adatait, majd válassza a **Csatlakozás** lehetőséget.
5. A **Források hozzáadása** párbeszédablakban válassza az **AdventureWorks2012** elemet, majd a **Hozzáadás**, végül a **Felmérés indítása** lehetőséget.

    > [!NOTE]
    > Ha a SSIS-t használja, a DMA jelenleg nem támogatja a forrás-SSISDB értékelését. A SSIS-projekteket/csomagokat azonban értékeli/érvényesíti a rendszer, mivel azokat újra üzembe helyezi a Azure SQL Database által üzemeltetett cél-SSISDB. A SSIS-csomagok áttelepítésével kapcsolatos további információkért tekintse [meg SQL Server Integration Services csomagok migrálása az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című cikket.

    A felmérés befejeztével az eredmények a következőképpen jelennek meg:

    ![Adatmigrálás felmérése](media/tutorial-sql-server-to-azure-sql-online/dma-assessments.png)

    Az önálló adatbázisok vagy a Azure SQL Database készletezett adatbázisai esetében az értékelések azonosítják a szolgáltatás paritásával kapcsolatos problémákat és az áttelepítés blokkolásával kapcsolatos problémákat egyetlen adatbázis vagy készletezett adatbázis telepítésekor.

    - Az **SQL Server szolgáltatásparitás** kategória átfogó javaslatokat ad, az Azure-ban elérhető alternatív megközelítéseket javasol, valamint kockázatcsökkentő lépéseket ajánl fel a migrálási projektek megtervezéséhez.
    - A **kompatibilitási problémák** kategóriája olyan részlegesen támogatott vagy nem támogatott funkciókat azonosít, amelyek a kompatibilitási problémákra utalnak, amelyek letiltják SQL Server adatbázis (ok) áttelepítését Azure SQL Databasere. A felmerülő problémák megoldására a program javaslatokat is tesz.

6. Tekintse át a felmérés eredményeit, és az egyes lehetőségek kiválasztásával ellenőrizze, fennáll-e bármilyen akadályozó, illetve paritási probléma.

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása

Miután elégedett az értékeléssel, és meggyőződött arról, hogy a kiválasztott adatbázis életképes jelölt az áttelepítéshez egy önálló adatbázisba vagy készletezett adatbázisba Azure SQL Databaseban, a DMA használatával telepítse át a sémát Azure SQL Databasere.

> [!NOTE]
> Mielőtt létrehoz egy áttelepítési projektet a DMA-ban, győződjön meg róla, hogy már létrehozott egy SQL-adatbázist az Azure-ban, ahogy az előfeltételek között szerepel. Ebben az oktatóanyagban az Azure SQL Database neve **AdventureWorksAzure**, de megadhat bármilyen másik nevet is.

> [!IMPORTANT]
> Ha a SSIS-t használja, a DMA jelenleg nem támogatja a forrás-SSISDB áttelepítését, de a SSIS-projekteket/csomagokat újból üzembe helyezheti Azure SQL Database által üzemeltetett cél-SSISDB. A SSIS-csomagok áttelepítésével kapcsolatos további információkért tekintse [meg SQL Server Integration Services csomagok migrálása az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című cikket.

A **AdventureWorks2012** -séma egyetlen adatbázisba vagy készletezett adatbázisba Azure SQL Database való áttelepítéséhez hajtsa végre a következő lépéseket:

1. A Data Migration Assistant programban válassza az Új (+) ikont, majd a **Projekt típusa** alatt válassza a **Migrálás** lehetőséget.
2. Nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza az **SQL Server** elemet, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database** lehetőséget.
3. A **Migrálási hatókör** alatt válassza a **Csak a séma** lehetőséget.

    A fent leírt lépések elvégzése után megjelenik a DMA felhasználói felülete, ahogy az a következő képen látható:

    ![Data Migration Assistant-projekt létrehozása](media/tutorial-sql-server-to-azure-sql-online/dma-create-project.png)

4. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.
5. A DMA-ban adja meg az SQL Server forráskapcsolati adatait, válassza a **Csatlakozás** lehetőséget, majd válassza ki az **AdventureWorks2012** adatbázist.

    ![Data Migration Assistant forráskapcsolati adatok](media/tutorial-sql-server-to-azure-sql-online/dma-source-connect.png)

6. Kattintson a **tovább**gombra, a **Csatlakozás a célkiszolgálóra**lehetőséghez, adja meg az Azure SQL Database cél kapcsolati adatait, válassza a **Csatlakozás**lehetőséget, majd válassza ki azt a **AdventureWorksAzure** -adatbázist, amelyet előre kiépített Azure SQL Databaseban.

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

3. Keresse meg az áttelepítést, majd a **Microsoft. DataMigration**jobb oldalán válassza a **regisztráció**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-sql-server-to-azure-sql-online/portal-register-resource-provider.png)

## <a name="create-an-instance"></a>Példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-sql-server-to-azure-sql-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-sql-server-to-azure-sql-online/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, amelyen az Azure Database Migration Service példányát létre szeretné hozni. 

5. Válasszon egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat biztosítja a Azure Database Migration Service hozzáférést a forrás SQL Server és a célként megadott Azure SQL Database példányhoz.

    Ha további információt szeretne arról, hogyan hozhat létre virtuális hálózatot a Azure Portalban, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](https://aka.ms/DMSVnet)című cikket.

6. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-sql-server-to-azure-sql-online/dms-settings2.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-sql-server-to-azure-sql-online/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.

    ![Az Azure Database Migration Service személyes példányának megkeresése](media/tutorial-sql-server-to-azure-sql-online/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az **Új migrálási projekt** képernyőn nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza ki az **SQL Servert**, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database-t**.
5. A **tevékenység típusának** kiválasztása szakaszban válassza az **online adatáttelepítés**lehetőséget.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-sql-server-to-azure-sql-online/dms-create-project3.png)

    > [!NOTE]
    > Másik lehetőségként választhatja a **projekt létrehozása** lehetőséget az áttelepítési projekt létrehozásához, és később végrehajthatja az áttelepítést.

6. Kattintson a **Mentés** gombra.

7. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

    ![Database Migration Service-tevékenység létrehozása és futtatása](media/tutorial-sql-server-to-azure-sql-online/dms-create-and-run-activity.png)

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server-példány kapcsolati adatait.

    Gondoskodjon róla, hogy az SQL Server-példány neveként teljes tartománynevet (FQDN) használjon. Amikor a DNS-névfeloldás nem lehetséges, az IP-címet is használhatja.

2. Ha nem telepített megbízható tanúsítványt a forráskiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítvánnyal titkosított TLS-kapcsolatok nem biztosítanak erős biztonságot. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Az önaláírt tanúsítványokat nem szabad a TLS-t használni éles környezetben vagy az internethez csatlakozó kiszolgálókon.

   ![Forrás részletei](media/tutorial-sql-server-to-azure-sql-online/dms-source-details3.png)

    > [!IMPORTANT]
    > Ha a SSIS-t használja, a DMS jelenleg nem támogatja a forrás-SSISDB áttelepítését, de a SSIS-projekteket/csomagokat újra üzembe helyezheti Azure SQL Database által üzemeltetett cél-SSISDB. A SSIS-csomagok áttelepítésével kapcsolatos további információkért tekintse [meg SQL Server Integration Services csomagok migrálása az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című cikket.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés**lehetőséget, majd az **áttelepítési cél részletei** képernyőn adja meg a cél Azure SQL Database kapcsolati adatait, amely a **ADVENTUREWORKS2012** -sémának a DMA használatával üzembe helyezett előre kiépített Azure SQL Database.

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
4. Ha az adatbázis migrálási állapota **Befejezve** értékre vált, csatlakoztassa alkalmazásait az új Azure SQL-céladatbázishoz.

    ![Tevékenység állapota: Befejezve](media/tutorial-sql-server-to-azure-sql-online/dms-activity-completed.png)

## <a name="next-steps"></a>További lépések

- További információ a Azure SQL Database való online áttelepítéssel kapcsolatos ismert problémákról és korlátozásokról: [Azure SQL Database online áttelepítéssel kapcsolatos ismert problémák és megkerülő megoldások](known-issues-azure-sql-online.md).
- Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- További információ a Azure SQL Databaseről: [Mi a Azure SQL Database szolgáltatás?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
