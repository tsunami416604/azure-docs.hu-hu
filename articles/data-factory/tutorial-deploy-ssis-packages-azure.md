---
title: Azure SSIS integrációs modul üzembe helyezése | Microsoft Docs
description: Ismerje meg, hogyan helyezheti üzembe az Azure SSIS integrációs modult az Azure Data Factoryben, hogy SSIS-csomagokat telepíthessen és futtathasson az Azure-ban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bd2c055d2f7f1e90918cb160cfdebfe88f7f8ea4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484797"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Az Azure SSIS integrációs modul üzembe helyezése az Azure Data Factoryban

Ez az oktatóanyag ismerteti, hogyan az Azure Portalon egy Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) az Azure Data Factory (ADF) kiépítéséhez. Az Azure-SSIS integrációs modul által támogatott csomagok üzembe helyezve az Azure SQL Database-kiszolgáló/Managed Instance (projekt üzembe helyezési modell) által üzemeltetett SSIS-katalógus (SSISDB) és mások által üzembe helyezett fájl rendszerek/fájl futtatása megosztások vagy az Azure Files (csomag üzembe helyezési modell). Miután az Azure-SSIS integrációs modul üzembe felhasználhatja ismert eszközökkel, például az SQL Server Data Tools (SSDT) / SQL Server Management Studio (SSMS), és a parancs parancssori segédprogramok, például `dtinstall` / `dtutil` / `dtexec`, az üzembe helyezése és az-csomagok futtatása az Azure-ban. Elméleti információk az Azure-SSIS integrációs modulokról: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
- **Azure SQL Database-kiszolgáló (nem kötelező)** . Ha Ön még nem rendelkezik adatbázis-kiszolgáló, hozzon létre egyet az Azure Portalon, a Kezdés előtt. Az ADF pedig SSISDB hoz létre az adatbázis-kiszolgálón. Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi, hogy az integrációs modul az SSISDB, az Azure-régiók határainak átlépése nélkül írjon végrehajtási naplókat. 
    - A kiválasztott adatbázis-kiszolgáló alapján az SSISDB létrehozható az Ön nevében önálló adatbázisként, egy rugalmas készlet részeként vagy egy felügyelt példányban, és elérhető nyilvános hálózatban vagy egy virtuális hálózathoz csatlakozva. Gazdagépre SSISDB adatbázis-kiszolgáló típusú megválasztásához útmutatásért lásd: [hasonlítsa össze az Azure SQL Database egy adatbázis és rugalmas készlet/Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Ha használatával Azure SQL Database-kiszolgáló virtuális hálózati szolgáltatás végpontok/Managed Instance egy virtuális hálózaton az SSISDB üzemeltetésére, illetve hozzáférést igényelnek a helyszíni adatokhoz, szeretné-e az Azure-SSIS integrációs modul csatlakoztatása egy virtuális hálózatot, lásd: [létrehozása az Azure-SSIS integrációs modul a virtuális hálózatban](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás engedélyezve van az adatbázis-kiszolgálón. Ez a nem alkalmazható virtuális hálózati szolgáltatás végpontok/felügyelt példányt az SSISDB-gazdagépre a virtuális hálózat az Azure SQL Database-kiszolgáló használata esetén. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Ezzel a beállítással engedélyezheti a PowerShell-lel [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Adja hozzá az ügyfélszámítógép IP-címét, vagy egy IP-címtartományt, amely tartalmazza az ügyfélszámítógépen, hogy az ügyfél IP-címeinek listájára az adatbázis-kiszolgáló tűzfalbeállításainál IP-címét. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../sql-database/sql-database-firewall-configure.md).
    - Az adatbázis-kiszolgáló használatával a kiszolgáló-rendszergazdai hitelesítő adataival SQL-hitelesítés vagy az Azure Active Directory (AAD) hitelesítés a felügyelt identitás az ADF csatlakozhat. Az utóbbi esetében hozzá kell adnia az ADF felügyelt identitását egy AAD-csoporthoz, amely rendelkezik engedélyekkel az adatbázis-kiszolgálóhoz, lásd: [Azure SSIS integrációs modul létrehozása AAD-hitelesítéssel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Győződjön meg arról, hogy az adatbázis-kiszolgáló nem rendelkezik az SSISDB már. Egy Azure-SSIS IR üzembe helyezése nem támogatja egy meglévő SSISDB használatával.

> [!NOTE]
> - Az ADF és az Azure-SSIS integrációs modul jelenleg érhetők el Azure-régiók listáját lásd: [ADF + SSIS integrációs modul rendelkezésre állása régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott. 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). 
1. Kattintson az **Új** elemre a bal oldali menüben, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Az **Új data factory** lapon, a **Név** mezőben adja meg a **MyAzureSsisDataFactory** értéket. 

   ![„Új data factory” lap](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Az Azure data factory nevének *globálisan egyedinek* kell lennie. Ha a következő hibaüzenetet kapja, módosítsa az adat-előállító nevét (például **&lt;sajátneve&gt;MyAzureSsisDataFactory** értékre), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja. 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. **Előfizetés:** válassza ki azt az Azure-előfizetést, amelyben az adat-előállítót létre szeretné hozni. 
1. Az **Erőforráscsoportban** hajtsa végre a következő lépések egyikét: 

   - Kattintson a **Meglévő használata** elemre, majd válasszon egy meglévő erőforráscsoportot a listából. 
   - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét. 

   Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket. 
1. **Verzió:** válassza a **V2 (előzetes verzió)** értéket. 
1. **Hely:** válassza ki az adat-előállító helyét. A listában csak az adat-előállítók létrehozását támogató helyek jelennek meg. 
1. Válassza a **Rögzítés az irányítópulton** lehetőséget. 
1. Kattintson a **Létrehozás** gombra. 
1. Az irányítópulton megjelenő csempén az **Adat-előállító üzembe helyezése** állapotleírás látható: 

   ![„Adat-előállító üzembe helyezése” csempe](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. A létrehozás befejezése után megjelenik az **Adat-előállító** lap. 

   ![Az adat-előállító kezdőlapja](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. A Data Factory felhasználói felületének új lapon való megnyitásához válassza az **Author & Monitor** (Létrehozás és monitorozás) csempét. 

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása

### <a name="from-the-data-factory-overview"></a>Az a Data Factory – áttekintés

1. Az **Első lépések** lapon válassza ki az **SSIS integrációs modul konfigurálása** csempét. 

   ![SSIS integrációs modul konfigurálása csempe](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Az Azure-SSIS integrációs modul beállításához szükséges további lépésekkel kapcsolatos információ: [Azure SSIS integrációs modul üzembe helyezése](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>A szerzői műveletekhez részben felhasználói felületről

1. Az Azure Data Factory felhasználói felületén váltson a **Szerkesztés** lapra, válassza a **Kapcsolatok** lehetőséget, majd váltson az **Integrációs modulok** lapra az adat-előállítójában megtalálható integrációs modulok megtekintéséhez. 

   ![Meglévő integrációs modulok megtekintésére szolgáló kiválasztások](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Válassza az **Új** lehetőséget egy Azure-SSIS integrációs modul létrehozásához. 

   ![Integrációs modul elérése a menüből](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Az **Integrációs modul telepítése** ablakban válassza a **Meglévő SSIS-csomagok áthelyezése az Azure-ban történő végrehajtáshoz** lehetőséget, majd kattintson a **Tovább** gombra. 

   ![Integrációs modulok típusának megadása](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Az Azure-SSIS integrációs modul beállításához szükséges további lépésekkel kapcsolatos információ: [Azure SSIS integrációs modul üzembe helyezése](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul üzembe helyezése

1. Az **Integrációs modul beállításai** lap **Általános beállítások** oldalán végezze el az alábbi lépéseket: 

   ![Általános beállítások](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. A **Név** mezőben adja meg az integrációs modul nevét. 

   b. A **Leírás** mezőben adja meg az integrációs modul leírását. 

   c. A **Hely** mezőben válassza ki az integrációs modul helyét. Csak a támogatott helyek jelennek meg. Javasoljuk, hogy az SSISDB-t üzemeltető adatbázis-kiszolgálóval megegyező helyet válasszon. 

   d. A **Csomópontméret** mezőben válassza ki az integrációsmodul-fürtön lévő csomópont méretét. Csak a támogatott csomópontméretek jelennek meg. Ha sok nagy számítási teljesítményt/sok memóriát igénylő csomagot szeretne futtatni, válasszon nagy csomópontméretet (vertikális felskálázás). 

   e. A **Csomópontszám** mezőben adja meg az integrációsmodul-fürtben található csomópontok számát. Csak a támogatott csomópontszámok jelennek meg. Ha sok csomagot szeretne párhuzamosan futtatni, válasszon egy nagy fürtöt sok csomóponttal (horizontális felskálázás). 

   f. A **Edition-licencre**, válassza ki az SQL Server/licenccel az integrációs modullal: Standard vagy Enterprise. Ha szeretne fejlett/prémium szolgáltatásokat használni az integrációs modulon, válassza az Enterprise verziót. 

   g. A **mentése pénzt**, válassza ki az integrációs modul az Azure Hybrid Benefit (AHB) lehetőséget: igen vagy nem. Akkor válassza az Igen lehetőséget, ha a saját, szoftverbiztosítással ellátott SQL Server-licencét szeretné használni, hogy élhessen a hibrid használatból fakadó költségcsökkentéssel. 

   h. Kattintson a **Tovább** gombra. 

1. Az **SQL-beállítások** oldalon végezze el a következő lépéseket: 

   ![SQL-beállítások](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. A **létrehozása SSIS-katalógus...**  jelölőnégyzetet, válassza ki az üzembe helyezési modell futtathatók az Azure-SSIS integrációs csomagok: Projekt üzembe helyezési modell, a csomagok telepítve vannak az SSISDB adatbázis-kiszolgáló által üzemeltetett vagy a csomag üzembe helyezési modell, a csomagok telepítve vannak a fájl rendszerek/fájl megosztások/Azure-bA fájlok. Ha bejelöli, szüksége lesz ahhoz, hogy a saját adatbázis-kiszolgáló gazdagépre SSISDB, hogy a rendszer létrehoz és kezelheti az Ön nevében.
   
   b. Az **Előfizetés** mezőben válassza azt az Azure-előfizetést, amelyikkel az adatbázis-kiszolgáló üzemelteti az SSISDB-t. 

   c. A **Hely** mezőben válassza ki az SSISDB-t üzemeltető adatbázis-kiszolgáló helyét. Javasoljuk, hogy az integrációs modullal megegyező helyet válasszon. 

   d. A **Katalógus adatbázis-kiszolgáló végpontja** mezőben válassza az SSISDB-t üzemeltető adatbázis-kiszolgáló végpontját. A kiválasztott adatbázis-kiszolgáló alapján az SSISDB létrehozható az Ön nevében önálló adatbázisként, egy rugalmas készlet részeként vagy egy felügyelt példányban, és elérhető nyilvános hálózatban vagy egy virtuális hálózathoz csatlakozva. Gazdagépre SSISDB adatbázis-kiszolgáló típusú megválasztásához útmutatásért lásd: [hasonlítsa össze az Azure SQL Database egy adatbázis és rugalmas készlet/Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Ha egy virtuális hálózatot az SSISDB üzemeltetésére, illetve hozzáférést igényelnek a helyszíni adatok Azure SQL Database-kiszolgáló virtuális hálózati szolgáltatás végpontok/Managed Instance-választ ki, szeretné-e az Azure-SSIS integrációs modul csatlakoztatása egy virtuális hálózatot, lásd: [létrehozása az Azure-SSIS Integrációs modul virtuális hálózatban lévő](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. A **használata AAD-hitelesítés...**  jelölőnégyzetet, válassza ki az SSISDB-gazdagépre adatbázis-kiszolgálóhoz tartozó hitelesítési módszer: SQL-hitelesítés vagy az ADF AAD-hitelesítését a felügyelt identitáshoz. Ha bejelöli, adja hozzá a felügyelt identitás számára az ADF egy hozzáférési engedélyeket az adatbázis-kiszolgálóhoz az AAD csoportjához, olvassa el kell [Azure-SSIS integrációs modul létrehozása AAD-hitelesítéssel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   f. A **Rendszergazdai felhasználónév** értékeként adja meg az SSISDB-t üzemeltető adatbázis-kiszolgálóhoz tartozó SQL-hitelesítési felhasználónevet. 

   g. A **Rendszergazdai jelszó** értékeként adja meg az SSISDB-t üzemeltető adatbázis-kiszolgálóhoz tartozó SQL-hitelesítési jelszót. 

   h. A **katalógus adatbázisokra vonatkozó szolgáltatási szint**, válassza ki a szolgáltatási rétegben, a gazdagép SSISDB adatbázis-kiszolgáló: Alapszintű/Standard/prémium szintű vagy a rugalmas készlet neve. 

   i. Kattintson a **Kapcsolat tesztelése** lehetőségre, és ha sikerrel járt, kattintson a **Tovább** gombra. 

1. A **Speciális beállítások** oldalon végezze el a következő lépéseket: 

   ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. A **Párhuzamos végrehajtások maximális száma csomópontonként** mezőben választhatja ki az integrációsmodul-fürt egy csomópontján egyszerre végrehajtható csomagok maximális számát. Csak a támogatott csomagszámok jelennek meg. Ha egynél több magot szeretne használni egy teljesítmény-/memóriaigényes, nagy csomag méretű futtatásához, válasszon alacsony számot. A magas értéket akkor válassza, ha egy magon szeretné kicsi/nem nagy igényű csomagot vagy csomagokat futtatni. 

   b. Az **Egyéni beállítástároló SAS URI-ja** mezőben opcionálisan megadható az ahhoz az Azure Storage Blob tárolóhoz tartozó közös hozzáférésű jogosultságkód (SAS) egyedi erőforrás-azonosító (URI), ahol a beállítás szkriptjét és az ahhoz társuló fájlokat tárolja, lásd: [Az Azure SSIS IR egyéni beállítása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. A **VNet kiválasztása...** jelölőnégyzetben döntheti el, hogy szeretné-e virtuális hálózathoz csatlakoztatni az integrációs modult. Nézze meg, ha az Azure SQL Database-kiszolgáló használata a virtuális hálózati szolgáltatás végpontok/felügyelt példányt az SSISDB-gazdagépre a virtuális hálózat vagy hozzáférést igényelnek a helyszíni adatokhoz, tekintse meg [Azure-SSIS integrációs hozzon létre egy virtuális hálózatban](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

1. Az integrációs modul létrehozásának elindításához kattintson a **Befejezés** gombra. 

   > [!NOTE]
   > Minden olyan egyéni beállítási idő, kivéve a folyamat 5 percen belül kell elvégezni.
   >
   > SSISDB használatakor ADF szolgáltatás az készíti elő az SSISDB adatbázis-kiszolgálóhoz fog csatlakozni. 
   > 
   > Amikor üzembe helyez egy Azure-SSIS integrációs Modult, Access Redistributable és az Azure Feature Pack for SSIS is telepítve lesz. Ezek az összetevők biztosítják a csatlakozást az Excel és Access-fájlok és a különböző Azure-adatforrásokhoz a beépített összetevők által már támogatott adatforrások mellett. További összetevőket is telepíthet lásd [Azure-SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md).

1. A **Kapcsolatok** lapon váltson az **Integrációs modulok** lapra, ha szükséges. Az állapot frissítéséhez kattintson a **Frissítés** gombra. 

   ![Létrehozási állapot a Frissítés gombbal](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Az integrációs modul leállításához/elindításához, szerkesztéséhez vagy törléséhez használja a **Műveletek** oszlopban található hivatkozásokat. Az integrációs modulhoz tartozó JSON-kód megtekintéséhez használja a legutolsó hivatkozást. A szerkesztés és törlés gombok csak akkor használhatók, ha az integrációs modul le van állítva. 

   ![A Műveletek oszlopban szereplő hivatkozások](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése

SSISDB használatakor az-csomagok üzembe, és futtathatja az Azure-SSIS integrációs modul, amely az adatbázis-kiszolgálóhoz, a kiszolgálói végpont-n keresztül csatlakozni az SSDT/SSMS eszközök használatával. Az Azure SQL Database-kiszolgáló/Managed instance egy nyilvános végponttal rendelkező, a kiszolgálói végpont formátuma `<server name>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`, illetve. SSISDB nem használja, ha telepíteni a fájl rendszerek/fájl megosztások/Azure-bA fájlok, és futtassa őket az Azure-SSIS integrációs modul használatával `dtinstall` / `dtutil` / `dtexec` parancssori segédeszközök. További információkért lásd: [üzembe SSIS-csomagok](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Mindkét esetben is futtathatja a telepített csomagokat az Azure-SSIS integrációs Modult az SSIS-csomag végrehajtása tevékenység ADF folyamatokban, lásd: [meghívása SSIS-csomag végrehajtása egy első osztályú ADF tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

Lásd még az SSIS dokumentációjának alábbi cikkeit: 

- [Üzembe helyezése, futtatása és figyelése az Azure-beli SSIS-csomagok](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Csatlakozás az Azure-ban SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Az Azure-ban csomag végrehajtás ütemezése](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul üzembe helyezése.
> * SSIS-csomagok üzembe helyezése

Ha többet szeretne megtudni az Azure-SSIS integrációs modul testreszabásáról, folytassa a következő cikkel: 

> [!div class="nextstepaction"]
> [Az Azure-SSIS integrációs modul testreszabása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)