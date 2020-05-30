---
title: Azure-SSIS Integration Runtime beállítása a PowerShell-lel
description: Megtudhatja, hogyan állíthat be egy Azure-SSIS Integration Runtime Azure Data Factory a PowerShellben, hogy SSIS-csomagokat telepítsen és futtasson az Azure-ban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 03/27/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 6ffc1aa6e28bf17d0de3783e5e03b6a2df541e4a
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194643"
---
# <a name="set-up-an-azure-ssis-ir-in-azure-data-factory-by-using-powershell"></a>Azure Data Factory Azure-SSIS IR beállítása a PowerShell használatával

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez az oktatóanyag azt ismerteti, hogyan használható a PowerShell egy Azure-SQL Server Integration Services Integration Runtime (Azure-SSIS IR) Azure Data Factory-ben való kiépítéséhez. Az Azure-SSIS IR a következőre telepített csomagok futtatását támogatja:

* SQL Database vagy SQL felügyelt példányban (a projekt-telepítési modellben) üzemeltetett SSIS-katalógus (SSISDB).
* Fájlrendszerek, fájlmegosztás vagy Azure Files-megosztás (a csomag telepítési modellje). 

Egy Azure-SSIS IR kiépítése után jól ismert eszközökkel üzembe helyezheti és futtathatja a csomagokat az Azure-ban. Ezek az eszközök a következők: SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) és parancssori eszközök, például:, `dtinstall` `dtutil` és `dtexec` .

Elméleti információk az Azure-SSIS integrációs modulokról: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).

> [!NOTE]
> Ez a cikk bemutatja, hogyan állíthatja be a Azure-SSIS IRt a Azure PowerShell használatával. Ha a Azure-SSIS IR beállításához a Azure Portal vagy Azure Data Factory alkalmazást szeretné használni, tekintse meg az [oktatóanyag: Azure-SSIS IR beállítása](tutorial-create-azure-ssis-runtime-portal.md)című témakört. 

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:
> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy Azure-SSIS Integration Runtime.
> * Indítsa el a Azure-SSIS Integration Runtime.
> * Tekintse át a teljes parancsfájlt.
> * SSIS-csomagok telepítése.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) . A Azure-SSIS IRával kapcsolatos elméleti információk: [Azure-SSIS Integration Runtime Overview (áttekintés](concepts-integration-runtime.md#azure-ssis-integration-runtime)).

- **SQL Database vagy SQL felügyelt példány**: Ha még nem rendelkezik ilyennel, hozzon létre egyet a Azure Portal az első lépések előtt. Azure Data Factory ezután létrehozza a SSISDB a SQL Database vagy az SQL felügyelt példányán. Azt javasoljuk, hogy hozzon létre SQL Database vagy SQL felügyelt példányt ugyanabban az Azure-régióban, mint az Integration Runtime. Ez a konfiguráció lehetővé teszi, hogy az integrációs modul Azure-régiók határainak átlépése nélkül írjon végrehajtási naplókat a katalógusadatbázisba. 
    - A kiválasztott adatbázis-kiszolgáló alapján a SSISDB létrehozhatók az Ön nevében egyetlen adatbázisként vagy egy rugalmas készlet részeként SQL Database, vagy az SQL felügyelt példányában, és elérhetők egy nyilvános hálózaton, vagy csatlakozhatnak egy virtuális hálózathoz. Az adatbázis-kiszolgáló SSISDB gazdagépként való kiválasztásával kapcsolatos útmutatásért lásd: [az SQL Database és az SQL felügyelt példányának összevetése](create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).
    
      Ha a SQL Database IP-tűzfallal vagy virtuális hálózati szolgáltatás-végpontokkal, vagy egy SQL-alapú felügyelt példányt használ a SSISDB üzemeltetéséhez, vagy ha a helyszíni adatközponthoz való hozzáférésre van szüksége, a saját üzemeltetésű integrációs modul konfigurálása nélkül csatlakoztassa a Azure-SSIS IRt egy virtuális hálózathoz. További információ: [Azure-SSIS IR létrehozása virtuális hálózaton](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás engedélyezve van a SQL Database. Ez a beállítás nem alkalmazható, ha a SQL Database IP-tűzfalszabályok vagy virtuális hálózati szolgáltatás-végpontokkal, vagy egy SQL-alapú felügyelt példányt használ a SSISDB futtatására szolgáló privát végponttal. További információkért lásd: [Az Azure SQL-adatbázis védelme](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Ha ezt a beállítást a PowerShell használatával szeretné engedélyezni, tekintse meg a [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)című témakört.
    - Adja hozzá az ügyfélszámítógép IP-címét vagy egy IP-címtartományt, beleértve az ügyfélszámítógép IP-címét az ügyfél IP-címeinek listájához a tűzfalbeállítások SQL Database. További információ: [kiszolgálói szintű és adatbázis-szintű tűzfalszabályok](../azure-sql/database/firewall-configure.md).
    - A kiszolgáló-rendszergazdai hitelesítő adataival vagy Azure Active Directory (Azure AD) hitelesítéssel az adat-előállító felügyelt identitásával csatlakozhat SQL Database vagy SQL felügyelt példányhoz. Azure AD-hitelesítés esetén ahhoz, hogy hozzá lehessen adni az adatokhoz tartozó felügyelt identitást az adatbázis-kiszolgálóhoz hozzáférési engedélyekkel rendelkező Azure AD-csoporthoz, tekintse meg a [Azure-SSIS IR Azure ad-hitelesítéssel történő létrehozását](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)ismertető témakört.
    - Győződjön meg arról, hogy SQL Database vagy az SQL felügyelt példánya még nem rendelkezik SSISDB. Egy Azure-SSIS IR beállítása nem támogatja a meglévő SSISDB használatát.

- Azure PowerShell. Ha PowerShell-parancsfájlt szeretne futtatni a Azure-SSIS IR beállításához, kövesse az [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-Az-ps)című témakör utasításait.

> [!NOTE]
> Azon Azure-régiók listáját, amelyekben a Azure Data Factory és a Azure-SSIS IR jelenleg elérhető, tekintse meg a [Azure Data Factory és a Azure-SSIS IR rendelkezésre állás régió szerint](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)című témakört. 

## <a name="open-the-windows-powershell-ise"></a>A Windows PowerShell integrált parancsprogram-kezelési környezet megnyitása

Nyissa meg a Windows PowerShell integrált parancsfájlkezelési környezetet (ISE) rendszergazdai engedélyekkel. 

## <a name="create-variables"></a>Változók létrehozása

Másolja a következő szkriptet az ISE-be. Határozza meg a változók értékeit. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character (for example, "$"), precede it with the escape character "`" (for example, "`$")
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS Integration Runtime info; this is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, although Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

### SSISDB info
$SSISDBServerEndpoint = "[your server name.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you're not using SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

## <a name="sign-in-and-select-your-subscription"></a>Jelentkezzen be, és válassza ki az előfizetését

A bejelentkezéshez és az Azure-előfizetés kiválasztásához adja hozzá a következő kódot a szkripthez:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-your-database-server"></a>Az adatbázis-kiszolgálóval létesített kapcsolatok ellenőrzése

A kapcsolódás ellenőrzéséhez adja hozzá a következő parancsfájlt: 

```powershell
# Validate only if you're using SSISDB
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
        Write-Warning "Cannot connect, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

Ha Azure SQL Database példányt szeretne létrehozni a parancsfájl részeként, tekintse meg a következő példát. Állítsa be a már nem definiált változók értékeit (például SSISDBServerName, FirewallIPAddress). 

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

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs használatával. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Ha az erőforráscsoport már létezik, ne másolja ezt a kódot a szkriptbe. 

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

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime létrehozása

Ha SSIS-csomagokat futtató Azure-SSIS Integration Runtime szeretne létrehozni az Azure-ban, futtassa a következő parancsokat. Ha nem használ SSISDB, kihagyhatja a CatalogServerEndpoint, a CatalogPricingTier és a CatalogAdminCredential paramétereket.

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

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you're using SSISDB
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

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data access
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
```

## <a name="start-the-azure-ssis-integration-runtime"></a>A Azure-SSIS Integration Runtime elindítása

A Azure-SSIS IR elindításához futtassa a következő parancsokat:

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
> Az egyéni telepítési idő kizárása esetén ezt a folyamatot öt percen belül el kell végezni.
>
> Ha a SSISDB-t használja, akkor a Azure Data Factory-szolgáltatás a SSISDB előkészítéséhez csatlakozik az adatbázis-kiszolgálóhoz. 
> 
> A Azure-SSIS IR beállításakor a rendszer az SSIS-hez készült Azure Feature Pack csomagot is telepíti. Ezek az összetevők az Excel/Access fájlokhoz és különböző Azure-adatforrásokhoz való kapcsolódást biztosítanak a beépített összetevők által már támogatott adatforrások mellett. További összetevőket is telepíthet, lásd: [Azure-SSIS IR egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="full-script"></a>Teljes szkript

Az ebben a szakaszban található PowerShell-szkript a SSIS-csomagokat futtató Azure-SSIS IR egy példányát konfigurálja. A szkript sikeres futtatása után SSIS-csomagokat helyezhet üzembe és futtathat az Azure-ban.

1. Nyissa meg az ISE-t.
2. Az ISE parancssorában futtassa a következő parancsot:  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Másolja az ebben a szakaszban található PowerShell-szkriptet az ISE-be.
4. Adja meg a szkript elején a paramétereknek megfelelő értékeket.
5. Futtassa a szkriptet. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS Integration Runtime info - This is a Data Factory compute resource for running SSIS packages
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
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x the number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

### SSISDB info
$SSISDBServerEndpoint = "[your server name.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you're not using SSISDB]" # WARNING: If you want to use SSISDB, ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you're using SSISDB
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
        Write-Warning "Cannot connect, exception: $_";
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

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you're using SSISDB
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

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data access
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
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

## <a name="monitor-and-manage-your-azure-ssis-ir"></a>A Azure-SSIS IR monitorozása és kezelése

A Azure-SSIS IR figyelésével és kezelésével kapcsolatos információkért lásd: 

- [A Azure-SSIS IR figyelése](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [A Azure-SSIS IR kezelése](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése

Ha a SSISDB-t használja, a csomagokat központilag telepítheti és futtathatja a Azure-SSIS IRon SQL Server Data Tools (SSDT) vagy SQL Server Management Studio (SSMS) eszközök használatával, amelyek kiszolgálói végpontján keresztül csatlakoznak az adatbázis-kiszolgálóhoz. Nyilvános végponttal rendelkező SQL Database vagy SQL felügyelt példány esetén a kiszolgálói végpont formátuma a következő: * <server name> . database.Windows.net* és * <server name> . public. <dns prefix> . database. Windows. net, 3342*, ill. 

Ha nem használ SSISDB-t, a csomagokat telepítheti fájlrendszerek, fájlmegosztás vagy egy Azure Files megosztásra, és futtathatja azokat a Azure-SSIS IR a `dtinstall` / `dtutil` / `dtexec` parancssori segédeszközök használatával. További információ: SSIS- [csomagok telepítése](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

Mindkét esetben a telepített csomagokat is futtathatja a Azure-SSIS IR a SSIS-csomag végrehajtása művelettel Azure Data Factory folyamatokban. További információ: [SSIS-csomag végrehajtásának meghívása első osztályú Azure Data Factory tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

További SSIS-dokumentációt a következő témakörben talál: 

- [SSIS-csomagok üzembe helyezése, futtatása és figyelése az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Kapcsolódás a SSISDB az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Csomagok végrehajtásának ütemezett végrehajtása az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy Azure-SSIS Integration Runtime.
> * Indítsa el a Azure-SSIS Integration Runtime.
> * Tekintse át a teljes parancsfájlt.
> * SSIS-csomagok telepítése.

A Azure-SSIS Integration Runtime testreszabásával kapcsolatos további tudnivalókért tekintse meg a következő cikket:

> [!div class="nextstepaction"]
>[A Azure-SSIS IR testreszabása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)