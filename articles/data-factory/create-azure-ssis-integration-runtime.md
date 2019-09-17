---
title: Azure-SSIS Integration Runtime létrehozása a Azure Data Factoryban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Azure-SSIS Integration Runtime a Azure Data Factoryban, hogy SSIS-csomagokat lehessen üzembe helyezni és futtatni az Azure-ban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 36cb4d306cd74dcde09fa55fc582a043bc324f65
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010028"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure-SSIS Integration Runtime létrehozása Azure Data Factory

Ez az oktatóanyag a Azure Data Factory (ADF) Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) üzembe helyezésének lépéseit ismerteti. Azure-SSIS IR támogatja a Azure SQL Database kiszolgáló/felügyelt példány (projekt-telepítési modell) által üzemeltetett SSIS-katalógusba (SSISDB) telepített csomagok futtatását, valamint a fájlrendszerek/fájlmegosztás/Azure Files (csomag-telepítési modell) által üzembe helyezett csomagokat. Azure-SSIS IR kiépítés után használhat ismerős eszközöket, például SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) és parancssori segédeszközöket, például `dtinstall` / `dtutil` / `dtexec` a csomagok üzembe helyezése és futtatása az Azure-ban.

Az [oktatóanyag: A kiépítés](tutorial-create-azure-ssis-runtime-portal.md) Azure-SSIS IR bemutatja, hogyan hozhat létre egy Azure-SSIS IR a Azure Portal/ADF-alkalmazáson keresztül, és igény szerint Azure SQL Database-kiszolgáló/felügyelt példányt is használhat a SSISDB futtatásához. Ez a cikk az oktatóanyagon alapul, és bemutatja, hogyan végezheti el a következőket:

- Igény szerint Azure SQL Database-kiszolgálót is használhat virtuális hálózati szolgáltatás végpontokkal/felügyelt példánnyal egy privát végponttal a SSISDB üzemeltetéséhez. Előfeltételként konfigurálnia kell a virtuális hálózati engedélyeket/beállításokat a Azure-SSIS IR számára a virtuális hálózathoz való csatlakozáshoz.

- A Azure Active Directory (HRE) hitelesítést az ADF felügyelt identitásával is használhatja Azure SQL Database kiszolgáló/felügyelt példányhoz való kapcsolódáshoz. Előfeltételként hozzá kell adnia az ADF felügyelt identitását olyan adatbázis-felhasználóként, amely képes SSISDB létrehozására.

- Igény szerint csatlakoztathatja a Azure-SSIS IRt egy virtuális hálózathoz, vagy konfigurálhatja a saját üzemeltetésű integrációs modult proxyként a Azure-SSIS IR számára a helyszíni adateléréshez.

## <a name="overview"></a>Áttekintés

Ez a cikk a Azure-SSIS IR kiépítés különböző módjait mutatja be:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager-sablon](#azure-resource-manager-template)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha még nem rendelkezik előfizetéssel, létrehozhat egy [ingyenes próbaverziós](https://azure.microsoft.com/pricing/free-trial/) fiókot is.
- **Azure SQL Database kiszolgáló/felügyelt példány (nem kötelező)** . Ha még nem rendelkezik adatbázis-kiszolgálóval, hozzon létre egyet a Azure Portal az első lépések megkezdése előtt. Az ADF ekkor létrehozza a SSISDB az adatbázis-kiszolgálón. Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi az integrációs modul írási végrehajtásának naplózását az Azure-régiók SSISDB nélkül. 
    - A kiválasztott adatbázis-kiszolgáló alapján az SSISDB létrehozható az Ön nevében önálló adatbázisként, egy rugalmas készlet részeként vagy egy felügyelt példányban, és elérhető nyilvános hálózatban vagy egy virtuális hálózathoz csatlakozva. Az adatbázis-kiszolgáló SSISDB gazdagépként való kiválasztásával kapcsolatos útmutatásért lásd: [Azure SQL Database önálló adatbázis/rugalmas készlet/felügyelt példány összehasonlítása](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Ha Azure SQL Database-kiszolgálót használ virtuális hálózati szolgáltatásbeli végpontokkal/felügyelt példánnyal egy privát végponttal a SSISDB üzemeltetéséhez, illetve a helyi adatközponthoz való hozzáférés megkövetelését anélkül, hogy saját üzemeltetésű integrációs modult kellene konfigurálnia, csatlakoztatnia kell a Azure-SSIS IRt egy virtuális hálózathoz, Lásd: [csatlakozás Azure-SSIS IR egy virtuális hálózathoz](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
    - Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás engedélyezve van az adatbázis-kiszolgálón. Ez nem alkalmazható, ha Azure SQL Database-kiszolgálót használ virtuális hálózati szolgáltatás végpontokkal/felügyelt példánnyal egy privát végponttal a SSISDB üzemeltetéséhez. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Ha ezt a beállítást a PowerShell használatával szeretné engedélyezni, tekintse meg a [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)című témakört.
    - Adja hozzá az ügyfélszámítógép IP-címét, vagy az ügyfél IP-címét tartalmazó IP-címtartományt az adatbázis-kiszolgáló tűzfal beállításai között az ügyfél IP-címei listához. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../sql-database/sql-database-firewall-configure.md).
    - Az adatbázis-kiszolgálóhoz SQL-hitelesítéssel, a kiszolgálói rendszergazdai hitelesítő adataival vagy a Azure Active Directory (HRE) hitelesítéssel kapcsolódhat az ADF felügyelt identitásával.  Az utóbbi esetében hozzá kell adnia az ADF felügyelt identitását egy olyan HRE-csoporthoz, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz, lásd: [HRE hitelesítés engedélyezése Azure-SSIS IRhoz](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
    - Győződjön meg arról, hogy az adatbázis-kiszolgáló már rendelkezik SSISDB. Egy Azure-SSIS IR kiépítés nem támogatja a meglévő SSISDB használatát.
- **Azure Resource Manager virtuális hálózat (nem kötelező)** . Ha a következő feltételek legalább egyike teljesül, rendelkeznie kell egy Azure Resource Manager virtuális hálózattal:
    - A SSISDB olyan Azure SQL Database-kiszolgálón üzemelteti, amelyen a virtuális hálózati szolgáltatás végpontok/felügyelt példánya privát végponttal rendelkezik.
    - Helyi adattárakhoz szeretne csatlakozni a Azure-SSIS IR futó SSIS-csomagokból a saját üzemeltetésű integrációs modul konfigurálása nélkül.
- **Azure PowerShell (nem kötelező)** . Kövesse a [Azure PowerShell telepítésének és konfigurálásának](/powershell/azure/install-az-ps)lépéseit, ha PowerShell-parancsfájlt szeretne futtatni a Azure-SSIS IR kiépítéséhez.

### <a name="region-support"></a>Régió támogatása

Az Azure-régiók listáját, amelyben az ADF és a Azure-SSIS IR jelenleg elérhető, lásd: [ADF + SSIS IR elérhetőség régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>SQL Database önálló adatbázis/rugalmas készlet és SQL Database felügyelt példány összehasonlítása

A következő táblázat összehasonlítja Azure SQL Database kiszolgáló és a felügyelt példány bizonyos funkcióit, mivel azok az Azure-SSIR IR-vel kapcsolódnak:

| Funkció | önálló adatbázis/rugalmas készlet| Felügyelt példány |
|---------|--------------|------------------|
| **Ütemezési** | SQL Server Agent nem érhető el.<br/><br/>Lásd: [csomagok végrehajtásának beosztása az ADF-folyamatban](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| A felügyelt példány ügynöke elérhető. |
| **Hitelesítés** | SSISDB hozhat létre egy olyan tárolt adatbázis-felhasználóval, amely bármely olyan HRE-csoportot képvisel, amely az ADF felügyelt identitásával rendelkezik a **db_owner** szerepkör tagjaként.<br/><br/>Lásd: [Az Azure ad-hitelesítés engedélyezése SSISDB létrehozásához Azure SQL Database kiszolgálón](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Létrehozhat SSISDB egy olyan adatbázis-felhasználóval, amely az ADF felügyelt identitását jelképezi. <br/><br/>Lásd: [Az Azure ad-hitelesítés engedélyezése SSISDB létrehozásához Azure SQL Database felügyelt példányban](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Szolgáltatásszint** | Ha Azure-SSIS IRt hoz létre a Azure SQL Database-kiszolgálóval, akkor kiválaszthatja a SSISDB szolgáltatási szintjét. Több szolgáltatási szint is rendelkezésre áll. | Ha a felügyelt példánnyal Azure-SSIS IR hoz létre, nem választhatja ki a SSISDB szolgáltatási szintjét. A felügyelt példány összes adatbázisa ugyanazt az erőforrást használja, mint a példány. |
| **Virtuális hálózat** | A csak Azure Resource Manager virtuális hálózatokat támogat a Azure-SSIS IRhoz, ha Azure SQL Database kiszolgálót használ virtuális hálózati szolgáltatás-végpontokkal, vagy a helyszíni adattárakhoz való hozzáférést igényel a saját üzemeltetésű integráció konfigurálása nélkül. | A csak Azure Resource Manager virtuális hálózatokat támogat a Azure-SSIS IRhoz való csatlakozáshoz. A virtuális hálózatra akkor van szükség, ha nem engedélyez nyilvános végpontot a felügyelt példány számára.<br/><br/>Ha a Azure-SSIS IR a felügyelt példányhoz tartozó virtuális hálózathoz csatlakoztatja, győződjön meg arról, hogy a Azure-SSIS IR a felügyelt példánytól eltérő alhálózaton van. Ha a Azure-SSIS IRt a felügyelt példánytól eltérő virtuális hálózathoz csatlakoztatja, a virtuális hálózati kapcsolatot vagy virtuális hálózatot javasolt virtuális hálózatra csatlakoztatni. Lásd: [az alkalmazás Összekötése Azure SQL Database felügyelt példányhoz](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Elosztott tranzakciók** | Rugalmas tranzakciókkal támogatott. A Microsoft Elosztott tranzakciók koordinátora (MSDTC) tranzakciói nem támogatottak. Ha a SSIS-csomagok az MSDTC használatával koordinálják az elosztott tranzakciókat, érdemes lehet áttelepíteni a Azure SQL Database rugalmas tranzakcióit. További információ: [Elosztott tranzakciók a felhőalapú adatbázisok között](../sql-database/sql-database-elastic-transactions-overview.md). | Nem támogatott. |
| | | |

## <a name="azure-portal"></a>Azure Portal

Ebben a szakaszban a Azure Portalt, konkrétan az ADF felhasználói felület (UI)/App használja Azure-SSIS IR létrehozásához.

### <a name="create-a-data-factory"></a>data factory létrehozása

Ha Azure Portal segítségével kívánja létrehozni az ADF-t, kövesse az [ADF létrehozása felhasználói felületen keresztül](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) című cikkben ismertetett lépéseket, és válassza a **rögzítés az irányítópulton** lehetőséget, ha így tesz a gyors hozzáférést a létrehozás után. 

Az ADF létrehozása után nyissa meg Azure Portal áttekintés lapját, és kattintson a **szerző & monitorozás** csempére, hogy elindítsa az **első lépések** lapot egy külön lapon, ahol továbbra is létrehozhatja a Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul üzembe helyezése

1. Az **első lépések** lapon kattintson a **SSIS konfigurálása Integration Runtime** csempére.

   ![SSIS integrációs modul konfigurálása csempe](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Az **Integrációs modul beállításai** lap **Általános beállítások** oldalán végezze el az alábbi lépéseket:

   ![Általános beállítások](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. A **Név** mezőben adja meg az integrációs modul nevét.

    b. A **Leírás** mezőben adja meg az integrációs modul leírását.

    c. A **Hely** mezőben válassza ki az integrációs modul helyét. Csak a támogatott helyek jelennek meg. Javasoljuk, hogy az SSISDB-t üzemeltető adatbázis-kiszolgálóval megegyező helyet válasszon.

    d. A **Csomópontméret** mezőben válassza ki az integrációsmodul-fürtön lévő csomópont méretét. Csak a támogatott csomópontméretek jelennek meg. Ha sok nagy számítási teljesítményt/sok memóriát igénylő csomagot szeretne futtatni, válasszon nagy csomópontméretet (vertikális felskálázás).

    e. A **Csomópontszám** mezőben adja meg az integrációsmodul-fürtben található csomópontok számát. Csak a támogatott csomópontszámok jelennek meg. Ha sok csomagot szeretne párhuzamosan futtatni, válasszon egy nagy fürtöt sok csomóponttal (horizontális felskálázás).

    f. A **kiadás/licenc**beállításnál válassza a SQL Server Edition/License lehetőséget az integrációs modulhoz: Standard vagy Enterprise. Ha szeretne fejlett/prémium szolgáltatásokat használni az integrációs modulon, válassza az Enterprise verziót.

    g. A **pénz megtakarítása**lehetőségnél válassza a Azure Hybrid BENEFIT (AHB) lehetőséget az integrációs modulhoz: igen vagy nem. Akkor válassza az Igen lehetőséget, ha a saját, szoftverbiztosítással ellátott SQL Server-licencét szeretné használni, hogy élhessen a hibrid használatból fakadó költségcsökkentéssel.

    h. Kattintson a **Tovább** gombra.

3. Az **SQL-beállítások** oldalon végezze el a következő lépéseket:

   ![SQL-beállítások](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. A **SSIS-katalógus létrehozása...** jelölőnégyzetből válassza ki a Azure-SSIS IR futtatandó csomagok telepítési modelljét: Projekt-telepítési modell, ahol a csomagok üzembe helyezése az adatbázis-kiszolgáló vagy a csomag telepítési modellje által üzemeltetett SSISDB történik, ahol a csomagok telepítése a fájlrendszer/fájlmegosztás/Azure Filesba történik. Ha bejelöli, saját adatbázis-kiszolgálót kell használnia a SSISDB gazdagépre, amelyet az Ön nevében fog létrehozni és kezelni.
   
    b. Az **Előfizetés** mezőben válassza azt az Azure-előfizetést, amelyikkel az adatbázis-kiszolgáló üzemelteti az SSISDB-t. 

    c. A **Hely** mezőben válassza ki az SSISDB-t üzemeltető adatbázis-kiszolgáló helyét. Javasoljuk, hogy az integrációs modullal megegyező helyet válasszon. 

    d. A **Katalógus adatbázis-kiszolgáló végpontja** mezőben válassza az SSISDB-t üzemeltető adatbázis-kiszolgáló végpontját. A kiválasztott adatbázis-kiszolgáló alapján az SSISDB létrehozható az Ön nevében önálló adatbázisként, egy rugalmas készlet részeként vagy egy felügyelt példányban, és elérhető nyilvános hálózatban vagy egy virtuális hálózathoz csatlakozva. Az adatbázis-kiszolgáló SSISDB gazdagépként való kiválasztásával kapcsolatos útmutatásért lásd: [Azure SQL Database önálló adatbázis/rugalmas készlet/felügyelt példány összehasonlítása](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Ha a virtuális hálózati szolgáltatással rendelkező végpontokkal/felügyelt példánnyal rendelkező Azure SQL Database kiszolgálót a SSISDB üzemeltetéséhez, illetve a helyszíni adataihoz való hozzáférés megköveteléséhez a saját üzemeltetésű integrációs modul konfigurálása nélkül választja, akkor a virtuális hálózathoz kell csatlakoztatnia a Azure-SSIS IR Lásd: [csatlakozás Azure-SSIS IR egy virtuális hálózathoz](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    e. A **HRE-hitelesítés használata...** jelölőnégyzet kiválasztásával adja meg az adatbázis-kiszolgáló hitelesítési módszerét a SSISDB üzemeltetéséhez: SQL-hitelesítés vagy HRE-hitelesítés az ADF felügyelt identitásával. Ha bejelöli, hozzá kell adnia az ADF felügyelt identitását egy olyan HRE-csoporthoz, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz, lásd: [HRE hitelesítés engedélyezése Azure-SSIS IRhoz](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    f. A **Rendszergazdai felhasználónév** értékeként adja meg az SSISDB-t üzemeltető adatbázis-kiszolgálóhoz tartozó SQL-hitelesítési felhasználónevet. 

    g. A **Rendszergazdai jelszó** értékeként adja meg az SSISDB-t üzemeltető adatbázis-kiszolgálóhoz tartozó SQL-hitelesítési jelszót. 

    h. A **katalógus-adatbázis szolgáltatási szintjéhez**válassza ki az adatbázis-kiszolgáló szolgáltatási SZINTJÉT a SSISDB üzemeltetéséhez: Alapszintű/standard/Prémium csomag vagy rugalmas készlet neve. 

    i. Kattintson a **Kapcsolat tesztelése** lehetőségre, és ha sikerrel járt, kattintson a **Tovább** gombra. 

4. A **Speciális beállítások** oldalon végezze el a következő lépéseket:

    ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. A **Párhuzamos végrehajtások maximális száma csomópontonként** mezőben választhatja ki az integrációsmodul-fürt egy csomópontján egyszerre végrehajtható csomagok maximális számát. Csak a támogatott csomagszámok jelennek meg. Válasszon ki egy kis számot, ha egynél több mag használatával szeretne egy nagy méretű/nagy súlyú, számítási és memória-igényű csomagot futtatni. A magas értéket akkor válassza, ha egy magon szeretné kicsi/nem nagy igényű csomagot vagy csomagokat futtatni.

    b. Az **Egyéni beállítástároló SAS URI-ja** mezőben opcionálisan megadható az ahhoz az Azure Storage Blob tárolóhoz tartozó közös hozzáférésű jogosultságkód (SAS) egyedi erőforrás-azonosító (URI), ahol a beállítás szkriptjét és az ahhoz társuló fájlokat tárolja, lásd: [Az Azure SSIS IR egyéni beállítása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. A **virtuális hálózat kiválasztása...** jelölőnégyzet kiválasztásával válassza ki, hogy az integrációs modult egy virtuális hálózathoz kívánja-e csatlakoztatni. Ellenőrizze, hogy van-e Azure SQL Database-kiszolgáló virtuális hálózati szolgáltatás-végpontokkal/felügyelt példánnyal egy privát végponttal a SSISDB üzemeltetéséhez, illetve a helyszíni adatforrásokhoz való hozzáférés megköveteléséhez, például a SSIS-csomagok helyszíni adatforrásaival/céljaival a saját üzemeltetésű integrációs modul konfigurálásával kapcsolatban lásd: [csatlakozás Azure-SSIS IR egy virtuális hálózathoz](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Ha bejelöli, hajtsa végre a következő lépéseket:

   ![Speciális beállítások virtuális hálózattal](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. Az **előfizetés**mezőben válassza ki a virtuális hálózattal rendelkező Azure-előfizetést.

    b. A **hely**esetében az integrációs modul ugyanazon helye van kiválasztva.

    c. A **Típus mezőben**válassza ki a virtuális hálózat típusát: Klasszikus vagy Azure Resource Manager. Javasoljuk, hogy Azure Resource Manager Virtual Network (virtuális hálózat) lehetőséget válassza, mivel a klasszikus virtuális hálózat hamarosan elavulttá válik.

    d. A **VNet neve**mezőben válassza ki a virtuális hálózat nevét. A virtuális hálózatnak ugyanazt kell használnia, mint a virtuális hálózatban lévő virtuális hálózati szolgáltatás végpontokkal/felügyelt példánnyal rendelkező Azure SQL Database-kiszolgáló számára a SSISDB vagy a helyszíni hálózathoz csatlakoztatott hálózat üzemeltetéséhez.

    e. Az **alhálózat neve**mezőben válassza ki a virtuális hálózatához tartozó alhálózat nevét. Ennek egy másik alhálózatnak kell lennie, mint amelyet a felügyelt példányhoz használt a virtuális hálózaton a SSISDB üzemeltetéséhez.

6. A saját üzemeltetésű **...** jelölőnégyzet bejelölésével adja meg, hogy szeretné-e konfigurálni a saját üzemeltetésű integrációs modult proxyként a Azure-SSIS IR számára, lásd: saját üzemeltetésű integrációs modul [beállítása proxyként](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Ha bejelöli, hajtsa végre a következő lépéseket:

   ![Speciális beállítások önkiszolgáló IR-vel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

    a. Saját üzemeltetésű **Integration Runtime**esetén válassza ki a meglévő, saját üzemeltetésű IR-t Azure-SSIS IR-proxyként.

    b. Az **átmeneti tároláshoz társított szolgáltatás**esetében válassza ki a meglévő Azure Blob Storage társított szolgáltatást, vagy hozzon létre egy újat az előkészítéshez.

    c. Az **előkészítési útvonal**mezőben adja meg a BLOB-tárolót a kiválasztott Azure-Blob Storage, vagy hagyja üresen, hogy alapértelmezettként használja az előkészítést.

7. Kattintson a **VNet érvényesítése** lehetőségre, vagy a **tovább**gombra. 

8. Az **Összefoglalás** lapon tekintse át az összes kiépítési beállítást, majd a javasolt dokumentációs hivatkozásokat, és kattintson a **Befejezés** gombra az integrációs modul létrehozásának megkezdéséhez.

    > [!NOTE]
    > Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül el kell végeznie, de körülbelül 20-30 percet is igénybe vehet, Azure-SSIS IR a virtuális hálózathoz való csatlakozáshoz.
    >
    > A SSISDB használata esetén az ADF szolgáltatás a SSISDB előkészítéséhez csatlakozik az adatbázis-kiszolgálóhoz. Emellett konfigurálja a virtuális hálózat engedélyeit és beállításait, ha meg van adva, és a Azure-SSIS IR a virtuális hálózathoz csatlakozik.
    > 
    > Amikor kiépít egy Azure-SSIS IR, a SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők az Excel/Access fájlokhoz és különböző Azure-adatforrásokhoz való kapcsolódást biztosítanak a beépített összetevők által már támogatott adatforrások mellett. További összetevőket is telepíthet, lásd: [Azure-SSIS IR egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md).

7. A **Kapcsolatok** ablakban váltson az **Integrációs modulok** lapra, ha szükséges. Az állapot frissítéséhez kattintson a **Frissítés** elemre.

   ![Létrehozás állapota](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Az Integration Runtime leállításához/elindításához, szerkesztéséhez vagy törléséhez használja a **műveletek** oszlopban található hivatkozásokat. Az integrációs modulhoz tartozó JSON-kód megtekintéséhez használja a legutolsó hivatkozást. A szerkesztés és törlés gombok csak akkor használhatók, ha az integrációs modul le van állítva.

   ![Azure SSIS integrációs modul – műveletek](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS integrációs modulok a portálon

1. Az Azure Data Factory felhasználói felületén váltson a **szerkesztési** lapra, kattintson a **Kapcsolatok** lehetőségre, majd váltson az **integrációs modulok** lapjára az adat-előállítójában megtalálható integrációs modulok megtekintéséhez.

   ![Meglévő IRs megtekintése](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Egy új Azure-SSIS integrációs modul létrehozásához kattintson az **Új** elemre.

   ![Integrációs modul elérése a menüből](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Egy Azure-SSIS integrációs modul létrehozásához kattintson az **Új** gombra a képen látható módon.

4. Az Integrációs modul telepítése ablakban válassza a **Létező SSIS-csomagok áthelyezése az Azure-ban történő végrehajtáshoz** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Integrációs modulok típusának megadása](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Az Azure-SSIS integrációs modul beállításához szükséges lépésekkel kapcsolatos információkért tekintse meg az [Azure SSIS integrációs modul üzembe helyezésével](#provision-an-azure-ssis-integration-runtime) foglalkozó részt.

## <a name="azure-powershell"></a>Azure PowerShell

Ebben a szakaszban a Azure PowerShell használatával hoz létre egy Azure-SSIS IR.

### <a name="create-variables"></a>Változók létrehozása

Másolja és illessze be a következő szkriptet – adja meg a változók értékeit. 

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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>Jelentkezzen be, és válassza az előfizetés lehetőséget.

Adja hozzá a következő kódot a szkripthez a bejelentkezéshez, és válassza ki az Azure-előfizetését:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Adatbázis-kiszolgálóval létesített kapcsolódás ellenőrzése

Adja hozzá a következő szkriptet a Azure SQL Database-kiszolgáló/felügyelt példány ellenőrzéséhez.

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

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat.

Ha az erőforráscsoport már létezik, ne másolja ezt a kódot a parancsfájlba. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>data factory létrehozása

Futtassa a következő parancsot egy adat-előállító létrehozásához.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Integrációs modul létrehozása

Futtassa az alábbi parancsokat egy olyan Azure SSIS integrációs modul létrehozásához, amely SSIS-csomagokat futtat az Azure-ban.

Ha nem használja a SSISDB, kihagyhatja a CatalogServerEndpoint, a CatalogPricingTier és a CatalogAdminCredential paramétereket.

Ha nem használja Azure SQL Database-kiszolgálót virtuális hálózati szolgáltatás végpontokkal/felügyelt példánnyal egy privát végponttal a SSISDB üzemeltetéséhez vagy a helyszíni adatokhoz való hozzáférés megköveteléséhez, kihagyhatja a VNetId és az alhálózati paramétereket, vagy üres értékeket adhat hozzájuk. Ha a saját üzemeltetésű integrációs modult proxyként konfigurálja a Azure-SSIS IR számára a helyszíni adateléréshez, akkor kihagyhatja őket. Ellenkező esetben nem hagyhatja ki őket, és a virtuális hálózat konfigurációjától érvényes értékeket kell átadnia, lásd: [csatlakozás Azure-SSIS IR virtuális hálózathoz](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Ha felügyelt példányt használ a SSISDB üzemeltetéséhez, kihagyhatja a CatalogPricingTier paramétert, vagy üres értéket adhat hozzá. Ellenkező esetben nem hagyhat el, és érvényes értéket kell átadnia a Azure SQL Database támogatott díjszabási szintjeinek listájáról: [SQL Database erőforrás-korlátok](../sql-database/sql-database-resource-limits.md).

Ha Azure Active Directory (HRE) hitelesítést használ az ADF felügyelt identitásával az adatbázis-kiszolgálóhoz való kapcsolódáshoz, kihagyhatja a CatalogAdminCredential paramétert, de hozzá kell adnia az ADF felügyelt identitását egy olyan HRE-csoporthoz, amelynek hozzáférése van az adatbázis-kiszolgáló engedélyeivel kapcsolatban lásd: [HRE-hitelesítés engedélyezése Azure-SSIS IRhoz](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Ellenkező esetben nem hagyhatja ki, és meg kell adnia egy érvényes objektumot, amely az SQL-hitelesítéshez tartozó kiszolgáló-rendszergazdai Felhasználónév és jelszó alapján lett létrehozva.

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

Futtassa az alábbi parancsokat az Azure-SSIS Integration Runtime elindításához.

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
> Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül el kell végeznie, de körülbelül 20-30 percet is igénybe vehet, Azure-SSIS IR a virtuális hálózathoz való csatlakozáshoz.
>
> A SSISDB használata esetén az ADF szolgáltatás a SSISDB előkészítéséhez csatlakozik az adatbázis-kiszolgálóhoz. Emellett konfigurálja a virtuális hálózat engedélyeit és beállításait, ha meg van adva, és a Azure-SSIS IR a virtuális hálózathoz csatlakozik.
> 
> Amikor kiépít egy Azure-SSIS IR, a SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők az Excel/Access fájlokhoz és különböző Azure-adatforrásokhoz való kapcsolódást biztosítanak a beépített összetevők által már támogatott adatforrások mellett. További összetevőket is telepíthet, lásd: [Azure-SSIS IR egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Teljes szkript

Itt látható a teljes parancsfájl, amely létrehoz egy Azure-SSIS integrációs modult.

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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

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

Ebben a szakaszban a Azure Resource Manager sablonnal hozhat létre Azure-SSIS integrációs modult. Íme egy példa a bemutatóra:

1. Hozzon létre egy JSON-fájlt a következő Azure Resource Manager sablonnal. A szögletes zárójelben lévő értékeket cserélje le a saját értékeire.

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

2. A Azure Resource Manager sablon üzembe helyezéséhez futtassa a New-AzResourceGroupDeployment parancsot az alábbi példában látható módon, ahol a ADFTutorialResourceGroup az erőforráscsoport neve, a ADFTutorialARM. JSON pedig az a fájl, amely a következő JSON-definícióját tartalmazza: az Ön adatait előállító és Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Ez a parancs létrehozza az adatelőállítót, és Azure-SSIS IR, de nem indítja el az IR-t.

3. A Azure-SSIS IR elindításához futtassa a Start-AzDataFactoryV2IntegrationRuntime parancsot:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül el kell végeznie, de körülbelül 20-30 percet is igénybe vehet, Azure-SSIS IR a virtuális hálózathoz való csatlakozáshoz.
>
> A SSISDB használata esetén az ADF szolgáltatás a SSISDB előkészítéséhez csatlakozik az adatbázis-kiszolgálóhoz. Emellett konfigurálja a virtuális hálózat engedélyeit és beállításait, ha meg van adva, és a Azure-SSIS IR a virtuális hálózathoz csatlakozik.
> 
> Amikor kiépít egy Azure-SSIS IR, a SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők az Excel/Access fájlokhoz és különböző Azure-adatforrásokhoz való kapcsolódást biztosítanak a beépített összetevők által már támogatott adatforrások mellett. További összetevőket is telepíthet, lásd: [Azure-SSIS IR egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése

Ha a SSISDB-t használja, a csomagokat üzembe helyezheti, és Azure-SSIS IR futtathatja azokat a SSDT/SSMS eszközök használatával, amelyek kiszolgálói végpontján keresztül csatlakoznak az adatbázis-kiszolgálóhoz.  Azure SQL Database kiszolgáló/felügyelt példány esetében egy nyilvános végponttal rendelkező privát végpont/felügyelt példány esetén a kiszolgálói végpont formátuma `<server name>.database.windows.net` / / `<server name>.<dns prefix>.database.windows.net` `<server name>.public.<dns prefix>.database.windows.net,3342`a következő:. Ha nem használja a SSISDB-t, a csomagokat fájlrendszerek/fájlmegosztás/Azure Filesba helyezheti, és futtathatja azokat Azure-SSIS IR parancssori segédeszközök `dtinstall` használatával `dtexec` /. `dtutil` / További információ: SSIS- [csomagok telepítése](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Mindkét esetben futtathatja az üzembe helyezett csomagokat Azure-SSIS IR a SSIS-csomag végrehajtása az ADF-folyamatokban művelettel történő futtatásával, lásd: [SSIS-csomag végrehajtásának meghívása első osztályú ADF-tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>További lépések

Lásd még a dokumentáció további Azure-SSIS IR témakörei:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk az integrációs modulokkal kapcsolatos általános információkat tartalmazza, beleértve a Azure-SSIS IR.
- [Azure-SSIS IR figyelése](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ebből a cikkből megtudhatja, hogyan kérheti le és értelmezheti a Azure-SSIS IR kapcsolatos információkat.
- [Azure-SSIS IR kezelése](manage-azure-ssis-integration-runtime.md). Ebből a cikkből megtudhatja, hogyan állíthatja le, indíthatja el vagy törölheti Azure-SSIS IR – ez azt is bemutatja, hogyan bővítheti a Azure-SSIS IR további csomópontok hozzáadásával.
- [Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk a Azure-SSIS IR virtuális hálózathoz való csatlakoztatásáról nyújt tájékoztatást.