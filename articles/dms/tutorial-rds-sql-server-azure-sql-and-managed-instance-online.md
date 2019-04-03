---
title: 'Oktatóanyag: Az Azure Database Migration Service használata az Azure SQL Database vagy egy Azure SQL Database felügyelt példány távoli asztali szolgáltatások SQL Server online áttelepítése |} A Microsoft Docs'
description: Végezzen el egy online migrálás a távoli asztali szolgáltatások az SQL Server az Azure SQL Database vagy az Azure SQL Database felügyelt példány az Azure Database Migration Service segítségével megtanulhatja, hogyan.
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
ms.openlocfilehash: 4990b5f42291856c3695b4bf0eb6ec4084e9214e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886403"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-database-managed-instance-online-using-dms"></a>Oktatóanyag: A távoli asztali szolgáltatások SQL Server migrálása az Azure SQL Database vagy Azure SQL Database felügyelt példány online DMS használatával
Az Azure Database Migration Service segítségével az adatbázisokat át egy távoli asztali szolgáltatások az SQL Server-példány [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) vagy egy [Azure SQL Database felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) minimális állásidővel. Ebben az oktatóanyagban áttelepítése a **Adventureworks2012** visszaállított adatbázis egy távoli asztali szolgáltatások SQL Server példány SQL Server 2012 (vagy újabb) Azure SQL Database vagy az Azure SQL Database felügyelt példány az Azure Database Migration használatával A szolgáltatás.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Azure SQL Database egy példányát, vagy egy Azure SQL Database felügyelt példány létrehozása. 
> * A mintaséma migrálása a Data Migration Assistant szolgáltatás használatával.
> * Egy Azure Database Migration Service-példány létrehozása.
> * Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.
> * Migrálási jelentés letöltése.

> [!NOTE]
> Az Azure Database Migration Service online áttelepítése használatához hozzon létre egy példányt prémium tarifacsomagban alapján. További információkért tekintse meg az Azure Database Migration Service [díjszabás](https://azure.microsoft.com/pricing/details/database-migration/) lapot.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a migrálási folyamat optimalizálása érdekében ugyanabban az Azure-régióban hozza létre az Azure Database Migration Service-példányt, mint amelyikben a céladatbázis is található. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk ismerteti az Azure SQL Database vagy egy Azure SQL Database felügyelt példány az SQL Server távoli asztali szolgáltatások egy online migrálás.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Hozzon létre egy [távoli asztali szolgáltatások az SQL Server-adatbázis](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
- Hozzon létre egy példányt az Azure SQL Database, amelyeket a következő a részletek a cikkben [egy Azure SQL database létrehozása az Azure Portalon](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).

    > [!NOTE]
    > Ha végzi az áttelepítést egy Azure SQL Database felügyelt példány, kövesse a cikk részletesen [hozzon létre egy Azure SQL Database felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started), majd hozzon létre egy üres adatbázist nevű **AdventureWorks2012**. 
 
- Töltse le a [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) 3.3-as vagy újabb verzióját.
- Hozzon létre egy Azure Virtual Network (VNET) az Azure Database Migration Service az Azure Resource Manager-alapú üzemi modell használatával. Ha az áttelepítés egy Azure SQL Database felügyelt példányt használ, ügyeljen arra, hogy a DMS-példány létrehozása ugyanabban a vnetben használt az Azure SQL Database felügyelt példány, de egy másik alhálózat.  Azt is megteheti Ha a DMS használja egy másik virtuális Hálózattal, meg kell hozhat létre a két virtuális hálózatok közötti Társviszonyt.
 
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
- Hozzon létre egy kiszolgálószintű [tűzfalszabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure), hogy az Azure SQL Database Migration Service hozzáférhessen a céladatbázisokhoz. Adja meg az Azure Database Migration Service-hez használt virtuális hálózat alhálózati tartományát.
- Győződjön meg arról, hogy a távoli asztali szolgáltatások SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok társítva, egy olyan fiókkal, amely a "db_owner" adatbázis-szerepkörök, amelyek az áttelepíteni kívánt összes adatbázis és a "Processadmin" kiszolgálói szerepkör tagja.
- Győződjön meg arról, hogy a cél Azure SQL Database-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezik-e adatbázis VEZÉRLÉSE engedéllyel a cél Azure SQL-adatbázisok és a sysadmin (rendszergazda) szerepkör tagjának, ha az áttelepítés az Azure SQL Database felügyelt példánya.
- A forrás SQL Server távoli asztali szolgáltatások kell lennie az SQL Server 2012 vagy újabb verzió. A futó SQL Server-példány verziójának megállapításához lásd [az SQL Server és összetevői verziójának, kiadásának és frissítési szintjének megállapításával](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an) foglalkozó cikket.
- Módosítási adatok rögzítése (CDC) engedélyezze a távoli asztali szolgáltatások SQL Server-adatbázis és a migrálásra kiválasztott összes felhasználó tábla.
    > [!NOTE]
    > Az alábbi parancsfájl használatával engedélyezze a CDC a egy távoli asztali szolgáltatások az SQL Server-adatbázisból.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Az alábbi parancsfájl használatával engedélyezze a CDC-az összes tábla.
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
- Tiltsa le az adatbázistriggereket az Azure SQL Database-céladatbázisban.
    > [!NOTE]
    > Az Azure SQL Database-céladatbázis triggereit az alábbi lekérdezéssel keresheti meg:
    ```
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    További információk: [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása
A séma migrálása az Azure SQL Database a DMA segítségével.

> [!NOTE]
> Mielőtt létrehozna egy migrálási projektet a DMA-ban, győződjön meg arról, hogy az előfeltételekben meghatározottak alapján már létrehozott egy Azure SQL Database-adatbázist. Az ebben az oktatóanyagban az Azure SQL Database nevére azt feltételezi, hogy **AdventureWorks2012**, de megadhat bármilyen kívánt nevét.

Az **AdventureWorks2012** séma Azure SQL Database-be való migrálásához végezze el a következő lépéseket:

1.  A Data Migration Assistant programban válassza az Új (+) ikont, majd a **Projekt típusa** alatt válassza a **Migrálás** lehetőséget.
2.  Nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza az **SQL Server** elemet, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database** lehetőséget.
3.  A **Migrálási hatókör** alatt válassza a **Csak a séma** lehetőséget.

    A fent leírt lépések elvégzése után megjelenik a DMA felhasználói felülete, ahogy az a következő képen látható:
    
    ![Data Migration Assistant-projekt létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4.  A projekt létrehozásához válassza a **Létrehozás** lehetőséget.
5.  A DMA-ban adja meg az SQL Server forráskapcsolati adatait, válassza a **Csatlakozás** lehetőséget, majd válassza ki az **AdventureWorks2012** adatbázist.

    ![Data Migration Assistant forráskapcsolati adatok](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6.  Válassza ki **tovább**alatt **csatlakozás célkiszolgálóhoz**, adja meg az Azure SQL database kapcsolati cél adatait, válassza ki **Connect**, majd válassza ki a **AdventureWorksAzure** előzetes kiosztása az Azure SQL Database adatbázis.

    ![Data Migration Assistant célkapcsolati adatok](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7.  A **Tovább** lehetőséggel lépjen tovább az **Objektumok kiválasztása** képernyőre, ahol megadhatja azokat a sémaobjektumokat az **AdventureWorks2012** adatbázisban, amelyeket üzembe kell helyezni az Azure SQL Database-ben.

    Alapértelmezés szerint az összes objektum ki van jelölve.

    ![SQL-szkriptek létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8.  Az **SQL-szkript létrehozása** lehetőséggel hozza létre az SQL-szkripteket, majd tekintse át azokat, hogy észrevehesse az esetleges hibákat.

    ![Sémaszkript](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9.  Válassza a **Séma üzembe helyezése** lehetőséget a séma üzembe helyezéséhez az Azure SQL Database-ben. Az üzembe helyezés után ellenőrizze a célt az esetleges hibákért.

    ![Séma üzembe helyezése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása
1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.
 
   ![Portál-előfizetések megtekintése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)
       
2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.
 
    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)
    
3.  Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.
 
    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Példány létrehozása
1.  Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2.  Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.
 
    ![Azure Database Migration Service-példány létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3.  **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, amelyen az Azure Database Migration Service példányát létre szeretné hozni. 

5. Válasszon ki egy meglévő virtuális hálózatot (VNET), vagy hozzon létre egy újat.

    A VNET biztosítja az Azure Database Migration Service számára a forrásként szolgáló SQL Serverhez és az Azure SQL Database-célpéldányhoz való hozzáférést.

    További információk a virtuális hálózatok létrehozásáról az Azure Portallal: [Virtuális hálózat létrehozása az Azure Portallal](https://aka.ms/DMSVnet).

6. Válasszon egy tarifacsomagot; az online áttelepítés mindenképpen a prémium tarifacsomag kiválasztásához.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

     ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7.  A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása
A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.
 
      ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.
 
     ![Az Azure Database Migration Service személyes példányának megkeresése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)
 
3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az a **új migrálási projekt** lapon adjon meg egy nevet a projektnek a **forráskiszolgáló típusa** szövegbeviteli mezőben válasszon ki **SQL Serverhez az AWS RDS**, a a  **Célkiszolgáló típusa** szövegbeviteli mezőben válasszon ki **Azure SQL Database**.
5. Az a **válassza ki a tevékenység típusát** szakaszban jelölje be **Online adatáttelepítés**.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Alternatív megoldásként választhatja a **csak a projektet létrehozó** lehetőséget, ha most csak a migrálási projektet szeretné létrehozni, és csak később szeretné elindítani a tényleges migrálást.

6. Kattintson a **Mentés** gombra.

7. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

    ![Database Migration Service-tevékenység létrehozása és futtatása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)
 
## <a name="specify-source-details"></a>Forrás adatainak megadása
1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server-példány kapcsolati adatait.
 
    Gondoskodjon róla, hogy az SQL Server-példány neveként teljes tartománynevet (FQDN) használjon.

2. Ha nem telepített megbízható tanúsítványt a forráskiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítványokkal titkosított SSL-kapcsolatok nem biztosítanak erős védelmet. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Éles környezetben vagy az internethez csatlakozó kiszolgálók esetén az önaláírt tanúsítványokkal működő SSL nem megbízható.

   ![Forrás részletei](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Cél adatainak megadása
1. Válassza a **Mentés** lehetőséget, majd a **Migrálási cél részletei** képernyőn adja meg a célul szolgáló Azure SQL Database Server kapcsolati adatait. Ez a cél az az Azure SQL Database, amelyen üzembe helyezte az **AdventureWorks2012** sémát a DMA-val.

    ![Cél kiválasztása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis neve megegyezik a forrásadatbázis nevével, az Azure Database Migration Service alapértelmezés szerint a céladatbázist választja ki.

    ![Leképezés céladatbázisokra](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Válassza a **Mentés**, lehetőséget, majd a **Táblák kiválasztása** képernyőn bontsa ki a táblák listáját, és tekintse át az érintett mezőket.

    Az Azure Database Migration Service automatikusan kiválasztja az összes üres forrástáblát, amely a célként szolgáló Azure SQL Database-példányon megtalálható. Ha azt szeretné, a táblákat, amelyek már tartalmazzák az adatok áttelepítéséhez, szüksége explicit módon válassza ki a táblákat, ezen a képernyőn.

    ![Select tables (Táblák kiválasztása)](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4.  Válassza ki **mentése**, miután beállította a következő **speciális online migrálás beállítások**.
    
    | Beállítás | Leírás |
    | ------------- | ------------- |
    | **Táblák párhuzamos betöltése maximális száma** | Itt adható meg, amely az áttelepítés során a DMS végrehajtja a párhuzamos táblák száma. Az alapértelmezett érték 5, de beállítható optimális érték alapján minden POC áttelepítések adott áttelepítési igényeinek. |
    | **Ha a forrástábla csonkítja** | Itt adhatja meg, hogy DMS a céltábla csonkolja a migrálás során. Ez a beállítás akkor lehet hasznos, ha egy vagy több tábla csonkolva lesznek az áttelepítési folyamat részeként. |
    | **A nagy méretű objektum (LOB) típusú adatok beállításainak konfigurálása** | Megadja, hogy a DMS áttelepíti a korlátlan LOB-adatok, vagy a LOB-adatok korlátok át egy adott méretet.  Korlátozva van üzleti adatokat áttelepíteni, minden LOB-adatok gyűjthessen, hogy a rendszer csonkolja. Az éles környezetbeli migrálások, azt javasoljuk, hogy válasszon **LOB korlátlan méretű engedélyezése** adatvesztés megelőzése érdekében. Adjon meg, hogy a LOB korlátlan méretű, válassza ki a **áttelepítése LOB-adatok egyetlen blokkot LOB mérete kisebb, mint (KB) Ha a megadott** melletti jelölőnégyzetet, hogy a teljesítmény javítása. |
    
    ![Online migrálás speciális beállításainak megadása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5.  Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása
- Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Inicializálás**.

    ![Tevékenység állapota: Inicializálás](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>A migrálás monitorozása
1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Fut** értékre.

2. Kattintson egy adatbázisra az **Adatok teljes betöltése** és a **Növekményes adatszinkronizálás** műveletek migrálási állapotának megtekintéséhez.

    ![Tevékenység állapota: Folyamatban](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Átállásos áttelepítés végrehajtása
Az első teljes betöltés elkészültével az adatbázisok **Átállásra kész** állapotúak lesznek.

1. Ha készen áll az adatbázis migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

    ![Átállás indítása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)
 
2.  Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.
3.  Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázis migrálási állapota **Befejezve** értékre vált, csatlakoztassa alkalmazásait az új Azure SQL Database-céladatbázishoz.
 
    ![Tevékenység állapota: Befejezve](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>További lépések
- Ismert problémák és korlátozások online migrálást az Azure SQL DatabaseL végrehajtásakor kapcsolatos információkért tekintse meg a cikket [ismert problémák és megoldások az Azure SQL Database online áttelepítések](known-issues-azure-sql-online.md).
- Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Azure SQL Database kapcsolatos információkért tekintse meg a cikket [Mi az az Azure SQL Database szolgáltatás?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
- További információ az Azure SQL Database felügyelt példány oldalon talál [Azure SQL Database felügyelt példányába](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index).
