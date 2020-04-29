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
ms.date: 12/23/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 91962b578f9620384af86d25a33ba35830abb285
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81418626"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Az Azure-SSIS integrációs modul kiépítése Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez az oktatóanyag az Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) Azure Data Factory-ben való kiépítésének lépéseit ismerteti a Azure Portal használatával. Az Azure-SSIS IR a következőket támogatja:

- Egy Azure SQL Database-kiszolgáló vagy egy felügyelt példány (projekt-telepítési modell) által üzemeltetett SSIS-katalógusba (SSISDB) telepített csomagok futtatása.
- Fájlrendszerek, fájlmegosztás vagy Azure Files (csomag-telepítési modell) üzembe helyezett csomagjainak futtatása. 

Egy Azure-SSIS IR kiépítése után jól ismert eszközökkel üzembe helyezheti és futtathatja a csomagokat az Azure-ban. Ezek az eszközök a következők: SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) és parancssori eszközök `dtinstall` `dtutil`, például:, `dtexec`és.

Elméleti információk az Azure-SSIS integrációs modulokról: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
- **Azure SQL Database kiszolgáló (nem kötelező)**. Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. Data Factory ekkor létrehoz egy SSISDB-példányt ezen az adatbázis-kiszolgálón. 

  Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi az integrációs modul írási végrehajtásának naplózását az Azure-régiók SSISDB nélkül.

  Tartsa szem előtt az alábbi szempontokat:

  - A kiválasztott adatbázis-kiszolgáló alapján a SSISDB-példány létrehozhatók az Ön nevében egyetlen adatbázisként egy rugalmas készlet részeként vagy egy felügyelt példányban. Nyilvános hálózaton vagy virtuális hálózathoz való csatlakozással is elérhető. Az adatbázis-kiszolgáló SSISDB futtatásához való kiválasztásával kapcsolatos útmutatásért lásd: [Azure SQL Database önálló adatbázis, rugalmas készlet és felügyelt példány összevetése](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). 
  
    Ha olyan Azure SQL Database-kiszolgálót használ, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy egy privát végponttal rendelkező felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyi adatcsatorna konfigurálása nélkül szeretné elérni a helyszíni adatait, csatlakoztatnia kell a Azure-SSIS IRt egy virtuális hálózathoz. További információ: [Azure-SSIS IR létrehozása virtuális hálózaton](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

  - Győződjön meg arról, hogy az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás engedélyezve van az adatbázis-kiszolgálón. Ez a beállítás nem alkalmazható, ha olyan Azure SQL Database-kiszolgálót használ, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy egy privát végponttal rendelkező felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Ha ezt a beállítást a PowerShell használatával szeretné engedélyezni, tekintse meg a [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)című témakört.

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

1. A **Integration Runtime Setup (telepítés** ) panelen válassza ki a **meglévő SSIS-csomagok az Azure csempén való végrehajtásához** , majd kattintson a **tovább**gombra. 

   ![Integrációs modulok típusának megadása](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Az Azure-SSIS integrációs modul beállításához szükséges további lépésekkel kapcsolatos információ: [Azure SSIS integrációs modul üzembe helyezése](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul üzembe helyezése

1. A **Integration Runtime telepítési** paneljének **általános beállítások** szakaszában hajtsa végre a következő lépéseket. 

   ![Általános beállítások](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. A **Név** mezőben adja meg az integrációs modul nevét. 

   1. A **Leírás** mezőben adja meg az integrációs modul leírását. 

   1. A **Hely** mezőben válassza ki az integrációs modul helyét. Csak a támogatott helyek jelennek meg. Javasoljuk, hogy az SSISDB-t üzemeltető adatbázis-kiszolgálóval megegyező helyet válasszon. 

   1. A **Csomópontméret** mezőben válassza ki az integrációsmodul-fürtön lévő csomópont méretét. Csak a támogatott csomópontméretek jelennek meg. Válasszon nagy méretű csomópontot (vertikális felskálázás), ha sok nagy számítási igényű vagy memória-igényű csomagot szeretne futtatni. 

   1. A **Csomópontszám** mezőben adja meg az integrációsmodul-fürtben található csomópontok számát. Csak a támogatott csomópontszámok jelennek meg. Ha egyszerre több csomagot szeretne futtatni, válasszon ki egy nagy méretű fürtöt sok csomóponttal. 

   1. A **kiadás/licenc**lapon válassza ki az integrációs modul SQL Server kiadását: standard vagy Enterprise. Válassza a vállalat lehetőséget, ha az integrációs modul speciális funkcióit szeretné használni. 

   1. A **pénz megtakarítása**lehetőségnél válassza a Azure Hybrid Benefit lehetőséget az integrációs futtatókörnyezethez: **Igen** vagy **nem**. Válassza az **Igen** lehetőséget, ha a frissítési garanciával rendelkező saját SQL Server licencét szeretné kihasználni a hibrid használattal járó költségmegtakarítással. 

   1. Kattintson a **Tovább** gombra. 

1. Az **SQL-beállítások** szakaszban hajtsa végre az alábbi lépéseket. 

   ![SQL-beállítások](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Válassza ki a **Azure SQL Database kiszolgáló/felügyelt példány által üzemeltetett SSIS-katalógust (SSISDB) a projektek/csomagok/környezetek/végrehajtási naplók tárolásához** jelölőnégyzetet, és válassza ki a Azure-SSIS IR futtatni kívánt csomagok telepítési modelljét. Választhatja a projekt üzembe helyezési modelljét is, ahol a csomagok üzembe helyezése az adatbázis-kiszolgáló által üzemeltetett SSISDB történik, vagy a csomag telepítési modellje, ahol a csomagok fájlrendszerekbe, fájlmegosztásba vagy Azure Filesba vannak telepítve.
   
      Ha bejelöli a jelölőnégyzetet, saját adatbázis-kiszolgálót kell használnia az Ön nevében létrehozandó és kezelt SSISDB.
   
      1. Az **Előfizetés** mezőben válassza azt az Azure-előfizetést, amelyikkel az adatbázis-kiszolgáló üzemelteti az SSISDB-t. 

      1. A **Hely** mezőben válassza ki az SSISDB-t üzemeltető adatbázis-kiszolgáló helyét. Javasoljuk, hogy az integrációs modullal megegyező helyet válasszon.

      1. A **Katalógus adatbázis-kiszolgáló végpontja** mezőben válassza az SSISDB-t üzemeltető adatbázis-kiszolgáló végpontját. 
   
         A kiválasztott adatbázis-kiszolgáló alapján a SSISDB-példány létrehozhatók az Ön nevében egyetlen adatbázisként egy rugalmas készlet részeként vagy egy felügyelt példányban. Nyilvános hálózaton vagy virtuális hálózathoz való csatlakozással is elérhető. Az adatbázis-kiszolgáló SSISDB futtatásához való kiválasztásával kapcsolatos útmutatásért lásd: [Azure SQL Database önálló adatbázis, rugalmas készlet és felügyelt példány összevetése](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).   

         Ha olyan Azure SQL Database-kiszolgálót választ ki, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy egy privát végponttal rendelkező felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyi adatközpont konfigurálása nélkül szeretné elérni a helyszíni adatkezelést, akkor csatlakoztatnia kell a Azure-SSIS IRt egy virtuális hálózathoz. További információ: [Azure-SSIS IR létrehozása virtuális hálózaton](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

      1. Jelölje be a **HRE-hitelesítés használata az ADF felügyelt identitással** jelölőnégyzetet, hogy kiválassza az adatbázis-kiszolgáló hitelesítési módszerét a SSISDB üzemeltetéséhez. Választhatja az SQL-hitelesítést vagy az Azure AD-hitelesítést az adatokhoz tartozó felügyelt identitással.

         Ha bejelöli a jelölőnégyzetet, hozzá kell adnia az adatgyár felügyelt identitását egy Azure AD-csoportba, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: [Azure-SSIS IR létrehozása Azure ad-hitelesítéssel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
   
      1. A **rendszergazdai Felhasználónév**mezőben adja meg az adatbázis-kiszolgáló SQL-hitelesítési felhasználónevét a SSISDB üzemeltetéséhez. 

      1. **Rendszergazdai jelszó**esetén adja meg az adatbázis-kiszolgáló SQL-hitelesítési jelszavát a SSISDB üzemeltetéséhez. 

      1. A **katalógus-adatbázis szolgáltatási szintjéhez**válassza ki az adatbázis-kiszolgáló szolgáltatási SZINTJÉT a SSISDB üzemeltetéséhez. Válassza ki az alapszintű, a standard vagy a prémium szintet, vagy válasszon egy rugalmas készlet nevét.

      1. Válassza a **kapcsolatok tesztelése**lehetőséget. Ha a teszt sikeres, kattintson a **Tovább gombra**. 

1. A **Speciális beállítások** szakaszban hajtsa végre az alábbi lépéseket. 

   ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. **Csomópontok maximális párhuzamos végrehajtásához**válassza ki az Integration Runtime-fürt csomópontjain egyidejűleg futtatandó csomagok maximális számát. Csak a támogatott csomagszámok jelennek meg. Ha egynél több mag használatával szeretné futtatni a számítási vagy a memória-igényű egyetlen nagyméretű csomagot, válasszon ki egy kis számot. Válassza ki a magas számot, ha egy vagy több kis csomagot szeretne futtatni egyetlen mag használatával. 

   1. Jelölje be a **Azure-SSIS Integration Runtime testreszabása további rendszerkonfigurációkkal/összetevő-telepítésekkel** jelölőnégyzetet, és válassza ki, hogy szeretné-e hozzáadni a szabványos/expressz egyéni beállításokat a Azure-SSIS IR. További információ: [Azure-SSIS IR egyéni beállítása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).
   
   1. Válassza ki a **VNet kiválasztása a Azure-SSIS Integration Runtimehoz, hogy az ADF bizonyos hálózati erőforrásokat hozzon létre, és opcionálisan saját statikus nyilvános IP-címeit** is megadhatja, ha azt szeretné kiválasztani, hogy csatlakoztatni kívánja-e a Azure-SSIS IRt egy virtuális hálózathoz.

      Válassza ezt a lehetőséget, ha olyan Azure SQL Database kiszolgálót használ, amely IP-tűzfalszabályok/virtuális hálózati szolgáltatás-végpontokkal vagy egy privát végponttal rendelkező felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyi hálózathoz szeretne hozzáférni saját üzemeltetésű integrációs modul konfigurálása nélkül. További információ: [Azure-SSIS IR létrehozása virtuális hálózaton](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
   
   1. Jelölje be a saját üzemeltetésű **Integration Runtime beállítása proxyként a Azure-SSIS Integration Runtime** jelölőnégyzetet annak kiválasztásához, hogy egy saját üzemeltetésű IR-t kíván-e konfigurálni a Azure-SSIS IRhoz. További információ: saját üzemeltetésű integrációs modul [beállítása proxyként](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).   

   1. Válassza a **Folytatás** elemet. 

1. Az **Összefoglalás** szakaszban tekintse át az összes kiépítési beállítást, könyvjelzőt az ajánlott dokumentációs hivatkozásokkal, és válassza a **Befejezés** lehetőséget az integrációs modul létrehozásának megkezdéséhez. 

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

Ha a SSISDB-t használja, a csomagokat üzembe helyezheti, és a Azure-SSIS IR SQL Server Data Tools (SSDT) vagy SQL Server Management Studio (SSMS) eszközzel futtathatja őket. Ezek az eszközök a kiszolgálói végponton keresztül csatlakoznak az adatbázis-kiszolgálóhoz: 

- Azure SQL Database-kiszolgáló esetén a kiszolgálói végpont formátuma a `<server name>.database.windows.net`következő:.
- Privát végponttal rendelkező felügyelt példány esetén a kiszolgálói végpont formátuma `<server name>.<dns prefix>.database.windows.net`a következő:.
- Nyilvános végponttal rendelkező felügyelt példány esetén a kiszolgálói végpont formátuma `<server name>.public.<dns prefix>.database.windows.net,3342`a következő:. 

Ha nem használja a SSISDB-t, a csomagokat fájlrendszerekbe, fájlmegosztásba vagy Azure Filesba is telepítheti, és a `dtinstall`, `dtutil`, és `dtexec` parancssori eszközök használatával futtathatja azokat a Azure-SSIS IR. További információ: SSIS- [csomagok telepítése](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

Mindkét esetben a telepített csomagokat Azure-SSIS IR is futtathatja a SSIS-csomag végrehajtása művelettel Data Factory folyamatokban. További információ: [SSIS-csomag végrehajtásának meghívása első osztályú Data Factory tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Lásd még a következő SSIS-dokumentációt: 

- [SSIS-csomagok üzembe helyezése, futtatása és figyelése az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Kapcsolódás a SSISDB az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Csomagok végrehajtásának ütemezett végrehajtása az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure-SSIS integrációs modul testreszabásáról, folytassa a következő cikkel: 

> [!div class="nextstepaction"]
> [Azure-SSIS IR testreszabása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)