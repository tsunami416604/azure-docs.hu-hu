---
title: Azure SSIS integrációs modul üzembe helyezése PowerShell segítségével | Microsoft Docs
description: Ismerje meg, hogyan helyezheti üzembe az Azure SSIS integrációs modult az Azure Data Factoryben a PowerShell használatával, hogy SSIS-csomagokat telepíthessen és futtathasson az Azure-ban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: hero-article
ms.date: 06/27/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 8dc41eb3507368bb810c30a7680b73d0d1f26408
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085411"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Azure SSIS integrációs modul üzembe helyezése az Azure Data Factoryben PowerShell segítségével
Ez az oktatóanyag egy Azure-SSIS integrációs modul (IR) az Azure Data Factoryben való üzembe helyezésének lépéseit ismerteti. Ezután az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) használatával SQL Server Integration Services- (SSIS-) csomagokat helyezhet üzembe és futtathat ebben az Azure-beli modulban. Az oktatóanyag során a következő lépéseket hajtja végre:

> [!NOTE]
> Ebben a cikkben az Azure PowerShellt használjuk egy Azure SSIS IR üzembe helyezéséhez. A Data Factory felhasználói felületének (UI) az Azure SSIS IR üzembe helyezéséhez való használatával kapcsolatban lásd az [Azure SSIS integrációs modul létrehozásának oktatóanyagát](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul létrehozása
> * Az Azure SSIS integrációs modul elindítása
> * SSIS-csomagok üzembe helyezése
> * A teljes szkript áttekintése

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot. Elméleti információk az Azure-SSIS integrációs modulra vonatkozóan: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime). 
- **Azure SQL Database-kiszolgáló**. Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. Ez a kiszolgáló üzemelteti az SSIS-katalógusadatbázist (SSISDB-t). Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi, hogy az integrációs modul Azure-régiók határainak átlépése nélkül írjon végrehajtási naplókat a katalógusadatbázisba. 
    - A kiválasztott adatbázis-kiszolgáló alapján az SSISDB létrehozható az Ön nevében önálló adatbázisként, egy rugalmas készlet részeként vagy egy előzetes verziójú felügyelt példányban, és elérhető nyilvános hálózatban vagy egy virtuális hálózathoz csatlakozva. Az SSISDB-t futtató adatbázis-kiszolgáló típusának kiválasztásával kapcsolatos útmutatásért lásd: [Az SQL Database és a felügyelt példány (előzetes verzió) összehasonlítása](create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview). Ha az Azure SQL Database-t virtuális hálózati szolgáltatásvégpontokkal/előzetes verziójú felügyelt példánnyal használja az SSISDB üzemeltetésére, vagy a helyszíni adatokhoz való hozzáférésre van szüksége, az Azure SSIS integrációs modulját csatlakoztatnia kell egy virtuális hálózathoz, lásd: [Azure SSIS integrációs modul létrehozása virtuális hálózatban](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 
    - Győződjön meg arról, hogy az **Allow access to Azure services** (Azure-szolgáltatások hozzáférésének engedélyezése) beállítás **BE** van kapcsolva az adatbázis-kiszolgálón. Ez a beállítás nem alkalmazható, amikor az Azure SQL Database-t virtuális hálózati szolgáltatásvégpontokkal/előzetes verziójú felügyelt példánnyal használja az SSISDB üzemeltetésére. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). A beállítás engedélyezése PowerShell használatával: [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1). 
    - Vegye fel az ügyfélszámítógép IP-címét vagy az ügyfélszámítógép IP-címét tartalmazó IP-címtartományt az ügyfél IP-címeinek listájára az adatbázis-kiszolgáló tűzfalbeállításainál. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../sql-database/sql-database-firewall-configure.md). 
    - Az adatbázis-kiszolgálóhoz SQL-hitelesítéssel, a kiszolgálói rendszergazdai hitelesítő adataival vagy Azure Active Directory (AAD) hitelesítéssel, az Azure Data Factory felügyeltszolgáltatás-identitásával (MSI) csatlakozhat.  Az utóbbi esetében hozzá kell adnia a Data Factory MSI-t egy AAD-csoporthoz, amely rendelkezik engedélyekkel az adatbázis-kiszolgálóhoz, lásd: [Azure SSIS integrációs modul létrehozása AAD-hitelesítéssel](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 
    - Győződjön meg arról, hogy az Azure SQL Database-kiszolgálóján nincs SSIS-katalógus (SSISDB-adatbázis). Az Azure-SSIS IR üzembe helyezése nem támogatja a meglévő SSIS-katalógusok használatát. 
- **Azure PowerShell**. Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-azurerm-ps) ismertető cikkben szereplő utasításokat. Egy PowerShell-lel futtatott szkripttel üzembe helyez egy Azure SSIS integrációs modult, amely a felhőben futtat SSIS-csomagokat. 

> [!NOTE]
> - Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. 
> - Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető az Azure-SSIS integrációs modul, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg az **SSIS integrációs modul**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt.

## <a name="launch-windows-powershell-ise"></a>A Windows PowerShell integrált parancsprogram-kezelési környezet (ISE) indítása
Indítsa el a **Windows PowerShell integrált parancsprogram-kezelési környezetet** rendszergazdai jogosultságokkal. 

## <a name="create-variables"></a>Változók létrehozása
Másolja ki, majd illessze be az alábbi kódot. Adja meg a változók értékeit. Az Azure SQL Database támogatott **tarifacsomagjainak** listájáért lásd az [SQL Database erőforrás-korlátozásaival](../sql-database/sql-database-resource-limits.md) foglalkozó témakört.

```powershell
# Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "[Azure subscription name]"
$ResourceGroupName = "[Azure resource group name]"
# Data factory name. Must be globally unique
$DataFactoryName = "[Data factory name]"
$DataFactoryLocation = "EastUS"

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[Specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[Specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS"
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B". For standard/premium/Elastic Pool tiers, specify "S0", "S1", "S2", "S3", etc.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>)]"
```

## <a name="validate-the-connection-to-database"></a>Az adatbázissal való kapcsolat ellenőrzése
Adja meg a következő szkriptet az Azure SQL Database-kiszolgáló (`<servername>.database.windows.net`) ellenőrzéséhez. 

```powershell
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
```

Tekintse meg a következő példát egy Azure SQL-adatbázisnak a szkript részeként történő létrehozásáról: 

Állítson be értékeket az eddig még meg nem határozott változók esetében. Például: SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="log-in-and-select-subscription"></a>Bejelentkezés és az előfizetés kiválasztása
Adja a következő kódot a szkripthez a bejelentkezéshez és az Azure-előfizetés kiválasztásához: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen.

Ha az erőforráscsoport már létezik, ne másolja ezt a kódot a szkriptbe. 

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
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogAdminCredential $serverCreds `
                                           -CatalogPricingTier $SSISDBPricingTier
```

## <a name="start-integration-runtime"></a>Integrációs modul indítása
Futtassa a következő parancsot az Azure SSIS integrációs modul indításához: 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
A parancs végrehajtása **20–30 percig** is eltarthat. 

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése
Most az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) segítségével helyezze üzembe az SSIS-csomagokat az Azure-ban. Csatlakozzon ahhoz az Azure SQL Serverhez, amelyen az SSIS-katalógus (SSISDB) található. Az Azure SQL Database-kiszolgáló nevének formátuma a következő: `<servername>.database.windows.net`. 

Lásd az SSIS dokumentációjának alábbi cikkeit: 

- [SSIS-csomag üzembe helyezése, futtatása és monitorozása az Azure-on](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Csatlakozás SSIS-katalógushoz Azure-on](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Csomag végrehajtásának ütemezése az Azure-on](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="full-script"></a>Teljes szkript
Az ebben a szakaszban szereplő PowerShell-szkript konfigurál a felhőben egy Azure SSIS integrációs modul példányt, amely SSIS-csomagokat futtat. A szkript sikeres futtatása után SSIS-csomagokat helyezhet üzembe és futtathat a Microsoft Azure-felhőben az Azure SQL Database-ben futó SSISDB mellett.

1. Indítsa el a Windows PowerShell integrált parancsfájlkezelési környezetet (ISE).
2. Az ISE környezetben futtassa a következő parancsot a parancssorból.    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. Másolja az ebben a szakaszban lévő PowerShell-szkriptet, majd illessze be az ISE környezetbe.
4. Adja meg a szkript elején a paramétereknek megfelelő értékeket.
5. Futtassa a szkriptet. A szkript végén lévő `Start-AzureRmDataFactoryV2IntegrationRuntime` parancs futtatása **20–30 percet** vesz igénybe.

> [!NOTE]
> - A szkript csatlakozik az Azure SQL Database-kiszolgálóhoz, és előkészíti az SSIS-katalógusadatbázist (SSISDB).

> - Amikor üzembe helyezi az Azure-SSIS IR egy példányát, az Azure Feature Pack for SSIS és az Access Redistributable is telepítve lesz. Ezek az összetevők biztosítják a csatlakozást az Excel- és Access-fájlokhoz és különböző Azure-adatforrásokhoz a beépített összetevők által támogatott adatforrások mellett. Ezenkívül további összetevőket is telepíthet. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md).

Az Azure SQL Database támogatott **tarifacsomagjainak** listájáért lásd az [SQL Database erőforrás-korlátozásaival](../sql-database/sql-database-resource-limits.md) foglalkozó témakört. 

Az Azure Data Factory 2-es verziója és az Azure-SSIS integrációs modulja által támogatott régiókat a [régiónként elérhető termékek](https://azure.microsoft.com/regions/services/) listájában tekintheti meg. Bontsa ki az **Adatok + analitika** csomópontot a **Data Factory 2. verziója** és az **SSIS integrációs modul** megtekintéséhez.

```powershell
# Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "[Azure subscription name]"
$ResourceGroupName = "[Azure resource group name]"
# Data factory name. Must be globally unique
$DataFactoryName = "[Data factory name]"
$DataFactoryLocation = "EastUS"

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[Specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[Specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS"
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B". For standard/premium/Elastic Pool tiers, specify "S0", "S1", "S2", "S3", etc.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>)]"

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

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
    
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
    
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
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogAdminCredential $serverCreds `
                                           -CatalogPricingTier $SSISDBPricingTier

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-virtual-network"></a>Az Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz
Ha az Azure SQL Database-t virtuális hálózathoz csatlakozó virtuális hálózati szolgáltatásvégpontokkal/előzetes verziójú felügyelt példánnyal használja az SSISDB üzemeltetésére, az Azure-SSIS integrációs modulját is ugyanahhoz a virtuális hálózathoz kell csatlakoztatnia. Az Azure Data Factory használatával csatlakoztathatja az Azure-SSIS integrációs modult egy virtuális hálózathoz. További információkért tekintse meg [az Azure-SSIS integrációs modul virtuális hálózathoz történő csatlakoztatásával](join-azure-ssis-integration-runtime-virtual-network.md) foglalkozó cikket.

Virtuális hálózathoz csatlakozó Azure-SSIS integrációs modul létrehozására alkalmas teljes szkript: [Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md).

## <a name="monitor-and-manage-azure-ssis-ir"></a>Az Azure-SSIS integrációs modul monitorozása és kezelése
Azure-SSIS integrációs modul monitorozásával és kezelésével kapcsolatban lásd az alábbi cikkeket. 

- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>További lépések
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
