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
ms.date: 10/06/2017
ms.author: spelluru
ms.openlocfilehash: 434c1de8a7310036fb1bb93d45c6b1364ba1fe6a
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="deploy-sql-server-integration-services-packages-to-azure"></a>SQL Server Integration Services-csomagok üzembe helyezése az Azure-ban

[!INCLUDE [data-factory-what-is-include-md](../../includes/data-factory-what-is-include.md)]

#### <a name="this-tutorial"></a>Ez az oktatóanyag

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd [a Data Factory 1. verziójának dokumentációját](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Ez az oktatóanyag egy Azure-SSIS integrációs modul (IR) az Azure Data Factoryben való üzembe helyezésének lépéseit ismerteti. Ezután az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) használatával üzembe helyezhet SQL Server Integration Services- (SSIS-) csomagokat ebben az Azure-beli modulban. Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul létrehozása
> * Az Azure SSIS integrációs modul elindítása
> * SSIS-csomagok üzembe helyezése
> * A teljes szkript áttekintése

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot. Elméleti információk az Azure-SSIS integrációs modulra vonatkozóan: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).

## <a name="prerequisites"></a>Előfeltételek
- **Azure SQL Database-kiszolgáló**. Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. Ez a kiszolgáló üzemelteti az SSIS-katalógusadatbázist (SSISDB-t). Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi, hogy az integrációs modul Azure-régiók határainak átlépése nélkül írjon végrehajtási naplókat a katalógusadatbázisba. 
    - Győződjön meg arról, hogy az **Allow access to Azure services** (Azure-szolgáltatások hozzáférésének engedélyezése) beállítás **BE** van kapcsolva az adatbázis-kiszolgálón. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). A beállítás engedélyezése PowerShell használatával: [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
    - Vegye fel az ügyfélszámítógép IP-címét vagy az ügyfélszámítógép IP-címét tartalmazó IP-címtartományt az ügyfél IP-címeinek listájára az adatbázis-kiszolgáló tűzfalbeállításainál. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../sql-database/sql-database-firewall-configure.md). 
- **Azure PowerShell**. Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-azurerm-ps) ismertető cikkben szereplő utasításokat. Egy PowerShell-lel futtatott szkripttel üzembe helyez egy Azure SSIS integrációs modult, amely a felhőben futtat SSIS-csomagokat. 

> [!NOTE]
> Az Azure Data Factory 2-es verziója és az Azure-SSIS integrációs modulja által támogatott régiókat a [régiónként elérhető termékek](https://azure.microsoft.com/regions/services/) listájában tekintheti meg. Bontsa ki az **Adatok + analitika** csomópontot a **Data Factory 2. verziója** és az **SSIS integrációs modul** megtekintéséhez.

## <a name="launch-windows-powershell-ise"></a>A Windows PowerShell integrált parancsprogram-kezelési környezet (ISE) indítása
Indítsa el a **Windows PowerShell integrált parancsprogram-kezelési környezetet** rendszergazdai jogosultságokkal. 

## <a name="create-variables"></a>Változók létrehozása
Másolja ki, majd illessze be az alábbi kódot. Adja meg a változók értékeit. Az Azure SQL Database támogatott **tarifacsomagjainak** listájáért lásd az [SQL Database erőforrás-korlátozásaival](../sql-database/sql-database-resource-limits.md) foglalkozó témakört.

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_A4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# In public preview, only 1-8 parallel executions per node are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 
```

## <a name="validate-the-connection-to-database"></a>Az adatbázissal való kapcsolat ellenőrzése
Adja meg a következő szkriptet az Azure SQL Database-kiszolgáló (server.database.windows.net) ellenőrzéséhez. 

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

Tekintse meg a következő példát egy Azure SQL-adatbázisnak a szkript részeként történő létrehozásáról: 

Állítson be értékeket az eddig még meg nem határozott változók esetében. Például: FirewallIPAddress. 

```powershell
New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName `
  -ServerName $SQLServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SQLServerAdmin, $(ConvertTo-SecureString -String $SQLServerPass -AsPlainText -Force))

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SQLServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SQLServerName -AllowAllAzureIPs
```


## <a name="log-in-and-select-subscription"></a>Bejelentkezés és az előfizetés kiválasztása
Adja a következő kódot a szkripthez a bejelentkezéshez és az Azure-előfizetés kiválasztásához: 

```powershell
Login-AzureRmAccount
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
Most az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) segítségével helyezze üzembe az SSIS-csomagokat az Azure-ban. Csatlakozzon ahhoz az Azure SQL Serverhez, amelyen az SSIS-katalógus (SSISDB) található. Az Azure SQL Server nevének formátuma a következő: `<servername>.database.windows.net` (az Azure SQL Database esetében). 

Lásd az SSIS dokumentációjának alábbi cikkeit: 

- [SSIS-csomag üzembe helyezése, futtatása és monitorozása az Azure-on](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Csatlakozás SSIS-katalógushoz Azure-on](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Csomag végrehajtásának ütemezése az Azure-on](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="full-script"></a>Teljes szkript
Ebben a kiadásban a PowerShell-lel kell üzembe helyeznie egy Azure SSIS integrációs modult, amely SSIS-csomagokat futtat a felhőben. A modult jelenleg nem lehet az Azure Portalról üzembe helyezni. 

Az ebben a szakaszban szereplő PowerShell-szkript konfigurál a felhőben egy Azure SSIS integrációs modul példányt, amely SSIS-csomagokat futtat. A szkript sikeres futtatása után SSIS-csomagokat helyezhet üzembe és futtathat a Microsoft Azure-felhőben az Azure SQL Database-ben futó SSISDB mellett.

1. Indítsa el a Windows PowerShell integrált parancsfájlkezelési környezetet (ISE).
2. Az ISE környezetben futtassa a következő parancsot a parancssorból.    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. Másolja az ebben a szakaszban lévő PowerShell-szkriptet, majd illessze be az ISE környezetbe.
4. Adja meg a szkript elején, az Azure-beli SSIS specifikációira vonatkozó szakaszban lévő szkriptparaméterek megfelelő értékeit. Ezeket a paramétereket a következő szakasz ismerteti.
5. Futtassa a szkriptet. A szkript végén lévő `Start-AzureRmDataFactoryV2IntegrationRuntime` parancs futtatása **20–30 percet** vesz igénybe.

> [!NOTE]
> A szkript csatlakozik az Azure SQL Database-hez, és előkészíti az SSIS-katalógusadatbázist (SSISDB). A szkript konfigurálja a virtuális hálózat engedélyeit és beállításait is, ha ez lett megadva, és csatlakoztatja az Azure SSIS integrációs modul új példányát a virtuális hálózathoz.

Az Azure SQL Database támogatott **tarifacsomagjainak** listájáért lásd az [SQL Database erőforrás-korlátozásaival](../sql-database/sql-database-resource-limits.md) foglalkozó témakört. 

Az Azure Data Factory 2-es verziója és az Azure-SSIS integrációs modulja által támogatott régiókat a [régiónként elérhető termékek](https://azure.microsoft.com/regions/services/) listájában tekintheti meg. Bontsa ki az **Adatok + analitika** csomópontot a **Data Factory 2. verziója** és az **SSIS integrációs modul** megtekintéséhez.

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_A4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# In public preview, only 1-8 parallel executions per node are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

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

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

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
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-vnet"></a>Az Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz
Ha felügyelt Azure SQL-példány (privát előzetes verzió) segítségével futtatja az SQL Server Integration Services (SSIS) katalógust egy virtuális hálózaton (VNeten), az Azure-SSIS integrációs modulját is ugyanahhoz a virtuális hálózathoz kell csatlakoztatnia. Az Azure Data Factory 2.(előzetes) verzió használatával csatlakoztathatja az Azure-SSIS integrációs modult egy klasszikus virtuális hálózathoz. További információkért lásd [az Azure-SSIS integrációs modul virtuális hálózathoz történő csatlakoztatásával](join-azure-ssis-integration-runtime-virtual-network.md) foglalkozó cikket.

Virtuális hálózathoz csatlakozó Azure-SSIS integrációs modul létrehozására alkalmas teljes szkript: [Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md).

## <a name="monitor-and-manage-azure-ssis-ir"></a>Az Azure-SSIS integrációs modul monitorozása és kezelése
Azure-SSIS integrációs modul monitorozásával és kezelésével kapcsolatban lásd az alábbi cikkeket. 

- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md)

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
