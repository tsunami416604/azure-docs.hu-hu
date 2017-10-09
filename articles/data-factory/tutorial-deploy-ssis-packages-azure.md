---
title: "SSIS-csomagok üzembe helyezése az Azure-ban | Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan helyezhetők üzembe SSIS-csomagok egy Azure Data Factory által biztosított Azure SSIS integrációs modulban."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 09/06/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b16bb831f7901c5c690ca20c24eb059188c68750
ms.contentlocale: hu-hu
ms.lasthandoff: 09/25/2017

---
# <a name="deploy-sql-server-integration-services-packages-to-azure"></a>SQL Server Integration Services-csomagok üzembe helyezése az Azure-ban
Az Azure Data Factory egy felhőalapú adatintegrációs szolgáltatás. Lehetővé teszi olyan, a felhőben futó, adatvezérelt munkafolyamatok létrehozását, amelyek alkalmasak az adatok átvitelének és átalakításának irányítására és automatizálására. Az Azure Data Factory segítségével létrehozhatók és ütemezhetők a különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok, feldolgozhatók és átalakíthatók az adatok különböző számítási szolgáltatások használatával (pl.: Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics és Azure Machine Learning), és a kimeneti adatok közzétehetők olyan adattárakban, mint az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket. 

Ez az oktatóanyag egy Azure SSIS integrációs modul az Azure Data Factoryben való üzembe helyezésének lépéseit ismerteti. Ezután az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) használatával üzembe helyezhet SQL Server Integration Services- (SSIS-) csomagokat ebben az Azure-beli modulban. Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul létrehozása
> * Az Azure SSIS integrációs modul elindítása
> * SSIS-csomagok üzembe helyezése
> * A teljes szkript áttekintése

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek
- **Azure SQL Database-kiszolgáló** vagy **felügyelt SQL Server-példány (privát előzetes verzió) (bővített privát előzetes verzió)**. Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. Ez a kiszolgáló üzemelteti az SSIS-katalógusadatbázist (SSISDB-t). Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi, hogy az integrációs modul Azure-régiók határainak átlépése nélkül írjon végrehajtási naplókat a katalógusadatbázisba. 
- **Klasszikus virtuális hálózat (VNet) (nem kötelező)**. Legalább egy Azure virtuális hálózat (VNet) szükséges, ha a következő feltételek bármelyike igaz:
    - Az SSIS-katalógusadatbázis egy olyan felügyelt SQL Server-példányon (privát előzetes verzió) fut, amely egy virtuális hálózat része.
    - Az Azure SSIS integrációs modulon futó SSIS-csomagokkal helyszíni adattárakhoz szeretne csatlakozni.
- **Azure PowerShell**. Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-azurerm-ps) ismertető cikkben szereplő utasításokat. Egy PowerShell-lel futtatott szkripttel üzembe helyez egy Azure SSIS integrációs modult, amely a felhőben futtat SSIS-csomagokat. 

## <a name="create-variables"></a>Változók létrehozása
Ebben az oktatóanyagban változókat határozhat meg a szkriptben való használatra:

```powershell
# Azure Data Factory version 2 information
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" # In public preview, only EastUS|EastUS2 are supported.

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
$AzureSSISLocation = "EastUS" # In public preview, only EastUS|NorthEurope are supported.
$AzureSSISNodeSize = "Standard_A4_v2" # In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeNumber = 2 # In public preview, only 1-10 nodes are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 # In public preview, only 1-8 parallel executions per node are supported.
$VnetId = "[your VNet resource ID or leave it empty]" # OPTIONAL: In public preview, only classic virtual network (VNET) is supported.
$SubnetName = "[your subnet name or leave it empty]" # OPTIONAL: In public preview, only classic VNet is supported.

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"
$SSISDBPricingTier = "[your Azure SQL Database pricing tier, e.g. S3, or leave it empty for Azure SQL Managed Instance (private preview)]" # Not applicable for Azure SQL Managed Instance (private preview)
```

## <a name="validate-the-connection-to-database"></a>Az adatbázissal való kapcsolat ellenőrzése
Adja hozzá a következő szkriptet az Azure SQL Database-kiszolgáló (server.database.windows.net) vagy a felügyelt Azure SQL-példány (privát előzetes verzió) kiszolgálóvégpontjának ellenőrzéséhez. 

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

## <a name="login-and-select-subscription"></a>Bejelentkezés és az előfizetés kiválasztása
Adja a következő kódot a szkripthez a bejelentkezéshez és az Azure-előfizetés kiválasztásához: 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása
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

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Data factory létrehozása
Futtassa a következő parancsot egy adat-előállító létrehozásához:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Integrációs modul létrehozása
Futtassa a következő parancsot egy Azure SSIS integrációs modul létrehozásához, amely SSIS-csomagokat futtat az Azure-ban: 

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
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

## <a name="start-integration-runtime"></a>Integrációs modul indítása
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

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése
Most az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) segítségével helyezze üzembe az SSIS-csomagokat az Azure-ban. Csatlakozzon ahhoz az Azure SQL Serverhez, amelyen az SSIS-katalógus (SSISDB) található. Az Azure SQL Server nevének formátuma a következő: &lt;servername&gt;.database.windows.net (az Azure SQL Database esetében). Útmutatásért tekintse meg a [csomagok üzembe helyezésével kapcsolatos](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) cikket. 

## <a name="full-script"></a>Teljes szkript
Ebben a kiadásban a PowerShell-lel kell üzembe helyeznie egy Azure SSIS integrációs modult, amely SSIS-csomagokat futtat a felhőben. A modult jelenleg nem lehet az Azure Portalról üzembe helyezni. 

Az ebben a szakaszban szereplő PowerShell-szkript konfigurál a felhőben egy Azure SSIS integrációs modul példányt, amely SSIS-csomagokat futtat. A szkript sikeres futtatása után SSIS-csomagokat helyezhet üzembe és futtathat a Microsoft Azure-felhőben az Azure SQL Database-ben vagy egy felügyelt SQL Server-példányon (privát előzetes verzión) futó SSISDB mellett.

1. Indítsa el a Windows PowerShell integrált parancsfájlkezelési környezetet (ISE).
2. Az ISE környezetben futtassa a következő parancsot a parancssorból.    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. Másolja az ebben a szakaszban lévő PowerShell-szkriptet, majd illessze be az ISE környezetbe.
4. Adja meg a szkript elején, az Azure-beli SSIS specifikációira vonatkozó szakaszban lévő szkriptparaméterek megfelelő értékeit. Ezeket a paramétereket a következő szakasz ismerteti.
5. Futtassa a szkriptet. A szkript végén lévő `Start-AzureRmDataFactoryV2IntegrationRuntime` parancs futtatása **20–30 percet** vesz igénybe.

> [!NOTE]
> A szkript csatlakozik az Azure SQL Database-hez vagy felügyelt SQL Server-példányhoz (privát előzetes verzió), és előkészíti az SSIS-katalógusadatbázist (SSISDB). A szkript konfigurálja a virtuális hálózat engedélyeit és beállításait is, ha ez lett megadva, és csatlakoztatja az Azure SSIS integrációs modul új példányát a virtuális hálózathoz.


```powershell
# If your inputs contain PSH special characters, e.g. "$", please precede it with the escape character "`" like "`$". 
# Azure Data Factory v2 information
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" # In Public Preview, only EastUS|EastUS2 are supported for now

# Azure-SSIS Integration Runtime info - This is Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS Integration Runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS Integration Runtime"
$AzureSSISLocation = "EastUS" # In Public Preview, only EastUS|NorthEurope are supported for now
$AzureSSISNodeSize = "Standard_A4_v2" # In Public Preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported for now
$AzureSSISNodeNumber = 2 # In Public Preview, only 1-10 nodes are supported for now
$AzureSSISMaxParallelExecutionsPerNode = 2 # In Public Preview, only 1-8 parallel executions per node are supported for now
$VnetId = "[your VNet resource ID or leave it empty]" # OPTIONAL: In Public Preview, only Classic VNet is supported for now
$SubnetName = "[your subnet name or leave it empty]" # OPTIONAL: In Public Preview, only Classic VNet is supported for now

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"
$SSISDBPricingTier = "[your Azure SQL Database pricing tier, e.g. S3, or leave it empty for Azure SQL Managed Instance (private preview)]" # Not applicable for Azure SQL Managed Instance (private preview)

##### Validate your Azure SQL Database/Managed Instance (private preview) server ##### 
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

##### Login and and select your Azure subscription #####
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

##### Automatically configure VNet permissions/settings for your Azure-SSIS Integration Runtime to join ##### 
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

##### Provision your Azure Data Factory  + Azure-SSIS Integration Runtime ##### 
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
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

##### Query/monitor your Azure-SSIS Integration Runtime #####
#Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status

##### Reconfigure your Azure-SSIS Integration Runtime, e.g. stopping/scaling out to 5 nodes/starting #####
# Stopping your Azure-SSIS Integration Runtime will release its nodes and stop billing
#Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 

# Scaling out your Azure-SSIS Integration Runtime to 5 nodes
#Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5

# Starting your Azure-SSIS Integration Runtime will allocate its nodes and start billing
#Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName

##### Clean up ######
#Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
#Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
#Remove-AzureRmDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
#Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force

```
## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul létrehozása
> * Az Azure SSIS integrációs modul elindítása
> * SSIS-csomagok üzembe helyezése
> * A teljes szkript áttekintése

Folytassa a következő oktatóanyaggal, amelyben az adatok a helyszíni rendszerből a felhőbe másolásának menetét ismerheti meg: 

> [!div class="nextstepaction"]
>[Adatok másolása a felhőben](tutorial-copy-data-dot-net.md)

