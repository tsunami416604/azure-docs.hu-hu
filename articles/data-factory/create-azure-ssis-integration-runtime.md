---
title: "Önálló üzemeltetett integrációs futásidejű létrehozása az Azure Data Factory |} Microsoft Docs"
description: "Ismerje meg, hogyan használhatja az SQL Server tárolt eljárási tevékenység meghívni a Data Factory-folyamat az az Azure SQL Database vagy az Azure SQL Data Warehouse tárolt eljárást."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: 7636f502a7dc631b96c3f091a6622c7db301b035
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Egy Azure-SSIS-integrációs futásidejű létrehozása az Azure Data Factory
Ez a cikk lépéseit egy Azure-SSIS-integráció futtatókörnyezetet, az Azure Data Factory történő üzembe helyezéséhez. Ezután az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) használatával üzembe helyezhet SQL Server Integration Services- (SSIS-) csomagokat ebben az Azure-beli modulban.

Az oktatóanyag: [oktatóanyag: központilag telepíteni az SQL Server Integration Services (SSIS) Azure](tutorial-deploy-ssis-packages-azure.md) bemutatta, hogyan hozhat létre egy Azure-SSIS integrációs futásidejű (IR) használatával az Azure SQL Database a tároló SSIS-katalógus. Ez a cikk kibővíti az oktatóanyag, és bemutatja, hogyan tegye a következőket: 

- Azure SQL felügyelt példány (magán előnézetben) használata egy SSIS katalógust (SSISDB adatbázis).
- Azure-SSIS-IR csatlakoztassa egy Azure virtuális hálózatot (VNet). Egy Azure-SSIS-IR csatlakoztatása egy virtuális hálózatot és egy virtuális hálózat konfigurálása az Azure-portálon kapcsolatos információkat lásd: [Azure-SSIS-IR csatlakoztassa a virtuális hálózatba](join-azure-ssis-integration-runtime-virtual-network.md). 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd [a Data Factory 1. verziójának dokumentációját](v1/data-factory-introduction.md).


## <a name="overview"></a>Áttekintés
Ez a cikk egy Azure-SSIS-IR-kiépítés különböző módokat ismerteti:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager-sablon](#azure-resource-manager-template)

Egy Azure-SSIS-IR létrehozásakor adat-előállító kapcsolódik az Azure SQL-adatbázis előkészítése a SSIS-katalógus-adatbázis (SSISDB). A szkript konfigurálja a virtuális hálózat engedélyeit és beállításait is, ha ez lett megadva, és csatlakoztatja az Azure SSIS integrációs modul új példányát a virtuális hálózathoz.

Ha az SQL-adatbázis üzemeltetéséhez SSISDB példánya, az Azure funkciócsomag SSIS és a hozzáférés Redistributable is települnek. Ezeket az összetevőket a kapcsolat az Excel és a hozzáférés fájlokhoz és különböző Azure-adatforrással, a beépített összetevők által támogatott adatforrások mellett azt is adja meg. Az SSIS külső összetevőket (beleértve a külső összetevők a Microsofttól, például az Oracle- és Teradata attunity és az SAP BI összetevői) jelenleg nem telepíthető.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, létrehozhat egy [ingyenes próbafiókot](http://azure.microsoft.com/pricing/free-trial/).
- **Azure SQL Database-kiszolgáló** vagy **felügyelt SQL Server-példány (privát előzetes verzió) (bővített privát előzetes verzió)**. Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. Ez a kiszolgáló üzemelteti az SSIS-katalógusadatbázist (SSISDB-t). Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi, hogy az integrációs modul Azure-régiók határainak átlépése nélkül írjon végrehajtási naplókat a katalógusadatbázisba. Jegyezze fel az Azure SQL-kiszolgáló tarifacsomagját. Az Azure SQL Database támogatott tarifacsomagok listáját lásd: [SQL-adatbázis erőforrás korlátok](../sql-database/sql-database-resource-limits.md).

    Győződjön meg arról, hogy az Azure SQL Database-kiszolgálóhoz vagy az SQL Server-felügyelt példány (kiterjesztett magán előnézetben) nem rendelkezik egy SSIS-katalógus (SSIDB adatbázis). Az Azure-SSIS-IR kiépítése nem támogatja a meglévő SSIS-katalógus használatával.
- **Klasszikus virtuális hálózat (VNet) (nem kötelező)**. Legalább egy Azure virtuális hálózat (VNet) szükséges, ha a következő feltételek bármelyike igaz:
    - Az SSIS-katalógusadatbázis egy olyan felügyelt SQL Server-példányon (privát előzetes verzió) fut, amely egy virtuális hálózat része.
    - Az Azure SSIS integrációs modulon futó SSIS-csomagokkal helyszíni adattárakhoz szeretne csatlakozni.
- **Azure PowerShell**. Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-azurerm-ps) ismertető cikkben szereplő utasításokat. Egy PowerShell-lel futtatott szkripttel üzembe helyez egy Azure SSIS integrációs modult, amely a felhőben futtat SSIS-csomagokat. 

> [!NOTE]
> Az Azure Data Factory 2-es verziója és az Azure-SSIS integrációs modulja által támogatott régiókat a [régiónként elérhető termékek](https://azure.microsoft.com/regions/services/) listájában tekintheti meg. Bontsa ki az **Adatok + analitika** csomópontot a **Data Factory 2. verziója** és az **SSIS integrációs modul** megtekintéséhez.

## <a name="azure-portal"></a>Azure Portal
Ebben a szakaszban használhatja az Azure-portálon, kifejezetten a Data Factory felhasználói felületén hozzon létre egy Azure-SSIS infravörös 

### <a name="create-a-data-factory"></a>Data factory létrehozása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).    
2. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Az a **új adat-előállító** lapján adja meg **MyAzureSsisDataFactory** a a **neve**. 
      
     ![Új előállító lap](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a következő hibaüzenet jelenik meg, változtassa meg az adat-előállítóban (például yournameMyAzureSsisDataFactory) nevét, és próbálja meg újra létrehozni. Lásd: [Data Factory - elnevezési szabályait](naming-rules.md) cikk az adat-előállító összetevők elnevezési szabályait.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
      Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. Válassza a **V2 (előzetes verzió)** értéket a **verzió** esetén.
5. Válassza ki a Data Factory **helyét**. A listában csak azok a helyek által támogatott adat-előállítók létrehozásához jelennek meg.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. A létrehozásának befejezése után megjelenik a **adat-előállító** lapon, az ábrán látható módon.
   
   ![Data factory kezdőlap](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Kattintson a **Szerző & figyelő** elindítani a Data Factory felhasználói felületének (UI) egy külön lapján. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Egy Azure SSIS-integrációs futásidejű kiépítése

1. A get elindított oldalon kattintson **SSIS-integrációs futásidejű konfigurálása** csempére. 

   ![SSIS-integrációs futásidejű csempe konfigurálása](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Az a **általános beállítások** oldalán **integrációs futásidejű telepítő**, hajtsa végre a következő lépéseket: 

   ![Általános beállítások](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Adjon meg egy **neve** az integráció futási időben.
    2. Válassza ki a **hely** az integráció futási időben. Csak a támogatott helyek jelennek meg.
    3. Válassza ki a **mérete, a csomópont** is van a SSIS-futtatókörnyezet kell konfigurálni.
    4. Adja meg a **csomópontok száma** a fürtben.
    5. Kattintson a **Tovább** gombra. 
1. Az a **SQL-beállítások**, hajtsa végre a következő lépéseket: 

    ![SQL settings](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Adja meg az Azure **előfizetés** , amely rendelkezik az Azure SQL-kiszolgáló. 
    2. Válassza ki az Azure SQL server a **katalógus adatbázis-kiszolgáló végpont**.
    3. Adja meg a **rendszergazda** felhasználónevet.
    4. Adja meg a **jelszó** a rendszergazda számára.  
    5. Válassza ki a **szolgáltatásréteg** az SSISDB adatbázis. Az alapértelmezett érték: Basic.
    6. Kattintson a **Tovább** gombra. 
1.  Az a **speciális beállítások** lapon, válassza ki az értéket a **maximális párhuzamos végrehajtások / csomópont**.   

    ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Ez a lépés **választható**. Ha telepítette a klasszikus virtuális hálózatot (VNet), amelyet a integrációs futásidejű való csatlakozáshoz válassza ki a **válasszon egy Vnetet az Azure-SSIS-integráció futási időben való csatlakozáshoz és a virtuális hálózat engedélybeállítások konfigurálása Azure-szolgáltatások engedélyezése** lehetőséget, majd hajtsa végre a következő lépéseket: 

    ![Speciális beállítások virtuális hálózaton](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. Adja meg a **előfizetés** , amely rendelkezik a klasszikus virtuális hálózatot. 
    2. Válassza ki a **VNet**. <br/>
    4. Válassza ki a **alhálózati**.<br/> 
1. Kattintson a **Befejezés** elindításához Azure-SSIS integrációs futásidejű létrehozását. 

    > [!IMPORTANT]
    > - Ez a folyamat körülbelül 20 percet vesz igénybe.
    > - A Data Factory szolgáltatásnak a katalógus SSIS-adatbázis (SSISDB) előkészítése az Azure SQL Database csatlakozik. A szkript konfigurálja a virtuális hálózat engedélyeit és beállításait is, ha ez lett megadva, és csatlakoztatja az Azure SSIS integrációs modul új példányát a virtuális hálózathoz.
7. Az a **kapcsolatok** ablakban váltson **integrációs futtatókörnyezetek** szükség esetén. Kattintson a **frissítése** a állapotának frissítéséhez. 

    ![Létrehozási állapota](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. A hivatkozások **műveletek** figyelése, leállítása vagy elindítása, szerkesztése vagy törlése a integrációs futásidejű alapját képező oszlop. Az utolsó hivatkozás segítségével megtekintheti az integrációs futásidejű JSON-kódot. A szerkesztési és törlési gombok engedélyezve vannak, csak akkor, ha az infravörös le van állítva. 

    ![Az Azure SSIS IR - műveletek](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        
9. Kattintson a **figyelő** hivatkozásra **műveletek**.  

    ![Az Azure SSIS IR - részletek](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-details.png)
10. Ha hiba történt egy **hiba** társított Azure SSIS infravörös, megjelenik a hibák számát ezen az oldalon, és a hiba részleteinek megtekintése hivatkozásra. Például ha az SSIS-katalógus már létezik a kiszolgálón, hibaüzenet jelenik meg, amely jelzi az SSISDB adatbázis létezését.  
11. Kattintson a **integrációs futtatókörnyezetek** lépjen vissza az előző lapot, melyen megtekintheti az adat-előállítóban társított összes integrációs futtatókörnyezetek legfelül.  

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Az Azure SSIS-integráció futtatókörnyezetek a portálon

1. Az Azure Data Factory felhasználói felületén váltani a **szerkesztése** lapra, majd **kapcsolatok**, és majd átváltása **integrációs futtatókörnyezetek** lap választásával tudja megtekinteni a meglévő integrációs futtatókörnyezetek a adat-előállítóban. 
    ![Meglévő IRs megtekintése](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Kattintson a **új** egy új Azure-SSIS infravörös létrehozása 

    ![Integrációs futásidejű keresztül menü](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Egy Azure-SSIS-integrációs futásidejű létrehozásához kattintson a **új** az ábrán látható módon. 
3. Az integrációs futásidejű beállítása ablakban válassza ki a **növekedési és shift meglévő SSIS-csomagok végrehajtása az Azure-ban**, és kattintson a **következő**.

    ![Adja meg az integrációs futásidejű típusa](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Tekintse meg a [kiépíteni az Azure SSIS-integrációs futásidejű](#provision-an-azure-ssis-integration-runtime) szakasz a fennmaradó lépéseit egy Azure-SSIS infravörös beállítása

## <a name="azure-powershell"></a>Azure PowerShell
Ebben a szakaszban az Azure PowerShell használatával hozzon létre egy Azure-SSIS infravörös

### <a name="create-variables"></a>Változók létrehozása
Ebben az oktatóanyagban változókat határozhat meg a szkriptben való használatra:

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_A4_v2" 
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# In public preview, only 1-8 parallel executions per node are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

# Remove these the following two OPTIONAL variables if you are using Azure SQL Database. 
# These two parameters apply if you are using VNet and Azure SQL Managed Instance (private preview). 
# Get the following information from the properties page for your Classic Virtual Network in the Azure portal
# It should be in the format: $VnetId = "/subscriptions/<Azure Subscription ID>/resourceGroups/<Azure Resource Group>/providers/Microsoft.ClassicNetwork/virtualNetworks/<Class Virtual Network Name>"

# OPTIONAL: In public preview, only classic virtual network (VNet) is supported.
$VnetId = "[your VNet resource ID or leave it empty]" 
$SubnetName = "[your subnet name or leave it empty]" 

```
### <a name="log-in-and-select-subscription"></a>Bejelentkezés és az előfizetés kiválasztása
Adja hozzá a következő kódot a parancsfájlt, amellyel be, és válassza ki az Azure-előfizetéséhez: 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Az adatbázissal való kapcsolat ellenőrzése
Adja hozzá a következő parancsfájl érvényesíteni az Azure SQL Database-kiszolgáló server.database.windows.net vagy az Azure SQL felügyelt példány (magán előnézetben) végpontját. 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
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
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Data factory létrehozása
Futtassa a következő parancsot egy adat-előállító létrehozásához:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Integrációs modul létrehozása
Egy Azure-SSIS-integrációs futásidejű SSIS-csomagok az Azure-ban futó létrehozásához a következő parancsot: a parancsfájl használata a szakaszában az adatbázis (Azure SQL Database és alapján Az Azure SQL felügyelt példány (magán előnézetben)) használ. 

#### <a name="azure-sql-database-to-host-the-ssisdb-database-ssis-catalog"></a>Az SSISDB adatbázist (SSIS-katalógussal) Azure SQL-adatbázis 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode
```

Nem kell értéket átadni VNetId és alhálózathoz, ha nem kell a helyszíni adatok elérése, ez azt jelenti, hogy rendelkezik a helyszíni adatok források/célok a SSIS-csomagok. Át kell adnia a CatalogPricingTier paraméter értékét. Az Azure SQL Database támogatott tarifacsomagok listáját lásd: [SQL-adatbázis erőforrás korlátok](../sql-database/sql-database-resource-limits.md).

#### <a name="azure-sql-managed-instance-private-preview-to-host-the-ssisdb-database"></a>Az Azure SQL felügyelt példány (magán előnézetben) az SSISDB adatbázis

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

Azure SQL felügyelt példánnyal (magán előnézetben), amelyhez csatlakozik egy Vnetet VnetId és alhálózati paraméterek értékeit továbbítani kell. A CatalogPricingTier paraméter nem érvényes az Azure SQL felügyelt példány. 

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
Ez a teljes parancsfájl, amely létrehoz egy Azure-SSIS-IR, és azt csatlakoztatja a virtuális hálózat. A parancsfájl feltételezi, hogy az Azure SQL felügyelt példány (MI) segítségével a SSIS-katalógust. 

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_A4_v2" 
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# In public preview, only 1-8 parallel executions per node are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## Remove these two OPTIONAL variables if you are using Azure SQL Database. 
## These two parameters apply if you are using VNet and Azure SQL Managed Instance (private preview). 
# In public preview, only classic virtual network (VNet) is supported.
$VnetId = "[your VNet resource ID or leave it empty]" 
$SubnetName = "[your subnet name or leave it empty]" 

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName

write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Ebben a szakaszban Azure Resource Manager-sablon létrehozása az Azure-SSIS-integrációs futásidejű használja. Íme egy minta forgatókönyv: 

1. Hozzon létre egy JSON-fájl a következő Resource Manager-sablon. Cserélje le a saját értékeit értéket a csúcsos zárójelek közé (hely tartozó felhasználók számára). 

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
                "name": "<Specify a name for the Azure SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D1_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 1
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL server>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL user",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Password>"
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
2. A Resource Manager sablon történő üzembe helyezéséhez futtassa a New-AzureRmResourceGroupDeployment parancsot, ahogy az a következő exmaple. Ebben a példában ADFTutorialResourceGroup pedig az erőforráscsoport neve. ADFTutorialARM.json a data factory és az Azure-SSIS infravörös JSON definícióját tartalmazó fájl 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Ez a parancs létrehozza a data factory és hoz létre egy Azure-SSIS-IR, de nem indul el a infravörös 
3. Az Azure-SSIS infravörös elindításához futtassa a Start-AzureRmDataFactoryV2IntegrationRuntime parancsot: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name> `
                                             -DataFactoryName <Data Factory Name> `
                                             -Name <Azure SSIS IR Name> `
                                             -Force
    ``` 

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése
Most az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) segítségével helyezze üzembe az SSIS-csomagokat az Azure-ban. Csatlakozzon ahhoz az Azure SQL Serverhez, amelyen az SSIS-katalógus (SSISDB) található. Az Azure SQL Server nevének formátuma a következő: &lt;servername&gt;.database.windows.net (az Azure SQL Database esetében). Útmutatásért tekintse meg a [csomagok üzembe helyezésével kapcsolatos](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) cikket. 

## <a name="next-steps"></a>További lépések
A jelen dokumentációban lévő más Azure-SSIS-IR témakörökben talál:

- [Azure-SSIS integrációs futásidejű](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk tájékoztatást általában többek között az Azure-SSIS infravörös integrációs futtatókörnyezetek 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-deploy-ssis-packages-azure.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutathatja, hogyan skálázhatja fel horizontálisan az Azure-SSIS integrációs modult úgy, hogy további csomópontokat ad hozzá. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz (VNethez) való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Azt is ismerteti, hogyan használható az Azure Portal a VNet oly módon való konfigurálására, hogy az Azure-SSIS integrációs modul csatlakozhasson a virtuális hálózathoz. 