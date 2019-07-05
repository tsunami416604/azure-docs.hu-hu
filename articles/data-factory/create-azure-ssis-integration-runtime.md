---
title: Az Azure-SSIS integrációs modul létrehozása az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Azure-SSIS integrációs modul az Azure Data Factory üzembe helyezése és SSIS-csomagok futtatása az Azure-ban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: fb5335c8dfd94006ba3f0d8d6b890869dd9f3717
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484832"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Az Azure-SSIS integrációs modul létrehozása az Azure Data Factoryban

Ez az oktatóanyag lépéseit egy Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) az Azure Data Factory (ADF) telepítéshez. Az Azure-SSIS integrációs modul által támogatott csomagok üzembe helyezve az Azure SQL Database-kiszolgáló/Managed Instance (projekt üzembe helyezési modell) által üzemeltetett SSIS-katalógus (SSISDB) és mások által üzembe helyezett fájl rendszerek/fájl futtatása megosztások vagy az Azure Files (csomag üzembe helyezési modell). Miután az Azure-SSIS integrációs modul üzembe felhasználhatja ismert eszközökkel, például az SQL Server Data Tools (SSDT) / SQL Server Management Studio (SSMS), és a parancs parancssori segédprogramok, például `dtinstall` / `dtutil` / `dtexec`, az üzembe helyezése és az-csomagok futtatása az Azure-ban.

A [oktatóanyag: Az Azure-SSIS integrációs modul kiépítés](tutorial-create-azure-ssis-runtime-portal.md) bemutatja, hogyan hozhat létre egy Azure-SSIS integrációs modul keresztül az Azure portal/ADF-alkalmazást, és igény szerint használhatja az Azure SQL Database server/Managed Instance SSISDB-gazdagépre. Ez a cikk az oktatóanyagon alapul, és bemutatja, hogyan tegye a következőket:

- Igény szerint használhatja az Azure SQL Database-kiszolgáló virtuális hálózati szolgáltatás végpontok/felügyelt példányt az SSISDB-gazdagépre a virtuális hálózat. Előfeltételként szeretne csatlakozni egy virtuális hálózatot az Azure-SSIS integrációs virtuális hálózat engedélyeinek/beállításainak konfigurálása.

- Szükség esetén azokat a felügyelt identitáshoz tartozó az ADF Azure Active Directory (AAD) hitelesítés használatával kapcsolódhat az Azure SQL Database server/Managed Instance. Előfeltételként kell hozzáadni a felügyelt identitás számára az ADF hozhat létre az SSISDB adatbázis-felhasználó.

## <a name="overview"></a>Áttekintés

Ez a cikk az üzembe helyezési Azure-SSIS integrációs modul különböző módszert mutat be:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager-sablon](#azure-resource-manager-template)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha Ön még nem rendelkezik előfizetéssel, létrehozhat egy [az ingyenes próbaidőszak](https://azure.microsoft.com/pricing/free-trial/) fiókot.
- **Az Azure SQL Database server/Managed Instance (nem kötelező)** . Ha Ön még nem rendelkezik adatbázis-kiszolgáló, hozzon létre egyet az Azure Portalon, a Kezdés előtt. Az ADF pedig SSISDB hoz létre az adatbázis-kiszolgálón. Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi, hogy az integrációs modul az SSISDB, az Azure-régiók határainak átlépése nélkül írjon végrehajtási naplókat. 
    - A kiválasztott adatbázis-kiszolgáló alapján az SSISDB létrehozható az Ön nevében önálló adatbázisként, egy rugalmas készlet részeként vagy egy felügyelt példányban, és elérhető nyilvános hálózatban vagy egy virtuális hálózathoz csatlakozva. Gazdagépre SSISDB adatbázis-kiszolgáló típusú megválasztásához útmutatásért lásd: [hasonlítsa össze az Azure SQL Database egy adatbázis és rugalmas készlet/Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Ha használatával Azure SQL Database-kiszolgáló virtuális hálózati szolgáltatás végpontok/Managed Instance egy virtuális hálózaton az SSISDB üzemeltetésére, illetve hozzáférést igényelnek a helyszíni adatokhoz, szeretné-e az Azure-SSIS integrációs modul csatlakoztatása egy virtuális hálózatot, lásd: [való csatlakozás az Azure-SSIS integrációs modul egy virtuális hálózat](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
    - Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás engedélyezve van az adatbázis-kiszolgálón. Ez a nem alkalmazható virtuális hálózati szolgáltatás végpontok/felügyelt példányt az SSISDB-gazdagépre a virtuális hálózat az Azure SQL Database-kiszolgáló használata esetén. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Ezzel a beállítással engedélyezheti a PowerShell-lel [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Adja hozzá az ügyfélszámítógép IP-címét, vagy egy IP-címtartományt, amely tartalmazza az ügyfélszámítógépen, hogy az ügyfél IP-címeinek listájára az adatbázis-kiszolgáló tűzfalbeállításainál IP-címét. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../sql-database/sql-database-firewall-configure.md).
    - Az adatbázis-kiszolgáló használatával a kiszolgáló-rendszergazdai hitelesítő adataival SQL-hitelesítés vagy az Azure Active Directory (AAD) hitelesítés a felügyelt identitás az ADF csatlakozhat.  Az utóbbi esetében van szükség egy AAD csoportjához való hozzáférési engedélyeket az adatbázis-kiszolgáló hozzáadása a felügyelt identitás az ADF, lásd: [Azure-SSIS integrációs modul engedélyezése AAD-hitelesítését](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
    - Győződjön meg arról, hogy az adatbázis-kiszolgáló nem rendelkezik az SSISDB már. Egy Azure-SSIS IR üzembe helyezése nem támogatja egy meglévő SSISDB használatával.
- **Az Azure Resource Managerbeli virtuális hálózat (nem kötelező)** . Rendelkeznie kell egy Azure Resource Managerbeli virtuális hálózat legalább egy, az alábbi feltételek teljesülése esetén:
    - Az Azure SQL Database-kiszolgáló virtuális hálózati szolgáltatás végpontok/Managed Instance egy virtuális hálózaton az SSISDB üzemelteti.
    - Szeretné csatlakoztatni a helyszíni adatokat tárolja az SSIS-csomagokat futtat az Azure-SSIS integrációs modult.
- **(Nem kötelező) az Azure PowerShell**. Kövesse az [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/install-az-ps), ha szeretne futtatni egy PowerShell-parancsprogram üzembe helyezése az Azure-SSIS integrációs modult.

### <a name="region-support"></a>Régió támogatása

Az Azure-régióban, amelyben az ADF és Azure-SSIS integrációs modul jelenleg érhető el, lásd: [ADF + SSIS integrációs modul rendelkezésre állása régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>Hasonlítsa össze az SQL Database egy adatbázis és rugalmas készlet és az SQL Database felügyelt példánya

A következő táblázat összehasonlítja a bizonyos funkciók az Azure SQL Database-kiszolgáló és a felügyelt példányt az Azure-SSIR integrációs modul valamelyikéhez kapcsolódnak:

| Funkció | önálló adatbázis és rugalmas készlet| Felügyelt példány |
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

2. Az **Integrációs modul beállításai** lap **Általános beállítások** oldalán végezze el az alábbi lépéseket:

   ![Általános beállítások](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. A **Név** mezőben adja meg az integrációs modul nevét.

    b. A **Leírás** mezőben adja meg az integrációs modul leírását.

    c. A **Hely** mezőben válassza ki az integrációs modul helyét. Csak a támogatott helyek jelennek meg. Javasoljuk, hogy az SSISDB-t üzemeltető adatbázis-kiszolgálóval megegyező helyet válasszon.

    d. A **Csomópontméret** mezőben válassza ki az integrációsmodul-fürtön lévő csomópont méretét. Csak a támogatott csomópontméretek jelennek meg. Ha sok nagy számítási teljesítményt/sok memóriát igénylő csomagot szeretne futtatni, válasszon nagy csomópontméretet (vertikális felskálázás).

    e. A **Csomópontszám** mezőben adja meg az integrációsmodul-fürtben található csomópontok számát. Csak a támogatott csomópontszámok jelennek meg. Ha sok csomagot szeretne párhuzamosan futtatni, válasszon egy nagy fürtöt sok csomóponttal (horizontális felskálázás).

    f. A **Edition-licencre**, válassza ki az SQL Server/licenccel az integrációs modullal: Standard vagy Enterprise. Ha szeretne fejlett/prémium szolgáltatásokat használni az integrációs modulon, válassza az Enterprise verziót.

    g. A **mentése pénzt**, válassza ki az integrációs modul az Azure Hybrid Benefit (AHB) lehetőséget: igen vagy nem. Akkor válassza az Igen lehetőséget, ha a saját, szoftverbiztosítással ellátott SQL Server-licencét szeretné használni, hogy élhessen a hibrid használatból fakadó költségcsökkentéssel.

    h. Kattintson a **Tovább** gombra.

3. Az **SQL-beállítások** oldalon végezze el a következő lépéseket:

   ![SQL-beállítások](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. A **létrehozása SSIS-katalógus...**  jelölőnégyzetet, válassza ki az üzembe helyezési modell futtathatók az Azure-SSIS integrációs csomagok: Projekt üzembe helyezési modell, a csomagok telepítve vannak az SSISDB adatbázis-kiszolgáló által üzemeltetett vagy a csomag üzembe helyezési modell, a csomagok telepítve vannak a fájl rendszerek/fájl megosztások/Azure-bA fájlok. Ha bejelöli, szüksége lesz ahhoz, hogy a saját adatbázis-kiszolgáló gazdagépre SSISDB, hogy a rendszer létrehoz és kezelheti az Ön nevében.
   
    b. Az **Előfizetés** mezőben válassza azt az Azure-előfizetést, amelyikkel az adatbázis-kiszolgáló üzemelteti az SSISDB-t. 

    c. A **Hely** mezőben válassza ki az SSISDB-t üzemeltető adatbázis-kiszolgáló helyét. Javasoljuk, hogy az integrációs modullal megegyező helyet válasszon. 

    d. A **Katalógus adatbázis-kiszolgáló végpontja** mezőben válassza az SSISDB-t üzemeltető adatbázis-kiszolgáló végpontját. A kiválasztott adatbázis-kiszolgáló alapján az SSISDB létrehozható az Ön nevében önálló adatbázisként, egy rugalmas készlet részeként vagy egy felügyelt példányban, és elérhető nyilvános hálózatban vagy egy virtuális hálózathoz csatlakozva. Gazdagépre SSISDB adatbázis-kiszolgáló típusú megválasztásához útmutatásért lásd: [hasonlítsa össze az Azure SQL Database egy adatbázis és rugalmas készlet/Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Ha egy virtuális hálózatot az SSISDB üzemeltetésére, illetve hozzáférést igényelnek a helyszíni adatok Azure SQL Database-kiszolgáló virtuális hálózati szolgáltatás végpontok/Managed Instance-választ ki, szeretné-e az Azure-SSIS integrációs modul csatlakoztatása egy virtuális hálózatot, lásd: [való csatlakozás Azure-SSIS integrációs modul egy virtuális hálózathoz](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    e. A **használata AAD-hitelesítés...**  jelölőnégyzetet, válassza ki az SSISDB-gazdagépre adatbázis-kiszolgálóhoz tartozó hitelesítési módszer: SQL-hitelesítés vagy az ADF AAD-hitelesítését a felügyelt identitáshoz. Ha bejelöli, adja hozzá a felügyelt identitás számára az ADF egy hozzáférési engedélyeket az adatbázis-kiszolgálóhoz az AAD csoportjához, olvassa el kell [Azure-SSIS integrációs modul engedélyezése AAD-hitelesítését](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    f. A **Rendszergazdai felhasználónév** értékeként adja meg az SSISDB-t üzemeltető adatbázis-kiszolgálóhoz tartozó SQL-hitelesítési felhasználónevet. 

    g. A **Rendszergazdai jelszó** értékeként adja meg az SSISDB-t üzemeltető adatbázis-kiszolgálóhoz tartozó SQL-hitelesítési jelszót. 

    h. A **katalógus adatbázisokra vonatkozó szolgáltatási szint**, válassza ki a szolgáltatási rétegben, a gazdagép SSISDB adatbázis-kiszolgáló: Alapszintű/Standard/prémium szintű vagy a rugalmas készlet neve. 

    i. Kattintson a **Kapcsolat tesztelése** lehetőségre, és ha sikerrel járt, kattintson a **Tovább** gombra. 

4. A **Speciális beállítások** oldalon végezze el a következő lépéseket:

    ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. A **Párhuzamos végrehajtások maximális száma csomópontonként** mezőben választhatja ki az integrációsmodul-fürt egy csomópontján egyszerre végrehajtható csomagok maximális számát. Csak a támogatott csomagszámok jelennek meg. Válassza ki a kevés, ha azt szeretné, egynél több mag használatával futtassa egy egyetlen nagy/aktív csomagot, amely számítási és memória-intenzív. A magas értéket akkor válassza, ha egy magon szeretné kicsi/nem nagy igényű csomagot vagy csomagokat futtatni.

    b. Az **Egyéni beállítástároló SAS URI-ja** mezőben opcionálisan megadható az ahhoz az Azure Storage Blob tárolóhoz tartozó közös hozzáférésű jogosultságkód (SAS) egyedi erőforrás-azonosító (URI), ahol a beállítás szkriptjét és az ahhoz társuló fájlokat tárolja, lásd: [Az Azure SSIS IR egyéni beállítása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. A **virtuális hálózat kiválasztása...**  jelölőnégyzetet, adja meg, hogy az integrációs modul virtuális hálózathoz csatlakozni szeretne. Ellenőrizze, ha az Azure SQL Database-kiszolgáló használata a virtuális hálózati szolgáltatás végpontok/felügyelt példányt az SSISDB-gazdagépre a virtuális hálózat vagy hozzáférést igényelnek a helyszíni adatokhoz; azt jelenti, az SSIS-csomagok rendelkezik helyszíni adatforrásokkal/célokkal, lásd: [egy virtuális hálózathoz csatlakozzon Azure-SSIS integrációs](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Ha bejelöli, kövesse az alábbi lépéseket:

   ![Virtuális hálózat speciális beállításai](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. A **előfizetés**, válassza ki az Azure-előfizetést, amely a virtuális hálózat rendelkezik.

    b. A **hely**, az integrációs modullal megegyező helyen van kiválasztva.

    c. A **típus**, válassza ki a virtuális hálózat típusát: Klasszikus vagy az Azure Resource Manager. Azt javasoljuk, hogy, válassza ki az Azure Resource Manager virtuális hálózatot, mert klasszikus virtuális hálózat hamarosan el fognak évülni.

    d. A **virtuális hálózat neve**, válassza ki a virtuális hálózat nevét. Ez a virtuális hálózat egyeznie kell egy virtuális hálózati szolgáltatás végpontok/Managed Instance virtuális hálózatban lévő Azure SQL Database-kiszolgálóhoz készült SSISDB és a egy, a helyszíni hálózatához csatlakoztatott üzemeltetéséhez használt.

    e. A **Alhálózatnév**, válassza ki a virtuális hálózat alhálózat nevét. Ez legyen, mint a használt felügyelt példányt az SSISDB-gazdagépre a virtuális hálózat egy másik alhálózatot.

6. Kattintson a **virtuális hálózat érvényesítési** , és ha sikeres, kattintson a **Befejezés** az Azure-SSIS integrációs modul létrehozásának elindításához.

    > [!NOTE]
    > Minden olyan egyéni beállítási idő, kivéve a folyamat 5 percen belül kell végrehajtani, de az Azure-SSIS integrációs modul virtuális hálózatokhoz való csatlakozás körülbelül 20-30 percig is eltarthat.
    >
    > SSISDB használatakor ADF szolgáltatás az készíti elő az SSISDB adatbázis-kiszolgálóhoz fog csatlakozni. Emellett konfigurálja az engedélyeit és a virtuális hálózat beállításait, ha meg van adva, és csatlakoztatja a virtuális hálózat az Azure-SSIS integrációs modul.
    > 
    > Amikor üzembe helyez egy Azure-SSIS integrációs Modult, Access Redistributable és az Azure Feature Pack for SSIS is telepítve lesz. Ezek az összetevők biztosítják a csatlakozást az Excel és Access-fájlok és a különböző Azure-adatforrásokhoz a beépített összetevők által már támogatott adatforrások mellett. További összetevőket is telepíthet lásd [Azure-SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md).

7. A **Kapcsolatok** ablakban váltson az **Integrációs modulok** lapra, ha szükséges. Az állapot frissítéséhez kattintson a **Frissítés** elemre.

   ![Létrehozás állapota](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. A hivatkozások **műveletek** leállításához/elindításához, szerkesztéséhez vagy törléséhez az integrációs modul oszlopot. Az integrációs modulhoz tartozó JSON-kód megtekintéséhez használja a legutolsó hivatkozást. A szerkesztés és törlés gombok csak akkor használhatók, ha az integrációs modul le van állítva.

   ![Azure SSIS integrációs modul – műveletek](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS integrációs modulok a portálon

1. Az Azure Data Factory felhasználói felületén váltson a **szerkesztési** lapra, kattintson a **Kapcsolatok** lehetőségre, majd váltson az **integrációs modulok** lapjára az adat-előállítójában megtalálható integrációs modulok megtekintéséhez.

   ![Létező integrációs modulok megtekintése](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Egy új Azure-SSIS integrációs modul létrehozásához kattintson az **Új** elemre.

   ![Integrációs modul elérése a menüből](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Egy Azure-SSIS integrációs modul létrehozásához kattintson az **Új** gombra a képen látható módon.

4. Az Integrációs modul telepítése ablakban válassza a **Létező SSIS-csomagok áthelyezése az Azure-ban történő végrehajtáshoz** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Integrációs modulok típusának megadása](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Az Azure-SSIS integrációs modul beállításához szükséges lépésekkel kapcsolatos információkért tekintse meg az [Azure SSIS integrációs modul üzembe helyezésével](#provision-an-azure-ssis-integration-runtime) foglalkozó részt.

## <a name="azure-powershell"></a>Azure PowerShell

Ebben a szakaszban az Azure PowerShell használatával hozzon létre egy Azure-SSIS integrációs modult.

### <a name="create-variables"></a>Változók létrehozása

Másolja és illessze be az alábbi parancsfájlt – adja meg a változók értékeit. 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance in a virtual network/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance in a virtual network

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>Jelentkezzen be, és válassza ki az előfizetést

A következő kódot a szkripthez a bejelentkezéshez, és válassza ki az Azure-előfizetés hozzáadása:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Adatbázis-kiszolgálóhoz való kapcsolat ellenőrzése

Adja hozzá a következő szkriptet az Azure SQL Database server/Managed Instance.

```powershell
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
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
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) használatával a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsot. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat.

Ha az erőforráscsoport már létezik, ne másolja ezt a kódot a szkriptet. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Data factory létrehozása

Futtassa a következő parancsot egy adat-előállító létrehozásához.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Integrációs modul létrehozása

Futtassa a következő parancsok futtatásával hozzon létre egy Azure-SSIS integrációs modult, amely SSIS-csomagokat futtat az Azure-ban.

If you do not use SSISDB, you can omit CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters.

Ha nem az Azure SQL Database-kiszolgáló használata a virtuális hálózati szolgáltatás végpontok/felügyelt példányt az SSISDB-gazdagépre a virtuális hálózat vagy hozzáférést igényelnek a helyszíni adatokhoz, VNetId és alhálózati paraméter nincs megadva, vagy üres értékeket adja át a számukra. Ellenkező esetben nem hagyja ki őket, és kell az érvényes értékek adhatók át a virtuális hálózati konfiguráció, lásd: [egy virtuális hálózathoz csatlakozzon Azure-SSIS integrációs](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Felügyelt példány gazdagép SSISDB használatakor CatalogPricingTier paraméter nincs megadva, vagy adja át az üres érték azt. Ellenkező esetben nem szereplejen és kell adja át egy érvényes értéket a listából tarifacsomagok az Azure SQL Database támogatott, lásd: [SQL Database erőforrás-korlátozások](../sql-database/sql-database-resource-limits.md).

Ha a felügyelt identitás, az ADF az Azure Active Directory (AAD) hitelesítés használata az adatbázis-kiszolgálóhoz való csatlakozáshoz, CatalogAdminCredential paramétert, akkor kihagyhatja, de fel kell vennie a felügyelt identitás az ADF-hozzáféréssel rendelkező AAD csoportjához az adatbázis-kiszolgálóhoz, engedélyek, lásd: [Azure-SSIS integrációs modul engedélyezése AAD-hitelesítését](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Ellenkező esetben nem hagyja, és át kell adnia egy érvényes, a kiszolgáló-rendszergazdai felhasználónevet és jelszót az SQL-hitelesítéshez megfelelő objektum.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

### <a name="start-integration-runtime"></a>Integrációs modul indítása

Futtassa az alábbi parancsokat az Azure-SSIS integrációs modul elindításához.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> Minden olyan egyéni beállítási idő, kivéve a folyamat 5 percen belül kell végrehajtani, de az Azure-SSIS integrációs modul virtuális hálózatokhoz való csatlakozás körülbelül 20-30 percig is eltarthat.
>
> SSISDB használatakor ADF szolgáltatás az készíti elő az SSISDB adatbázis-kiszolgálóhoz fog csatlakozni. Emellett konfigurálja az engedélyeit és a virtuális hálózat beállításait, ha meg van adva, és csatlakoztatja a virtuális hálózat az Azure-SSIS integrációs modul.
> 
> Amikor üzembe helyez egy Azure-SSIS integrációs Modult, Access Redistributable és az Azure Feature Pack for SSIS is telepítve lesz. Ezek az összetevők biztosítják a csatlakozást az Excel és Access-fájlok és a különböző Azure-adatforrásokhoz a beépített összetevők által már támogatott adatforrások mellett. További összetevőket is telepíthet lásd [Azure-SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Teljes szkript

Itt látható a teljes szkript, amely létrehoz egy Azure-SSIS integrációs modult.

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance in a virtual network/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance in a virtual network

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
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
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
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

2. Az Azure Resource Manager-sablon üzembe helyezéséhez futtassa a New-AzResourceGroupDeployment parancsot, ahol ADFTutorialResourceGroup az az erőforráscsoport neve pedig ADFTutorialARM.json JSON-definíciót tartalmazó fájl a következő példában látható módon a data factory és az Azure-SSIS integrációs modult.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Ez a parancs létrehozza a data factory és az Azure-SSIS integrációs modul benne, de nem indul el az integrációs

3. Az Azure-SSIS integrációs modul indításához futtassa a Start-AzDataFactoryV2IntegrationRuntime parancsot:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> Minden olyan egyéni beállítási idő, kivéve a folyamat 5 percen belül kell végrehajtani, de az Azure-SSIS integrációs modul virtuális hálózatokhoz való csatlakozás körülbelül 20-30 percig is eltarthat.
>
> SSISDB használatakor ADF szolgáltatás az készíti elő az SSISDB adatbázis-kiszolgálóhoz fog csatlakozni. Emellett konfigurálja az engedélyeit és a virtuális hálózat beállításait, ha meg van adva, és csatlakoztatja a virtuális hálózat az Azure-SSIS integrációs modul.
> 
> Amikor üzembe helyez egy Azure-SSIS integrációs Modult, Access Redistributable és az Azure Feature Pack for SSIS is telepítve lesz. Ezek az összetevők biztosítják a csatlakozást az Excel és Access-fájlok és a különböző Azure-adatforrásokhoz a beépített összetevők által már támogatott adatforrások mellett. További összetevőket is telepíthet lásd [Azure-SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése

SSISDB használatakor az-csomagok üzembe, és futtathatja az Azure-SSIS integrációs modul, amely az adatbázis-kiszolgálóhoz, a kiszolgálói végpont-n keresztül csatlakozni az SSDT/SSMS eszközök használatával.  Az Azure SQL Database server/Managed Instance az egy virtuális hálózat/Managed instance egy nyilvános végponttal rendelkező, a kiszolgálói végpont formátuma `<server name>.database.windows.net` / `<server name>.<dns prefix>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`, illetve. SSISDB nem használja, ha telepíteni a fájl rendszerek/fájl megosztások/Azure-bA fájlok, és futtassa őket az Azure-SSIS integrációs modul használatával `dtinstall` / `dtutil` / `dtexec` parancssori segédeszközök. További információkért lásd: [üzembe SSIS-csomagok](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Mindkét esetben is futtathatja a telepített csomagokat az Azure-SSIS integrációs Modult az SSIS-csomag végrehajtása tevékenység ADF folyamatokban, lásd: [meghívása SSIS-csomag végrehajtása egy első osztályú ADF tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>További lépések

Lásd még: Ez a dokumentáció az Azure-SSIS integrációs modul témaköröket:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk ismerteti, integrációs modulok általában többek között az Azure-SSIS integrációs modult.
- [Az Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhető le, és megismerheti az információ az Azure-SSIS integrációs modult.
- [Manage Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan leállítása, indítása vagy törlése az Azure-SSIS integrációs modul – azt is bemutatja, hogyan skálázhatja fel horizontálisan a az Azure-SSIS integrációs modul további fürtcsomópontok hozzáadásával.
- [Az Azure-SSIS integrációs modul csatlakoztatása virtuális hálózat](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk ismerteti, az Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz.