---
title: "Azure SSIS integrációs modul üzembe helyezése a portál használatával | Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan lehet az Azure Portal használatával létrehozni egy Azure-SSIS integrációs modult."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: spelluru
ms.openlocfilehash: b6a795f8a26340f24f9e09aea371ba90afe50101
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-data-factory-ui"></a>Azure SSIS integrációs modul üzembe helyezése a Data Factory felhasználói felületén
Ez az oktatóanyag azt ismerteti, hogyan lehet üzembe helyezni egy Azure-SSIS integrációs modul (Integration Runtime, IR) modult az Azure Data Factoryban az Azure Portal felületén. Ezután az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) használatával üzembe helyezhet SQL Server Integration Services- (SSIS-) csomagokat ebben az Azure-beli modulban. Elméleti információk az Azure-SSIS integrációs modulra vonatkozóan: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure-SSIS integrációs modul létrehozása és elindítása

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd [a Data Factory 1. verziójának dokumentációját](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot. 
- **Azure SQL Database-kiszolgáló**. Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. Az Azure Data Factory létrehoz egy SSIS katalógus-adatbázist (SSISDB) az adatbázis-kiszolgálón. Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi, hogy az integrációs modul Azure-régiók határainak átlépése nélkül írjon végrehajtási naplókat a katalógusadatbázisba. 
    - Győződjön meg arról, hogy az „**Azure-szolgáltatásokhoz való hozzáférés engedélyezése**” beállítás engedélyezve van az adatbázis-kiszolgálón. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). A beállítás engedélyezése PowerShell használatával: [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
    - Vegye fel az ügyfélszámítógép IP-címét vagy az ügyfélszámítógép IP-címét tartalmazó IP-címtartományt az ügyfél IP-címeinek listájára az adatbázis-kiszolgáló tűzfalbeállításainál. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../sql-database/sql-database-firewall-configure.md). 
 
## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).    
2. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Az **Új data factory** lapon, a **Név** mezőben adja meg a következőt: **MyAzureSsisDataFactory**. 
      
     ![Új adat-előállító lap](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a következő hibaüzenetet kapja, változtassa meg az adat-előállító nevét (például sajátneveMyAzureSsisDataFactoryra), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
      Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. Válassza a **V2 (előzetes verzió)** értéket a **verzió** esetén.
5. Válassza ki a Data Factory **helyét**. A listában csak az adat-előállítók létrehozását támogató helyek jelennek meg.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
   ![Data factory kezdőlap](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. A Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul üzembe helyezése

1. Az első lépések oldalán kattintson az **SSIS integrációs modul konfigurálása** csempére. 

   ![SSIS integrációs modul konfigurálása csempe](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Az **Integrációs modul beállításai** lap **Általános beállítások** oldalán végezze el az alábbi lépéseket: 

   ![Általános beállítások](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Adja meg az integrációs modul **nevét**.
    2. Válassza ki az integrációs modul **helyét**. Csak a támogatott helyek jelennek meg.
    3. Válassza ki az SSIS modulhoz konfigurálandó **csomópont méretét**.
    4. Adja meg a fürtben található **csomópontok számát**.
    5. Kattintson a **Tovább** gombra. 
1. Az **SQL-beállítások** menüben tegye az alábbiakat: 

    ![SQL-beállítások](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Adja meg az Azure SQL Servert tartalmazó Azure-**előfizetést**. 
    2. A **katalógus-adatbázis kiszolgáló-végpontjaként** válassza ki Azure SQL Server-kiszolgálóját.
    3. Adja meg a **rendszergazda** felhasználónevét.
    4. Adja meg a rendszergazdához tartozó **jelszót**.  
    5. Válassza ki az SSISDB-adatbázishoz tartozó **szolgáltatásszintet**. Az alapértelmezett érték az Alapszintű.
    6. Kattintson a **Tovább** gombra. 
1.  A **Speciális beállítások** oldalon válassza ki, hogy mennyi legyen a **maximális párhuzamos végrehajtások száma csomópontonként**.   

    ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Ez a lépés **nem kötelező**. Ha rendelkezik egy olyan klasszikus virtuális hálózattal (VNet), amelyhez szeretné csatlakoztatni az integrációs modult, válassza ki a **VNet kiválasztása az Azure SSIS integrációs modullal történő összekapcsoláshoz, és a VNet engedélyezéseinek/beállításainak Azure-szolgáltatások által történő konfigurálásának engedélyezése** lehetőséget, majd hajtsa végre a következő lépéseket: 

    ![Virtuális hálózatra vonatkozó speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. Adja meg a klasszikus virtuális hálózatot tartalmazó **előfizetést**. 
    2. Válassza ki a **VNet** hálózatot. <br/>
    4. Válassza ki az **alhálózatot**.<br/> 
1. Az Azure-SSIS integrációs modul létrehozásának elindításához kattintson a **Befejezés** gombra. 

    > [!IMPORTANT]
    > - A folyamat elvégzése hozzávetőleg 20 percet vesz igénybe.
    > - A Data Factory szolgáltatás csatlakozik az Azure SQL Database-hez, és előkészíti az SSIS-katalógusadatbázist (SSISDB). A szkript konfigurálja a virtuális hálózat engedélyeit és beállításait is, ha ez lett megadva, és csatlakoztatja az Azure SSIS integrációs modul új példányát a virtuális hálózathoz.
7. A **Kapcsolatok** ablakban váltson az **Integrációs modulok** lapra, ha szükséges. Az állapot frissítéséhez kattintson a **Frissítés** elemre. 

    ![Létrehozás állapota](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Az integrációs modul monitorozásához, leállításához/elindításához, szerkesztéséhez vagy törléséhez használja a **Műveletek** oszlop alatt található hivatkozásokat. Az integrációs modulhoz tartozó JSON-kód megtekintéséhez használja a legutolsó hivatkozást. A szerkesztés és törlés gombok csak akkor használhatók, ha az integrációs modul le van állítva. 

    ![Azure SSIS integrációs modul – műveletek](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        
9. Kattintson a **monitorozás** hivatkozására a **Műveletek** alatt.  

    ![Azure SSIS integrációs modul – részletek](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-details.png)
10. Ha bármilyen, az Azure SSIS integrációs modullal kapcsolatos **hibát** észlel, ezen az oldalon látni fogja a hibák számát és a hiba részleteinek megtekintését lehetővé tevő hivatkozást. Ha például az SSIS katalógus már létezik az adatbázis-kiszolgálón, látni fog egy hibaüzenetet, amely erre figyelmezteti.  
11. Kattintson a fent található **Integrációs modulok** elemre az előző oldalra való visszatéréshez, és az adat-előállítóhoz társított összes integrációs modul megtekintéséhez.  

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS integrációs modulok a portálon

1. Az Azure Data Factory felhasználói felületén váltson a **szerkesztési** lapra, kattintson a **Kapcsolatok** lehetőségre, majd váltson az **integrációs modulok** lapjára az adat-előállítójában megtalálható integrációs modulok megtekintéséhez. 
    ![Létező integrációs modulok megtekintése](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Egy új Azure-SSIS integrációs modul létrehozásához kattintson az **Új** elemre. 

    ![Integrációs modul elérése a menüből](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Egy Azure-SSIS integrációs modul létrehozásához kattintson az **Új** gombra a képen látható módon. 
3. Az Integrációs modul telepítése ablakban válassza a **Létező SSIS-csomagok áthelyezése az Azure-ban történő végrehajtáshoz** lehetőséget, majd kattintson a **Tovább** gombra.

    ![Integrációs modulok típusának megadása](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Az Azure-SSIS integrációs modul beállításához szükséges lépésekkel kapcsolatos információkért tekintse meg az [Azure SSIS integrációs modul üzembe helyezésével](#provision-an-azure-ssis-integration-runtime) foglalkozó részt. 

    
## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése
Most az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) segítségével helyezze üzembe az SSIS-csomagokat az Azure-ban. Csatlakozzon ahhoz az Azure SQL Serverhez, amelyen az SSIS-katalógus (SSISDB) található. Az Azure SQL Server nevének formátuma a következő: `<servername>.database.windows.net` (az Azure SQL Database esetében). 

Lásd az SSIS dokumentációjának alábbi cikkeit: 

- [SSIS-csomag üzembe helyezése, futtatása és monitorozása az Azure-on](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Csatlakozás SSIS-katalógushoz Azure-on](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Csomag végrehajtásának ütemezése az Azure-on](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure-SSIS integrációs modul létrehozása és elindítása

Folytassa a következő oktatóanyaggal, amelyben az adatok a helyszíni rendszerből a felhőbe másolásának menetét ismerheti meg: 

> [!div class="nextstepaction"]
>[Adatok másolása a felhőben](tutorial-copy-data-portal.md)
