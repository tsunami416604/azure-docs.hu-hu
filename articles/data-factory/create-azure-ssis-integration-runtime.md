---
title: Azure SSIS integrációs modul létrehozása a Azure Data Factoryban | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Azure-SSIS integrációs modult Azure Data Factory, így SSIS-csomagokat helyezhet üzembe és futtathat az Azure-ban.
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
ms.openlocfilehash: ddc91a3317d362f6b56e486556f2edf6cdb85131
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326697"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure SSIS integrációs modul létrehozása Azure Data Factory

Ez a cikk az Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) Azure Data Factory-ben való üzembe helyezésének lépéseit ismerteti. Az Azure-SSIS IR a következőket támogatja:

- Egy Azure SQL Database-kiszolgáló vagy egy felügyelt példány (projekt-telepítési modell) által üzemeltetett SSIS-katalógusba (SSISDB) telepített csomagok futtatása.
- Fájlrendszerek, fájlmegosztás vagy Azure Files (csomag-telepítési modell) üzembe helyezett csomagjainak futtatása. 

Egy Azure-SSIS IR kiépítése után jól ismert eszközökkel üzembe helyezheti és futtathatja a csomagokat az Azure-ban. Ezek az eszközök közé tartoznak a SQL Server Data Tools, a SQL Server Management Studio és a parancssori eszközök, például a `dtinstall`, a `dtutil` és a `dtexec`.

A [kiépítési Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) oktatóanyag azt mutatja be, hogyan hozható létre egy Azure-SSIS IR a Azure Portal vagy a Data Factory alkalmazás használatával. Az oktatóanyag azt is bemutatja, hogyan használhat egy Azure SQL Database-kiszolgálót vagy felügyelt példányt a SSISDB üzemeltetéséhez. Ez a cikk az oktatóanyagon alapul, és leírja, hogyan végezheti el ezeket a választható feladatokat:

- Használjon egy Azure SQL Database kiszolgálót virtuális hálózati szolgáltatás-végpontokkal vagy egy felügyelt példánnyal, amely privát végponttal rendelkezik a SSISDB üzemeltetéséhez. Előfeltételként a virtuális hálózatokhoz való csatlakozáshoz konfigurálnia kell a virtuális hálózati engedélyeket és a Azure-SSIS IR beállításait.

- Azure Active Directory-(Azure AD-) hitelesítést használhat az adatgyár felügyelt identitásával egy Azure SQL Database-kiszolgálóhoz vagy felügyelt példányhoz való kapcsolódáshoz. Előfeltételként hozzá kell adnia az adatok előállítójának felügyelt identitását olyan adatbázis-felhasználóként, aki létrehozhat egy SSISDB-példányt.

- Csatlakoztassa a Azure-SSIS IRt egy virtuális hálózathoz, vagy konfigurálja a saját üzemeltetésű integrációs modult proxyként a Azure-SSIS IR számára a helyszíni adateléréshez.

Ez a cikk bemutatja, hogyan helyezhet üzembe egy Azure-SSIS IR a Azure Portal, a Azure PowerShell és egy Azure Resource Manager sablon használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha még nem rendelkezik előfizetéssel, létrehozhat egy [ingyenes próbaverziós](https://azure.microsoft.com/pricing/free-trial/) fiókot is.
- **Azure SQL Database kiszolgáló vagy felügyelt példány (nem kötelező)** . Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. Data Factory ekkor létrehoz egy SSISDB-példányt ezen az adatbázis-kiszolgálón. 

  Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi az integrációs modul írási végrehajtásának naplózását az Azure-régiók SSISDB nélkül.

  Tartsa szem előtt az alábbi szempontokat:

  - A kiválasztott adatbázis-kiszolgáló alapján a SSISDB-példány létrehozhatók az Ön nevében egyetlen adatbázisként egy rugalmas készlet részeként vagy egy felügyelt példányban. Nyilvános hálózaton vagy virtuális hálózathoz való csatlakozással is elérhető. Az adatbázis-kiszolgáló típusának SSISDB való kiválasztásával kapcsolatos útmutatásért tekintse meg a jelen cikk [Azure SQL Database önálló adatbázis, rugalmas készlet és felügyelt példány összevetése](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) című szakaszát. 
  
    Ha olyan Azure SQL Database kiszolgálót használ virtuális hálózati szolgáltatás-végpontokkal vagy felügyelt példánnyal, amely egy privát végponttal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyi integrációs modul konfigurálása nélkül szeretné elérni a helyszíni adatait, akkor csatlakoztatnia kell a Azure-SSIS IRt egy virtuális hálózathoz hálózati. További információ: [Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
  - Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás engedélyezve van az adatbázis-kiszolgálón. Ez a beállítás nem alkalmazható, ha olyan Azure SQL Database kiszolgálót használ virtuális hálózati szolgáltatás-végpontokkal vagy felügyelt példánnyal, amely privát végponttal rendelkezik a SSISDB üzemeltetéséhez. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Ha ezt a beállítást a PowerShell használatával szeretné engedélyezni, tekintse meg a [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)című témakört.
  - Adja hozzá az ügyfélszámítógép IP-címét, vagy egy olyan IP-címtartományt, amely tartalmazza az ügyfélszámítógép IP-címét az adatbázis-kiszolgáló tűzfal beállításai között az ügyfél IP-címei listára. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../sql-database/sql-database-firewall-configure.md).
  - A kiszolgáló-rendszergazdai hitelesítő adataival SQL-hitelesítéssel, vagy az adat-előállító felügyelt identitásával az Azure AD-hitelesítés használatával kapcsolódhat az adatbázis-kiszolgálóhoz. Az utóbbi esetében hozzá kell adnia az adatok előállítójának felügyelt identitását egy Azure AD-csoportba, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: az [Azure ad-hitelesítés engedélyezése Azure-SSIS IRhoz](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
  - Győződjön meg arról, hogy az adatbázis-kiszolgáló már rendelkezik SSISDB-példánnyal. Egy Azure-SSIS IR kiépítés nem támogatja a meglévő SSISDB-példányok használatát.
- **Azure Resource Manager virtuális hálózat (nem kötelező)** . Ha a következő feltételek legalább egyike teljesül, rendelkeznie kell egy Azure Resource Manager virtuális hálózattal:
    - A SSISDB-t egy olyan Azure SQL Database-kiszolgálón üzemelteti, amelyen virtuális hálózati szolgáltatás-végpontok vagy egy privát végponttal rendelkező felügyelt példány található.
    - Helyi adattárakhoz szeretne csatlakozni a Azure-SSIS IR futó SSIS-csomagokból a saját üzemeltetésű integrációs modul konfigurálása nélkül.
- **Azure PowerShell (nem kötelező)** . Kövesse a [Azure PowerShell telepítésének és konfigurálásának](/powershell/azure/install-az-ps)lépéseit, ha PowerShell-parancsfájlt szeretne futtatni a Azure-SSIS IR kiépítéséhez.

### <a name="regional-support"></a>Regionális támogatás

Azon Azure-régiók listájáért, amelyekben Data Factory és Azure-SSIS IR elérhetők, tekintse meg a [Data Factory és a SSIS az IR elérhetősége régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>SQL Database önálló adatbázis, rugalmas készlet és felügyelt példány összehasonlítása

Az alábbi táblázat összehasonlítja az Azure SQL Database-kiszolgáló és a felügyelt példány bizonyos funkcióit, mivel azok az Azure-SSIR IR-hez kapcsolódnak:

| Szolgáltatás | Önálló adatbázis/rugalmas készlet| Felügyelt példány |
|---------|--------------|------------------|
| **Ütemezési** | A SQL Server Agent nem érhető el.<br/><br/>Lásd: [csomagok végrehajtásának ütemezett Data Factory folyamata](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| A felügyelt példány ügynöke elérhető. |
| **Hitelesítés** | Létrehozhat egy SSISDB-példányt egy olyan tárolt adatbázis-felhasználóval, aki egyetlen Azure AD-csoportot képvisel az adat-előállító felügyelt identitásával a **db_owner** szerepkör tagjaként.<br/><br/>Lásd: az [Azure ad-hitelesítés engedélyezése egy SSISDB-példány létrehozásához egy Azure SQL Database kiszolgálón](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Létrehozhat egy SSISDB-példányt egy olyan tárolt adatbázis-felhasználóval, aki az adatelőállító felügyelt identitását képviseli. <br/><br/>Lásd: az [Azure ad-hitelesítés engedélyezése egy SSISDB-példány létrehozásához egy Azure SQL Database felügyelt példányban](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Szolgáltatásszint** | Amikor létrehoz egy Azure-SSIS IR a Azure SQL Database-kiszolgálóval, kiválaszthatja a SSISDB szolgáltatási szintjét. Több szolgáltatási szint is rendelkezésre áll. | Ha felügyelt példánnyal hoz létre Azure-SSIS IR, nem választhatja ki a SSISDB szolgáltatási szintjét. A felügyelt példány összes adatbázisa ugyanazt az erőforrást használja, mint a példány. |
| **Virtuális hálózat** | A Azure-SSIS IR csak akkor csatlakozhat Azure Resource Manager virtuális hálózatokhoz, ha Azure SQL Database kiszolgálót használ virtuális hálózati szolgáltatás-végpontokkal, vagy ha a helyi integrációs modul konfigurálása nélkül szeretné elérni a helyszíni adattárakat. | A Azure-SSIS IR csak Azure Resource Manager virtuális hálózatokat tud csatlakoztatni. A virtuális hálózatra akkor van szükség, ha nem engedélyez nyilvános végpontot a felügyelt példány számára.<br/><br/>Ha a Azure-SSIS IR a felügyelt példányhoz tartozó virtuális hálózathoz csatlakoztatja, győződjön meg arról, hogy a Azure-SSIS IR a felügyelt példánytól eltérő alhálózaton található. Ha a Azure-SSIS IR egy másik virtuális hálózathoz csatlakoztatja a felügyelt példányból, a virtuális hálózat vagy a hálózat – hálózat közötti kapcsolatot javasoljuk. Lásd: [az alkalmazás összekötése egy Azure SQL Database felügyelt példányhoz](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Elosztott tranzakciók** | Ez a funkció rugalmas tranzakciókkal támogatott. A Microsoft Elosztott tranzakciók koordinátora (MSDTC) tranzakciói nem támogatottak. Ha a SSIS-csomagok az MSDTC használatával koordinálják az elosztott tranzakciókat, érdemes lehet áttelepíteni a Azure SQL Database rugalmas tranzakcióit. További információ: [Elosztott tranzakciók felhőalapú adatbázisok között](../sql-database/sql-database-elastic-transactions-overview.md). | Nem támogatott. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Integrációs modul létrehozása a Azure Portal használatával

Ebben a szakaszban a Azure-SSIS IR létrehozásához a Azure Portal, konkrétan a Data Factory felhasználói felületét (UI) vagy alkalmazást kell használnia.

### <a name="create-a-data-factory"></a>Data factory létrehozása

Ha az adatgyárat a Azure Portal segítségével szeretné létrehozni, kövesse az [adatfeldolgozó létrehozása a felhasználói felületen](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)című témakör részletes utasításait. Ha ezt megteszi, válassza a **rögzítés az irányítópulton** lehetőséget, hogy a létrehozása után engedélyezze a gyors hozzáférést. 

Az adatelőállító létrehozása után nyissa meg a Azure Portal áttekintés lapját. Válassza a **szerző & monitor** csempét, hogy megnyissa az első **lépések** oldalt egy külön lapon. Itt továbbra is létrehozhatja a Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul üzembe helyezése

1. Az **Első lépések** lapon válassza ki az **SSIS integrációs modul konfigurálása** csempét.

   ![SSIS integrációs modul konfigurálása csempe](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. **Integration Runtime telepítő** **általános beállítások** lapján végezze el a következő lépéseket.

   ![Általános beállítások](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. A **Név** mezőben adja meg az integrációs modul nevét.

    b. A **Leírás** mezőben adja meg az integrációs modul leírását.

    c. A **Hely** mezőben válassza ki az integrációs modul helyét. Csak a támogatott helyek jelennek meg. Javasoljuk, hogy az SSISDB-t üzemeltető adatbázis-kiszolgálóval megegyező helyet válasszon.

    d. A **csomópont mérete**beállításnál válassza ki a csomópont méretét az Integration Runtime-fürtben. Csak a támogatott csomópontméretek jelennek meg. Válasszon nagy méretű csomópontot (vertikális felskálázás), ha sok nagy számítási igényű vagy memória-igényű csomagot szeretne futtatni.

    e. A **Csomópontszám** mezőben adja meg az integrációsmodul-fürtben található csomópontok számát. Csak a támogatott csomópontszámok jelennek meg. Ha egyszerre több csomagot szeretne futtatni, válasszon ki egy nagy méretű fürtöt sok csomóponttal.

    f. A **kiadás/licenc**lapon válassza ki az integrációs modul SQL Server kiadását: standard vagy Enterprise. Válassza a vállalat lehetőséget, ha az integrációs modul speciális funkcióit szeretné használni.

    g. A **pénz megtakarítása**lehetőségnél válassza a Azure Hybrid Benefit lehetőséget az integrációs futtatókörnyezethez: **Igen** vagy **nem**. Válassza az **Igen** lehetőséget, ha a frissítési garanciával rendelkező saját SQL Server licencét szeretné kihasználni a hibrid használattal járó költségmegtakarítással.

    h. Kattintson a **Tovább** gombra.

3. Az **SQL-beállítások** lapon végezze el a következő lépéseket.

   ![SQL-beállítások](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Jelölje be a **SSIS-katalógus létrehozása...** jelölőnégyzetet, és válassza ki a Azure-SSIS IRon futtatandó csomagok telepítési modelljét. Választhatja a projekt üzembe helyezési modelljét is, ahol a csomagok üzembe helyezése az adatbázis-kiszolgáló által üzemeltetett SSISDB történik, vagy a csomag telepítési modellje, ahol a csomagok fájlrendszerekbe, fájlmegosztásba vagy Azure Filesba vannak telepítve. 
    
   Ha bejelöli a jelölőnégyzetet, a saját adatbázis-kiszolgálóját kell használnia az Ön nevében létrehozandó és kezelt SSISDB-példány üzemeltetéséhez.
   
   b. Az **Előfizetés** mezőben válassza azt az Azure-előfizetést, amelyikkel az adatbázis-kiszolgáló üzemelteti az SSISDB-t. 

   c. A **Hely** mezőben válassza ki az SSISDB-t üzemeltető adatbázis-kiszolgáló helyét. Javasoljuk, hogy az integrációs modullal megegyező helyet válasszon. 

   d. A **Katalógus adatbázis-kiszolgáló végpontja** mezőben válassza az SSISDB-t üzemeltető adatbázis-kiszolgáló végpontját. 
    
   A kiválasztott adatbázis-kiszolgáló alapján a SSISDB-példány létrehozhatók az Ön nevében egyetlen adatbázisként egy rugalmas készlet részeként vagy egy felügyelt példányban. Nyilvános hálózaton vagy virtuális hálózathoz való csatlakozással is elérhető. Az adatbázis-kiszolgáló típusának SSISDB való kiválasztásával kapcsolatos útmutatásért tekintse meg a jelen cikk [Azure SQL Database önálló adatbázis, rugalmas készlet és felügyelt példány összevetése](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) című szakaszát. 
    
   Ha olyan Azure SQL Database-kiszolgálót választ ki, amely virtuális hálózati szolgáltatás-végpontokkal vagy felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyi integrációs modul konfigurálása nélkül kívánja elérni a helyszíni adatkezelést, akkor csatlakoztatnia kell a Azure-SSIS IR virtuális hálózat. További információ: [Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

   e. Jelölje be a **HRE-hitelesítés használata az ADF felügyelt identitással** jelölőnégyzetet, hogy kiválassza az adatbázis-kiszolgáló hitelesítési módszerét a SSISDB üzemeltetéséhez. Választhatja az SQL-hitelesítést vagy az Azure AD-hitelesítést az adatokhoz tartozó felügyelt identitással. 
    
   Ha bejelöli a jelölőnégyzetet, hozzá kell adnia az adatgyár felügyelt identitását egy Azure AD-csoportba, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: az [Azure ad-hitelesítés engedélyezése Azure-SSIS IRhoz](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

   f. A **rendszergazdai Felhasználónév**mezőben adja meg az adatbázis-kiszolgáló SQL-hitelesítési felhasználónevét a SSISDB üzemeltetéséhez. 

   g. **Rendszergazdai jelszó**esetén adja meg az adatbázis-kiszolgáló SQL-hitelesítési jelszavát a SSISDB üzemeltetéséhez. 

   h. A **katalógus-adatbázis szolgáltatási szintjéhez**válassza ki az adatbázis-kiszolgáló szolgáltatási SZINTJÉT a SSISDB üzemeltetéséhez. Válassza ki az alapszintű, a standard vagy a prémium szintet, vagy válasszon egy rugalmas készlet nevét. 

   i. Válassza a **kapcsolatok tesztelése**lehetőséget. Ha a teszt sikeres, kattintson a **Tovább gombra**. 

4. A **Speciális beállítások** lapon végezze el a következő lépéseket.

   ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. **Csomópontok maximális párhuzamos végrehajtásához**válassza ki az Integration Runtime-fürt csomópontjain egyidejűleg futtatandó csomagok maximális számát. Csak a támogatott csomagszámok jelennek meg. Ha egynél több mag használatával szeretné futtatni a számítási vagy a memória-igényű egyetlen nagyméretű csomagot, válasszon ki egy kis számot. Válassza ki a magas számot, ha egy vagy több kis csomagot szeretne futtatni egyetlen mag használatával.

   b. Az **egyéni telepítési tároló sas URI-ja**esetében opcionálisan megadhatja az Azure Blob Storage-tároló közös hozzáférésű aláírásának (SAS) egységes erőforrás-azonosítóját (URI), amelyben a telepítési parancsfájlt és a hozzá tartozó fájlokat tárolja. További információ: [Azure-SSIS IR egyéni beállítása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. Jelölje be a **VNet kiválasztása a Azure-SSIS Integration Runtimehoz, és az ADF engedélyezése bizonyos hálózati erőforrások létrehozásához** jelölőnégyzetet, és válassza ki, hogy csatlakoztatni kívánja-e az integrációs modult egy virtuális hálózathoz. 

   Válassza ki, ha Azure SQL Database kiszolgálót használ virtuális hálózati szolgáltatás-végpontokkal vagy egy felügyelt példánnyal, amely egy privát végponttal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyszíni információhoz való hozzáférésre van szüksége. (Ez azt eredményezi, hogy a saját üzemeltetésű integrációs modul konfigurálása nélkül rendelkezik helyszíni adatforrásokkal vagy célállomásokkal a SSIS-csomagokban.) További információ: [Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

   Ha bejelöli a jelölőnégyzetet, hajtsa végre a következő lépéseket.

   ![Virtuális hálózat speciális beállításai](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

   a. Az **előfizetés**mezőben válassza ki a virtuális hálózattal rendelkező Azure-előfizetést.

   b. A **hely**esetében az integrációs modul ugyanazon helye van kiválasztva.

   c. A **Típus mezőben**válassza ki a virtuális hálózat típusát: klasszikus vagy Azure Resource Manager. Javasoljuk, hogy válasszon ki egy Azure Resource Manager virtuális hálózatot, mert a klasszikus virtuális hálózatok hamarosan elavulttá válnak.

   d. A **VNet neve**mezőben válassza ki a virtuális hálózat nevét. A virtuális hálózatnak meg kell egyeznie a virtuális hálózati szolgáltatás-végpontokkal vagy egy virtuális hálózatban lévő felügyelt példánnyal a SSISDB üzemeltetéséhez használt Azure SQL Database-kiszolgáló esetében. Vagy a virtuális hálózatnak meg kell egyeznie a helyszíni hálózathoz csatlakoztatott hálózattal.

   e. Az **alhálózat neve**mezőben válassza ki a virtuális hálózatához tartozó alhálózat nevét. Ennek a virtuális hálózaton a felügyelt példányhoz a SSISDB üzemeltetéséhez használt másik alhálózatnak kell lennie.

6. Jelölje be a saját üzemeltetésű **Integration Runtime beállítása proxyként a Azure-SSIS Integration Runtime** jelölőnégyzetet annak kiválasztásához, hogy egy saját üzemeltetésű integrációs modult kíván-e konfigurálni a Azure-SSIS IRhoz. További információ: saját üzemeltetésű [IR beállítása proxyként](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

   Ha bejelöli a jelölőnégyzetet, hajtsa végre a következő lépéseket.

   ![Speciális beállítások önkiszolgáló IR-vel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

   a. Saját üzemeltetésű **Integration Runtime**esetén válassza ki a meglévő, saját üzemeltetésű IR-t a Azure-SSIS IRhoz.

   b. Az **átmeneti tároláshoz társított szolgáltatás**esetében válassza ki a meglévő Azure Blob Storage-beli társított szolgáltatást. Vagy hozzon létre egy újat az előkészítéshez.

   c. Az **átmeneti elérési út**mezőben adja meg a BLOB-tárolót a kiválasztott Azure Blob Storage-fiókban. Vagy hagyja üresen, hogy alapértelmezettként használja az előkészítést.

7. Válassza a **VNet érvényesítése** > **tovább**lehetőséget. 

8. Az **Összefoglalás** lapon tekintse át az összes kiépítési beállítást, a könyvjelzőt az ajánlott dokumentációs hivatkozásokat, és válassza a **Befejezés** lehetőséget az integrációs modul létrehozásának elindításához.

    > [!NOTE]
    > Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül be kell fejeződnie. Azonban 20-30 percet is igénybe vehet, amíg a Azure-SSIS IR csatlakozhat egy virtuális hálózathoz.
    >
    > Ha a SSISDB-t használja, akkor a Data Factory-szolgáltatás a SSISDB előkészítéséhez csatlakozik az adatbázis-kiszolgálóhoz. Emellett konfigurálja a virtuális hálózat engedélyeit és beállításait, ha meg van adva, és a Azure-SSIS IR a virtuális hálózathoz csatlakozik.
    > 
    > Amikor kiépít egy Azure-SSIS IR, a SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők az Excel-fájlokhoz, a fájlokhoz és a különböző Azure-adatforrásokhoz való kapcsolódást biztosítják a beépített összetevők által már támogatott adatforrások mellett. További információ a telepíthető egyéb összetevőkről: a [Azure-SSIS IR egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md).

7. A **Kapcsolatok** lapon váltson az **Integrációs modulok** lapra, ha szükséges. Az állapot frissítéséhez kattintson a **Frissítés** gombra.

   ![Létrehozás állapota](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Az integrációs modul leállításához/elindításához, szerkesztéséhez vagy törléséhez használja a **Műveletek** oszlopban található hivatkozásokat. Az integrációs modulhoz tartozó JSON-kód megtekintéséhez használja a legutolsó hivatkozást. A szerkesztés és törlés gombok csak akkor használhatók, ha az integrációs modul le van állítva.

   ![Azure SSIS-beli IR-műveletek](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS integrációs modulok a portálon

1. A Azure Data Factory felhasználói felületen váltson a **Szerkesztés** lapra, és válassza a **kapcsolatok**lehetőséget. Ezután váltson az **Integration Runtimes (integrációs** modulok) lapra, ahol megtekintheti a meglévő integrációs modulokat az adatgyárban.

   ![Meglévő IRs megtekintése](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Új Azure-SSIS IR létrehozásához válassza az **új** lehetőséget.

   ![Integrációs modul elérése a menüből](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

4. Az **Integrációs modul telepítése** ablakban válassza a **Meglévő SSIS-csomagok áthelyezése az Azure-ban történő végrehajtáshoz** lehetőséget, majd kattintson a **Tovább** gombra.

   ![Integrációs modulok típusának megadása](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Az Azure-SSIS IR beállításának hátralévő lépéseiért tekintse meg az [Azure SSIS Integration Runtime kiépítése](#provision-an-azure-ssis-integration-runtime) című szakaszt.

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Integrációs modul létrehozása Azure PowerShell használatával

Ebben a szakaszban a Azure PowerShell használatával hoz létre egy Azure-SSIS IR.

### <a name="create-variables"></a>Változók létrehozása

Másolja és illessze be az alábbi szkriptet. Határozza meg a változók értékeit. 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"
```

### <a name="sign-in-and-select-a-subscription"></a>Jelentkezzen be, és válasszon egy előfizetést

Adja hozzá a következő szkriptet a bejelentkezéshez, és válassza ki az Azure-előfizetését.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Adatbázis-kiszolgálóval létesített kapcsolódás ellenőrzése

Adja hozzá a következő szkriptet a Azure SQL Database-kiszolgáló vagy a felügyelt példány ellenőrzéséhez.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
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

### <a name="configure-the-virtual-network"></a>A virtuális hálózat konfigurálása

Adja hozzá a következő szkriptet a virtuális hálózati engedélyek és beállítások automatikus konfigurálásához az Azure-SSIS Integration runtimehez való csatlakozáshoz.

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

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat.

Ha az erőforráscsoport már létezik, ne másolja ezt a kódot a szkriptbe. 

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

Futtassa az alábbi parancsokat egy olyan Azure SSIS integrációs modul létrehozásához, amely SSIS-csomagokat futtat az Azure-ban.

Ha nem használja a SSISDB, kihagyhatja a `CatalogServerEndpoint`, `CatalogPricingTier` és `CatalogAdminCredential` paramétert.

Ha nem használ olyan Azure SQL Database-kiszolgálót, amely virtuális hálózati szolgáltatás-végpontokkal vagy felügyelt példánnyal rendelkezik a SSISDB üzemeltetéséhez, vagy ha a helyszíni adatokhoz való hozzáférésre van szüksége, kihagyhatja a `VNetId` és `Subnet` paramétereket, vagy üres értékeket adhat meg a következőhöz: őket. Ha a saját üzemeltetésű integrációs modult proxyként konfigurálja az Azure-SSIS IR számára a helyszíni adateléréshez, akkor kihagyhatja őket. Ellenkező esetben nem hagyhatja ki őket, és a virtuális hálózat konfigurációjától érvényes értékeket kell átadnia. További információ: [Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Ha felügyelt példányt használ a SSISDB üzemeltetéséhez, kihagyhatja a `CatalogPricingTier` paramétert, vagy átadhat egy üres értéket. Ellenkező esetben nem hagyhatja ki, és érvényes értéket kell átadnia a Azure SQL Database támogatott díjszabási szintjeinek listájából. További információ: [SQL Database erőforrás-korlátok](../sql-database/sql-database-resource-limits.md).

Ha az Azure AD-hitelesítést az adatokhoz tartozó felügyelt identitással együtt használja az adatbázis-kiszolgálóhoz való kapcsolódáshoz, kihagyhatja a `CatalogAdminCredential` paramétert. Az adatok előállítójának felügyelt identitását azonban hozzá kell adnia egy Azure AD-csoportba, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: az [Azure ad-hitelesítés engedélyezése Azure-SSIS IRhoz](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Ellenkező esetben nem hagyhatja ki, és meg kell adnia egy érvényes objektumot, amely az SQL-hitelesítéshez tartozó kiszolgáló-rendszergazdai Felhasználónév és jelszó alapján lett létrehozva.

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
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add the SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

### <a name="start-the-integration-runtime"></a>Az Integration Runtime elindítása

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
> Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül be kell fejeződnie. Azonban 20-30 percet is igénybe vehet, amíg a Azure-SSIS IR csatlakozhat egy virtuális hálózathoz.
>
> Ha a SSISDB-t használja, akkor a Data Factory-szolgáltatás a SSISDB előkészítéséhez csatlakozik az adatbázis-kiszolgálóhoz. Emellett konfigurálja a virtuális hálózat engedélyeit és beállításait, ha meg van adva, és a Azure-SSIS IR a virtuális hálózathoz csatlakozik.
> 
> Amikor kiépít egy Azure-SSIS IR, a SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők az Excel-fájlokhoz, a fájlokhoz és a különböző Azure-adatforrásokhoz való kapcsolódást biztosítják a beépített összetevők által már támogatott adatforrások mellett. További információ a telepíthető egyéb összetevőkről: a [Azure-SSIS IR egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Teljes szkript

Itt látható a teljes parancsfájl, amely létrehoz egy Azure-SSIS integrációs modult.

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
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

### Configure a virtual network
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

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add the SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Integrációs modul létrehozása Azure Resource Manager sablonnal

Ebben a szakaszban egy Azure Resource Manager sablonnal hozza létre az Azure-SSIS integrációs modult. Íme egy példa a bemutatóra:

1. Hozzon létre egy JSON-fájlt a következő Azure Resource Manager sablonnal. A szögletes zárójelben (helyőrzők) lévő értékeket cserélje le a saját értékeire.

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

2. A Azure Resource Manager sablon üzembe helyezéséhez futtassa a `New-AzResourceGroupDeployment` parancsot az alábbi példában látható módon. A példában a `ADFTutorialResourceGroup` az erőforráscsoport neve. a `ADFTutorialARM.json` az a fájl, amely tartalmazza az adatelőállító JSON-definícióját és a Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Ez a parancs létrehozza az adatelőállítót, és Azure-SSIS IR, de nem indítja el az IR-t.

3. A Azure-SSIS IR elindításához futtassa a `Start-AzDataFactoryV2IntegrationRuntime` parancsot:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül be kell fejeződnie. Azonban 20-30 percet is igénybe vehet, amíg a Azure-SSIS IR csatlakozhat egy virtuális hálózathoz.
>
> Ha a SSISDB-t használja, akkor a Data Factory-szolgáltatás a SSISDB előkészítéséhez csatlakozik az adatbázis-kiszolgálóhoz. Emellett konfigurálja a virtuális hálózat engedélyeit és beállításait, ha meg van adva, és a Azure-SSIS IR a virtuális hálózathoz csatlakozik.
> 
> Amikor kiépít egy Azure-SSIS IR, a SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők az Excel-fájlokhoz, a fájlokhoz és a különböző Azure-adatforrásokhoz való kapcsolódást biztosítják a beépített összetevők által már támogatott adatforrások mellett. További információ a telepíthető egyéb összetevőkről: a [Azure-SSIS IR egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése

Ha a SSISDB-t használja, a csomagokat üzembe helyezheti, és SQL Server Data Tools vagy SQL Server Management Studio eszközök használatával futtathatja azokat a Azure-SSIS IR. Ezek az eszközök a kiszolgálói végponton keresztül csatlakoznak az adatbázis-kiszolgálóhoz: 

- Privát végponttal rendelkező Azure SQL Database-kiszolgáló esetén a kiszolgálói végpont formátuma `<server name>.database.windows.net`.
- Privát végponttal rendelkező felügyelt példány esetén a kiszolgálói végpont formátuma `<server name>.<dns prefix>.database.windows.net`.
- Nyilvános végponttal rendelkező felügyelt példány esetén a kiszolgálói végpont formátuma `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Ha nem használja a SSISDB-t, a csomagokat fájlrendszerekbe, fájlmegosztásba vagy Azure Filesba is telepítheti. Ezután a Azure-SSIS IR futtathatja őket a `dtinstall`, `dtutil` és `dtexec` parancssori eszköz használatával. További információ: SSIS- [csomagok telepítése](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

Mindkét esetben a telepített csomagokat is futtathatja a Azure-SSIS IR a SSIS-csomag végrehajtása művelettel Data Factory folyamatokban. További információ: [SSIS-csomag végrehajtásának meghívása első osztályú Data Factory tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Következő lépések

További Azure-SSIS IR témakörök a jelen dokumentációban:

- [Azure-SSIS integrációs](concepts-integration-runtime.md#azure-ssis-integration-runtime)modul. Ez a cikk általában az integrációs modulokról tartalmaz információkat, beleértve a Azure-SSIS IRt is.
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ebből a cikkből megtudhatja, hogyan kérheti le és értelmezheti a Azure-SSIS IR kapcsolatos információkat.
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy törölni a Azure-SSIS IR. Azt is bemutatja, hogyan bővítheti Azure-SSIS IR több csomópont hozzáadásával.
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk a Azure-SSIS IR virtuális hálózathoz való csatlakoztatásáról nyújt tájékoztatást.
