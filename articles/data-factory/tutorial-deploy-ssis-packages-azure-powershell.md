---
title: Azure SSIS integrációs modul üzembe helyezése PowerShell segítségével | Microsoft Docs
description: Ismerje meg, hogyan helyezheti üzembe az Azure SSIS integrációs modult az Azure Data Factoryben a PowerShell használatával, hogy SSIS-csomagokat telepíthessen és futtathasson az Azure-ban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bbbbc45bd050b8f68499febeed6285ad1aa883c4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484779"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Azure SSIS integrációs modul üzembe helyezése az Azure Data Factoryben PowerShell segítségével

Ez az oktatóanyag lépéseit egy Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) az Azure Data Factory (ADF) telepítéshez. Az Azure-SSIS integrációs modul által támogatott csomagok üzembe helyezve az Azure SQL Database-kiszolgáló/Managed Instance (projekt üzembe helyezési modell) által üzemeltetett SSIS-katalógus (SSISDB) és mások által üzembe helyezett fájl rendszerek/fájl futtatása megosztások vagy az Azure Files (csomag üzembe helyezési modell). Miután az Azure-SSIS integrációs modul üzembe felhasználhatja ismert eszközökkel, például az SQL Server Data Tools (SSDT) / SQL Server Management Studio (SSMS), és a parancs parancssori segédprogramok, például `dtinstall` / `dtutil` / `dtexec`, az üzembe helyezése és az-csomagok futtatása az Azure-ban. Az oktatóanyag során a következő lépéseket hajtja végre:

> [!NOTE]
> Ez a cikk az Azure Powershellt üzembe helyezése egy Azure-SSIS integrációs modult. Az Azure portal/ADF-alkalmazás üzembe helyezése egy Azure-SSIS integrációs modul használja, lásd: [oktatóanyag: Az Azure-SSIS integrációs modul kiépítés](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul létrehozása
> * Az Azure SSIS integrációs modul elindítása
> * A teljes szkript áttekintése
> * SSIS-csomagok üzembe helyezése

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot. Elméleti információk az Azure-SSIS integrációs modulra vonatkozóan: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).
- **Azure SQL Database-kiszolgáló (nem kötelező)** . Ha Ön még nem rendelkezik adatbázis-kiszolgáló, hozzon létre egyet az Azure Portalon, a Kezdés előtt. Az ADF pedig SSISDB hoz létre az adatbázis-kiszolgálón. Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi, hogy az integrációs modul az SSISDB, az Azure-régiók határainak átlépése nélkül írjon végrehajtási naplókat. 
    - A kiválasztott adatbázis-kiszolgáló alapján az SSISDB létrehozható az Ön nevében önálló adatbázisként, egy rugalmas készlet részeként vagy egy felügyelt példányban, és elérhető nyilvános hálózatban vagy egy virtuális hálózathoz csatlakozva. Gazdagépre SSISDB adatbázis-kiszolgáló típusú megválasztásához útmutatásért lásd: [hasonlítsa össze az Azure SQL Database egy adatbázis és rugalmas készlet/Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Ha használatával Azure SQL Database-kiszolgáló virtuális hálózati szolgáltatás végpontok/Managed Instance egy virtuális hálózaton az SSISDB üzemeltetésére, illetve hozzáférést igényelnek a helyszíni adatokhoz, szeretné-e az Azure-SSIS integrációs modul csatlakoztatása egy virtuális hálózatot, lásd: [létrehozása az Azure-SSIS integrációs modul a virtuális hálózatban](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás engedélyezve van az adatbázis-kiszolgálón. Ez a nem alkalmazható virtuális hálózati szolgáltatás végpontok/felügyelt példányt az SSISDB-gazdagépre a virtuális hálózat az Azure SQL Database-kiszolgáló használata esetén. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Ezzel a beállítással engedélyezheti a PowerShell-lel [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Adja hozzá az ügyfélszámítógép IP-címét, vagy egy IP-címtartományt, amely tartalmazza az ügyfélszámítógépen, hogy az ügyfél IP-címeinek listájára az adatbázis-kiszolgáló tűzfalbeállításainál IP-címét. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../sql-database/sql-database-firewall-configure.md).
    - Az adatbázis-kiszolgáló használatával a kiszolgáló-rendszergazdai hitelesítő adataival SQL-hitelesítés vagy az Azure Active Directory (AAD) hitelesítés a felügyelt identitás az ADF csatlakozhat.  Az utóbbi esetében hozzá kell adnia az ADF felügyelt identitását egy AAD-csoporthoz, amely rendelkezik engedélyekkel az adatbázis-kiszolgálóhoz, lásd: [Azure SSIS integrációs modul létrehozása AAD-hitelesítéssel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Győződjön meg arról, hogy az adatbázis-kiszolgáló nem rendelkezik az SSISDB már. Egy Azure-SSIS IR üzembe helyezése nem támogatja egy meglévő SSISDB használatával.
- **Azure PowerShell**. Kövesse a [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/install-Az-ps), ha szeretne futtatni egy PowerShell-parancsprogram üzembe helyezése az Azure-SSIS integrációs modult.

> [!NOTE]
> - Az ADF és az Azure-SSIS integrációs modul jelenleg érhetők el Azure-régiók listáját lásd: [ADF + SSIS integrációs modul rendelkezésre állása régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="launch-windows-powershell-ise"></a>A Windows PowerShell integrált parancsprogram-kezelési környezet (ISE) indítása

Indítsa el a **Windows PowerShell integrált parancsprogram-kezelési környezetet** rendszergazdai jogosultságokkal. 

## <a name="create-variables"></a>Változók létrehozása

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

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

## <a name="sign-in-and-select-subscription"></a>Jelentkezzen be, és válassza ki az előfizetést

Adja hozzá a következő kódot a szkripthez a bejelentkezéshez, és válassza ki az Azure-előfizetésében.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-database-server"></a>Adatbázis-kiszolgálóhoz való kapcsolat ellenőrzése

Adja hozzá a következő szkriptet az Azure SQL Database-kiszolgálóhoz. 

```powershell
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
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
```

Hozzon létre egy Azure SQL Database, a szkript részeként, tekintse meg az alábbi példában: 

Állítson be értékeket az eddig még meg nem határozott változók esetében. Példa: SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) használatával a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsot. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat.

Ha az erőforráscsoport már létezik, ne másolja ezt a kódot a szkriptet. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Data factory létrehozása

Futtassa a következő parancsot egy adat-előállító létrehozásához:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Integrációs modul létrehozása

Futtassa a következő parancsok futtatásával hozzon létre egy Azure-SSIS integrációs modult, amely SSIS-csomagokat futtat az Azure-ban.

If you do not use SSISDB, you can omit CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters.

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
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
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

## <a name="start-integration-runtime"></a>Integrációs modul indítása

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
> Minden olyan egyéni beállítási idő, kivéve a folyamat 5 percen belül kell elvégezni.
>
> SSISDB használatakor ADF szolgáltatás az készíti elő az SSISDB adatbázis-kiszolgálóhoz fog csatlakozni. 
> 
> Amikor üzembe helyez egy Azure-SSIS integrációs Modult, Access Redistributable és az Azure Feature Pack for SSIS is telepítve lesz. Ezek az összetevők biztosítják a csatlakozást az Excel és Access-fájlok és a különböző Azure-adatforrásokhoz a beépített összetevők által már támogatott adatforrások mellett. További összetevőket is telepíthet lásd [Azure-SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="full-script"></a>Teljes szkript

Ez a szakasz a PowerShell-parancsprogram konfigurálja, amely SSIS-csomagokat futtat az Azure-SSIS integrációs modul egy példányát. Ez a szkript sikeres futtatása után telepítheti és SSIS-csomagok futtatása az Azure-ban.

1. Indítsa el a Windows PowerShell integrált parancsfájlkezelési környezetet (ISE).
2. Az ISE környezetben futtassa a következő parancsot a parancssorból.  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Másolja az ebben a szakaszban lévő PowerShell-szkriptet, majd illessze be az ISE környezetbe.
4. Adja meg a szkript elején a paramétereknek megfelelő értékeket.
5. Futtassa a szkriptet. 

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

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you want to use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
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
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
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

## <a name="monitor-and-manage-azure-ssis-ir"></a>Az Azure-SSIS integrációs modul monitorozása és kezelése

Azure-SSIS integrációs modul monitorozásával és kezelésével kapcsolatban lásd az alábbi cikkeket. 

- [Monitor Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Manage Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése

SSISDB használatakor az-csomagok üzembe, és futtathatja az Azure-SSIS integrációs modul, amely az adatbázis-kiszolgálóhoz, a kiszolgálói végpont-n keresztül csatlakozni az SSDT/SSMS eszközök használatával. Az Azure SQL Database-kiszolgáló/Managed instance egy nyilvános végponttal rendelkező, a kiszolgálói végpont formátuma `<server name>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`, illetve. SSISDB nem használja, ha telepíteni a fájl rendszerek/fájl megosztások/Azure-bA fájlok, és futtassa őket az Azure-SSIS integrációs modul használatával `dtinstall` / `dtutil` / `dtexec` parancssori segédeszközök. További információkért lásd: [üzembe SSIS-csomagok](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Mindkét esetben is futtathatja a telepített csomagokat az Azure-SSIS integrációs Modult az SSIS-csomag végrehajtása tevékenység ADF folyamatokban, lásd: [meghívása SSIS-csomag végrehajtása egy első osztályú ADF tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Lásd még az SSIS dokumentációjának alábbi cikkeit: 

- [Üzembe helyezése, futtatása és figyelése az Azure-beli SSIS-csomagok](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Csatlakozás az Azure-ban SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Az Azure-ban csomag végrehajtás ütemezése](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul létrehozása
> * Az Azure SSIS integrációs modul elindítása
> * A teljes szkript áttekintése
> * SSIS-csomagok üzembe helyezése

Ha többet szeretne megtudni az Azure-SSIS integrációs modul testreszabásáról, folytassa a következő cikkel:

> [!div class="nextstepaction"]
>[Az Azure-SSIS integrációs modul testreszabása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)