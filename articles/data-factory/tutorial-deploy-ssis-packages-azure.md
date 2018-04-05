---
title: SSIS-csomagok üzembe helyezése az Azure-ban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet az Azure Data Factoryvel SSIS-csomagokat üzembe helyezni az Azure-ban, és létrehozni egy Azure-SSIS integrációs modult.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: hero-article
ms.date: 01/29/2018
ms.author: douglasl
ms.openlocfilehash: aca9f822bf3fd3b26e554240a4fee2474b89143d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="deploy-sql-server-integration-services-packages-to-azure"></a>SQL Server Integration Services-csomagok üzembe helyezése az Azure-ban
Ez az oktatóanyag azt ismerteti, hogyan lehet üzembe helyezni egy Azure-SSIS integrációs modul (Integration Runtime, IR) modult az Azure Data Factoryban az Azure Portal felületén. Ezután az SQL Server Data Tools vagy az SQL Server Management Studio használatával üzembe helyezhet SQL Server Integration Services- (SSIS-) csomagokat ebben az Azure-beli modulban. Elméleti információk az Azure-SSIS integrációs modulokról: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul üzembe helyezése.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, tekintse meg [a Data Factory 1. verziójának dokumentációját](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 
- **Azure SQL Database-kiszolgáló**. Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. Az Azure Data Factory létrehoz egy SSIS-katalógust (SSIDB-adatbázist) az adatbázis-kiszolgálón. Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi, hogy az integrációs modul az Azure-régiók határainak átlépése nélkül írjon végrehajtási naplókat az SSISDB-adatbázisba. 
- Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás engedélyezve van az adatbázis-kiszolgálón. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). A beállítás engedélyezése PowerShell használatával: [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
- Vegye fel az ügyfélszámítógép IP-címét vagy az ügyfélszámítógép IP-címét tartalmazó IP-címtartományt az ügyfél IP-címeinek listájára az adatbázis-kiszolgáló tűzfalbeállításainál. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../sql-database/sql-database-firewall-configure.md).
- Győződjön meg arról, hogy az Azure SQL Database-kiszolgálóján nincs SSIS-katalógus (SSISDB-adatbázis). Az Azure-SSIS IR üzembe helyezése nem támogatja a meglévő SSIS-katalógusok használatát.

> [!NOTE]
> - 2-es verziójú adat-előállítót a következő régiókban hozhat létre: USA keleti régiója, USA 2. keleti régiója, Délkelet-Ázsia és Nyugat-Európa. 
> - Azure-SSIS integrációs modult a következő régiókban hozhat létre: USA keleti régiója, USA 2. keleti régiója, USA középső régiója, Észak-Európa, Nyugat-Európa és Kelet-Ausztrália. 

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).    
3. Kattintson az **Új** elemre a bal oldali menüben, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Az **Új data factory** lapon, a **Név** mezőben adja meg a **MyAzureSsisDataFactory** értéket. 
      
   ![„Új data factory” lap](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének *globálisan egyedinek* kell lennie. Ha a következő hibaüzenetet kapja, módosítsa az adat-előállító nevét (például **&lt;sajátneve&gt;MyAzureSsisDataFactory** értékre), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
   `Data factory name “MyAzureSsisDataFactory” is not available`
4. **Előfizetés:** válassza ki azt az Azure-előfizetést, amelyben az adat-előállítót létre szeretné hozni. 
5. Az **Erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
   - Kattintson a **Meglévő használata** elemre, majd válasszon egy meglévő erőforráscsoportot a listából. 
   - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
   Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
6. **Verzió:** válassza a **V2 (előzetes verzió)** értéket.
7. **Hely:** válassza ki az adat-előállító helyét. A listában csak az adat-előállítók létrehozását támogató helyek jelennek meg.
8. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
9. Kattintson a **Létrehozás** gombra.
10. Az irányítópulton megjelenő csempén az **Adat-előállító üzembe helyezése** állapotleírás látható: 

   ![„Adat-előállító üzembe helyezése” csempe](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
11. A létrehozás befejezése után megjelenik az **Adat-előállító** lap.
   
   ![Az adat-előállító kezdőlapja](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
12. A Data Factory felhasználói felületének új lapon való megnyitásához válassza az **Author & Monitor** (Létrehozás és monitorozás) csempét. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul üzembe helyezése

1. Az **Első lépések** lapon válassza ki az **SSIS integrációs modul konfigurálása** csempét. 

   ![SSIS integrációs modul konfigurálása csempe](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Az **Integrációs modul beállításai** lap **Általános beállítások** oldalán végezze el az alábbi lépéseket: 

   ![Általános beállítások](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. A **Név** mezőben adja meg az integrációs modul nevét.

   b. A **Hely** mezőben válassza ki az integrációs modul helyét. Csak a támogatott helyek jelennek meg.

   c. A **Csomópontméret** mezőben válassza ki az SSIS-modulhoz konfigurálni kívánt csomópont méretét.

   d. A **Csomópontszám** mezőben adja meg a fürtben található csomópontok számát.
   
   e. Kattintson a **Tovább** gombra. 
3. Az **SQL-beállítások** oldalon végezze el a következő lépéseket: 

   ![SQL-beállítások](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Az **Előfizetés** mezőben adja meg az Azure adatbázis-kiszolgálót tartalmazó Azure-előfizetést.

   b. A **Katalógus adatbázis-kiszolgáló végpontja** értékeként válassza ki az Azure-adatbáziskiszolgálót.

   c. A **Rendszergazdai felhasználónév** értékeként adja meg a rendszergazda felhasználónevét.

   d. A **Rendszergazdai jelszó** mezőbe írja be a rendszergazda jelszavát.

   e. A **Katalógus-adatbáziskiszolgáló szintje** értékeként válassza ki az SSISDB-adatbázis szolgáltatásszintjét. Az alapértelmezett érték az **Alapszintű**.

   f. Kattintson a **Tovább** gombra. 
4. A **Speciális beállítások** oldalon válassza ki a **Maximális párhuzamos végrehajtások száma csomópontonként** beállítás értékét.   

   ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Ez a lépés *nem kötelező*. Ha rendelkezik egy olyan (a klasszikus üzemi modellen vagy az Azure Resource Manageren keresztül létrehozott) klasszikus virtuális hálózattal, amelyhez csatlakoztatni kívánja az integrációs modult, jelölje be a **VNet kiválasztása az Azure SSIS integrációs modullal történő összekapcsoláshoz, és a VNet engedélyezéseinek/beállításainak Azure-szolgáltatások által történő konfigurálásának engedélyezése** jelölőnégyzetet. Ezután hajtsa végre a következő lépéseket: 

   ![Virtuális hálózat speciális beállításai](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

   a. Az **Előfizetés** területen adja meg a virtuális hálózatot tartalmazó előfizetést.

   b. A **Virtuális hálózat neve** mezőben válassza ki a virtuális hálózat nevét.

   c. Az **Alhálózat neve** mezőben válassza ki a virtuális hálózatban lévő alhálózat nevét. 
6. Az Azure-SSIS integrációs modul létrehozásának elindításához válassza a **Befejezés** lehetőséget. 

   > [!IMPORTANT]
   > A folyamat elvégzése hozzávetőleg 20 percet vesz igénybe.
   >
   > A Data Factory szolgáltatás csatlakozik az Azure SQL Database-adatbázishoz, és előkészíti az SSIS-katalógust (SSISDB-adatbázist). A szkript konfigurálja a virtuális hálózat engedélyeit és beállításait is, ha ez lett megadva. Ezenkívül csatlakoztatja az Azure-SSIS integrációs modul új példányát a virtuális hálózathoz.
   > 
   > Amikor üzembe helyez egy Azure-SSIS integrációs modul egy példányát, az Azure Feature Pack for SSIS és az Access Redistributable is telepítve lesz. Ezek az összetevők biztosítják a csatlakozást az Excel- és Access-fájlokhoz és különböző Azure-adatforrásokhoz a beépített összetevők által támogatott adatforrások mellett. Az SSIS-hez jelenleg nem lehet külső gyártóktól származó összetevőket telepíteni. (Ebbe a korlátozásba tartoznak a nem a Microsofttól származó összetevők, például az Attunity Oracle és Teradata összetevői és az SAP BI-összetevők).

7. A **Kapcsolatok** lapon váltson az **Integrációs modulok** lapra, ha szükséges. Az állapot frissítéséhez kattintson a **Frissítés** gombra. 

   ![Létrehozási állapot a Frissítés gombbal](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Az integrációs modul leállításához/elindításához, szerkesztéséhez vagy törléséhez használja a **Műveletek** oszlopban található hivatkozásokat. Az integrációs modulhoz tartozó JSON-kód megtekintéséhez használja a legutolsó hivatkozást. A szerkesztés és törlés gombok csak akkor használhatók, ha az integrációs modul le van állítva. 

   ![A Műveletek oszlopban szereplő hivatkozások](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása

1. Az Azure Data Factory felhasználói felületén váltson a **Szerkesztés** lapra, válassza a **Kapcsolatok** lehetőséget, majd váltson az **Integrációs modulok** lapra az adat-előállítójában megtalálható integrációs modulok megtekintéséhez. 
   ![Meglévő integrációs modulok megtekintésére szolgáló kiválasztások](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
2. Válassza az **Új** lehetőséget egy Azure-SSIS integrációs modul létrehozásához. 

   ![Integrációs modul elérése a menüből](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
3. Az **Integrációs modul telepítése** ablakban válassza a **Meglévő SSIS-csomagok áthelyezése az Azure-ban történő végrehajtáshoz** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Integrációs modulok típusának megadása](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Az Azure-SSIS integrációs modul beállításához szükséges további lépésekkel kapcsolatos információ: [Azure SSIS integrációs modul üzembe helyezése](#provision-an-azure-ssis-integration-runtime). 

 
## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése
Most az SQL Server Data Tools vagy az SQL Server Management Studio segítségével helyezze üzembe az SSIS-csomagokat az Azure-ban. Csatlakozzon ahhoz az Azure-adatbázishoz, amelyen az SSIS-katalógus (SSISDB-adatbázis) található. Az Azure-adatbázis nevének formátuma a következő: `<servername>.database.windows.net` (az Azure SQL Database esetében). 

Lásd az SSIS dokumentációjának alábbi cikkeit: 

- [SSIS-csomag üzembe helyezése, futtatása és monitorozása az Azure-on](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Csatlakozás SSIS-katalógushoz Azure-on](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Csomag végrehajtásának ütemezése az Azure-on](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul üzembe helyezése.

Az adatok helyszíni rendszerből a felhőre való másolásának megismeréséhez folytassa a következő oktatóanyaggal: 

> [!div class="nextstepaction"]
> [Adatok másolása a felhőbe](tutorial-copy-data-portal.md)
