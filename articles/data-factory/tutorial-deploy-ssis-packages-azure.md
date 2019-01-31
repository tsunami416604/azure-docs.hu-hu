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
ms.date: 10/28/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: b3c9c2b5040dfc00aa1e8c5eeea57693f86b62d1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453161"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Az Azure SSIS integrációs modul üzembe helyezése az Azure Data Factoryban
Ez az oktatóanyag azt ismerteti, hogyan lehet üzembe helyezni egy Azure-SSIS integrációs modul (Integration Runtime, IR) modult az Azure Data Factoryban az Azure Portal felületén. Ezután az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) használatával SQL Server Integration Services- (SSIS-) csomagokat helyezhet üzembe és futtathat ebben az Azure-beli modulban. Elméleti információk az Azure-SSIS integrációs modulokról: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 
- **Azure SQL Database-kiszolgáló**. Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. Az Azure Data Factory létrehoz egy SSIS-katalógust (SSIDB-adatbázist) az adatbázis-kiszolgálón. Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi, hogy az integrációs modul az Azure-régiók határainak átlépése nélkül írjon végrehajtási naplókat az SSISDB-adatbázisba. 
- A kiválasztott adatbázis-kiszolgáló alapján az SSISDB létrehozható az Ön nevében önálló adatbázisként, egy rugalmas készlet részeként vagy egy felügyelt példányban, és elérhető nyilvános hálózatban vagy egy virtuális hálózathoz csatlakozva. Ha az Azure SQL Database-t virtuális hálózati szolgáltatásvégpontokkal/ felügyelt példánnyal használja az SSISDB üzemeltetésére, vagy a helyszíni adatokhoz való hozzáférésre van szüksége, az Azure SSIS integrációs modulját csatlakoztatnia kell egy virtuális hálózathoz; lásd: [Azure SSIS integrációs modul létrehozása virtuális hálózatban](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
- Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás engedélyezve van az adatbázis-kiszolgálón. Ez nem érvényes akkor, amikor az Azure SQL Database-t virtuális hálózati szolgáltatásvégpontokkal/felügyelt példánnyal használja az SSISDB üzemeltetésére. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). A beállítás engedélyezése PowerShell használatával: [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1). 
- Vegye fel az ügyfélszámítógép IP-címét vagy az ügyfélszámítógép IP-címét tartalmazó IP-címtartományt az ügyfél IP-címeinek listájára az adatbázis-kiszolgáló tűzfalbeállításainál. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../sql-database/sql-database-firewall-configure.md). 
- Az adatbázis-kiszolgálóhoz SQL-hitelesítéssel, a kiszolgálói rendszergazdai hitelesítő adataival vagy Azure Active Directory- (AAD-) hitelesítéssel, az Azure Data Factory (ADF) felügyelt identitásával csatlakozhat.  Az utóbbi esetében hozzá kell adnia az ADF felügyelt identitását egy AAD-csoporthoz, amely rendelkezik engedélyekkel az adatbázis-kiszolgálóhoz, lásd: [Azure SSIS integrációs modul létrehozása AAD-hitelesítéssel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
- Győződjön meg arról, hogy az Azure SQL Database-kiszolgálóján nincs SSIS-katalógus (SSISDB-adatbázis). Az Azure-SSIS IR üzembe helyezése nem támogatja a meglévő SSIS-katalógusok használatát. 

> [!NOTE]
> - Ha kíváncsi azon Azure-régiók listájára, amelyekben a Data Factory és az Azure-SSIS integrációs modul jelenleg elérhető, tekintse meg az [ADF és az SSIS integrációs modul régió szerinti elérhetőségét](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

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

   a. Az **Előfizetés** mezőben válassza azt az Azure-előfizetést, amelyikkel az adatbázis-kiszolgáló üzemelteti az SSISDB-t. 

   b. A **Hely** mezőben válassza ki az SSISDB-t üzemeltető adatbázis-kiszolgáló helyét. Javasoljuk, hogy az integrációs modullal megegyező helyet válasszon. 

   c. A **Katalógus adatbázis-kiszolgáló végpontja** mezőben válassza az SSISDB-t üzemeltető adatbázis-kiszolgáló végpontját. A kiválasztott adatbázis-kiszolgáló alapján az SSISDB létrehozható az Ön nevében önálló adatbázisként, egy rugalmas készlet részeként vagy egy felügyelt példányban, és elérhető nyilvános hálózatban vagy egy virtuális hálózathoz csatlakozva. Gazdagépre SSISDB adatbázis-kiszolgáló típusú megválasztásához útmutatásért lásd: [hasonlítsa össze az Azure SQL Database önálló adatbázisok és rugalmas készletek és a felügyelt példány](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Ha az Azure SQL Database-t virtuális hálózati szolgáltatásvégpontokkal/felügyelt példánnyal választja az SSISDB üzemeltetésére, vagy a helyszíni adatokhoz való hozzáférésre van szüksége, az Azure SSIS integrációs modulját csatlakoztatnia kell egy virtuális hálózathoz. További információ: [Azure SSIS integrációs modul létrehozása virtuális hálózatban](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   d. A **használata AAD-hitelesítés...**  jelölőnégyzetet, válassza ki az SSISDB-gazdagépre adatbázis-kiszolgálóhoz tartozó hitelesítési módszer: SQL- vagy Azure Active Directory (AAD) az Azure Data Factory (ADF) felügyelt identitással. Ha bejelöli, hozzá kell adnia az ADF felügyelt identitását egy AAD-csoporthoz, amely rendelkezik engedélyekkel az adatbázis-kiszolgálóhoz, lásd: [Azure SSIS integrációs modul létrehozása AAD-hitelesítéssel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. A **Rendszergazdai felhasználónév** értékeként adja meg az SSISDB-t üzemeltető adatbázis-kiszolgálóhoz tartozó SQL-hitelesítési felhasználónevet. 

   f. A **Rendszergazdai jelszó** értékeként adja meg az SSISDB-t üzemeltető adatbázis-kiszolgálóhoz tartozó SQL-hitelesítési jelszót. 

   g. A **katalógus adatbázisokra vonatkozó szolgáltatási szint**, válassza ki a szolgáltatási rétegben, a gazdagép SSISDB adatbázis-kiszolgáló: Alapszintű/Standard/prémium szintű vagy a rugalmas készlet neve. 

   h. Kattintson a **Kapcsolat tesztelése** lehetőségre, és ha sikerrel járt, kattintson a **Tovább** gombra. 

1. A **Speciális beállítások** oldalon végezze el a következő lépéseket: 

   ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. A **Párhuzamos végrehajtások maximális száma csomópontonként** mezőben választhatja ki az integrációsmodul-fürt egy csomópontján egyszerre végrehajtható csomagok maximális számát. Csak a támogatott csomagszámok jelennek meg. Ha egynél több magot szeretne használni egy teljesítmény-/memóriaigényes, nagy csomag méretű futtatásához, válasszon alacsony számot. A magas értéket akkor válassza, ha egy magon szeretné kicsi/nem nagy igényű csomagot vagy csomagokat futtatni. 

   b. Az **Egyéni beállítástároló SAS URI-ja** mezőben opcionálisan megadható az ahhoz az Azure Storage Blob tárolóhoz tartozó közös hozzáférésű jogosultságkód (SAS) egyedi erőforrás-azonosító (URI), ahol a beállítás szkriptjét és az ahhoz társuló fájlokat tárolja, lásd: [Az Azure SSIS IR egyéni beállítása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. A **VNet kiválasztása...** jelölőnégyzetben döntheti el, hogy szeretné-e virtuális hálózathoz csatlakoztatni az integrációs modult. Akkor jelölje be, ha az Azure SQL Database-t virtuális hálózati szolgáltatásvégpontokkal/felügyelt példánnyal használja az SSISDB üzemeltetésére, vagy a helyszíni adatokhoz való hozzáférésre van szüksége, lásd: [Azure SSIS integrációs modul létrehozása virtuális hálózatban](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

1. Az integrációs modul létrehozásának elindításához kattintson a **Befejezés** gombra. 

   > [!IMPORTANT]
   > A folyamat elvégzése hozzávetőleg 20–30 percet vesz igénybe.
   >
   > A Data Factory szolgáltatás csatlakozik az Azure SQL Database-kiszolgálóhoz, és előkészíti az SSIS-katalógust (SSISDB-adatbázist). 
   > 
   > Amikor üzembe helyez egy Azure-SSIS integrációs modul egy példányát, az Azure Feature Pack for SSIS és az Access Redistributable is telepítve lesz. Ezek az összetevők biztosítják a csatlakozást az Excel- és Access-fájlokhoz és különböző Azure-adatforrásokhoz a beépített összetevők által támogatott adatforrások mellett. Ezenkívül további összetevőket is telepíthet. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md). 

1. A **Kapcsolatok** lapon váltson az **Integrációs modulok** lapra, ha szükséges. Az állapot frissítéséhez kattintson a **Frissítés** gombra. 

   ![Létrehozási állapot a Frissítés gombbal](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Az integrációs modul leállításához/elindításához, szerkesztéséhez vagy törléséhez használja a **Műveletek** oszlopban található hivatkozásokat. Az integrációs modulhoz tartozó JSON-kód megtekintéséhez használja a legutolsó hivatkozást. A szerkesztés és törlés gombok csak akkor használhatók, ha az integrációs modul le van állítva. 

   ![A Műveletek oszlopban szereplő hivatkozások](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése
Most az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) segítségével helyezze üzembe az SSIS-csomagokat az Azure-ban. Csatlakozzon ahhoz az Azure SQL Database-kiszolgálóhoz, amelyen az SSIS-katalógus (SSISDB-adatbázis) található. Az Azure SQL Database nevének formátuma a következő: `<servername>.database.windows.net`. 

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

Ha többet szeretne megtudni az Azure-SSIS integrációs modul testreszabásáról, folytassa a következő cikkel: 

> [!div class="nextstepaction"]
> [Az Azure-SSIS integrációs modul testreszabása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)
