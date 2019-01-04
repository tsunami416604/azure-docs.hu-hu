---
title: Az Azure-SSIS integrációs modul létrehozása az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Azure-SSIS integrációs modul az Azure Data Factory üzembe helyezése és SSIS-csomagok futtatása az Azure-ban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: b772aa314316906a4079e3d6b8b4e0aeb0f54fba
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022988"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Az Azure-SSIS integrációs modul létrehozása az Azure Data Factoryban
Ez a cikk lépéseit a kiépítési Azure-SSIS integrációs modul (IR) az Azure Data Factory (ADF). Ezt követően használhatja az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) üzembe helyezéséhez és futtatásához az SQL Server Integration Services (SSIS) csomagokat a ezt az integrációs modult az Azure-ban. 

A [oktatóanyag: SSIS csomagok üzembe helyezése az Azure-bA](tutorial-create-azure-ssis-runtime-portal.md) bemutatja, hogyan hozhat létre Azure-SSIS integrációs modul gazdagép SSIS-katalógusadatbázist (SSISDB) az Azure SQL Database-kiszolgáló használatával. Ez a cikk az oktatóanyagon alapul, és bemutatja, hogyan tegye a következőket: 

- Igény szerint használhatja az Azure SQL Database-kiszolgáló virtuális hálózati szolgáltatás végpontok/Managed Instance SSISDB-gazdagépre. Gazdagépre SSISDB adatbázis-kiszolgáló típusú megválasztásához útmutatásért lásd: [hasonlítsa össze az Azure SQL Database-kiszolgáló és a felügyelt példány](create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance). Előfeltételként kell az Azure-SSIS integrációs modul csatlakoztatása virtuális hálózat és a virtuális hálózat engedélyeinek/beállításainak konfigurálásához szükség szerint. Lásd: [egy virtuális hálózathoz csatlakozzon Azure-SSIS integrációs](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

- Igény szerint használata az Azure Active Directory (AAD) hitelesítés a felügyelt identitást az ADF az adatbázis-kiszolgálóhoz való csatlakozáshoz. Előfeltételként kell hozzáadni a felügyelt identitás számára az ADF tartalmazottadatbázis-felhasználó hozhat létre az SSISDB a az Azure SQL Database server/Managed Instance, lásd: [Azure-SSIS integrációs modul engedélyezése AAD-hitelesítését](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

## <a name="overview"></a>Áttekintés
Ez a cikk az üzembe helyezési Azure-SSIS integrációs modul különböző módszert mutat be: 

- [Azure Portal](#azure-portal) 
- [Azure PowerShell](#azure-powershell) 
- [Azure Resource Manager-sablon](#azure-resource-manager-template) 

Az Azure-SSIS integrációs modul létrehozásakor ADF szolgáltatás csatlakozik az Azure SQL Database server/Managed Instance készíti elő az SSISDB. Emellett a virtuális hálózat engedélyeinek/beállításainak konfigurálása, ha meg van adva, és csatlakoztatja a virtuális hálózat az Azure-SSIS integrációs modul. 

Amikor üzembe helyezi az Azure-SSIS integrációs modul, a az Azure Feature Pack for SSIS és az Access Redistributable is telepítve lesz. Ezek az összetevők biztosítják a kapcsolatot az Excel vagy fájlokat és a különböző Azure-adatforrásokhoz a beépített összetevők által támogatott adatforrások mellett. Ezenkívül további összetevőket is telepíthet. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md). 

## <a name="prerequisites"></a>Előfeltételek 
- **Azure-előfizetés**. Ha Ön még nem rendelkezik előfizetéssel, létrehozhat egy [az ingyenes próbaidőszak](https://azure.microsoft.com/pricing/free-trial/) fiókot. 

- **Az Azure SQL Database-kiszolgálóhoz vagy a felügyelt példány**. Ha Ön még nem rendelkezik adatbázis-kiszolgáló, a Kezdés előtt létrehozhat egyet az Azure Portalon. Ez a kiszolgáló SSISDB fogja futtatni. Javasoljuk, hogy az adatbázis-kiszolgáló ugyanabban a régióban az Azure, az integration runtime hozza létre. Ez a konfiguráció lehetővé teszi, hogy az integrációs modul Azure-régiók határainak átlépése nélkül írjon végrehajtási naplókat SSISDB. A kiválasztott adatbázis-kiszolgáló alapján, SSISDB létrehozhatók az Ön nevében önálló adatbázisként része, egy rugalmas készlet vagy a felügyelt példány és elérhető-e nyilvános hálózaton vagy virtuális hálózatokhoz való csatlakozás. Az Azure SQL Database támogatott tarifacsomagok listáját lásd: [SQL Database erőforrás-korlátozások](../sql-database/sql-database-resource-limits.md). 

    Győződjön meg arról, hogy az Azure SQL Database server/Managed Instance még nem rendelkezik az SSISDB. Az Azure-SSIS IR üzembe helyezése nem támogatja egy meglévő SSISDB használatával. 

- **Az Azure Resource Managerbeli virtuális hálózat (nem kötelező)**. Rendelkeznie kell egy Azure Resource Managerbeli virtuális hálózat legalább egy, az alábbi feltételek teljesülése esetén: 
    - A virtuális hálózati Szolgáltatásvégpontok Azure SQL Database-kiszolgáló vagy a felügyelt példányt, amely egy virtuális hálózatban lévő üzemeltetett SSISDB. 
    - Szeretné csatlakoztatni a helyszíni adatokat tárolja az SSIS-csomagokat futtat az Azure-SSIS integrációs modult. 

- **Azure PowerShell**. Kövesse az [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/install-azurerm-ps), ha szeretne futtatni egy PowerShell-parancsprogram üzembe helyezni az Azure-SSIS integrációs modult. 

### <a name="region-support"></a>Régió támogatása
Az Azure-régióban, amelyben az ADF és Azure-SSIS integrációs modul jelenleg érhető el, lásd: [ADF + SSIS integrációs modul rendelkezésre állása régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

### <a name="compare-sql-database-logical-server-and-sql-database-managed-instance"></a>Hasonlítsa össze az SQL Database logikai kiszolgáló és az SQL Database felügyelt példánya

A következő táblázat összehasonlítja a bizonyos funkciók az Azure SQL Database-kiszolgáló és a felügyelt példányt az Azure-SSIR integrációs modul valamelyikéhez kapcsolódnak:

| Szolgáltatás | Azure SQL Database-kiszolgáló| Felügyelt példány |
|---------|--------------|------------------|
| **Ütemezés** | SQL Server-ügynök nem érhető el.<br/><br/>Lásd: [ADF folyamat egy csomag végrehajtásának ütemezése](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Felügyelt példány az ügynök nem érhető el. |
| **Hitelesítés** | SSISDB egy tartalmazottadatbázis-felhasználó jelölő az ADF azokat a felügyelt identitáshoz bármely AAD csoport tagjaként hozhatja létre a **db_owner** szerepkör.<br/><br/>Lásd: [SSISDB létrehozása az Azure SQL Database-kiszolgálóhoz történő hitelesítés engedélyezése az Azure AD](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Az ADF-felügyelt identitásnak jelölő tartalmazottadatbázis-felhasználó az SSISDB hozhat létre. <br/><br/>Lásd: [SSISDB létrehozása az Azure SQL Database felügyelt példányába történő hitelesítés engedélyezése az Azure AD](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Szolgáltatásszint** | Az Azure-SSIS integrációs modul az Azure SQL Database-kiszolgálóval való létrehozásakor kiválaszthatja a szolgáltatási rétegben az SSISDB. Nincsenek több szolgáltatásszinttel. | A felügyelt példány az Azure-SSIS integrációs modul létrehozásakor, a szolgáltatási rétegben SSISDB nem lehet kiválasztani. A felügyelt példány összes adatbázisának ossza meg ehhez a példányhoz lefoglalt ugyanarra az erőforrásra. |
| **Virtuális hálózat** | Csak az Azure Resource Manager virtuális hálózatot az Azure-SSIS integrációs csatlakozni, ha a virtuális hálózati Szolgáltatásvégpontok Azure SQL Database-kiszolgáló használata, vagy hozzáférést igényelnek a helyszíni adattárak támogatja. | Támogatja a csak az Azure Resource Managerbeli virtuális hálózat számára az Azure-SSIS integrációs modul csatlakozni. A virtuális hálózat mindig szükség.<br/><br/>Ha az Azure-SSIS integrációs modul csatlakoztatása a felügyelt példány ugyanazon a virtuális hálózaton, ellenőrizze, hogy az Azure-SSIS integrációs modul egy másik alhálózatot, mint a felügyelt példányhoz. Ha egy másik virtuális hálózatot az Azure-SSIS integrációs modul csatlakoztatása, mint a felügyelt példány, ajánlott egy virtuális hálózati társviszony-létesítés vagy egy virtuális hálózat virtuális hálózati kapcsolat. Lásd: [alkalmazását az Azure SQL Database felügyelt példányába való csatlakozás](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Elosztott tranzakciók** | Rugalmas tranzakciók keresztül támogatja. A Microsoft elosztott tranzakciók koordinátora (MSDTC) tranzakciók nem támogatottak. Ha az SSIS-csomagok az elosztott tranzakciók koordinálására MSDTC használja, fontolja meg az Azure SQL Database rugalmas tranzakciók-ba való migrálás. További információ: [elosztott tranzakciók több felhőalapú adatbázisban](../sql-database/sql-database-elastic-transactions-overview.md). | Nem támogatott. |
| | | |

## <a name="azure-portal"></a>Azure Portal
Ebben a szakaszban használhatja az Azure portal, kifejezetten ADF felhasználói felületének (UI) / alkalmazás létrehozása az Azure-SSIS integrációs modult. 

### <a name="create-a-data-factory"></a>Data factory létrehozása 
1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott. 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). 
1. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 

   ![New (Új)->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Az **Új data factory** lapon, a **Név** mezőben adja meg a következőt: **MyAzureSsisDataFactory**. 

   ![Új adat-előállító lap](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a következő hibaüzenetet kapja, változtassa meg az adat-előállító nevét (például sajátneveMyAzureSsisDataFactoryra), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezésére vonatkozó részleteket a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja. 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
1. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét: 

   - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
   - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét. 

   Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket. 

1. A **Verzió** résznél válassza a **V2** értéket. 
1. Válassza ki a Data Factory **helyét**. A listában csak az adat-előállítók létrehozását támogató helyek jelennek meg. 
1. Válassza a **Rögzítés az irányítópulton** lehetőséget. 
1. Kattintson a **Create** (Létrehozás) gombra. 
1. Az irányítópulton a következő állapotleírás látható: **Data factory üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg. 

    ![Data factory kezdőlap](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. A Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul üzembe helyezése 
1. Az első lépések oldalán kattintson az **SSIS integrációs modul konfigurálása** csempére. 

   ![SSIS integrációs modul konfigurálása csempe](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

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

    c. A **Katalógus adatbázis-kiszolgáló végpontja** mezőben válassza az SSISDB-t üzemeltető adatbázis-kiszolgáló végpontját. A kiválasztott adatbázis-kiszolgáló alapján az SSISDB létrehozható az Ön nevében önálló adatbázisként, egy rugalmas készlet részeként vagy egy felügyelt példányban, és elérhető nyilvános hálózatban vagy egy virtuális hálózathoz csatlakozva. 

    d. A **használata AAD-hitelesítés...**  jelölőnégyzetet, válassza ki az SSISDB-gazdagépre adatbázis-kiszolgálóhoz tartozó hitelesítési módszer: SQL- vagy Azure Active Directory (AAD) az Azure Data Factory azokat a felügyelt identitáshoz. Ha bejelöli, adja hozzá a felügyelt identitás számára az ADF egy AAD csoport az adatbázis-kiszolgáló a hozzáférési engedélyek, olvassa el kell [Azure-SSIS integrációs modul engedélyezése AAD-hitelesítését](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    e. A **Rendszergazdai felhasználónév** értékeként adja meg az SSISDB-t üzemeltető adatbázis-kiszolgálóhoz tartozó SQL-hitelesítési felhasználónevet. 

    f. A **Rendszergazdai jelszó** értékeként adja meg az SSISDB-t üzemeltető adatbázis-kiszolgálóhoz tartozó SQL-hitelesítési jelszót. 

    g. A **katalógus adatbázisokra vonatkozó szolgáltatási szint**, válassza ki a szolgáltatási rétegben, a gazdagép SSISDB adatbázis-kiszolgáló: Alapszintű/Standard/prémium szintű vagy a rugalmas készlet neve. 

    h. Kattintson a **Kapcsolat tesztelése** lehetőségre, és ha sikerrel járt, kattintson a **Tovább** gombra. 

1.  A **Speciális beállítások** oldalon végezze el a következő lépéseket: 

    ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. A **Párhuzamos végrehajtások maximális száma csomópontonként** mezőben választhatja ki az integrációsmodul-fürt egy csomópontján egyszerre végrehajtható csomagok maximális számát. Csak a támogatott csomagszámok jelennek meg. Válassza ki a kevés, ha azt szeretné, egynél több mag használatával futtassa egy egyetlen nagy/aktív csomagot, amely számítási és memória-intenzív. A magas értéket akkor válassza, ha egy magon szeretné kicsi/nem nagy igényű csomagot vagy csomagokat futtatni. 

    b. Az **Egyéni beállítástároló SAS URI-ja** mezőben opcionálisan megadható az ahhoz az Azure Storage Blob tárolóhoz tartozó közös hozzáférésű jogosultságkód (SAS) egyedi erőforrás-azonosító (URI), ahol a beállítás szkriptjét és az ahhoz társuló fájlokat tárolja, lásd: [Az Azure SSIS IR egyéni beállítása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

1. A **virtuális hálózat kiválasztása...**  jelölőnégyzetet, adja meg, hogy az integrációs modul virtuális hálózathoz csatlakozni szeretne. Ellenőrizze, ha az Azure SQL Database használata a virtuális hálózati szolgáltatás végpontok/Managed Instance SSISDB-gazdagépre vagy hozzáférést igényelnek a helyszíni adatokhoz; azt jelenti, az SSIS-csomagok rendelkezik helyszíni adatforrásokkal/célokkal, lásd: [egy virtuális hálózathoz csatlakozzon Azure-SSIS integrációs](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Ha bejelöli, kövesse az alábbi lépéseket: 

   ![Virtuális hálózat speciális beállításai](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. A **előfizetés**, válassza ki az Azure-előfizetést, amely a virtuális hálózat rendelkezik. 

    b. A **hely**, az integrációs modullal megegyező helyen van kiválasztva. 

    c. A **típus**, válassza ki a virtuális hálózat típusát: Klasszikus vagy az Azure Resource Manager. Azt javasoljuk, hogy, válassza ki az Azure Resource Manager virtuális hálózatot, mert klasszikus virtuális hálózat hamarosan el fognak évülni. 

    d. A **virtuális hálózat neve**, válassza ki a virtuális hálózat nevét. Ez a virtuális hálózat az Azure SQL Database virtuális hálózati szolgáltatás végpontok/Managed Instance-üzemeltetéséhez használt SSISDB és vagy a helyszíni hálózatához csatlakoztatott egy ugyanazon virtuális hálózaton kell lennie. 

    e. A **Alhálózatnév**, válassza ki a virtuális hálózat alhálózat nevét. Egy másik alhálózatot, mint a használt felügyelt példányt az SSISDB kell lennie. 

1. Kattintson a **virtuális hálózat érvényesítési** , és ha sikeres, kattintson a **Befejezés** az Azure-SSIS integrációs modul létrehozásának elindításához. 

    > [!IMPORTANT]
    > - Ez a folyamat mintegy 20 – 30 percet vesz igénybe.
    > - A Data Factory szolgáltatás csatlakozik az Azure SQL Database-hez, és előkészíti az SSIS-katalógusadatbázist (SSISDB). Emellett konfigurálja az engedélyeit és a virtuális hálózat beállításait, ha meg van adva, és csatlakoztatja a virtuális hálózat az Azure-SSIS integrációs modul új példányát. 

1. A **Kapcsolatok** ablakban váltson az **Integrációs modulok** lapra, ha szükséges. Az állapot frissítéséhez kattintson a **Frissítés** elemre. 

   ![Létrehozás állapota](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. A hivatkozások **műveletek** leállításához/elindításához, szerkesztéséhez vagy törléséhez az integrációs modul oszlopot. Az integrációs modulhoz tartozó JSON-kód megtekintéséhez használja a legutolsó hivatkozást. A szerkesztés és törlés gombok csak akkor használhatók, ha az integrációs modul le van állítva. 

   ![Azure SSIS integrációs modul – műveletek](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS integrációs modulok a portálon
1. Az Azure Data Factory felhasználói felületén váltson a **szerkesztési** lapra, kattintson a **Kapcsolatok** lehetőségre, majd váltson az **integrációs modulok** lapjára az adat-előállítójában megtalálható integrációs modulok megtekintéséhez. 

   ![Létező integrációs modulok megtekintése](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Egy új Azure-SSIS integrációs modul létrehozásához kattintson az **Új** elemre. 

   ![Integrációs modul elérése a menüből](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Egy Azure-SSIS integrációs modul létrehozásához kattintson az **Új** gombra a képen látható módon. 
1. Az Integrációs modul telepítése ablakban válassza a **Létező SSIS-csomagok áthelyezése az Azure-ban történő végrehajtáshoz** lehetőséget, majd kattintson a **Tovább** gombra. 

   ![Integrációs modulok típusának megadása](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Az Azure-SSIS integrációs modul beállításához szükséges lépésekkel kapcsolatos információkért tekintse meg az [Azure SSIS integrációs modul üzembe helyezésével](#provision-an-azure-ssis-integration-runtime) foglalkozó részt. 

## <a name="azure-powershell"></a>Azure PowerShell
Ebben a szakaszban az Azure PowerShell használatával hozzon létre egy Azure-SSIS integrációs modult.

### <a name="create-variables"></a>Változók létrehozása
Ebben az oktatóanyagban változókat határozhat meg a szkriptben való használatra:

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS" 
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

### <a name="log-in-and-select-subscription"></a>Bejelentkezés és az előfizetés kiválasztása
A következő kódot a szkripthez a bejelentkezéshez, és válassza ki az Azure-előfizetés hozzáadása: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Az adatbázissal való kapcsolat ellenőrzése
Adja hozzá a következő szkriptet az Azure SQL Database kiszolgálói végpontot. 

```powershell
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}
```

### <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása
Adja hozzá a következő szkriptet a virtuális hálózat engedélyeinek/beállításainak automatikus konfigurálásához, hogy az Azure SSIS integrációs modul csatlakozni tudjon.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Data factory létrehozása
Futtassa a következő parancsot egy adat-előállító létrehozásához.

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Integrációs modul létrehozása
Futtassa a következő parancsok futtatásával hozzon létre egy Azure-SSIS integrációs modult, amely SSIS-csomagokat futtat az Azure-ban. 

Nem Azure SQL Database használata a virtuális hálózati szolgáltatás végpontok/Managed Instance SSISDB-gazdagépre, és hozzáférést igényelnek a helyszíni adatokhoz, ha kihagyja VNetId és alhálózati paramétereket, vagy üres értékeket adja át a számukra. Ellenkező esetben nem hagyja ki őket, és kell az érvényes értékek adhatók át a virtuális hálózati konfiguráció, lásd: [egy virtuális hálózathoz csatlakozzon Azure-SSIS integrációs](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

Felügyelt példány gazdagép SSISDB használatakor CatalogPricingTier paraméter nincs megadva, vagy adja át az üres érték azt. Ellenkező esetben nem szereplejen és kell adja át egy érvényes értéket a listából tarifacsomagok az Azure SQL Database támogatott, lásd: [SQL Database erőforrás-korlátozások](../sql-database/sql-database-resource-limits.md). 

Ha a felügyelt identitás, az Azure Data Factory az Azure Active Directory (AAD) hitelesítés használata az adatbázis-kiszolgálóhoz való csatlakozáshoz, CatalogAdminCredential paramétert, akkor kihagyhatja, de fel kell vennie a felügyelt identitás az ADF-AAD csoportba az az adatbázis-kiszolgálón történő hozzáférési engedélyek, lásd: [Azure-SSIS integrációs modul engedélyezése AAD-hitelesítését](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Ellenkező esetben nem hagyja, és át kell adnia egy érvényes, a kiszolgáló-rendszergazdai felhasználónevet és jelszót az SQL-hitelesítéshez megfelelő objektum.

```powershell               
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

# Add CatalogAdminCredential parameter when you do not use AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}
```

### <a name="start-integration-runtime"></a>Integrációs modul indítása
Futtassa a következő parancsot az Azure SSIS integrációs modul indításához: 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

A parancs végrehajtása **20–30 percig** is eltarthat. 

### <a name="full-script"></a>Teljes szkript
Itt látható a teljes szkript, amely létrehoz egy Azure-SSIS integrációs modult. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS" 
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"

### Log in and select subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

# Add CatalogAdminCredential parameter when you do not use AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}

### Start integration runtime   
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Ebben a szakaszban használhatja az Azure Resource Manager-sablon létrehozása az Azure-SSIS integrációs modul. Íme egy példa forgatókönyv: 

1. Hozzon létre egy JSON-fájlt a következő Azure Resource Manager-sablont. Cserélje le a csúcsos zárójelek közé (hely jogosultak) értékeket a saját értékeire. 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
    
1. Az Azure Resource Manager-sablon üzembe helyezéséhez futtassa a New-AzureRmResourceGroupDeployment parancs, ahol a ADFTutorialResourceGroup az erőforráscsoport neve, és a ADFTutorialARM.json JSON tartalmazó fájl a következő példában látható módon a definíció az adat-előállító és az Azure-SSIS integrációs modult. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Ez a parancs létrehozza a data factory és az Azure-SSIS integrációs modul benne, de nem indul el az integrációs 

1. Az Azure-SSIS integrációs modul indításához futtassa a Start-AzureRmDataFactoryV2IntegrationRuntime parancsot: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ``` 

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése
Most az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) segítségével helyezze üzembe az SSIS-csomagokat az Azure-ban. Csatlakozás az adatbázis-kiszolgálóhoz, amelyen az SSIS-katalógus (SSISDB). Adatbázis-kiszolgáló neve a következő formátumban van: &lt;Az Azure SQL Database-kiszolgálónév&gt;. database.windows.net vagy &lt;felügyelt példány neve. DNS-előtag&gt;. database.windows.net. Útmutatásért tekintse meg a [csomagok üzembe helyezésével kapcsolatos](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) cikket. 

## <a name="next-steps"></a>További lépések
Ebben a dokumentációban a többi Azure-SSIS integrációs modul témakörök: 

- [Az Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk tájékoztatást integrációs modulok általában többek között az Azure-SSIS integrációs modult. 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutathatja, hogyan skálázhatja fel horizontálisan az Azure-SSIS integrációs modult úgy, hogy további csomópontokat ad hozzá. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk tájékoztatást az Azure-SSIS integrációs modul Azure-beli virtuális hálózathoz történő csatlakoztatásához. Azt is ismerteti, hogyan használható az Azure Portal a virtuális hálózat oly módon való konfigurálására, hogy az Azure-SSIS integrációs modul csatlakozhasson ahhoz. 
