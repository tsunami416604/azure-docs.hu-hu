---
title: Azure-SSIS integrációs futásidejű létrehozása az Azure Data Factory |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy Azure-SSIS-integrációs futásidejű az Azure Data Factoryben, telepítheti és SSIS-csomagok futtassa az Azure-ban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f75d08074fc6020541226318d6422da373136a2d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268149"
---
# <a name="create-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Az Azure-SSIS-integrációs futásidejű létrehozása az Azure Data Factory
Ez a cikk lépéseit egy Azure-SSIS-integráció futtatókörnyezetet, az Azure Data Factory történő üzembe helyezéséhez. Ezután használhatja SQL Server Data Tools (SSDT) vagy SQL Server Management Studio (SSMS) telepítéséhez és futtatásához az SQL Server Integration Services (SSIS) csomag az Azure-ban a futtatókörnyezetben.

Az oktatóanyag [oktatóanyag: központilag telepíteni az SQL Server Integration Services (SSIS) Azure](tutorial-create-azure-ssis-runtime-portal.md) bemutatja, hogyan hozzon létre egy Azure-SSIS integrációs futásidejű (IR) Azure SQL Database segítségével a SSIS-katalógust. Ez a cikk kibővíti az oktatóanyag, és bemutatja, hogyan tegye a következőket: 

- Opcionálisan segítségével az Azure SQL Database a virtuális hálózati szolgáltatás végpontok/felügyelt példány (előzetes verzió) az adatbázis-kiszolgálójaként, a SSIS-katalógus (SSISDB adatbázis). Előfeltételként szüksége lesz az Azure-SSIS-IR csatlakoztatása egy virtuális hálózatot, és konfigurálja a virtuális hálózati engedélyek és beállítások szerint szükséges, lásd: [csatlakozás Azure-SSIS IR egy virtuális hálózathoz](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  
- Opcionálisan Azure Active Directory (AAD) hitelesítés használatára az az Azure adatok gyári felügyelt szolgáltatás Identity (MSI) az Azure-SSIS-IR az adatbázis-kiszolgálóhoz való csatlakozáshoz. Előfeltételként szüksége lesz egy AAD csoporthoz, az adatbázis-kiszolgáló rendelkezik hozzáférési engedélyekkel a Data Factory MSI hozzáadni, lásd: [engedélyezése az AAD-hitelesítés az Azure-SSIS-IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd [a Data Factory 1. verziójának dokumentációját](v1/data-factory-introduction.md).


## <a name="overview"></a>Áttekintés
Ez a cikk egy Azure-SSIS-IR-kiépítés különböző módokat ismerteti:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager-sablon](#azure-resource-manager-template)

Egy Azure-SSIS-IR létrehozásakor adat-előállító kapcsolódik az Azure SQL-adatbázis előkészítése a SSIS-katalógus-adatbázis (SSISDB). A parancsfájl is engedélyek és a virtuális hálózatra vonatkozó beállításainak konfigurálása, ha meg van adva, és csatlakoztatja a virtuális hálózat az Azure-SSIS integrációs futásidejű új példányát.

Amikor üzembe helyezi az Azure-SSIS IR egy példányát, az Azure Feature Pack for SSIS és az Access Redistributable is telepítve lesz. Ezek az összetevők biztosítják a csatlakozást az Excel- és Access-fájlokhoz és különböző Azure-adatforrásokhoz a beépített összetevők által támogatott adatforrások mellett. Ezenkívül további összetevőket is telepíthet. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, létrehozhat egy [ingyenes próbafiókot](http://azure.microsoft.com/pricing/free-trial/).
- **Az Azure SQL Database server/felügyelt példány (előzetes verzió)**. Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. Ez a kiszolgáló üzemelteti az SSIS-katalógusadatbázist (SSISDB-t). Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi, hogy az integrációs modul Azure-régiók határainak átlépése nélkül írjon végrehajtási naplókat a katalógusadatbázisba. A kiválasztott adatbázis-kiszolgáló alapján, SSISDB hozhatók létre az Ön nevében egy önálló adatbázis, a rugalmas készlethez, vagy egy felügyelt példányban (előzetes verzió), és elérhető nyilvános hálózatot, vagy a virtuális hálózatokhoz való csatlakozás. Az Azure SQL Database támogatott tarifacsomagok listáját lásd: [SQL-adatbázis erőforrás korlátok](../sql-database/sql-database-resource-limits.md).

    Győződjön meg arról, hogy az Azure SQL Database server/felügyelt példány (előzetes verzió) nem rendelkezik egy SSIS-katalógus (SSIDB adatbázis). Az Azure-SSIS IR üzembe helyezése nem támogatja a meglévő SSIS-katalógusok használatát.
- **Klasszikus vagy az Azure Resource Manager virtuális hálózat (nem kötelező)**. Egy Azure virtuális hálózatra kell rendelkeznie, ha a következő feltételek valamelyike teljesül:
    - Virtuális hálózati szolgáltatás végpontok/felügyelt példány (előzetes verzió), amely a virtuális hálózaton belül az Azure SQL Database SSIS-katalógus adatbázis üzemeltet.
    - Az Azure SSIS integrációs modulon futó SSIS-csomagokkal helyszíni adattárakhoz szeretne csatlakozni.
- **Azure PowerShell**. Kövesse az utasításokat a [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/install-azurerm-ps), a PowerShell parancsfájl futtatása az Azure-SSIS rendelkezés integrációs futásidejű SSIS-csomagok a felhőben futó használatakor. 

> [!NOTE]
> - 2-es verziójú adat-előállítót a következő régiókban hozhat létre: USA keleti régiója, USA 2. keleti régiója, Délkelet-Ázsia és Nyugat-Európa. 
> - A következő régiókban hozhat létre Azure-SSIS integrációs modult: USA keleti régiója, USA 2. keleti régiója, USA középső régiója, USA 2. nyugati régiója, Észak-Európa, Nyugat-Európa, az Egyesült királyság déli régiója és Kelet-Ausztrália.

## <a name="azure-portal"></a>Azure Portal
Ebben a szakaszban használhatja az Azure-portálon, kifejezetten a Data Factory felhasználói felületén hozzon létre egy Azure-SSIS infravörös 

### <a name="create-a-data-factory"></a>Data factory létrehozása

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).    
3. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Az **Új data factory** lapon, a **Név** mezőben adja meg a következőt: **MyAzureSsisDataFactory**. 
      
     ![Új adat-előállító lap](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a következő hibaüzenetet kapja, változtassa meg az adat-előállító nevét (például sajátneveMyAzureSsisDataFactoryra), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezésére vonatkozó részleteket a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`

3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
      Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. Válassza a **V2 (előzetes verzió)** értéket a **verzió** esetén.
5. Válassza ki a Data Factory **helyét**. A listában csak az adat-előállítók létrehozását támogató helyek jelennek meg.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
   ![Data factory kezdőlap](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. A Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul üzembe helyezése

1. Az első lépések oldalán kattintson az **SSIS integrációs modul konfigurálása** csempére. 

   ![SSIS integrációs modul konfigurálása csempe](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Az **Integrációs modul beállításai** lap **Általános beállítások** oldalán végezze el az alábbi lépéseket: 

    ![Általános beállítások](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. A **neve**, adja meg a integrációs futásidejű nevét.
    
    b. A **leírás**, írja be a integrációs futásidejű leírását.
    
    c. A **hely**, válassza ki a integrációs futásidejű helyét. Csak a támogatott helyek jelennek meg. Azt javasoljuk, hogy bejelöli-e a gazdagépen SSISDB adatbázis-kiszolgáló ugyanazon a helyen.
    
    d. A **csomópont méretének**, válassza ki a csomópont-méretét a integrációs futásidejű fürtben. Egyetlen támogatott csomópont méretek jelennek meg. A nagy méretű csomópont mérete (felskálázott), válassza, ha sok compute/memória futtatni kívánt – intenzív csomagok.
    
    e. A **csomópontszám**, válassza ki a csomópontok számát a integrációs futásidejű fürtben. Egyetlen támogatott csomópont számok jelennek meg. A nagy fürtben sok csomópontot (bővített), akkor válassza, ha azt szeretné, a csomagok száma párhuzamos futtatása.
     
    f. A **Edition/licenc**, válassza ki az SQL Server edition/licencet a integrációs futásidejű: Standard vagy Enterprise. Vállalati, akkor válassza, ha azt szeretné, az integráció Runtime speciális/premium szolgáltatásainak használatával. 
    
    g. A **mentése pénz**, adja meg Azure hibrid juttatás (AHB) beállítását a integrációs futásidejű: Igen vagy nem értéket. Igen, akkor válassza, ha azt szeretné, a saját Software Assurance költséghatékony hibrid használatával kihasználják az SQL Server-licenc.
    
    h. Kattintson a **Tovább** gombra. 
3. Az **SQL-beállítások** oldalon végezze el a következő lépéseket: 

    ![SQL-beállítások](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. A **előfizetés**, válassza ki az Azure-előfizetés, amely rendelkezik a gazdagéphez SSISDB adatbázis-kiszolgáló. 
    
    b. A **hely**, adja meg a helyét a gazdagépen SSISDB adatbázis-kiszolgáló. Azt javasoljuk, hogy bejelöli-e az integráció futtatókörnyezet ugyanazon a helyen.
    
    c. A **katalógus adatbázis-kiszolgáló végpont**, válassza ki a végpontot a gazdagépre SSISDB adatbázis-kiszolgáló. A kiválasztott adatbázis-kiszolgáló alapján, SSISDB hozhatók létre az Ön nevében egy önálló adatbázis, a rugalmas készlethez, vagy egy felügyelt példányban (előzetes verzió), és elérhető nyilvános hálózatot, vagy a virtuális hálózatokhoz való csatlakozás.
    
    d. A **használata AAD-hitelesítés...**  jelölőnégyzetet, válassza ki a gazdagéphez SSISDB adatbázis-kiszolgáló a hitelesítési módszert: SQL- vagy Azure Active Directory (AAD) az az Azure adatok gyári felügyelt szolgáltatás identitásának (MSI). Ha bejelöli, szeretné-e az aad-ben csoporthoz az adatbázis-kiszolgáló rendelkezik hozzáférési engedélyekkel a Data Factory MSI hozzáadni, lásd: [engedélyezése az AAD-hitelesítés az Azure-SSIS-IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
    
    e. A **rendszergazda felhasználóneve**, adja meg az SQL-hitelesítéshez használt felhasználónevet az adatbázis-kiszolgáló állomás SSISDB lesz.
    
    f. A **rendszergazdai jelszó**, az adatbázis-kiszolgáló gazdagépre SSISDB SQL hitelesítési jelszó megadása.  
    
    g. A **katalógus adatbázis szolgáltatási rétegben**, válassza ki a szolgáltatási rétegben a gazdagéphez SSISDB adatbázis-kiszolgáló: Basic vagy Standard vagy prémium csomagban vagy a rugalmas készlet nevét.
    
    h. Kattintson a **kapcsolat tesztelése** , és ha sikeres, kattintson **következő**. 
4.  Az a **speciális beállítások** lapján tegye a következőket:   

    ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
    
    a. A **maximális párhuzamos végrehajtások / csomópont**, válassza ki a csomagok egyes a integrációs futásidejű fürt csomópontjain egyidejűleg végrehajtása maximális számát. Csak akkor támogatott a csomag számok jelennek meg. Jelöljön ki egy kis számot, ha azt szeretné, egynél több core segítségével futtassa egy egyetlen nagy/aktív számítási/memória-intenzív. A magas érték, akkor válassza, ha azt szeretné, egy vagy több kis/passzív csomagot egyetlen mag futtatásához. 
    
    b. A **egyéni telepítő tároló SAS URI**, opcionálisan közös hozzáférésű Jogosultságkód (SAS) egységes erőforrás-azonosító (URI) az Azure Storage-Blob tároló adja meg a telepítési parancsfájlt és a hozzá kapcsolódó fájlok tárolására, tekintse meg a [Egyéni beállítása az Azure-SSIS-IR](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 
5. A **olyan virtuális hálózatot válasszon...**  jelölőnégyzetet, adja meg, hogy az integrációs futásidejű egy virtuális hálózathoz csatlakozni szeretne. Ellenőrizze azt, hogy használ az Azure SQL Database virtuális hálózati szolgáltatás végpontok/felügyelt példány (előzetes verzió) SSISDB vagy hozzáférést igényelnek a helyszíni adatokhoz; Ez azt jelenti, hogy rendelkezik a helyszíni adatok források/célok a SSIS-csomagok, lásd: [csatlakozás Azure-SSIS-IR egy virtuális hálózathoz](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Ha bejelöli, kövesse az alábbi lépéseket:


    ![Virtuális hálózat a Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    a. A **előfizetés**, válassza ki az Azure-előfizetés, amely rendelkezik a virtuális hálózat. 
    
    b. A **hely**, ugyanazon a helyen, az integráció futtatókörnyezet van kiválasztva.
    
    b. A **típus**, adja meg a virtuális hálózat: klasszikus vagy Azure Resource Manager. Azt javasoljuk, hogy Azure Resource Manager virtuális hálózatot választ, óta klasszikus virtuális hálózati elavulttá válik hamarosan.
    
    c. A **VNet neve**, válassza ki a virtuális hálózat nevét. Ez a virtuális hálózat ugyanazt a virtuális hálózatot az Azure SQL Database a virtuális hálózati szolgáltatás végpontok/felügyelt példány (előzetes verzió) tárolására szolgálnak SSISDB és vagy a csatlakozik a helyi hálózaton kell lennie.

    d. A **alhálózati név**, válassza ki a virtuális hálózati alhálózat nevét. Ez a virtuális hálózat SSISDB gazdagépre kell felügyelt példány (előzetes verzió) használttól különböző alhálózatokhoz.
6. Kattintson a **VNet érvényesítési** , és ha sikeres, kattintson **Befejezés** létrehozása az Azure-SSIS-integrációs futásidejű elindításához. 

    > [!IMPORTANT]
    > - A folyamat elvégzése hozzávetőleg 20 percet vesz igénybe.
    > - A Data Factory szolgáltatás csatlakozik az Azure SQL Database-hez, és előkészíti az SSIS-katalógusadatbázist (SSISDB). A parancsfájl is engedélyek és a virtuális hálózatra vonatkozó beállításainak konfigurálása, ha meg van adva, és csatlakoztatja a virtuális hálózat az Azure-SSIS integrációs futásidejű új példányát.
7. A **Kapcsolatok** ablakban váltson az **Integrációs modulok** lapra, ha szükséges. Az állapot frissítéséhez kattintson a **Frissítés** elemre. 

    ![Létrehozás állapota](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. A hivatkozások **műveletek** leállítása vagy elindítása, szerkesztése vagy törlése a integrációs futásidejű oszlop. Az integrációs modulhoz tartozó JSON-kód megtekintéséhez használja a legutolsó hivatkozást. A szerkesztés és törlés gombok csak akkor használhatók, ha az integrációs modul le van állítva. 

    ![Azure SSIS integrációs modul – műveletek](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS integrációs modulok a portálon

1. Az Azure Data Factory felhasználói felületén váltson a **szerkesztési** lapra, kattintson a **Kapcsolatok** lehetőségre, majd váltson az **integrációs modulok** lapjára az adat-előállítójában megtalálható integrációs modulok megtekintéséhez. 
    ![Létező integrációs modulok megtekintése](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Egy új Azure-SSIS integrációs modul létrehozásához kattintson az **Új** elemre. 

    ![Integrációs modul elérése a menüből](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Egy Azure-SSIS integrációs modul létrehozásához kattintson az **Új** gombra a képen látható módon. 
3. Az Integrációs modul telepítése ablakban válassza a **Létező SSIS-csomagok áthelyezése az Azure-ban történő végrehajtáshoz** lehetőséget, majd kattintson a **Tovább** gombra.

    ![Integrációs modulok típusának megadása](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Az Azure-SSIS integrációs modul beállításához szükséges lépésekkel kapcsolatos információkért tekintse meg az [Azure SSIS integrációs modul üzembe helyezésével](#provision-an-azure-ssis-integration-runtime) foglalkozó részt.

## <a name="azure-powershell"></a>Azure PowerShell
Ebben a szakaszban az Azure PowerShell használatával hozzon létre egy Azure-SSIS infravörös

### <a name="create-variables"></a>Változók létrehozása
Ebben az oktatóanyagban változókat határozhat meg a szkriptben való használatra:

```powershell
### Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = “[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = “[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"
```

### <a name="log-in-and-select-subscription"></a>Bejelentkezés és az előfizetés kiválasztása
Adja hozzá a következő kódot a parancsfájlt, amellyel be, és válassza ki az Azure-előfizetéséhez: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Az adatbázissal való kapcsolat ellenőrzése
Adja hozzá a következő parancsfájl az Azure SQL Database-kiszolgáló végpont érvényesítéséhez. 

```powershell
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    if(![string]::IsNullOrEmpty($SSISDBPricingTier))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=“ + $SSISDBServerAdminPassword
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_"  ;
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
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
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
A következő parancsokat egy Azure-SSIS-integrációs futásidejű SSIS-csomagok az Azure-ban futó létrehozásához. 

Ha nem használja az Azure SQL Database a virtuális hálózati szolgáltatás végpontok/felügyelt példány (előzetes verzió) SSISDB üzemeltetni, és hozzáférést igényelnek a helyszíni adatokhoz, VNetId és alhálózati paraméter nincs megadva, vagy üres értéket átadni a számukra. Ellenkező esetben akkor nem hagyja ki őket, és az érvényes értékek adhatók át a virtuális hálózati konfiguráció, lásd: [csatlakozás Azure-SSIS-IR egy virtuális hálózathoz](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Ha felügyelt példány (előzetes verzió) segítségével állomás SSISDB, CatalogPricingTier paraméter nincs megadva, vagy át azt egy értéke üres. Ellenkező esetben akkor nem nincs megadva, és adjon át egy érvényes értéket közül támogatott tarifacsomagok az Azure SQL Database, lásd: [SQL-adatbázis erőforrás korlátok](../sql-database/sql-database-resource-limits.md). 

Használatához Azure Active Directory (AAD) hitelesítés a a Azure adatok gyári felügyelt szolgáltatás Identity (MSI) az adatbázis-kiszolgálóhoz való csatlakozáshoz CatalogAdminCredential paraméter, akkor kihagyhatja, de egy AAD csoport hozzáférést kell adnia a Data Factory MSI az adatbázis-kiszolgáló, az engedélyek, lásd: [engedélyezése az AAD-hitelesítés az Azure-SSIS-IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

```powershell               
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

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

Végre kell hajtania az Azure SQL felügyelt példánnyal (előzetes verzió), amelyhez csatlakozik egy virtuális hálózati VnetId és alhálózati paraméter értékét. A CatalogPricingTier paraméter nem érvényes az Azure SQL felügyelt példány (előzetes verzió). 

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

Ez a teljes parancsfájl, amely egy Azure-SSIS-integrációs futásidejű hoz létre. 

```powershell
### Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = “[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = “[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"

### Log in and select subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    if(![string]::IsNullOrEmpty($SSISDBPricingTier))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=“ + $SSISDBServerAdminPassword
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_"  ;
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
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
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
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

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
Ebben a szakaszban az Azure Resource Manager-sablon létrehozása az Azure-SSIS integrációs futásidejű használja. Íme egy minta forgatókönyv: 

1. Hozzon létre egy JSON-fájlt a következő Azure Resource Manager sablon. Cserélje le a saját értékeit értéket a csúcsos zárójelek közé (hely tartozó felhasználók számára). 

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
                            "nodeSize": "Standard_D4_v2",
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
    
2. Az Azure Resource Manager sablon üzembe helyezése, futtassa a következő példában látható módon új-AzureRmResourceGroupDeployment parancsot, ahol a ADFTutorialResourceGroup az erőforráscsoportnak a nevét, és a ADFTutorialARM.json a fájl, amely JSON-adatokat tartalmaz a data factory és az Azure-SSIS infravörös definíciója 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Ez a parancs létrehozza a data factory és az Azure-SSIS-IR azt, de nem indul el a infravörös 
3. Az Azure-SSIS-IR elindításához futtassa a Start-AzureRmDataFactoryV2IntegrationRuntime parancsot: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ``` 

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése
Most az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) segítségével helyezze üzembe az SSIS-csomagokat az Azure-ban. Az adatbázis-kiszolgáló, amelyen az SSIS-katalógusban (SSISDB) kapcsolódni. Adatbázis-kiszolgáló neve nem a formátumban: &lt;Azure SQL adatbázis-kiszolgáló neve&gt;. database.windows.net vagy &lt;felügyelt példány (előzetes verzió) neve. DNS-előtagja&gt;. database.windows.net. Útmutatásért tekintse meg a [csomagok üzembe helyezésével kapcsolatos](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) cikket.

## <a name="next-steps"></a>További lépések
A jelen dokumentációban lévő más Azure-SSIS-IR témakörökben talál:

- [Azure-SSIS integrációs futásidejű](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk tájékoztatást általában többek között az Azure-SSIS infravörös integrációs futtatókörnyezetek 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutathatja, hogyan skálázhatja fel horizontálisan az Azure-SSIS integrációs modult úgy, hogy további csomópontokat ad hozzá. 
- [Egy Azure-SSIS-IR csatlakoztatása egy virtuális hálózati](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk tájékoztatást az Azure-SSIS-IR csatlakoztatása az Azure virtuális hálózat. Emellett biztosítja, hogy az Azure-SSIS-IR csatlakozhat a virtuális hálózat virtuális hálózat konfigurálása az Azure-portálon használandó lépéseket. 

