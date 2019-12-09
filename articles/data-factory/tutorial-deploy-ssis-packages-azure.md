---
title: Az Azure SSIS Integration Runtime kiépítése
description: Ismerje meg, hogyan helyezheti üzembe az Azure SSIS integrációs modult az Azure Data Factoryben, hogy SSIS-csomagokat telepíthessen és futtathasson az Azure-ban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: 2cec7fdae1d3a2a336decc11347ef9bd1039ce7f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926555"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Az Azure-SSIS integrációs modul kiépítése Azure Data Factory

Ez az oktatóanyag az Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) Azure Data Factory-ben való kiépítésének lépéseit ismerteti a Azure Portal használatával. Az Azure-SSIS IR a következőket támogatja:

- Egy Azure SQL Database-kiszolgáló vagy egy felügyelt példány (projekt-telepítési modell) által üzemeltetett SSIS-katalógusba (SSISDB) telepített csomagok futtatása.
- Fájlrendszerek, fájlmegosztás vagy Azure Files (csomag-telepítési modell) üzembe helyezett csomagjainak futtatása. 

Egy Azure-SSIS IR kiépítése után jól ismert eszközökkel üzembe helyezheti és futtathatja a csomagokat az Azure-ban. Ezek az eszközök közé tartoznak a SQL Server Data Tools, a SQL Server Management Studio és a parancssori eszközök, például a `dtinstall`, a `dtutil`és a `dtexec`.

Elméleti információk az Azure-SSIS integrációs modulokról: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
- **Azure SQL Database kiszolgáló (nem kötelező)** . Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. Data Factory ekkor létrehoz egy SSISDB-példányt ezen az adatbázis-kiszolgálón. 

  Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi az integrációs modul írási végrehajtásának naplózását az Azure-régiók SSISDB nélkül.

  Tartsa szem előtt az alábbi szempontokat:

  - A kiválasztott adatbázis-kiszolgáló alapján a SSISDB-példány létrehozhatók az Ön nevében egyetlen adatbázisként egy rugalmas készlet részeként vagy egy felügyelt példányban. Nyilvános hálózaton vagy virtuális hálózathoz való csatlakozással is elérhető. Az adatbázis-kiszolgáló SSISDB futtatásához való kiválasztásával kapcsolatos útmutatásért lásd: [Azure SQL Database önálló adatbázis, rugalmas készlet és felügyelt példány összevetése](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). 
  
    Ha olyan Azure SQL Database kiszolgálót használ virtuális hálózati szolgáltatás-végpontokkal vagy felügyelt példánnyal, amely egy privát végponttal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyi integrációs modul konfigurálása nélkül szeretné elérni a helyszíni adatait, akkor csatlakoztatnia kell a Azure-SSIS IRt egy virtuális hálózathoz hálózati. További információ: [Azure-SSIS IR létrehozása virtuális hálózaton](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
  - Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás engedélyezve van az adatbázis-kiszolgálón. Ez a beállítás nem alkalmazható, ha olyan Azure SQL Database kiszolgálót használ virtuális hálózati szolgáltatás-végpontokkal vagy felügyelt példánnyal, amely privát végponttal rendelkezik a SSISDB üzemeltetéséhez. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Ha ezt a beállítást a PowerShell használatával szeretné engedélyezni, tekintse meg a [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)című témakört.
  - Adja hozzá az ügyfélszámítógép IP-címét, vagy egy olyan IP-címtartományt, amely tartalmazza az ügyfélszámítógép IP-címét az adatbázis-kiszolgáló tűzfal beállításai között az ügyfél IP-címei listára. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../sql-database/sql-database-firewall-configure.md).
  - A kiszolgáló-rendszergazdai hitelesítő adataival SQL-hitelesítéssel, vagy az adat-előállító felügyelt identitásával az Azure AD-hitelesítés használatával kapcsolódhat az adatbázis-kiszolgálóhoz. Az utóbbi esetében hozzá kell adnia az adatok előállítójának felügyelt identitását egy Azure AD-csoportba, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: [Azure-SSIS IR létrehozása Azure ad-hitelesítéssel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
  - Győződjön meg arról, hogy az adatbázis-kiszolgáló már rendelkezik SSISDB-példánnyal. Egy Azure-SSIS IR kiépítés nem támogatja a meglévő SSISDB-példányok használatát.


> [!NOTE]
> Azon Azure-régiók listájáért, amelyekben a Data Factory és a Azure-SSIS IR jelenleg elérhetők, tekintse meg a [Data Factory és a SSIS az IR elérhetősége régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Data factory létrehozása

Ha az adatgyárat a Azure Portal segítségével szeretné létrehozni, kövesse az [adatfeldolgozó létrehozása a felhasználói felületen](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)című témakör részletes utasításait. Ha ezt megteszi, válassza a **rögzítés az irányítópulton** lehetőséget, hogy a létrehozása után engedélyezze a gyors hozzáférést. 

Az adatelőállító létrehozása után nyissa meg a Azure Portal áttekintés lapját. Válassza a **szerző & figyelés** csempét, és nyissa meg az **első lépések** lapot egy külön lapon. Itt továbbra is létrehozhatja a Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása

### <a name="from-the-data-factory-overview"></a>A Data Factory áttekintése

1. Az **Első lépések** lapon válassza ki az **SSIS integrációs modul konfigurálása** csempét. 

   ![SSIS integrációs modul konfigurálása csempe](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Az Azure-SSIS integrációs modul beállításához szükséges további lépésekkel kapcsolatos információ: [Azure SSIS integrációs modul üzembe helyezése](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>A szerzői felhasználói felületen

1. A Azure Data Factory felhasználói felületen váltson a **Szerkesztés** lapra, és válassza a **kapcsolatok**lehetőséget. Ezután váltson az **Integration Runtimes (integrációs** modulok) lapra, ahol megtekintheti a meglévő integrációs modulokat az adatgyárban. 

   ![Meglévő integrációs modulok megtekintésére szolgáló kiválasztások](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Válassza az **Új** lehetőséget egy Azure-SSIS integrációs modul létrehozásához. 

   ![Integrációs modul elérése a menüből](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Az **Integrációs modul telepítése** ablakban válassza a **Meglévő SSIS-csomagok áthelyezése az Azure-ban történő végrehajtáshoz** lehetőséget, majd kattintson a **Tovább** gombra. 

   ![Integrációs modulok típusának megadása](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Az Azure-SSIS integrációs modul beállításához szükséges további lépésekkel kapcsolatos információ: [Azure SSIS integrációs modul üzembe helyezése](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul üzembe helyezése

1. **Integration Runtime telepítő** **általános beállítások** lapján végezze el a következő lépéseket. 

   ![Általános beállítások](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. A **Név** mezőben adja meg az integrációs modul nevét. 

   b. A **Leírás** mezőben adja meg az integrációs modul leírását. 

   c. A **Hely** mezőben válassza ki az integrációs modul helyét. Csak a támogatott helyek jelennek meg. Javasoljuk, hogy az SSISDB-t üzemeltető adatbázis-kiszolgálóval megegyező helyet válasszon. 

   d. A **Csomópontméret** mezőben válassza ki az integrációsmodul-fürtön lévő csomópont méretét. Csak a támogatott csomópontméretek jelennek meg. Válasszon nagy méretű csomópontot (vertikális felskálázás), ha sok nagy számítási igényű vagy memória-igényű csomagot szeretne futtatni. 

   e. A **Csomópontszám** mezőben adja meg az integrációsmodul-fürtben található csomópontok számát. Csak a támogatott csomópontszámok jelennek meg. Ha egyszerre több csomagot szeretne futtatni, válasszon ki egy nagy méretű fürtöt sok csomóponttal. 

   f. A **kiadás/licenc**lapon válassza ki az integrációs modul SQL Server kiadását: standard vagy Enterprise. Válassza a vállalat lehetőséget, ha az integrációs modul speciális funkcióit szeretné használni. 

   g. A **pénz megtakarítása**lehetőségnél válassza a Azure Hybrid Benefit lehetőséget az integrációs futtatókörnyezethez: **Igen** vagy **nem**. Válassza az **Igen** lehetőséget, ha a frissítési garanciával rendelkező saját SQL Server licencét szeretné kihasználni a hibrid használattal járó költségmegtakarítással. 

   h. Kattintson a **Tovább** gombra. 

1. Az **SQL-beállítások** lapon végezze el a következő lépéseket. 

   ![SQL-beállítások](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Jelölje be a **SSIS-katalógus létrehozása...** jelölőnégyzetet, és válassza ki a Azure-SSIS IRon futtatandó csomagok telepítési modelljét. Választhatja a projekt üzembe helyezési modelljét is, ahol a csomagok üzembe helyezése az adatbázis-kiszolgáló által üzemeltetett SSISDB történik, vagy a csomag telepítési modellje, ahol a csomagok fájlrendszerekbe, fájlmegosztásba vagy Azure Filesba vannak telepítve.
   
   Ha bejelöli a jelölőnégyzetet, saját adatbázis-kiszolgálót kell használnia az Ön nevében létrehozandó és kezelt SSISDB.
   
   b. Az **Előfizetés** mezőben válassza azt az Azure-előfizetést, amelyikkel az adatbázis-kiszolgáló üzemelteti az SSISDB-t. 

   c. A **Hely** mezőben válassza ki az SSISDB-t üzemeltető adatbázis-kiszolgáló helyét. Javasoljuk, hogy az integrációs modullal megegyező helyet válasszon.

   d. A **Katalógus adatbázis-kiszolgáló végpontja** mezőben válassza az SSISDB-t üzemeltető adatbázis-kiszolgáló végpontját. 
   
   A kiválasztott adatbázis-kiszolgáló alapján a SSISDB-példány létrehozhatók az Ön nevében egyetlen adatbázisként egy rugalmas készlet részeként vagy egy felügyelt példányban. Nyilvános hálózaton vagy virtuális hálózathoz való csatlakozással is elérhető. Az adatbázis-kiszolgáló SSISDB futtatásához való kiválasztásával kapcsolatos útmutatásért lásd: [Azure SQL Database önálló adatbázis, rugalmas készlet és felügyelt példány összevetése](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).   

   Ha olyan Azure SQL Database-kiszolgálót választ ki, amely virtuális hálózati szolgáltatás-végpontokkal vagy felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyi integrációs modul konfigurálása nélkül kívánja elérni a helyszíni adatkezelést, akkor csatlakoztatnia kell a Azure-SSIS IR virtuális hálózat. További információ: [Azure-SSIS IR létrehozása virtuális hálózaton](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

   e. Jelölje be a **HRE-hitelesítés használata az ADF felügyelt identitással** jelölőnégyzetet, hogy kiválassza az adatbázis-kiszolgáló hitelesítési módszerét a SSISDB üzemeltetéséhez. Választhatja az SQL-hitelesítést vagy az Azure AD-hitelesítést az adatokhoz tartozó felügyelt identitással.

   Ha bejelöli a jelölőnégyzetet, hozzá kell adnia az adatgyár felügyelt identitását egy Azure AD-csoportba, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: [Azure-SSIS IR létrehozása Azure ad-hitelesítéssel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
   
   f. A **rendszergazdai Felhasználónév**mezőben adja meg az adatbázis-kiszolgáló SQL-hitelesítési felhasználónevét a SSISDB üzemeltetéséhez. 

   g. **Rendszergazdai jelszó**esetén adja meg az adatbázis-kiszolgáló SQL-hitelesítési jelszavát a SSISDB üzemeltetéséhez. 

   h. A **katalógus-adatbázis szolgáltatási szintjéhez**válassza ki az adatbázis-kiszolgáló szolgáltatási SZINTJÉT a SSISDB üzemeltetéséhez. Válassza ki az alapszintű, a standard vagy a prémium szintet, vagy válasszon egy rugalmas készlet nevét.

   i. Válassza a **Kapcsolat tesztelése** lehetőséget. Ha a teszt sikeres, kattintson a **Tovább gombra**. 

1. A **Speciális beállítások** lapon végezze el a következő lépéseket. 

   ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. **Csomópontok maximális párhuzamos végrehajtásához**válassza ki az Integration Runtime-fürt csomópontjain egyidejűleg futtatandó csomagok maximális számát. Csak a támogatott csomagszámok jelennek meg. Ha egynél több mag használatával szeretné futtatni a számítási vagy a memória-igényű egyetlen nagyméretű csomagot, válasszon ki egy kis számot. Válassza ki a magas számot, ha egy vagy több kis csomagot szeretne futtatni egyetlen mag használatával. 

   b. Az **egyéni telepítési tároló sas URI-ja**esetében opcionálisan megadhatja az Azure Blob Storage-tároló közös hozzáférésű aláírásának (SAS) egységes erőforrás-azonosítóját (URI), amelyben a telepítési parancsfájlt és a hozzá tartozó fájlokat tárolja. További információ: [Azure-SSIS IR egyéni beállítása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).
   
   c. Jelölje be a **VNet kiválasztása a Azure-SSIS Integration Runtimehoz, és az ADF engedélyezése bizonyos hálózati erőforrások létrehozásához** jelölőnégyzetet, és válassza ki, hogy csatlakoztatni kívánja-e az integrációs modult egy virtuális hálózathoz.

   Jelölje be, ha a virtuális hálózati szolgáltatás-végpontokkal vagy egy felügyelt példánnyal rendelkező Azure SQL Database kiszolgálót használ a SSISDB üzemeltetéséhez, vagy ha a helyi adatközpontot nem konfigurálja saját üzemeltetésű integrációs modul konfigurálása nélkül. További információ: [Azure-SSIS IR létrehozása virtuális hálózaton](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
   
   d. Jelölje be a saját üzemeltetésű **Integration Runtime beállítása proxyként a Azure-SSIS Integration Runtime** jelölőnégyzetet annak kiválasztásához, hogy egy saját üzemeltetésű integrációs modult kíván-e konfigurálni a Azure-SSIS IRhoz. További információ: saját üzemeltetésű [IR beállítása proxyként](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).   

1. Kattintson a **Tovább** gombra. 

1. Az **Összefoglalás** lapon tekintse át az összes kiépítési beállítást, a könyvjelzőt az ajánlott dokumentációs hivatkozásokat, és válassza a **Befejezés** lehetőséget az integrációs modul létrehozásának elindításához. 

   > [!NOTE]
   > Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül be kell fejeződnie.
   >
   > Ha a SSISDB-t használja, akkor a Data Factory-szolgáltatás a SSISDB előkészítéséhez csatlakozik az adatbázis-kiszolgálóhoz. 
   > 
   > Amikor kiépít egy Azure-SSIS IR, a SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők az Excel-fájlokhoz, a fájlokhoz és a különböző Azure-adatforrásokhoz való kapcsolódást biztosítják a beépített összetevők által már támogatott adatforrások mellett. További információ a telepíthető egyéb összetevőkről: a [Azure-SSIS IR egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md).

1. A **Kapcsolatok** lapon váltson az **Integrációs modulok** lapra, ha szükséges. Az állapot frissítéséhez kattintson a **Frissítés** gombra. 

   ![Létrehozási állapot a Frissítés gombbal](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Az integrációs modul leállításához/elindításához, szerkesztéséhez vagy törléséhez használja a **Műveletek** oszlopban található hivatkozásokat. Az integrációs modulhoz tartozó JSON-kód megtekintéséhez használja a legutolsó hivatkozást. A szerkesztés és törlés gombok csak akkor használhatók, ha az integrációs modul le van állítva. 

   ![A Műveletek oszlopban szereplő hivatkozások](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése

Ha a SSISDB-t használja, a csomagokat üzembe helyezheti, és SQL Server Data Tools vagy SQL Server Management Studio eszközök használatával futtathatja azokat a Azure-SSIS IR. Ezek az eszközök a kiszolgálói végponton keresztül csatlakoznak az adatbázis-kiszolgálóhoz: 

- Privát végponttal rendelkező Azure SQL Database-kiszolgáló esetén a kiszolgálói végpont formátuma `<server name>.database.windows.net`.
- Privát végponttal rendelkező felügyelt példány esetén a kiszolgálói végpont formátuma `<server name>.<dns prefix>.database.windows.net`.
- Nyilvános végponttal rendelkező felügyelt példány esetén a kiszolgálói végpont formátuma `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Ha nem használja a SSISDB-t, a csomagokat fájlrendszerekbe, fájlmegosztásba vagy Azure Filesba is telepítheti. Ezután a Azure-SSIS IR futtathatja őket a `dtinstall`, a `dtutil`és a `dtexec` parancssori eszközök használatával. További információ: SSIS- [csomagok telepítése](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

Mindkét esetben a telepített csomagokat is futtathatja a Azure-SSIS IR a SSIS-csomag végrehajtása művelettel Data Factory folyamatokban. További információ: [SSIS-csomag végrehajtásának meghívása első osztályú Data Factory tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Lásd még a következő SSIS-dokumentációt: 

- [SSIS-csomagok üzembe helyezése, futtatása és figyelése az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Kapcsolódás a SSISDB az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Csomagok végrehajtásának ütemezett végrehajtása az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure-SSIS integrációs modul testreszabásáról, folytassa a következő cikkel: 

> [!div class="nextstepaction"]
> [Azure-SSIS IR testreszabása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)