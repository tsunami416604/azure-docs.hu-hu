---
title: 'Oktatóanyag: Az RDS SQL Server áttelepítése online az SQL-adatbázisba'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan hajthat végre online áttelepítést az RDS SQL Server kiszolgálóról az Azure SQL Database egyetlen adatbázisába vagy felügyelt példányba az Azure Database Migration Service használatával.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 8d538deca610fd9981d401d28b6bea1c31c6d4c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298872"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-database-managed-instance-online-using-dms"></a>Oktatóanyag: Az RDS SQL Server áttelepítése az Azure SQL Database-be vagy egy Azure SQL Database felügyelt példányába online a DMS használatával
Az Azure Database Migration Service segítségével áttelepítheti az adatbázisokat egy RDS SQL Server-példányból az [Azure SQL Database-be](https://docs.microsoft.com/azure/sql-database/) vagy egy [Azure SQL Database által felügyelt példányba](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) minimális állásidővel. Ebben az oktatóanyagban az **Adventureworks2012** adatbázist az SQL Server 2012 (vagy újabb) RDS SQL Server példányára visszaállított adatbázisát az Azure SQL Database vagy egy Azure SQL Database által felügyelt példányba telepíti át az Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Hozzon létre egy példányt az Azure SQL Database vagy egy Azure SQL Database felügyelt példány. 
> * A mintaséma migrálása a Data Migration Assistant szolgáltatás használatával.
> * Egy Azure Database Migration Service-példány létrehozása.
> * Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.
> * Migrálási jelentés letöltése.

> [!NOTE]
> Az Azure Database Migration Service online áttelepítés végrehajtásához létre kell adnia egy példányt a prémium díjszabási szint alapján. További információkért tekintse meg az Azure Database Migration Service [díjszabási](https://azure.microsoft.com/pricing/details/database-migration/) lap.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a migrálási folyamat optimalizálása érdekében ugyanabban az Azure-régióban hozza létre az Azure Database Migration Service-példányt, mint amelyikben a céladatbázis is található. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk az RDS SQL Server-ről az Azure SQL Database-re vagy egy Azure SQL Database által felügyelt példányra való online áttelepítést ismerteti.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [RdS SQL Server adatbázis](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html)létrehozása .
* Hozzon létre egy példányt az Azure SQL Database, amelyet a cikk részletesen az [Azure PORTALon Hozzon létre.](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)

    > [!NOTE]
    > Ha egy Azure SQL Database felügyelt példányba migrál, kövesse az [Azure SQL Database felügyelt példányának létrehozása](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)című cikk részleteit, majd hozzon létre egy **AdventureWorks2012**nevű üres adatbázist. 
 
* Töltse le a [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) 3.3-as vagy újabb verzióját.
* Hozzon létre egy Microsoft Azure virtuális hálózatot az Azure adatbázis-áttelepítési szolgáltatáshoz az Azure Resource Manager telepítési modelljének használatával. Ha egy Azure SQL Database felügyelt példányba migrál, győződjön meg arról, hogy a DMS-példányt ugyanabban a virtuális hálózatban hozza létre, amelyet az Azure SQL Database felügyelt példányához használnak, de egy másik alhálózatban.  Ha másik virtuális hálózatot használ a DMS-hez, létre kell hoznia egy virtuális hálózati társviszony-létesítést a két virtuális hálózat között. A virtuális hálózat létrehozásáról további információt a [Virtuális hálózati dokumentációban](https://docs.microsoft.com/azure/virtual-network/)és különösen a részletes en című rövid útmutatóban talál.

    > [!NOTE]
    > A virtuális hálózat beállítása során, ha az ExpressRoute szolgáltatást hálózati társviszony-létesítéssel használja a Microsofthoz, adja hozzá a következő [szolgáltatásvégpontokat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) ahhoz az alhálózathoz, amelyben a szolgáltatás ki lesz építve:
    >
    > * Cél adatbázis-végpont (például SQL-végpont, Cosmos DB-végpont és így tovább)
    > * Tárolási végpont
    > * Szolgáltatásbusz végpontja
    >
    > Erre a konfigurációra azért van szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal. 

* Győződjön meg arról, hogy a virtuális hálózati biztonsági csoport szabályai nem blokkolják a következő bejövő kommunikációs portokat az Azure Database Migration Service szolgáltatásba: 443, 53, 9354, 445, 12000. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részleteket a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal című témakörben olvashat.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse az SQL-kiszolgáló forrását, amely alapértelmezés szerint az 1433-as TCP-port.
* Hozzon létre egy kiszolgálószintű [tűzfalszabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure SQL-adatbáziskiszolgáló számára, hogy az Azure SQL Database Migration Service hozzáférhessen a céladatbázisokhoz. Adja meg az Azure database migration service-hez használt virtuális hálózat alhálózati tartományát.
* Győződjön meg arról, hogy a forrás RDS SQL Server-példányhoz való csatlakozáshoz használt hitelesítő adatok egy olyan fiókhoz vannak társítva, amely a "Processadmin" kiszolgálói szerepkör tagja, és az "db_owner" adatbázis-szerepkörök tagja az összes áttelepítendő adatbázisban.
* Győződjön meg arról, hogy a célhoz használt Azure SQL Database-példányhoz való csatlakozáshoz használt hitelesítő adatok CONTROL DATABASE engedéllyel rendelkeznek a cél Azure SQL-adatbázisokra és a rendszergazdai szerepkör egy tagjára, ha egy Azure SQL Database felügyelt példányba való áttelepítéskor.
* A forrás RDS SQL Server verziónak SQL Server 2012-es és újabb verziónak kell lennie. A futó SQL Server-példány verziójának megállapításához lásd [az SQL Server és összetevői verziójának, kiadásának és frissítési szintjének megállapításával](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an) foglalkozó cikket.
* Engedélyezze a Change Data Capture (CDC) módosítását az RDS SQL Server adatbázisában és az áttelepítésre kijelölt összes felhasználói táblában.
    > [!NOTE]
    > Az alábbi parancsfájl segítségével engedélyezheti a CDC-t egy RDS SQL Server adatbázisban.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Használhatja az alábbi szkriptet, hogy a CDC minden táblában.
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
* Tiltsa le az adatbázistriggereket az Azure SQL-céladatbázisban.
    > [!NOTE]
    > Az Azure SQL-céladatbázis triggereit az alábbi lekérdezéssel keresheti meg:
    ```
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    További információk: [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása
A DMA segítségével telepítse át a sémát az Azure SQL Database-be.

> [!NOTE]
> Mielőtt létrehozna egy migrálási projektet a DMA-ban, győződjön meg arról, hogy az előfeltételekben meghatározottak alapján már létrehozott egy Azure SQL-adatbázist. Az oktatóanyag alkalmazásában az Azure SQL Database neve **AdventureWorks2012,** de bármilyen nevet megadhat.

Az **AdventureWorks2012** séma Azure SQL Database-be való migrálásához végezze el a következő lépéseket:

1. A Data Migration Assistant programban válassza az Új (+) ikont, majd a **Projekt típusa** alatt válassza a **Migrálás** lehetőséget.
2. Nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza az **SQL Server** elemet, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database** lehetőséget.
3. A **Migrálási hatókör** alatt válassza a **Csak a séma** lehetőséget.

    A fent leírt lépések elvégzése után megjelenik a DMA felhasználói felülete, ahogy az a következő képen látható:

    ![Data Migration Assistant-projekt létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.
5. A DMA-ban adja meg az SQL Server forráskapcsolati adatait, válassza a **Csatlakozás** lehetőséget, majd válassza ki az **AdventureWorks2012** adatbázist.

    ![Data Migration Assistant forráskapcsolati adatok](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. Válassza a **Tovább**lehetőséget, a **Csatlakozás a célkiszolgálóhoz**csoportban adja meg az Azure SQL-adatbázis célkapcsolatának részleteit, válassza a **Csatlakozás**lehetőséget, majd válassza ki **az** Azure-adatbázist, amelyet előre kiépített az Azure SQL Database-ben.

    ![Data Migration Assistant célkapcsolati adatok](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. A **Tovább** lehetőséggel lépjen tovább az **Objektumok kiválasztása** képernyőre, ahol megadhatja azokat a sémaobjektumokat az **AdventureWorks2012** adatbázisban, amelyeket üzembe kell helyezni az Azure SQL Database-ben.

    Alapértelmezés szerint az összes objektum ki van jelölve.

    ![SQL-szkriptek létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Az **SQL-szkript létrehozása** lehetőséggel hozza létre az SQL-szkripteket, majd tekintse át azokat, hogy észrevehesse az esetleges hibákat.

    ![Sémaszkript](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. Válassza a **Séma üzembe helyezése** lehetőséget a séma üzembe helyezéséhez az Azure SQL Database-ben. Az üzembe helyezés után ellenőrizze a célt az esetleges hibákért.

    ![Séma üzembe helyezése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd jobbra a **Microsoft.DataMigration**programtól, és válassza a **Regisztráció**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, amelyen az Azure Database Migration Service példányát létre szeretné hozni. 

5. Jelöljön ki egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat hozzáférést biztosít az Azure Database Migration Service számára a forrás SQL Server és a cél Azure SQL Database-példány eléréséhez.

    A virtuális hálózat Azure Portalon való létrehozásáról további információt a Virtuális hálózat létrehozása az Azure Portal használatával című témakörben [talál.](https://aka.ms/DMSVnet)

6. Válasszon ki egy tarifacsomagot; az online áttelepítéshez mindenképpen válassza ki a prémium díjszabási szintet.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

     ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.

     ![Az Azure Database Migration Service személyes példányának megkeresése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az **Új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **Forráskiszolgáló típusa** mezőben válassza az **AWS RDS for SQL Server**lehetőséget, a **Célkiszolgáló típusa** szövegmezőben válassza az Azure SQL **Database**lehetőséget.

    > [!NOTE]
    > A célkiszolgáló típusa, válassza az **Azure SQL Database** áttelepítéséhez mind az Azure SQL Database singleton adatbázis, valamint egy Azure SQL Database felügyelt példány.

5. A **Tevékenység típusának kiválasztása** csoportban válassza az **Online adatáttelepítés**lehetőséget.

    > [!IMPORTANT]
    > Ügyeljen arra, hogy válassza **az online adatok áttelepítése**; offline áttelepítések nem támogatottak ebben a forgatókönyvben.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Másik lehetőségként **választhatja** a Projekt létrehozása csak az áttelepítési projekt most, és az áttelepítés későbbi végrehajtásához lehetőséget.

6. Kattintson a **Mentés** gombra.

7. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

    ![Database Migration Service-tevékenység létrehozása és futtatása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server-példány kapcsolati adatait.

    Gondoskodjon róla, hogy az SQL Server-példány neveként teljes tartománynevet (FQDN) használjon.

2. Ha nem telepített megbízható tanúsítványt a forráskiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítvánnyal titkosított TLS-kapcsolatok nem nyújtanak erős biztonságot. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Ne hagyatkozzon a TLS-re, amely önaláírt tanúsítványokat használ éles környezetben vagy az internethez kapcsolódó kiszolgálókon.

   ![Forrás részletei](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés** lehetőséget, majd a **Migrálási cél részletei** képernyőn adja meg a célul szolgáló Azure SQL-adatbáziskiszolgáló kapcsolati adatait. Ez a cél az az Azure SQL Database, amelyen üzembe helyezte az **AdventureWorks2012** sémát a DMA-val.

    ![Cél kiválasztása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis neve megegyezik a forrásadatbázis nevével, az Azure Database Migration Service alapértelmezés szerint a céladatbázist választja ki.

    ![Leképezés céladatbázisokra](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Válassza a **Mentés**, lehetőséget, majd a **Táblák kiválasztása** képernyőn bontsa ki a táblák listáját, és tekintse át az érintett mezőket.

    Az Azure Database Migration Service automatikusan kiválasztja az összes üres forrástáblát, amely a célként szolgáló Azure SQL Database-példányon megtalálható. Ha olyan táblákat szeretne újrakivándorolni, amelyek már tartalmaznak adatokat, explicit módon ki kell jelölnie a képernyőn megjelenő táblákat.

    ![Select tables (Táblák kiválasztása)](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. A következő speciális online áttelepítési beállítások megadása után válassza a **Mentés** **gombot.**

    | Beállítás | Leírás |
    | ------------- | ------------- |
    | **A párhuzamosan betölthető táblák maximális száma** | Megadja, hogy a DMS hány táblát hajt végre párhuzamosan az áttelepítés során. Az alapértelmezett érték 5, de beállítható egy optimális értékre, hogy megfeleljen a poc áttelepítések en alapuló speciális áttelepítési igényeknek. |
    | **A forrástábla csonkolása** | Itt adható meg, hogy a DMS csonkolja-e a céltáblát az áttelepítés során. Ez a beállítás akkor lehet hasznos, ha egy vagy több tábla csonkul az áttelepítési folyamat részeként. |
    | **Nagy méretű objektumok (LOB) adatainak konfigurálása** | Itt adható meg, hogy a DMS korlátlan LOB-adatokat telepít-e át, vagy egy adott méretre korlátozza-e az áttelepített LOB-adatokat.  Ha a lob-adatok áttelepítése korlátozva van, a rendszer csonkolja az ezen a korláton túli lob-adatokat. Éles áttelepítések esetén ajánlott a **Korlátlan LOB-méret engedélyezése** az adatvesztés megelőzése érdekében. Ha a korlátlan lob-méretet szeretné megadni, a teljesítmény javítása érdekében jelölje be az **Üzletág-adatok áttelepítése egyetlen blokkban jelölőnégyzetet, ha a lob-méret kisebb (KB).** |

    ![Speciális online áttelepítési beállítások megadása](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét, majd tekintse át az összegzést, és ellenőrizze, hogy a forrás és a cél adatai megegyeznek-e a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

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

2. Mindenképpen állítsa le a forrásadatbázis összes bejövő tranzakcióját, és várjon, amíg a **Függőben lévő módosítások** számlálója **0**-t nem mutat.
3. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.
4. Ha az adatbázis migrálási állapota **Befejezve** értékre vált, csatlakoztassa alkalmazásait az új Azure SQL-céladatbázishoz.

    ![Tevékenység állapota: Befejezve](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>További lépések

* Az Azure SQL DatabaseL-be való online áttelepítések során fellépő ismert problémákról és korlátozásokról az Ismert problémák és megoldások az [Azure SQL Database online áttelepítéseivel című témakörben](known-issues-azure-sql-online.md)olvashat.
* Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Az Azure SQL Database szolgáltatásról a [Mi az Azure SQL Database szolgáltatás?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)cikkben olvashat.
* Az Azure SQL Database felügyelt példányairól az [Azure SQL Database felügyelt példányai című](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)lapon talál további információt.
