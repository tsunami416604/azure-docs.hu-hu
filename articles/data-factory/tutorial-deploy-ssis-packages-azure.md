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
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: b3b180fe4b465f3e0c7de888043326fd1a43cf23
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009655"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Az Azure SSIS integrációs modul üzembe helyezése az Azure Data Factoryban

Ez az oktatóanyag azt ismerteti, hogyan lehet a Azure Portal használatával kiépíteni egy Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) a Azure Data Factoryban (ADF). Azure-SSIS IR támogatja a Azure SQL Database kiszolgáló/felügyelt példány (projekt-telepítési modell) által üzemeltetett SSIS-katalógusba (SSISDB) telepített csomagok futtatását, valamint a fájlrendszerek/fájlmegosztás/Azure Files (csomag-telepítési modell) által üzembe helyezett csomagokat. Azure-SSIS IR kiépítés után használhat ismerős eszközöket, például SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) és parancssori segédeszközöket, például `dtinstall` / `dtutil` / `dtexec` a csomagok üzembe helyezése és futtatása az Azure-ban. Elméleti információk az Azure-SSIS integrációs modulokról: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
- **Azure SQL Database kiszolgáló (nem kötelező)** . Ha még nem rendelkezik adatbázis-kiszolgálóval, hozzon létre egyet a Azure Portal az első lépések megkezdése előtt. Az ADF ekkor létrehozza a SSISDB az adatbázis-kiszolgálón. Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi az integrációs modul írási végrehajtásának naplózását az Azure-régiók SSISDB nélkül. 
    - A kiválasztott adatbázis-kiszolgáló alapján az SSISDB létrehozható az Ön nevében önálló adatbázisként, egy rugalmas készlet részeként vagy egy felügyelt példányban, és elérhető nyilvános hálózatban vagy egy virtuális hálózathoz csatlakozva. Az adatbázis-kiszolgáló SSISDB gazdagépként való kiválasztásával kapcsolatos útmutatásért lásd: [Azure SQL Database önálló adatbázis/rugalmas készlet/felügyelt példány összehasonlítása](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Ha Azure SQL Database-kiszolgálót használ virtuális hálózati szolgáltatásbeli végpontokkal/felügyelt példánnyal egy privát végponttal a SSISDB üzemeltetéséhez, illetve a helyi adatközponthoz való hozzáférés megkövetelését anélkül, hogy saját üzemeltetésű integrációs modult kellene konfigurálnia, csatlakoztatnia kell a Azure-SSIS IRt egy virtuális hálózathoz, Lásd: [Azure-SSIS IR létrehozása virtuális hálózaton](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás engedélyezve van az adatbázis-kiszolgálón. Ez nem alkalmazható, ha Azure SQL Database-kiszolgálót használ virtuális hálózati szolgáltatás végpontokkal/felügyelt példánnyal egy privát végponttal a SSISDB üzemeltetéséhez. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Ha ezt a beállítást a PowerShell használatával szeretné engedélyezni, tekintse meg a [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)című témakört.
    - Adja hozzá az ügyfélszámítógép IP-címét, vagy az ügyfél IP-címét tartalmazó IP-címtartományt az adatbázis-kiszolgáló tűzfal beállításai között az ügyfél IP-címei listához. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../sql-database/sql-database-firewall-configure.md).
    - Az adatbázis-kiszolgálóhoz SQL-hitelesítéssel, a kiszolgálói rendszergazdai hitelesítő adataival vagy a Azure Active Directory (HRE) hitelesítéssel kapcsolódhat az ADF felügyelt identitásával. Az utóbbi esetében hozzá kell adnia az ADF felügyelt identitását egy AAD-csoporthoz, amely rendelkezik engedélyekkel az adatbázis-kiszolgálóhoz, lásd: [Azure SSIS integrációs modul létrehozása AAD-hitelesítéssel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Győződjön meg arról, hogy az adatbázis-kiszolgáló már rendelkezik SSISDB. Egy Azure-SSIS IR kiépítés nem támogatja a meglévő SSISDB használatát.

> [!NOTE]
> - Azon Azure-régiók listáját, amelyekben az ADF és a Azure-SSIS IR jelenleg elérhető, lásd: [ADF + SSIS IR elérhetőség régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>data factory létrehozása

Ha Azure Portal segítségével kívánja létrehozni az ADF-t, kövesse az [ADF létrehozása felhasználói felületen keresztül](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) című cikkben ismertetett lépéseket, és válassza a **rögzítés az irányítópulton** lehetőséget, ha így tesz a gyors hozzáférést a létrehozás után. 

Az ADF létrehozása után nyissa meg Azure Portal áttekintés lapját, és kattintson a **szerző & monitorozás** csempére, hogy elindítsa az **első lépések** lapot egy külön lapon, ahol továbbra is létrehozhatja a Azure-SSIS IR.   

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása

### <a name="from-the-data-factory-overview"></a>A Data Factory áttekintése

1. Az **első lépések** lapon kattintson a **SSIS konfigurálása Integration Runtime** csempére. 

   ![SSIS integrációs modul konfigurálása csempe](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Az Azure-SSIS integrációs modul beállításához szükséges további lépésekkel kapcsolatos információ: [Azure SSIS integrációs modul üzembe helyezése](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>A szerzői felhasználói felületen

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

   f. A **kiadás/licenc**beállításnál válassza a SQL Server Edition/License lehetőséget az integrációs modulhoz: Standard vagy Enterprise. Ha szeretne fejlett/prémium szolgáltatásokat használni az integrációs modulon, válassza az Enterprise verziót. 

   g. A **pénz megtakarítása**lehetőségnél válassza a Azure Hybrid BENEFIT (AHB) lehetőséget az integrációs modulhoz: igen vagy nem. Akkor válassza az Igen lehetőséget, ha a saját, szoftverbiztosítással ellátott SQL Server-licencét szeretné használni, hogy élhessen a hibrid használatból fakadó költségcsökkentéssel. 

   h. Kattintson a **Tovább** gombra. 

1. Az **SQL-beállítások** oldalon végezze el a következő lépéseket: 

   ![SQL-beállítások](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. A **SSIS-katalógus létrehozása...** jelölőnégyzetből válassza ki a Azure-SSIS IR futtatandó csomagok telepítési modelljét: Projekt-telepítési modell, ahol a csomagok üzembe helyezése az adatbázis-kiszolgáló vagy a csomag telepítési modellje által üzemeltetett SSISDB történik, ahol a csomagok telepítése a fájlrendszer/fájlmegosztás/Azure Filesba történik. Ha bejelöli, saját adatbázis-kiszolgálót kell használnia a SSISDB gazdagépre, amelyet az Ön nevében fog létrehozni és kezelni.
   
   b. Az **Előfizetés** mezőben válassza azt az Azure-előfizetést, amelyikkel az adatbázis-kiszolgáló üzemelteti az SSISDB-t. 

   c. A **Hely** mezőben válassza ki az SSISDB-t üzemeltető adatbázis-kiszolgáló helyét. Javasoljuk, hogy az integrációs modullal megegyező helyet válasszon.

   d. A **Katalógus adatbázis-kiszolgáló végpontja** mezőben válassza az SSISDB-t üzemeltető adatbázis-kiszolgáló végpontját. A kiválasztott adatbázis-kiszolgáló alapján az SSISDB létrehozható az Ön nevében önálló adatbázisként, egy rugalmas készlet részeként vagy egy felügyelt példányban, és elérhető nyilvános hálózatban vagy egy virtuális hálózathoz csatlakozva. Az adatbázis-kiszolgáló SSISDB gazdagépként való kiválasztásával kapcsolatos útmutatásért lásd: [Azure SQL Database önálló adatbázis/rugalmas készlet/felügyelt példány összehasonlítása](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Ha a virtuális hálózati szolgáltatással rendelkező végpontokkal/felügyelt példánnyal rendelkező Azure SQL Database kiszolgálót a SSISDB üzemeltetéséhez, illetve a helyszíni adataihoz való hozzáférés megköveteléséhez a saját üzemeltetésű integrációs modul konfigurálása nélkül választja, akkor a virtuális hálózathoz kell csatlakoztatnia a Azure-SSIS IR Lásd: [Azure-SSIS IR létrehozása virtuális hálózaton](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. A **HRE-hitelesítés használata...** jelölőnégyzet kiválasztásával adja meg az adatbázis-kiszolgáló hitelesítési módszerét a SSISDB üzemeltetéséhez: SQL-hitelesítés vagy HRE-hitelesítés az ADF felügyelt identitásával. Ha bejelöli, hozzá kell adnia az ADF felügyelt identitását egy olyan HRE-csoporthoz, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz, lásd: [Azure-SSIS IR létrehozása HRE-hitelesítéssel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   f. A **Rendszergazdai felhasználónév** értékeként adja meg az SSISDB-t üzemeltető adatbázis-kiszolgálóhoz tartozó SQL-hitelesítési felhasználónevet. 

   g. A **Rendszergazdai jelszó** értékeként adja meg az SSISDB-t üzemeltető adatbázis-kiszolgálóhoz tartozó SQL-hitelesítési jelszót. 

   h. A **katalógus-adatbázis szolgáltatási szintjéhez**válassza ki az adatbázis-kiszolgáló szolgáltatási SZINTJÉT a SSISDB üzemeltetéséhez: Alapszintű/standard/Prémium csomag vagy rugalmas készlet neve.

   i. Kattintson a **Kapcsolat tesztelése** lehetőségre, és ha sikerrel járt, kattintson a **Tovább** gombra. 

1. A **Speciális beállítások** oldalon végezze el a következő lépéseket: 

   ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. A **Párhuzamos végrehajtások maximális száma csomópontonként** mezőben választhatja ki az integrációsmodul-fürt egy csomópontján egyszerre végrehajtható csomagok maximális számát. Csak a támogatott csomagszámok jelennek meg. Ha egynél több magot szeretne használni egy teljesítmény-/memóriaigényes, nagy csomag méretű futtatásához, válasszon alacsony számot. A magas értéket akkor válassza, ha egy magon szeretné kicsi/nem nagy igényű csomagot vagy csomagokat futtatni. 

   b. Az **Egyéni beállítástároló SAS URI-ja** mezőben opcionálisan megadható az ahhoz az Azure Storage Blob tárolóhoz tartozó közös hozzáférésű jogosultságkód (SAS) egyedi erőforrás-azonosító (URI), ahol a beállítás szkriptjét és az ahhoz társuló fájlokat tárolja, lásd: [Az Azure SSIS IR egyéni beállítása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. A **VNet kiválasztása...** jelölőnégyzetben döntheti el, hogy szeretné-e virtuális hálózathoz csatlakoztatni az integrációs modult. Győződjön meg arról, hogy ha Azure SQL Database-kiszolgálót használ virtuális hálózati szolgáltatás végpontokkal/felügyelt példánnyal egy privát végponttal a SSISDB üzemeltetéséhez, illetve a helyszíni információkhoz való hozzáférést a saját üzemeltetésű integráció konfigurálása nélkül, tekintse meg a Azure-SSIS IR létrehozása című témakört. [ virtuális hálózat](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   d. A saját üzemeltetésű **...** jelölőnégyzet bejelölésével adja meg, hogy szeretné-e konfigurálni a saját üzemeltetésű integrációs modult proxyként a Azure-SSIS IR számára, lásd: saját üzemeltetésű integrációs modul [beállítása proxyként](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

1. Kattintson a **Tovább** gombra. 

1. Az **Összefoglalás** lapon tekintse át az összes kiépítési beállítást, majd a javasolt dokumentációs hivatkozásokat, és kattintson a **Befejezés** gombra az integrációs modul létrehozásának megkezdéséhez. 

   > [!NOTE]
   > Az egyéni telepítési idő kizárása esetén ezt a folyamatot 5 percen belül el kell végezni.
   >
   > A SSISDB használata esetén az ADF szolgáltatás a SSISDB előkészítéséhez csatlakozik az adatbázis-kiszolgálóhoz. 
   > 
   > Amikor kiépít egy Azure-SSIS IR, a SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők az Excel/Access fájlokhoz és különböző Azure-adatforrásokhoz való kapcsolódást biztosítanak a beépített összetevők által már támogatott adatforrások mellett. További összetevőket is telepíthet, lásd: [Azure-SSIS IR egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md).

1. A **Kapcsolatok** lapon váltson az **Integrációs modulok** lapra, ha szükséges. Az állapot frissítéséhez kattintson a **Frissítés** gombra. 

   ![Létrehozási állapot a Frissítés gombbal](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Az integrációs modul leállításához/elindításához, szerkesztéséhez vagy törléséhez használja a **Műveletek** oszlopban található hivatkozásokat. Az integrációs modulhoz tartozó JSON-kód megtekintéséhez használja a legutolsó hivatkozást. A szerkesztés és törlés gombok csak akkor használhatók, ha az integrációs modul le van állítva. 

   ![A Műveletek oszlopban szereplő hivatkozások](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése

Ha a SSISDB-t használja, a csomagokat üzembe helyezheti, és Azure-SSIS IR futtathatja azokat a SSDT/SSMS eszközök használatával, amelyek kiszolgálói végpontján keresztül csatlakoznak az adatbázis-kiszolgálóhoz. Nyilvános végponttal rendelkező Azure SQL Database kiszolgáló/felügyelt példány esetén a kiszolgálói végpont formátuma `<server name>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`a következő:. Ha nem használja a SSISDB-t, a csomagokat fájlrendszerek/fájlmegosztás/Azure Filesba helyezheti, és futtathatja azokat Azure-SSIS IR parancssori segédeszközök `dtinstall` használatával `dtexec` /. `dtutil` / További információ: SSIS- [csomagok telepítése](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Mindkét esetben futtathatja az üzembe helyezett csomagokat Azure-SSIS IR a SSIS-csomag végrehajtása az ADF-folyamatokban művelettel történő futtatásával, lásd: [SSIS-csomag végrehajtásának meghívása első osztályú ADF-tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

Lásd még a következő cikkeket a SSIS dokumentációjában: 

- [SSIS-csomagok üzembe helyezése, futtatása és figyelése az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Kapcsolódás a SSISDB az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Csomagok végrehajtásának ütemezett végrehajtása az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
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